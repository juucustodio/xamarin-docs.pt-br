---
title: Notificações remotas com Google Cloud Messaging
description: Este tutorial fornece uma explicação passo a passo de como usar Google Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo Xamarin. Android. Ele descreve as várias classes que você deve implementar para se comunicar com o Google Cloud Messaging (GCM), explica como definir permissões no manifesto do Android para acesso ao GCM e demonstra o sistema de mensagens de ponta a ponta com um programa de teste de exemplo.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: b621d61584cc39f669c662db3d1df264d9a92eb9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292307"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notificações remotas com Google Cloud Messaging

> [!WARNING]
> O Google preteriu o GCM a partir de 10 de abril de 2018. Os documentos e projetos de exemplo a seguir podem não ser mais mantidos. As APIs de cliente e servidor GCM do Google serão removidas assim que 29 de maio de 2019. O Google recomenda migrar aplicativos GCM para o firebase Cloud Messaging (FCM). Para obter mais informações sobre a substituição e a migração do GCM, consulte [Google Cloud Messaging PREterido](https://developers.google.com/cloud-messaging/).
>
> Para começar a usar as notificações remotas usando o firebase Cloud Messaging com o Xamarin, consulte [notificações remotas com FCM](remote-notifications-with-fcm.md).

_Este tutorial fornece uma explicação passo a passo de como usar Google Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo Xamarin. Android. Ele descreve as várias classes que você deve implementar para se comunicar com o Google Cloud Messaging (GCM), explica como definir permissões no manifesto do Android para acesso ao GCM e demonstra o sistema de mensagens de ponta a ponta com um programa de teste de exemplo._

## <a name="gcm-notifications-overview"></a>Visão geral das notificações do GCM

Neste tutorial, criaremos um aplicativo Xamarin. Android que usa Google Cloud Messaging (GCM) para implementar notificações remotas (também conhecidas como *notificações por push*). Implementaremos os vários serviços de tentativa e de ouvinte que usam o GCM para mensagens remotas e testaremos nossa implementação com um programa de linha de comando que simula um servidor de aplicativos.

Antes de prosseguir com este passo a passos, você deve adquirir as credenciais necessárias para usar os servidores GCM do Google; Esse processo é explicado em [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md).
Em particular, você precisará de uma *chave de API* e uma ID de *remetente* para inserir no código de exemplo apresentado neste passo a passos.

Usaremos as seguintes etapas para criar um aplicativo cliente Xamarin. Android habilitado para GCM:

1. Instale pacotes adicionais necessários para comunicações com servidores GCM.
2. Configure permissões de aplicativo para acesso a servidores GCM.
3. Implemente o código para verificar a presença de Google Play Services.
4. Implemente um serviço de tentativa de registro que negocia com GCM para um token de registro.
5. Implemente um serviço de escuta de ID de instância que escuta atualizações de token de registro do GCM.
6. Implemente um serviço de ouvinte do GCM que recebe mensagens remotas do servidor de aplicativos por meio do GCM.

Este aplicativo usará um novo recurso do GCM, conhecido como *mensagens de tópico*. No tópico mensagens, o servidor de aplicativos envia uma mensagem para um tópico, em vez de uma lista de dispositivos individuais. Os dispositivos que assinam esse tópico podem receber mensagens de tópico como notificações por push.

Quando o aplicativo cliente estiver pronto, implementaremos um aplicativo de linha C# de comando que envia uma notificação por push ao nosso aplicativo cliente por meio do gcm.

## <a name="walkthrough"></a>Passo a passo

Para começar, vamos criar uma nova solução vazia chamada **RemoteNotifications**. Em seguida, vamos adicionar um novo projeto do Android a essa solução com base no modelo de **aplicativo do Android** . Vamos chamar este projeto de **ClientApp**. (Se você não estiver familiarizado com a criação de projetos do Xamarin. Android, consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) O projeto **ClientApp** conterá o código para o aplicativo cliente Xamarin. Android que recebe notificações remotas via gcm.

### <a name="add-required-packages"></a>Adicionar pacotes necessários

Antes de implementarmos nosso código de aplicativo cliente, devemos instalar vários pacotes que usaremos para comunicação com o GCM. Além disso, devemos adicionar o aplicativo Google Play Store ao nosso dispositivo se ele ainda não estiver instalado.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Adicionar o pacote Xamarin Google Play Services GCM

Para receber mensagens de Google Cloud Messaging, a estrutura de [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) deve estar presente no dispositivo. Sem essa estrutura, um aplicativo Android não pode receber mensagens de servidores GCM. Google Play Services é executado em segundo plano enquanto o dispositivo Android está ligado, ouvindo silenciosamente as mensagens do GCM. Quando essas mensagens chegam, o Google Play Services converte as mensagens em tentativas e, em seguida, transmite essas intenções para aplicativos que se registraram para elas.

No Visual Studio, clique com o botão direito do mouse em **referências > gerenciar pacotes NuGet...** ; em Visual Studio para Mac, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** . Pesquise por **Xamarin Google Play Services-GCM** e instale esse pacote no projeto **ClientApp** :

[![instalar Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quando você instala o **xamarin Google Play Services-GCM**, o **Xamarin Google Play Services-base** é instalado automaticamente. Se você receber um erro, altere a configuração *mínima do Android para o destino* do projeto para um valor diferente de **compilar usando a versão do SDK** e tente instalar o NuGet novamente.

Em seguida, edite **MainActivity.cs** e adicione as seguintes instruções `using`:

```csharp
using Android.Gms.Common;
using Android.Util;
```

Isso torna os tipos no pacote Google Play Services GMS disponíveis para nosso código e adiciona a funcionalidade de log que usaremos para acompanhar nossas transações com GMS.

#### <a name="google-play-store"></a>Google Play Store

Para receber mensagens do GCM, o aplicativo Google Play Store deve ser instalado no dispositivo. (Sempre que um aplicativo Google Play é instalado em um dispositivo, o Google Play Store também é instalado, portanto, é provável que ele já esteja instalado em seu dispositivo de teste.) Sem Google Play, um aplicativo Android não pode receber mensagens do GCM.
Se você ainda não tiver o aplicativo Google Play Store instalado em seu dispositivo, visite o site do [Google Play](https://support.google.com/googleplay) para baixar e instalar o Google Play.

Como alternativa, você pode usar um emulador do Android executando o Android 2,2 ou posterior em vez de um dispositivo de teste (não é necessário instalar Google Play Store em um emulador do Android). No entanto, se você usar um emulador, deverá usar o Wi-Fi para se conectar ao GCM e deverá abrir várias portas em seu firewall Wi-Fi, conforme explicado posteriormente neste guia.

### <a name="set-the-package-name"></a>Definir o nome do pacote

Em [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), especificamos um nome de pacote para nosso aplicativo habilitado para GCM (esse nome de pacote também serve como a *ID do aplicativo* associada à nossa chave de API e à ID de remetente). Vamos abrir as propriedades do projeto **ClientApp** e definir o nome do pacote como esta cadeia de caracteres. Neste exemplo, definimos o nome do pacote como `com.xamarin.gcmexample`:

[![definir o nome do pacote](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Observe que o aplicativo cliente não poderá receber um token de registro do GCM se esse nome de pacote não corresponder *exatamente* ao nome do pacote que inserimos no console do desenvolvedor do Google.

### <a name="add-permissions-to-the-android-manifest"></a>Adicionar permissões ao manifesto do Android

Um aplicativo Android deve ter as seguintes permissões configuradas antes que possa receber notificações de Google Cloud Messaging:

- `com.google.android.c2dm.permission.RECEIVE` &ndash; concede permissão ao nosso aplicativo para registrar e receber mensagens de Google Cloud Messaging. (O que `c2dm` significa? Isso significa o _sistema de mensagens da nuvem para o dispositivo_, que é a predecessora agora preterida para gcm.
    O GCM ainda usa `c2dm` em muitas de suas cadeias de caracteres de permissão.)

- `android.permission.WAKE_LOCK` &ndash; (opcional) impede que a CPU do dispositivo vá para a suspensão durante a escuta de uma mensagem.

- `android.permission.INTERNET` &ndash; concede acesso à Internet para que o aplicativo cliente possa se comunicar com o GCM.

- *package_name*`.permission.C2D_MESSAGE` &ndash; registra o aplicativo com o Android e solicita permissão para receber exclusivamente todas as mensagens de C2D (nuvem para dispositivo). O prefixo de *package_name* é o mesmo que a ID do aplicativo.

Vamos definir essas permissões no manifesto do Android. Vamos editar **AndroidManifest. xml** e substituir o conteúdo pelo seguinte XML:

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

No XML acima, altere *YOUR_PACKAGE_NAME* para o nome do pacote do seu projeto de aplicativo cliente. Por exemplo, `com.xamarin.gcmexample`.

### <a name="check-for-google-play-services"></a>Verificar Google Play Services

Para este passo a passos, estamos criando um aplicativo básico com um único `TextView` na interface do usuário. Esse aplicativo não indica diretamente a interação com o GCM. Em vez disso, veremos a janela de saída para ver como nossos Handshakes de aplicativo com o GCM, e verificaremos a bandeja de notificação em busca de novas notificações à medida que elas chegam.

Primeiro, vamos criar um layout para a área de mensagem. Edite **Resources. layout. Main. axml** e substitua o conteúdo pelo seguinte XML:

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

Salve **Main. axml** e feche-o.

Quando o aplicativo cliente é iniciado, queremos que ele verifique se Google Play Services está disponível antes de tentar entrar em contato com o GCM. Edite **MainActivity.cs** e substitua a declaração da variável de instância de ``count`` pela seguinte declaração de variável de instância:

```csharp
TextView msgText;
```

Em seguida, adicione o seguinte método à classe **MainActivity** :

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

Esse código verifica o dispositivo para ver se o Google Play Services APK está instalado. Se não estiver instalado, uma mensagem será exibida na área de mensagem que instrui o usuário a baixar um APK do Google Play Store (ou habilitá-lo nas configurações do sistema do dispositivo). Como queremos executar essa verificação quando o aplicativo cliente é iniciado, adicionaremos uma chamada para esse método no final de `OnCreate`.

Em seguida, substitua o método `OnCreate` pelo código a seguir:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Esse código verifica a presença do Google Play Services APK e grava o resultado na área de mensagem.

Vamos recompilar completamente e executar o aplicativo. Você deverá ver uma tela parecida com a seguinte captura:

[![Google Play Services está disponível](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Se você não obtiver esse resultado, verifique se o Google Play Services APK está instalado em seu dispositivo e se o pacote **Xamarin Google Play Services-GCM** é adicionado ao seu projeto do **ClientApp** , conforme explicado anteriormente. Se você receber um erro de compilação, tente limpar a solução e compilar o projeto novamente.

Em seguida, escreveremos o código para entrar em contato com o GCM e recuperar um token de registro.

### <a name="register-with-gcm"></a>Registrar com GCM

Antes que o aplicativo possa receber notificações remotas do servidor de aplicativos, ele deve se registrar no GCM e recuperar um token de registro. O trabalho de registrar nosso aplicativo com o GCM é tratado por um `IntentService` que criamos. Nosso `IntentService` executa as seguintes etapas:

1. Usa a API [InstanceId](https://developers.google.com/instance-id/) para gerar tokens de segurança que autorizam o aplicativo cliente a acessar o servidor de aplicativos. Em retorno, obtemos um token de registro do GCM.

2. Encaminha o token de registro para o servidor de aplicativos (se o servidor de aplicativos exigir).

3. Assina um ou mais canais de tópico de notificação.

Depois de implementar esse `IntentService`, vamos testá-lo para ver se obtemos um token de registro do GCM.

Adicione um novo arquivo chamado **RegistrationIntentService.cs** e substitua o código do modelo pelo seguinte:

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

No código de exemplo acima, altere *YOUR_SENDER_ID* para o número de ID de remetente do seu projeto de aplicativo cliente. Para obter a ID do remetente do seu projeto:

1. Faça logon no [console do Google Cloud](https://console.cloud.google.com/) e selecione o nome do projeto no menu suspenso. No painel de **informações do projeto** que é exibido para seu projeto, clique em **ir para configurações do projeto**:

    [![selecionar o projeto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2. Na página **configurações** , localize o **número do projeto** &ndash; esta é a ID do remetente do seu projeto:

    [![número de projeto exibido](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Queremos iniciar nossa `RegistrationIntentService` quando o aplicativo começa a ser executado. Edite **MainActivity.cs** e modifique o método `OnCreate` para que o `RegistrationIntentService` seja iniciado depois de verificar a presença de Google Play Services:

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

Agora vamos dar uma olhada em cada seção de `RegistrationIntentService` para entender como ela funciona.

Primeiro, anotamos nosso `RegistrationIntentService` com o seguinte atributo para indicar que nosso serviço não deve ser instanciado pelo sistema:

```csharp
[Service (Exported = false)]
```

O construtor de `RegistrationIntentService` nomeia o thread de trabalho *RegistrationIntentService* para facilitar a depuração.

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

A funcionalidade principal do `RegistrationIntentService` reside no método `OnHandleIntent`. Vamos examinar esse código para ver como ele registra nosso aplicativo com o GCM.

#### <a name="request-a-registration-token"></a>Solicitar um token de registro

`OnHandleIntent` primeiro chama o método [InstanceId. GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) do Google para solicitar um token de registro do gcm. Encapsulamos esse código em um `lock` para proteger contra a possibilidade de várias tentativas de registro ocorrerem simultaneamente &ndash; o `lock` garante que essas intenções sejam processadas em sequência. Se não conseguirmos obter um token de registro, uma exceção é lançada e registramos um erro. Se o registro for bem sucedido, `token` será definido para o token de registro que obtemos de volta do GCM:

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

#### <a name="forward-the-registration-token-to-the-app-server"></a>Encaminhar o token de registro para o servidor de aplicativos

Se recebermos um token de registro (ou seja, nenhuma exceção foi lançada), chamamos `SendRegistrationToAppServer` para associar o token de registro do usuário à conta do lado do servidor (se houver) que é mantida pelo nosso aplicativo. Como essa implementação depende do design do servidor de aplicativos, um método vazio é fornecido aqui:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Em alguns casos, o servidor de aplicativos não precisa do token de registro do usuário; Nesse caso, esse método pode ser omitido. Quando um token de registro é enviado ao servidor de aplicativos, `SendRegistrationToAppServer` deve manter um booliano para indicar se o token foi enviado ao servidor. Se esse booliano for false, `SendRegistrationToAppServer` enviará o token para o servidor de aplicativos &ndash; caso contrário, o token já foi enviado para o servidor de aplicativos em uma chamada anterior.

#### <a name="subscribe-to-the-notification-topic"></a>Assinar o tópico de notificação

Em seguida, chamamos nosso método `Subscribe` para indicar ao GCM que desejamos assinar um tópico de notificação. Em `Subscribe`, chamamos a API GcmPubSub. subscribe para assinar nosso aplicativo cliente para todas as mensagens em `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

O servidor de aplicativos deve enviar mensagens de notificação para `/topics/global` se forem recebê-las. Observe que o nome do tópico em `/topics` pode ser qualquer coisa que você desejar, desde que o servidor de aplicativos e o aplicativo cliente concordem com esses nomes. (Aqui, escolhemos o nome `global` para indicar que desejamos receber mensagens em todos os tópicos com suporte no servidor de aplicativos.)

#### <a name="implement-an-instance-id-listener-service"></a>Implementar um serviço de ouvinte de ID de instância

Os tokens de registro são exclusivos e seguros; no entanto, o aplicativo cliente (ou GCM) pode precisar atualizar o token de registro no caso de reinstalação do aplicativo ou um problema de segurança. Por esse motivo, devemos implementar um `InstanceIdListenerService` que responda a solicitações de atualização de token do GCM.

Adicione um novo arquivo chamado **InstanceIdListenerService.cs** e substitua o código do modelo pelo seguinte:

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

Anote `InstanceIdListenerService` com o seguinte atributo para indicar que o serviço não deve ser instanciado pelo sistema e que ele possa receber as solicitações de atualização do token de registro do GCM (também chamada de *ID de instância*):

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

O método `OnTokenRefresh` em nosso serviço inicia o `RegistrationIntentService` para que ele possa interceptar o novo token de registro.

#### <a name="test-registration-with-gcm"></a>Testar o registro com o GCM

Vamos recompilar completamente e executar o aplicativo. Se você receber com êxito um token de registro do GCM, o token de registro deverá ser exibido na janela saída. Por exemplo:

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Manipular mensagens downstream

O código que implementamos até agora é apenas "configurar" código; Ele verifica se Google Play Services está instalado e negocia com o GCM e o servidor de aplicativos para preparar o aplicativo cliente para receber notificações remotas. No entanto, ainda vamos implementar o código que realmente recebe e processa mensagens de notificação de downstream. Para fazer isso, devemos implementar um *serviço de ouvinte GCM*. Esse serviço recebe mensagens de tópico do servidor de aplicativos e as transmite localmente como notificações. Depois de implementarmos esse serviço, criaremos um programa de teste para enviar mensagens para o GCM, para que possamos ver se a implementação funciona corretamente.

#### <a name="add-a-notification-icon"></a>Adicionar um ícone de notificação

Primeiro, vamos adicionar um pequeno ícone que aparecerá na área de notificação quando nossa notificação for iniciada. Você pode copiar [esse ícone](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) para seu projeto ou criar seu próprio ícone personalizado. Vamos nomear o arquivo de ícone **ic_stat_button_click. png** e copiá-lo para a pasta **recursos/desenhar** . Lembre-se de usar **adicionar > item existente...** para incluir esse arquivo de ícone em seu projeto.

#### <a name="implement-a-gcm-listener-service"></a>Implementar um serviço de ouvinte do GCM

Adicione um novo arquivo chamado **GcmListenerService.cs** e substitua o código do modelo pelo seguinte:

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

Vamos dar uma olhada em cada seção de nossa `GcmListenerService` para entender como ela funciona.

Primeiro, anotamos `GcmListenerService` com um atributo para indicar que esse serviço não deve ser instanciado pelo sistema e incluímos um filtro de intenção para indicar que ele recebe as mensagens do GCM:

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Quando `GcmListenerService` recebe uma mensagem do GCM, o método `OnMessageReceived` é invocado. Esse método extrai o conteúdo da mensagem do `Bundle`passado, registra o conteúdo da mensagem (para que possamos exibi-lo na janela de saída) e chama `SendNotification` para iniciar uma notificação local com o conteúdo da mensagem recebida:

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

O método `SendNotification` usa `Notification.Builder` para criar a notificação e, em seguida, usa o `NotificationManager` para iniciar a notificação. Efetivamente, isso converte a mensagem de notificação remota em uma notificação local para ser apresentada ao usuário.
Para obter mais informações sobre como usar `Notification.Builder` e `NotificationManager`, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

#### <a name="declare-the-receiver-in-the-manifest"></a>Declarar o receptor no manifesto

Antes que possamos receber mensagens do GCM, devemos declarar o ouvinte do GCM no manifesto do Android. Vamos editar **AndroidManifest. xml** e substituir a seção `<application>` pelo seguinte XML:

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

No XML acima, altere *YOUR_PACKAGE_NAME* para o nome do pacote do seu projeto de aplicativo cliente. Em nosso exemplo, o nome do pacote é `com.xamarin.gcmexample`.

Vejamos o que cada configuração nesse XML faz:

|Configuração|DESCRIÇÃO|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Declara que nosso aplicativo implementa um receptor GCM que captura e processa mensagens de notificação por push de entrada.|
|`com.google.android.c2dm.permission.SEND`|Declara que somente os servidores GCM podem enviar mensagens diretamente para o aplicativo.|
|`com.google.android.c2dm.intent.RECEIVE`|O filtro de intenção anuncia que nosso aplicativo lida com mensagens de difusão do GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|O filtro de intenção anuncia que nosso aplicativo lida com novas tentativas de registro (ou seja, implementamos um serviço de ouvinte de ID de instância).|

Como alternativa, você pode decorar `GcmListenerService` com esses atributos em vez de especificá-los em XML; aqui, nós os especificamos em **AndroidManifest. xml** para que os exemplos de código sejam mais fáceis de seguir.

### <a name="create-a-message-sender-to-test-the-app"></a>Criar um remetente de mensagem para testar o aplicativo

Vamos adicionar um C# projeto de aplicativo de console de desktop à solução e chamá-lo de **MessageSender**. Usaremos esse aplicativo de console para simular um servidor de aplicativos &ndash; ele enviará mensagens de notificação para **ClientApp** por meio do gcm.

#### <a name="add-the-jsonnet-package"></a>Adicionar o pacote Json.NET

Nesse aplicativo de console, estamos criando um conteúdo JSON que contém a mensagem de notificação que queremos enviar ao aplicativo cliente. Usaremos o pacote **JSON.net** no **MessageSender** para facilitar a criação do objeto JSON exigido pelo gcm. No Visual Studio, clique com o botão direito do mouse em **referências > gerenciar pacotes NuGet...** ; em Visual Studio para Mac, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** .

Vamos procurar o pacote **JSON.net** e instalá-lo no projeto:

[![instalar o pacote Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)

#### <a name="add-a-reference-to-systemnethttp"></a>Adicionar uma referência a System .net. http

Também precisaremos adicionar uma referência a `System.Net.Http` para que possamos criar uma instância de um `HttpClient` para enviar nossa mensagem de teste para o GCM. No projeto **MessageSender** , clique com o botão direito do mouse em **referências > Adicionar referência** e role para baixo até ver **System .net. http**. Coloque uma marca de seleção ao lado de **System .net. http** e clique em **OK**.

#### <a name="implement-code-that-sends-a-test-message"></a>Implementar o código que envia uma mensagem de teste

Em **MessageSender**, edite **Program.cs** e substitua o conteúdo pelo código a seguir:

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

No código acima, altere *YOUR_API_KEY* para a chave de API para seu projeto de aplicativo cliente.

Este servidor de aplicativo de teste envia a seguinte mensagem formatada em JSON para GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

O GCM, por sua vez, encaminha essa mensagem para seu aplicativo cliente. Vamos criar **MessageSender** e abrir uma janela de console na qual podemos executá-la na linha de comando.

### <a name="try-it"></a>Experimente!

Agora estamos prontos para testar nosso aplicativo cliente. Se você estiver usando um emulador ou se o dispositivo estiver se comunicando com o GCM por Wi-Fi, você deverá abrir as seguintes portas TCP no firewall para que as mensagens do GCM obtenham por meio de: 5228, 5229 e 5230.

Inicie o aplicativo cliente e assista à janela saída. Depois que o `RegistrationIntentService` receber com êxito um token de registro do GCM, a janela de saída deverá exibir o token com a saída do log semelhante ao seguinte:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

Neste ponto, o aplicativo cliente está pronto para receber uma mensagem de notificação remota. Na linha de comando, execute o programa **MessageSender. exe** para enviar uma mensagem de notificação "Olá, Xamarin" para o aplicativo cliente.
Se você ainda não tiver criado o projeto **MessageSender** , faça isso agora.

Para executar o **MessageSender. exe** no Visual Studio, abra um prompt de comando, altere para o diretório **MessageSender/bin/Debug** e execute o comando diretamente:

```cmd
MessageSender.exe
```

Para executar o **MessageSender. exe** em Visual Studio para Mac, abra uma sessão de terminal, altere para **MessageSender/bin/Debug** The Directory e use mono para executar o **MessageSender. exe**

```bash
mono MessageSender.exe
```

Pode levar até um minuto para que a mensagem seja propagada por meio do GCM e retorne para o aplicativo cliente. Se a mensagem for recebida com êxito, devemos ver a saída semelhante à seguinte na janela de saída:

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Além disso, você deve observar que um novo ícone de notificação apareceu na bandeja de notificação:

[![ícone de notificação aparece no dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Ao abrir a bandeja de notificação para exibir notificações, você deverá ver nossa notificação remota:

[![mensagem de notificação é exibida](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Parabéns, seu aplicativo recebeu sua primeira notificação remota!

Observe que as mensagens do GCM não serão mais recebidas se o aplicativo for interrompido por força. Para retomar as notificações após uma parada forçada, o aplicativo deve ser reiniciado manualmente. Para obter mais informações sobre essa política do Android, consulte [Iniciar controles nos aplicativos interrompidos](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) e a [postagem de estouro de pilha](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267).

## <a name="summary"></a>Resumo

Este passo a passo detalha as etapas para implementar notificações remotas em um aplicativo Xamarin. Android. Ele descreveu como instalar pacotes adicionais necessários para comunicações GCM e explicou como configurar permissões de aplicativo para acesso a servidores GCM.
Ele forneceu um código de exemplo que ilustra como verificar a presença de Google Play Services, como implementar um serviço de tentativa de registro e um serviço de ouvinte de ID de instância que negocia com GCM para um token de registro e como implementar um ouvinte do GCM serviço que recebe e processa mensagens de notificação remotas. Por fim, implementamos um programa de teste de linha de comando para enviar notificações de teste para nosso aplicativo cliente por meio do GCM.

## <a name="related-links"></a>Links relacionados

- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
