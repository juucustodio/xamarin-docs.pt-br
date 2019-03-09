---
title: Notificações remotas com o Google Cloud Messaging
description: Este passo a passo fornece uma explicação passo a passo de como usar o Google Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Android. Ele descreve as várias classes que você deve implementar para se comunicar com o Google Cloud Messaging (GCM), ele explica como definir permissões no manifesto do Android para o acesso para o GCM e ele demonstra o sistema de mensagens de ponta a ponta com um programa de teste de exemplo.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: e5a5e44a61d352b5de05564ebb7192d21ed83dfa
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668888"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notificações remotas com o Google Cloud Messaging

_Este passo a passo fornece uma explicação passo a passo de como usar o Google Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Android. Ele descreve as várias classes que você deve implementar para se comunicar com o Google Cloud Messaging (GCM), ele explica como definir permissões no manifesto do Android para o acesso para o GCM e ele demonstra o sistema de mensagens de ponta a ponta com um programa de teste de exemplo._

> [!NOTE]
> Foi substituído pelo GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e APIs de cliente [foram preteridos](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estará disponível assim que 11 de abril de 2019.

## <a name="gcm-notifications-overview"></a>Visão geral de notificações do GCM

Neste passo a passo, vamos criar um aplicativo xamarin. Android que usa o Google Cloud Messaging (GCM) para implementar notificações remotas (também conhecido como *notificações por push*). Implementaremos os vários serviços de intenção e o ouvinte de usam o GCM para mensagens remotas e faremos o teste nossa implementação com um programa de linha de comando que simula um servidor de aplicativos. 

Observe que o Firebase Cloud Messaging (FCM) é a nova versão do GCM &ndash; Google recomenda o uso de FCM, em vez de GCM. Se você estiver usando o GCM, atualizar para o FCM é recomendado. Para obter mais informações sobre o FCM, consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Antes de continuar com este passo a passo, você deve adquirir as credenciais necessárias para usar os servidores do GCM do Google; Esse processo é explicado [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). Em particular, você precisará de uma *chave de API* e uma *ID de remetente* para inserir o código de exemplo apresentado neste passo a passo. 

Vamos usar as etapas a seguir para criar um aplicativo de cliente habilitado para GCM xamarin. Android:

1.  Instale pacotes adicionais necessários para comunicações com servidores do GCM.
2.  Configure permissões de aplicativo para acesso aos servidores do GCM.
3.  Implementar o código para verificar a presença do Google Play Services. 
4.  Implemente um serviço de intenção de registro que negocia com o GCM para um token de registro.
5.  Implemente um serviço de escuta de ID de instância que escuta as atualizações de token de registro do GCM.
6.  Implemente um serviço de escuta do GCM que recebe mensagens remotas do servidor do aplicativo por meio do GCM.

Esse aplicativo usará um novo recurso do GCM conhecido como *sistema de mensagens do tópico*. No sistema de mensagens do tópico, o servidor de aplicativo envia uma mensagem para um tópico, em vez de uma lista de dispositivos individuais. Dispositivos que assinam para esse tópico podem receber mensagens do tópico como notificações por push. Para obter mais informações sobre as mensagens de tópico do GCM, consulte do Google [implementar mensagens do tópico](https://developers.google.com/cloud-messaging/topic-messaging). 

Quando o aplicativo cliente está pronto, implementaremos uma linha de comando C# aplicativo que envia uma notificação por push para nosso aplicativo de cliente por meio do GCM. 

## <a name="walkthrough"></a>Passo a passo

Para começar, vamos criar uma nova solução vazia chamada **RemoteNotifications**. Em seguida, vamos adicionar um novo projeto Android dessa solução que se baseia o **aplicativo Android** modelo. Vamos chamar esse projeto **ClientApp**. (Se você não estiver familiarizado com a criação de projetos do xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) O **ClientApp** projeto conterá o código para o aplicativo cliente xamarin. Android que recebe notificações remotas por meio do GCM. 

### <a name="add-required-packages"></a>Adicione os pacotes necessários

Antes podemos implementar nosso código de aplicativo do cliente, é necessário instalar vários pacotes que usaremos para a comunicação com o GCM. Além disso, devemos adicionar o aplicativo Google Play Store nosso dispositivo se ele não ainda estiver instalado.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Adicione o pacote do Xamarin Google Play Services GCM

Para receber mensagens do Google Cloud Messaging, o [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework deve estar presente no dispositivo. Sem esse framework, um aplicativo do Android não pode receber mensagens de servidores do GCM. Google Play Services é executado em segundo plano enquanto o dispositivo Android estiver ligado, no modo silencioso ouvir as mensagens do GCM. Quando essas mensagens chegam, o Google Play Services converte as mensagens em intenções e, em seguida, transmite essas intenções para aplicativos que foram registrados para eles. 

No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** ; no Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes...** . Pesquise **Xamarin Google Play Services - GCM** e instalar esse pacote para o **ClientApp** projeto: 

[![Instalando serviços do Google Play](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quando você instala **Xamarin Google Play Services - GCM**, **Xamarin Google Play Services - Base** é instalado automaticamente. Se você receber um erro, altere o projeto *Android mínimo para o destino* definir como um valor diferente de **compilar usando a versão do SDK** e tente repetir a instalação do NuGet. 

Em seguida, edite **MainActivity.cs** e adicione o seguinte `using` instruções:

```csharp
using Android.Gms.Common;
using Android.Util;
```

Isso disponibiliza tipos no pacote do Google Play Services GMS ao nosso código, e ele adiciona a funcionalidade de registro em log que usaremos para acompanhar nossos transações com GMS. 

#### <a name="google-play-store"></a>Google Play Store

Para receber mensagens do GCM, o aplicativo do Google Play Store deve ser instalado no dispositivo. (Sempre que um aplicativo do Google Play é instalado em um dispositivo, Google Play Store também é instalado, portanto, é provável que já está instalado em seu dispositivo de teste.) Sem o Google Play, um aplicativo do Android não pode receber mensagens do GCM. Se você ainda não tiver o aplicativo do Google Play Store instalado em seu dispositivo, visite o [Google Play](https://support.google.com/googleplay) site da web para baixar e instalar o Google Play. 

Como alternativa, você pode usar um emulador do Android que executam o Android 2.2 ou posterior, em vez de um dispositivo de teste (você não precisa instalar o Google Play Store em um emulador do Android). No entanto, se você usar um emulador, você deve usar o Wi-Fi para conectar-se para o GCM e você deve abrir várias portas no firewall Wi-Fi, conforme explicado mais adiante neste passo a passo. 

### <a name="set-the-package-name"></a>Defina o nome do pacote

Na [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), especificamos um nome de pacote para nosso aplicativo habilitado para GCM (esse nome de pacote também serve como o *ID do aplicativo* que está associado com a nossa chave de API e a ID de remetente). Vamos abrir as propriedades para o **ClientApp** do projeto e defina o nome do pacote como essa cadeia de caracteres. Neste exemplo, definimos o nome do pacote como `com.xamarin.gcmexample`:

[![Definir o nome do pacote](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Observe que o aplicativo cliente poderá receber um token de registro do GCM, se o nome do pacote não *exatamente* corresponder ao nome do pacote que são inseridos no console do desenvolvedor do Google. 

### <a name="add-permissions-to-the-android-manifest"></a>Adicionar permissões para o manifesto do Android

Um aplicativo do Android deve ter as seguintes permissões configuradas antes que ele pode receber notificações do Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Concede permissão para nosso aplicativo para se registrar e receber mensagens do Google Cloud Messaging. (O que faz `c2dm` significam? Isso significa _nuvem para mensagens de dispositivo_, que é o predecessor agora foi preterida GCM. 
    GCM ainda usa `c2dm` em muitas das suas cadeias de caracteres de permissão.) 

-   `android.permission.WAKE_LOCK` &ndash; (Opcional) Impede que o dispositivo da CPU no estado de suspensão enquanto ouve uma mensagem. 

-   `android.permission.INTERNET` &ndash; Concede acesso à internet para que o aplicativo cliente possa se comunicar com o GCM. 

-   *nome_do_pacote* `.permission.C2D_MESSAGE` &ndash; registra o aplicativo com o Android e solicita permissão para receber exclusivamente C2D todas as mensagens (nuvem para dispositivo). O *nome_do_pacote* prefixo é o mesmo que sua ID do aplicativo. 

Vamos definir essas permissões no manifesto do Android. Vamos editar **androidmanifest. XML** e substitua o conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

No XML acima, altere *YOUR_PACKAGE_NAME* para o nome do pacote para o seu projeto de aplicativo do cliente. Por exemplo, `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Check for Google Play Services

Para este passo a passo, estamos criando um aplicativo básico com um único `TextView` na interface do usuário. Esse aplicativo diretamente não indica a interação com o GCM. Em vez disso, podemos assiste a janela de saída para ver como nosso handshakes de aplicativo com o GCM, e vamos verificar a bandeja de notificação para novas notificações assim que elas chegam. 

Primeiro, vamos criar um layout para a área de mensagem. Edite **Resources.layout.Main.axml** e substitua o conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Salve **Main. axml** e fechá-la.

Quando o aplicativo cliente é iniciado, queremos que ela para verificar se o Google Play Services está disponível antes de tentar entrar em contato com o GCM. Edite **MainActivity.cs** e substitua o ``count`` declaração de variável com a seguinte declaração de variável de instância da instância: 

```csharp
TextView msgText;
```

Em seguida, adicione o seguinte método para o **MainActivity** classe: 

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "Sorry, this device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Esse código verifica o dispositivo para ver se o APK Google Play Services está instalado. Se não estiver instalado, uma mensagem será exibida na área de mensagem que instrui o usuário a fazer o download de um APK o Google Play Store (ou habilitá-lo nas configurações do sistema do dispositivo). Como queremos executar essa verificação, quando o aplicativo cliente é iniciado, vamos adicionar uma chamada para esse método no final da `OnCreate`. 


Em seguida, substitua o `OnCreate` método com o código a seguir:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Esse código verifica a presença do Google Play Services APK e grava o resultado para a área de mensagem. 

Vamos completamente recompile e execute o aplicativo. Você verá uma tela semelhante à seguinte captura de tela: 

[![Google Play Services está disponível](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Se você não receber esse resultado, verifique se o APK de serviços do Google Play é instalado no dispositivo e que o **Xamarin Google Play Services - GCM** pacote é adicionado à sua **ClientApp** projeto conforme explicado anteriormente. Se você receber um erro de build, tente a solução de limpeza e compilar o projeto novamente. 

Em seguida, vamos escrever código para entre em contato com o GCM e obter um token de registro.

### <a name="register-with-gcm"></a>Se registrar no GCM

Antes do aplicativo pode receber notificações remotas do servidor de aplicativo, ele deve se registrar no GCM e obter um token de registro de volta. O trabalho de registro de nosso aplicativo com o GCM é tratado por um `IntentService` que criamos. Nosso `IntentService` executa as seguintes etapas: 

1.  Usa o [InstanceID](https://developers.google.com/instance-id/) API para gerar tokens de segurança que autorizam o nosso aplicativo de cliente para acessar o servidor de aplicativo. Em troca, obtemos um registro de token do GCM.

2.  Encaminha o token de registro para o servidor de aplicativo (se o servidor de aplicativo exige que ele).

3.  Assina um ou mais canais de tópico de notificação.

Depois que podemos implementar isso `IntentService`, nós o testaremos para ver se podemos obter volta um token de registro do GCM.

Adicionar um novo arquivo chamado **RegistrationIntentService.cs** e substitua o código de modelo a seguir:


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

No código de exemplo acima, altere *YOUR_SENDER_ID* para o número de ID de remetente para o seu projeto de aplicativo do cliente. Para obter a ID de remetente para o seu projeto: 

1.  Faça logon na [Google Cloud Console](https://console.cloud.google.com/) e selecione o nome do projeto no menu suspenso. No **informações do projeto** painel é exibido para o seu projeto, clique em **vá para configurações de projeto**:

    [![Selecionando projeto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  No **as configurações** , localize o **número do projeto** &ndash; esta é a ID de remetente para o seu projeto:

    [![Número de projeto exibido](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Queremos começar nossa `RegistrationIntentService` ao nosso aplicativo começa a ser executado. Edite **MainActivity.cs** e modifique a `OnCreate` método para que nossa `RegistrationIntentService` é iniciado depois que podemos verificar a presença do Google Play Services: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Agora vamos dar uma olhada em cada seção do `RegistrationIntentService` para entender como ele funciona. 

Primeiro, podemos fazer anotações em nossa `RegistrationIntentService` com o seguinte atributo para indicar que nosso serviço não deve ser instanciado pelo sistema: 

```csharp
[Service (Exported = false)]
```

O `RegistrationIntentService` construtor nomeia o thread de trabalho *RegistrationIntentService* para facilitar a depuração. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

A principal funcionalidade do `RegistrationIntentService` reside no `OnHandleIntent` método. Vamos examinar esse código para ver como ele registra o nosso aplicativo com o GCM.


##### <a name="request-a-registration-token"></a>Solicitar um Token de registro

`OnHandleIntent` primeiro chama do Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) método para solicitar um token de registro do GCM. Podemos encapsular esse código em um `lock` para se proteger contra a possibilidade de várias tentativas de registro ocorrendo simultaneamente &ndash; o `lock` garante que essas tentativas são processadas em sequência. Se não conseguirmos obter um token de registro, uma exceção é lançada e registramos um erro. Se o registro terá êxito, `token` é definido como o token de registro que obtivemos de volta do GCM: 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>Encaminhar o Token de registro para o servidor de aplicativo

Se podemos obter um token de registro (ou seja, nenhuma exceção foi lançada), podemos chamar `SendRegistrationToAppServer` para associar o registro do usuário token com a conta do lado do servidor (se houver) que é mantido pelo nosso aplicativo. Como essa implementação depende do design do servidor de aplicativo, um método vazio é fornecido aqui: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Em alguns casos, o servidor de aplicativo não precisa de token de registro do usuário; Nesse caso, esse método pode ser omitido. Quando um token de registro é enviado para o servidor de aplicativo, `SendRegistrationToAppServer` deve manter um valor booliano que indica se o token foi enviado ao servidor. Se esse valor booliano for falso, `SendRegistrationToAppServer` envia o token para o servidor de aplicativo &ndash; caso contrário, o token já foi enviado para o servidor de aplicativo em uma chamada anterior. 


##### <a name="subscribe-to-the-notification-topic"></a>Assinar o tópico de notificação

Em seguida, chamamos nosso `Subscribe` método para indicar para o GCM que desejamos assinar um tópico de notificação. Na `Subscribe`, podemos chamar o [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API para assinar nosso aplicativo de cliente para todas as mensagens em `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

O servidor de aplicativo deve enviar mensagens de notificação para `/topics/global` se estivermos para recebê-las. Observe que o nome do tópico em `/topics` pode ser qualquer coisa que você deseja, desde que o servidor de aplicativo e o aplicativo cliente concordar com esses nomes. (Aqui, escolhemos o nome `global` para indicar que desejamos receber mensagens em todos os tópicos com suporte pelo servidor de aplicativo.) 

Para obter informações sobre o tópico do GCM no lado do servidor de mensagens, consulte do Google [enviar mensagens para tópicos](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Implementar um serviço de escuta de ID de instância

Tokens do registro são exclusivos e seguro; No entanto, o aplicativo cliente (ou GCM) talvez seja necessário atualizar o token de registro no caso de reinstalação do aplicativo ou um problema de segurança. Por esse motivo, devemos implementar um `InstanceIdListenerService` que responde a solicitações de atualização do token do GCM. 

Adicionar um novo arquivo chamado **InstanceIdListenerService.cs** e substitua o código de modelo a seguir: 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Anotar `InstanceIdListenerService` com o seguinte atributo para indicar que o serviço não deve ser instanciado pelo sistema e que ela possa receber o token de registro GCM (também chamado *ID da instância*) solicitações de atualização: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

O `OnTokenRefresh` método em nosso serviço começa a `RegistrationIntentService` , de modo que ele pode interceptar o novo token de registro.


#### <a name="test-registration-with-gcm"></a>Registro de teste com o GCM

Vamos completamente recompile e execute o aplicativo. Se você receber um token de registro do GCM com êxito, o token de registro deverá ser exibido na janela de saída. Por exemplo: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Lidar com mensagens de Downstream 

O código que implementamos até o momento é somente código de "configuração"; ele verifica se o Google Play Services está instalado e negocia com o GCM e o servidor de aplicativo para preparar o nosso aplicativo de cliente para o recebimento de notificações remotas. No entanto, ainda precisamos implementar o código que realmente recebe e processa as mensagens de notificação de downstream. Para fazer isso, devemos implementar um *serviço de escuta do GCM*. Esse serviço recebe mensagens do tópico do servidor de aplicativo e localmente transmite-os como notificações. Depois que podemos implementar esse serviço, vamos criar um programa de teste para enviar mensagens para o GCM para que possamos ver se nossa implementação funciona corretamente. 


#### <a name="add-a-notification-icon"></a>Adicionar um ícone de notificação

Vamos adicionar um pequeno ícone que será exibido na área de notificação quando nossa notificação é iniciada pela primeira vez. Você pode copiar [esse ícone](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) ao seu projeto ou criar seu próprio ícone personalizado. Nomear o arquivo de ícone **ic_stat_button_click.png** e copie-o para o **recursos/drawable** pasta. Lembre-se de usar **Adicionar > Item existente...**  para incluir esse arquivo de ícone em seu projeto.


#### <a name="implement-a-gcm-listener-service"></a>Implementar um serviço de escuta do GCM

Adicionar um novo arquivo chamado **GcmListenerService.cs** e substitua o código de modelo a seguir:

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

Vamos dar uma olhada em cada seção do nosso `GcmListenerService` para entender como ele funciona. 

Primeiro, estamos anotar `GcmListenerService` com um atributo para indicar que esse serviço não deve ser instanciado pelo sistema, e nós incluir um filtro intencional para indicar que ele recebe mensagens do GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Quando `GcmListenerService` recebe uma mensagem do GCM, o `OnMessageReceived` método é invocado. Esse método extrai o conteúdo da mensagem de passado `Bundle`, registra o conteúdo da mensagem (de modo que podemos pode exibi-lo na janela de saída) e chamadas `SendNotification` para iniciar uma notificação local com o conteúdo da mensagem recebida: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

O `SendNotification` método usos `Notification.Builder` criar a notificação e, em seguida, ele usa o `NotificationManager` para iniciar a notificação. Na verdade, isso converte a mensagem de notificação remota em uma notificação de local para ser apresentado ao usuário.
Para obter mais informações sobre como usar `Notification.Builder` e `NotificationManager`, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Declare o receptor no manifesto

Podemos possa receber mensagens do GCM, podemos deve declarar o ouvinte do GCM no manifesto do Android. Vamos editar **androidmanifest. XML** e substitua o `<application>` seção com o XML a seguir: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

No XML acima, altere *YOUR_PACKAGE_NAME* para o nome do pacote para o seu projeto de aplicativo do cliente. Em nosso exemplo de passo a passo, o nome do pacote é `com.xamarin.gcmexample`. 

Vejamos o que faz cada configuração neste XML:

|Configuração|Descrição|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Declara que o nosso aplicativo implementa um receptor do GCM que captura e processa as mensagens de notificação por push recebidas.|
|`com.google.android.c2dm.permission.SEND`|Declara que os servidores do GCM só podem enviar mensagens diretamente para o aplicativo.|
|`com.google.android.c2dm.intent.RECEIVE`|Filtro de intenção de publicidade que nosso aplicativo lida com mensagens de difusão do GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Filtro intencional de publicidade que nosso aplicativo lida com novas tentativas de registro (ou seja, implementamos um serviço de escuta de ID de instância).|

Como alternativa, você pode decorar `GcmListenerService` com esses atributos em vez de especificá-los em XML; aqui, especifique-os na **androidmanifest. XML** para que os exemplos de código são mais fáceis de seguir. 


### <a name="create-a-message-sender-to-test-the-app"></a>Criar um remetente da mensagem para testar o aplicativo

Vamos adicionar um C# aplicativo de console da área de trabalho do projeto à solução e chamá-lo **MessageSender**. Vamos usar esse aplicativo de console para simular um servidor de aplicativos &ndash; ele enviará as mensagens de notificação para **ClientApp** por meio do GCM. 


#### <a name="add-the-jsonnet-package"></a>Adicionar o pacote Json.NET

Neste aplicativo de console, estamos criando uma carga JSON que contém a mensagem de notificação que desejamos enviar para o aplicativo cliente. Vamos usar o **Json.NET** de pacote na **MessageSender** para torná-lo mais fácil de criar o objeto JSON exigido pelo GCM. No Visual Studio, clique com botão direito **referências > Gerenciar pacotes NuGet...** ; no Visual Studio para Mac, clique com botão direito **pacotes > Adicionar pacotes...** . 

Vamos procurar o **Json.NET** de pacote e instalá-lo no projeto: 

[![Instalando o pacote Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Adicione uma referência a System.NET. http

Podemos também precisará adicionar uma referência a `System.Net.Http` , de modo que podemos pode instanciar um `HttpClient` para o envio de nossa mensagem de teste para o GCM. No **MessageSender** do projeto, clique com botão direito **referências > Adicionar referência** e role para baixo até ver **HTTP**. Coloque uma marca de seleção ao lado **System.NET. HTTP** e clique em **Okey**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Implementar o código que envia uma mensagem de teste

Na **MessageSender**, edite **Program.cs** e substitua o conteúdo pelo seguinte código:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

No código acima, altere *YOUR_API_KEY* para a chave de API para seu projeto de aplicativo do cliente. 

Este servidor de aplicativo de teste envia a mensagem formatada em JSON a seguir para o GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, por sua vez, encaminha essa mensagem, para seu aplicativo cliente. Vamos criar **MessageSender** e abra uma janela do console onde é possível executá-lo da linha de comando.



### <a name="try-it"></a>Experimente!

Agora estamos prontos para testar nosso aplicativo de cliente. Se você estiver usando um emulador ou se seu dispositivo está se comunicando com o GCM Wi-Fi, é necessário abrir as seguintes portas TCP no firewall para que as mensagens do GCM obter por meio de: 5228, 5229 e 5230.

Inicie o aplicativo cliente e observe a janela de saída. Após o `RegistrationIntentService` recebe com êxito um registro de token do GCM, a janela de saída deve exibir o token de saída de log a seguir:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

Agora o aplicativo cliente está pronto para receber uma mensagem de notificação remota. Na linha de comando, execute as **MessageSender.exe** programa para enviar uma mensagem de notificação "Hello, Xamarin" para o aplicativo cliente.
Se você ainda não criou o **MessageSender** de projeto, faça isso agora.

Para executar **MessageSender.exe** em Visual Studio, abra um prompt de comando, altere para o **MessageSender/bin/Debug** diretório e execute o comando diretamente:

```cmd
MessageSender.exe
```

Para executar **MessageSender.exe** em Visual Studio para Mac, abra uma sessão de Terminal, altere para **MessageSender/bin/Debug** o diretório e use o mono para executar **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Ele pode levar até um minuto para a mensagem propagar por meio do GCM e volta para seu aplicativo cliente. Se a mensagem é recebida com êxito, deveremos ver saída a seguir na janela de saída: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Além disso, você deve observar que um novo ícone de notificação foi exibido na bandeja de notificação: 

[![Ícone de notificação é exibida no dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Quando você abre a bandeja de notificação para exibir as notificações, você deverá ver nossa notificação remota:

[![Mensagem de notificação é exibida.](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Parabéns, seu aplicativo recebeu sua primeira notificação remota!

Observe que mensagens do GCM não serão recebidas se o aplicativo for interrompido por força. Para retomar as notificações após uma parada de força, o aplicativo deve ser reiniciado manualmente. Para obter mais informações sobre esta política de Android, consulte [inicie controles em aplicativos interrompidos](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) e isso [postagem de estouro de pilha](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Resumo

Este passo a passo detalhadas as etapas para implementar notificações remotas em um aplicativo xamarin. Android. Ele descreveu como instalar pacotes adicionais necessários para comunicações do GCM e explicou como configurar permissões de aplicativo para acesso aos servidores do GCM. Ele é fornecido o código de exemplo que ilustra como verificar a presença do Google Play Services, como implementar um serviço de intenção de registro e o serviço de escuta de ID de instância que negocia com o GCM para um token de registro e como implementar um ouvinte GCM serviço que recebe e processa as mensagens de notificação remota. Por fim, implementamos um programa de teste de linha de comando para enviar notificações de teste para nosso aplicativo de cliente por meio do GCM. 


## <a name="related-links"></a>Links relacionados

- [GCM RemoteNotifications (amostra)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
