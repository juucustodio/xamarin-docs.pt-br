---
title: Notificações remotas com o firebase Cloud Messaging
description: Este tutorial fornece uma explicação passo a passo de como usar o firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo Xamarin. Android. Ele ilustra como implementar as várias classes que são necessárias para comunicações com o firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o sistema de mensagens downstream usando o firebase MMC.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: df13c1080be5fd466c4875ed8a3bdc2012a70df0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526165"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notificações remotas com o firebase Cloud Messaging

_Este tutorial fornece uma explicação passo a passo de como usar o firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo Xamarin. Android. Ele ilustra como implementar as várias classes que são necessárias para comunicações com o firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o sistema de mensagens downstream usando o firebase MMC._

## <a name="fcm-notifications-overview"></a>Visão geral das notificações do FCM

Neste tutorial, um aplicativo básico chamado **FCMClient** será criado para ilustrar os conceitos básicos do FCM Messaging. **FCMClient** verifica a presença de Google Play Services, recebe tokens de registro de FCM, exibe notificações remotas que você envia do console do firebase e assina as mensagens de tópico:

[![Captura de tela de exemplo do aplicativo](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

As seguintes áreas de tópico serão exploradas:

1. Notificações em segundo plano

2. Mensagens de tópico

3. Notificações em primeiro plano

Durante esta explicação, você adicionará de forma incremental a funcionalidade para **FCMClient** e a executará em um dispositivo ou emulador para entender como ele interage com o FCM. Você usará o log para testemunhar transações de aplicativos dinâmicos com servidores FCM e observará como as notificações são geradas de mensagens FCM que você insere na GUI de notificações do console do firebase.

## <a name="requirements"></a>Requisitos


Será útil se familiarizar com os [diferentes tipos de mensagens](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) que podem ser enviadas pelo firebase Cloud Messaging. A carga da mensagem determinará como um aplicativo cliente receberá e processará a mensagem.

Antes de prosseguir com este passo a passos, você deve adquirir as credenciais necessárias para usar os servidores FCM do Google; Esse processo é explicado em [firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
Em particular, você deve baixar o arquivo **Google-Services. JSON** para usar com o código de exemplo apresentado neste passo a passos. Se você ainda não tiver criado um projeto no console do firebase (ou se ainda não tiver baixado o arquivo do **Google-Services. JSON** ), consulte [firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Para executar o aplicativo de exemplo, você precisará de um dispositivo de teste do Android ou emulador compatível com firebase. O firebase Cloud Messaging dá suporte a clientes em execução no Android 4,0 ou superior, e esses dispositivos também devem ter o aplicativo Google Play Store instalado (Google Play Services 9.2.1 ou posterior é necessário). Se você ainda não tiver o aplicativo Google Play Store instalado no seu dispositivo, visite o site do [Google Play](https://support.google.com/googleplay) para baixá-lo e instalá-lo. Como alternativa, você pode usar o emulador SDK do Android com Google Play Services instalado em vez de um dispositivo de teste (não é necessário instalar o Google Play Store se você estiver usando o emulador SDK do Android).

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Para começar, crie um novo projeto Xamarin. Android vazio chamado **FCMClient**. Se você não estiver familiarizado com a criação de projetos do Xamarin. Android, consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Depois que o novo aplicativo é criado, a próxima etapa é definir o nome do pacote e instalar vários pacotes NuGet que serão usados para comunicação com o FCM.

### <a name="set-the-package-name"></a>Definir o nome do pacote

No [firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), você especificou um nome de pacote para o aplicativo habilitado para FCM. Esse nome de pacote também serve como a [*ID do aplicativo*](./firebase-cloud-messaging.md#fcm-in-action-app-id) que está associada à chave de [API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configure o aplicativo para usar este nome de pacote:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra as propriedades do projeto **FCMClient** .

2. Na página **manifesto do Android** , defina o nome do pacote.

No exemplo a seguir, o nome do pacote é definido `com.xamarin.fcmexample`como:

[![Configurando o nome do pacote](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, verifique também se a `Internet` permissão está habilitada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra as propriedades do projeto **FCMClient** .

2. Na página **aplicativo do Android** , defina o nome do pacote.

No exemplo a seguir, o nome do pacote é definido `com.xamarin.fcmexample`como:

[![Configurando o nome do pacote](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Enquanto você estiver atualizando o **manifesto do Android**, verifique também se a `INTERNET` permissão está habilitada (em **permissões necessárias**).

-----

> [!IMPORTANT]
> O aplicativo cliente não poderá receber um token de registro de FCM se esse nome de pacote não corresponder *exatamente* ao nome do pacote inserido no console do firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Adicionar o pacote base do Xamarin Google Play Services

Como o firebase Cloud Messaging depende de Google Play Services, o pacote NuGet do [xamarin Google Play Services-base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) deve ser adicionado ao projeto Xamarin. Android. Você precisará da versão 29.0.0.2 ou posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No Visual Studio, clique com o botão direito do mouse em **referências > gerenciar pacotes NuGet...** .

2. Clique na guia **procurar** e procure por **Xamarin. GooglePlayServices. base**.

3. Instale este pacote no projeto **FCMClient** :

    [![Instalando a base de Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Em Visual Studio para Mac, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** .

2. Procure por **Xamarin. GooglePlayServices. base**.

3. Instale este pacote no projeto **FCMClient** :

    [![Instalando a base de Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se você receber um erro durante a instalação do NuGet, feche o projeto **FCMClient** , abra-o novamente e repita a instalação do NuGet.

Quando você instala o **Xamarin. GooglePlayServices. base**, todas as dependências necessárias também são instaladas. Edite **MainActivity.cs** e adicione a `using` seguinte instrução:

```csharp
using Android.Gms.Common;
```

Essa instrução torna a `GoogleApiAvailability` classe no **Xamarin. GooglePlayServices. base** disponível para o código **FCMClient** .
`GoogleApiAvailability`é usado para verificar a presença de Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Adicionar o pacote de mensagens do Xamarin firebase

Para receber mensagens do FCM, o pacote NuGet [firebase-Messaging do Xamarin](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) deve ser adicionado ao projeto do aplicativo. Sem esse pacote, um aplicativo Android não pode receber mensagens de servidores FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No Visual Studio, clique com o botão direito do mouse em **referências > gerenciar pacotes NuGet...** .

2. Procure por **Xamarin. firebase. Messaging**.

3. Instale este pacote no projeto **FCMClient** :

    [![Instalando o Xamarin firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Em Visual Studio para Mac, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** .

2. Procure por **Xamarin. firebase. Messaging**.

3. Instale este pacote no projeto **FCMClient** :

    [![Instalando o Xamarin firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Quando você instala o **Xamarin. firebase. Messaging**, todas as dependências necessárias também são instaladas.

Em seguida, edite **MainActivity.cs** e adicione `using` as seguintes instruções:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

As duas primeiras instruções fazem com que os tipos no pacote NuGet **Xamarin. firebase. Messaging** esteja disponível para o código **FCMClient** . O **Android. util** adiciona a funcionalidade de registro em log que será usada para observar transações com o FMS.

### <a name="add-googleplayservices-json"></a>Adicionar o arquivo JSON dos serviços do Google

A próxima etapa é adicionar o arquivo **Google-Services. JSON** ao diretório raiz do seu projeto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copie **Google-Services. JSON** para a pasta do projeto.

2. Adicione **Google-Services. JSON** ao projeto do aplicativo (clique em **Mostrar todos os arquivos** na **Gerenciador de soluções**, clique com o botão direito do mouse em **Google-Services. JSON**e selecione **incluir no projeto**).

3. Selecione **Google-Services. JSON** na janela **Gerenciador de soluções** .

4. No painel **Propriedades** , defina a **ação de Build** como **GoogleServicesJson**:

    [![Definindo a ação de Build como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Se a ação de compilação **GoogleServicesJson** não for exibida, salve e feche a solução e, em seguida, reabra-a.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Copie **Google-Services. JSON** para a pasta do projeto.

2. Adicione o **Google-Services. JSON** ao projeto do aplicativo.

3. Clique com o botão direito do mouse em **Google-Services. JSON**.

4. Defina a **ação de compilação** como **GoogleServicesJson**:

    [![Definindo a ação de Build como GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Quando o **Google-Services. JSON** é adicionado ao projeto (e a ação de compilação **GoogleServicesJson** é definida), o processo de compilação extrai a ID do cliente e a [chave de API](./firebase-cloud-messaging.md#fcm-in-action-api-key) e, em seguida, adiciona essas credenciais ao mesclado/gerado  **AndroidManifest. xml** que reside em **obj/Debug/Android/AndroidManifest. xml**. Esse processo de mesclagem adiciona automaticamente quaisquer permissões e outros elementos FCM que são necessários para a conexão com servidores FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Verificar Google Play Services e criar um canal de notificação

O Google recomenda que os aplicativos Android verifiquem a presença do Google Play Services APK antes de acessar os recursos do Google Play Services (para obter mais informações, consulte [verificar os serviços de Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Um layout inicial para a interface do usuário do aplicativo será criado primeiro. Edite Resources **/layout/Main. axml** e substitua seu conteúdo pelo XML a seguir:

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

Isso `TextView` será usado para exibir mensagens que indicam se Google Play Services está instalado. Salve as alterações em **Main. axml**.


Edite **MainActivity.cs** e adicione as seguintes variáveis de instância `MainActivity` à classe:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

As variáveis `CHANNEL_ID` e `NOTIFICATION_ID` serão usadas no método [`CreateNotificationChannel`](#create-notification-channel-code) que será adicionado `MainActivity` posteriormente neste passo a passos.


No exemplo a seguir, o `OnCreate` método verificará se Google Play Services está disponível antes de o aplicativo tentar usar os serviços FCMs.
Adicione o seguinte método à `MainActivity` classe:

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
            msgText.Text = "This device is not supported";
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

Esse código verifica o dispositivo para ver se o Google Play Services APK está instalado. Se não estiver instalado, será exibida uma mensagem no `TextBox` que instrui o usuário a baixar um apk do Google Play Store (ou para habilitá-lo nas configurações do sistema do dispositivo).

<a name="create-notification-channel-code"></a>Os aplicativos que estão em execução no Android 8,0 (API nível 26) ou superior devem criar um [_canal de notificação_](~/android/app-fundamentals/notifications/local-notifications.md) para publicar suas notificações.  Adicione o seguinte método à `MainActivity` classe que criará o canal de notificação (se necessário):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Substitua o método `OnCreate` pelo seguinte código:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable`é chamado no final de `OnCreate` para que a verificação de Google Play Services seja executada sempre que o aplicativo for iniciado. O método `CreateNotificationChannel` é chamado para garantir que exista um canal de notificação para dispositivos que executam o Android 8 ou superior. Se seu aplicativo tiver um `OnResume` método, ele também deverá `IsPlayServicesAvailable` ser `OnResume` chamado de. Recompile completamente e execute o aplicativo. Se tudo estiver configurado corretamente, você verá uma tela parecida com a seguinte captura:

[![O aplicativo indica que Google Play Services está disponível](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se você não obtiver esse resultado, verifique se o Google Play Services APK está instalado em seu dispositivo (para obter mais informações, consulte Configurando [Google Play Services](https://developers.google.com/android/guides/setup)).
Verifique também se você adicionou o pacote **Xamarin. google. Play. Services. base** ao seu projeto **FCMClient** , conforme explicado anteriormente.


## <a name="add-the-instance-id-receiver"></a>Adicionar o destinatário da ID da instância

A próxima etapa é adicionar um serviço que se estenda `FirebaseInstanceIdService` para manipular a criação, a rotação e a atualização de tokens de [registro do firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). O `FirebaseInstanceIdService` serviço é necessário para que o FCM possa enviar mensagens para o dispositivo. Quando o `FirebaseInstanceIdService` serviço for adicionado ao aplicativo cliente, o aplicativo receberá automaticamente as mensagens FCM e as exibirá como notificações sempre que o aplicativo estiver em segundo plano.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Declarar o receptor no manifesto do Android

Edite **AndroidManifest. xml** e insira os `<receiver>` seguintes elementos na `<application>` seção:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Esse XML faz o seguinte:

- Declara uma `FirebaseInstanceIdReceiver` implementação que fornece um [identificador exclusivo](https://developers.google.com/instance-id/) para cada instância do aplicativo. Esse receptor também autentica e autoriza ações.

- Declara um interno `FirebaseInstanceIdInternalReceiver` implementação que é usada para iniciar os serviços com segurança.

- A [ID do aplicativo](./firebase-cloud-messaging.md#fcm-in-action-app-id) é armazenada no arquivo **Google-Services. JSON** que foi [adicionado ao projeto](#add-googleplayservices-json). As associações firebase do Xamarin. Android substituirão o token `${applicationId}` pela ID do aplicativo; nenhum código adicional é exigido pelo aplicativo cliente para fornecer a ID do aplicativo.

O `FirebaseInstanceIdReceiver` é um `WakefulBroadcastReceiver` que recebe `FirebaseInstanceId` e `FirebaseMessaging` eventos e os entrega à classe da `FirebaseInstanceIdService`qual você deriva.

### <a name="implement-the-firebase-instance-id-service"></a>Implementar o serviço de ID de instância firebase

O trabalho de registrar o aplicativo com o FCM é tratado pelo serviço personalizado `FirebaseInstanceIdService` que você fornece.
`FirebaseInstanceIdService`o executa as seguintes etapas:

1. Usa a [API de ID de instância](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) para gerar tokens de segurança que autorizam o aplicativo cliente a acessar o FCM e o servidor de aplicativos. Em retorno, o aplicativo obtém de volta um [token de registro](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) de FCM.

2. Encaminha o token de registro para o servidor de aplicativos se o servidor de aplicativos exigir.

Adicione um novo arquivo chamado **MyFirebaseIIDService.cs** e substitua seu código de modelo pelo seguinte:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Esse serviço implementa um `OnTokenRefresh` método que é invocado quando o token de registro é inicialmente criado ou alterado. Quando `OnTokenRefresh` é executado, ele recupera o token mais recente `FirebaseInstanceId.Instance.Token` da propriedade (que é atualizado de forma assíncrona por FCM). Neste exemplo, o token atualizado é registrado para que possa ser exibido na janela de saída:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh`é invocado com pouca frequência: é usado para atualizar o token nas seguintes circunstâncias:

- Quando o aplicativo é instalado ou desinstalado.

- Quando o usuário exclui dados de aplicativo.

- Quando o aplicativo apaga a ID da instância.

- Quando a segurança do token foi comprometida.

De acordo com a documentação da [ID de instância](https://developers.google.com/instance-id/guides/android-implementation) do Google, o serviço de ID de instância FCM solicitará que o aplicativo atualize seu token periodicamente (normalmente, a cada 6 meses).

`OnTokenRefresh`também chama `SendRegistrationToAppServer` para associar o token de registro do usuário à conta do lado do servidor (se houver) que é mantida pelo aplicativo:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Como essa implementação depende do design do servidor de aplicativos, um corpo de método vazio é fornecido neste exemplo. Se o servidor de aplicativos exigir informações de registro FCM `SendRegistrationToAppServer` , modifique para associar o token de ID de instância FCM do usuário a qualquer conta do lado do servidor mantida por seu aplicativo. (Observe que o token é opaco para o aplicativo cliente.)

Quando um token é enviado ao servidor de aplicativos, `SendRegistrationToAppServer` o deve manter um booliano para indicar se o token foi enviado ao servidor. Se esse booliano for false `SendRegistrationToAppServer` , enviará o token para o &ndash; servidor de aplicativos, caso contrário, o token já foi enviado para o servidor de aplicativos em uma chamada anterior. Em alguns casos (como neste `FCMClient` exemplo), o servidor de aplicativos não precisa do token; portanto, esse método não é necessário para este exemplo.

## <a name="implement-client-app-code"></a>Implementar o código do aplicativo cliente

Agora que os serviços receptores estão em vigor, o código do aplicativo cliente pode ser escrito para aproveitar esses serviços. Nas seções a seguir, um botão é adicionado à interface do usuário para registrar o token de registro (também chamado de *token de ID de instância*) e mais código `MainActivity` é adicionado `Intent` ao para exibir informações quando o aplicativo é iniciado de uma notificação:

[![Botão de token de log adicionado à tela do aplicativo](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Tokens de log

O código adicionado nesta etapa destina-se apenas a fins &ndash; de demonstração, um aplicativo cliente de produção não precisaria registrar tokens de registro. Edite Resources **/layout/Main. axml** e `Button` adicione a seguinte declaração `TextView` imediatamente após o elemento:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Adicione o código a seguir ao final do método `MainActivity.OnCreate`:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Esse código registra o token atual na janela de saída quando o botão do **token de log** é tocado.

### <a name="handle-notification-intents"></a>Lidar com tentativas de notificação

Quando o usuário toca em uma notificação emitida de **FCMClient**, todos os dados que acompanham essa mensagem de `Intent` notificação são disponibilizados em extras. Edite **MainActivity.cs** e adicione o seguinte código à parte superior do `OnCreate` método (antes da chamada para `IsPlayServicesAvailable`):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

O inicializador `Intent` do aplicativo é acionado quando o usuário toca `Intent` em sua mensagem de notificação, portanto, esse código registrará em log os dados que o acompanham na janela de saída. Se for necessário `Intent` acionar um diferente, `click_action` o campo da mensagem de notificação `Intent` deverá ser definido como (o iniciador `Intent` será usado quando `click_action` não for especificado).


## <a name="background-notifications"></a>Notificações em segundo plano

Compile e execute o aplicativo **FCMClient** . O botão de **token de log** é exibido:

[![O botão de token de log é exibido](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toque no botão **token de log** . Uma mensagem semelhante à seguinte deve ser exibida na janela saída do IDE:

[![Token de ID de instância exibido na janela de saída](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

A cadeia de caracteres longa rotulada com **token** é o token de ID de instância que você colará &ndash; no console do firebase selecione e copie essa cadeia de caracteres para a área de transferência. Se você não vir um token de ID de instância, adicione a seguinte linha à parte superior do `OnCreate` método para verificar se o **Google-Services. JSON** foi analisado corretamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

O `google_app_id` valor registrado na janela de saída deve `mobilesdk_app_id` corresponder ao valor registrado no **Google-Services. JSON**.

### <a name="send-a-message"></a>Enviar uma mensagem

Entre no [console do firebase](https://console.firebase.google.com), selecione seu projeto, clique em **notificações**e clique em **enviar sua primeira mensagem**:

[![Botão enviar sua primeira mensagem](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Na página da **mensagem de composição** , insira o texto da mensagem e selecione **único dispositivo**. Copie o token de ID de instância da janela de saída do IDE e cole-o no campo **token de registro FCM** do console do firebase:

[![Caixa de diálogo compor mensagem](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

No dispositivo Android (ou emulador), em segundo plano o aplicativo, tocando no botão **visão geral** do Android e tocando na tela inicial. Quando o dispositivo estiver pronto, clique em **Enviar mensagem** no console do firebase:

[![Botão enviar mensagem](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando a caixa de diálogo de **mensagem de revisão** for exibida, clique em **Enviar**.
O ícone de notificação deve aparecer na área de notificação do dispositivo (ou emulador):

[![O ícone de notificação é mostrado](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Abra o ícone de notificação para exibir a mensagem. A mensagem de notificação deve ser exatamente o que foi digitado no campo de **texto da mensagem** do console do firebase:

[![A mensagem de notificação é exibida no dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toque no ícone de notificação para iniciar o aplicativo **FCMClient** . Os `Intent` extras enviados para **FCMClient** são listados na janela saída do IDE:

[![As listas extras de intenção são de chave, ID da mensagem e chave de recolhimento](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

Neste exemplo, a chave **from** é definida como o número de projeto firebase do aplicativo (neste exemplo, `41590732`), e **collapse_key** é definido como seu nome de pacote (**com. xamarin. fcmexample**).
Se você não receber uma mensagem, tente excluir o aplicativo **FCMClient** no dispositivo (ou emulador) e repita as etapas acima.


> [!NOTE]
> Se você forçar o fechamento do aplicativo, o FCM deixará de entregar notificações. O Android impede que os serviços em segundo plano transmitam de forma inadvertida ou desnecessária componentes de aplicativos interrompidos. (Para obter mais informações sobre esse comportamento, consulte [Iniciar controles em aplicativos interrompidos](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Por esse motivo, é necessário desinstalar manualmente o aplicativo cada vez que você executá-lo e interrompê-lo de uma &ndash; sessão de depuração isso força o FCM a gerar um novo token para que as mensagens continuem a ser recebidas.

### <a name="add-a-custom-default-notification-icon"></a>Adicionar um ícone de notificação padrão personalizado

No exemplo anterior, o ícone de notificação é definido como o ícone do aplicativo. O XML a seguir configura um ícone padrão personalizado para notificações. O Android exibe esse ícone padrão personalizado para todas as mensagens de notificação em que o ícone de notificação não está definido explicitamente.

Para adicionar um ícone de notificação padrão personalizado, adicione o ícone ao diretório de **recursos/empates** , edite **AndroidManifest. xml**e insira o `<meta-data>` seguinte elemento na `<application>` seção:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

Neste exemplo, o ícone de notificação que reside na notificação de **estatística\_\_IC\_de recursos/empates/IC. o png** será usado como o ícone de notificação padrão personalizado. Se um ícone padrão personalizado não estiver configurado em **AndroidManifest. xml** e nenhum ícone for definido na carga de notificação, o Android usará o ícone do aplicativo como o ícone de notificação (como visto no ícone de notificação captura de tela acima).

## <a name="handle-topic-messages"></a>Manipular mensagens de tópico

O código escrito até agora lida com tokens de registro e adiciona funcionalidade de notificação remota ao aplicativo. O exemplo a seguir adiciona um código que escuta *mensagens de tópico* e as encaminha para o usuário como notificações remotas. As mensagens de tópico são mensagens FCM que são enviadas para um ou mais dispositivos que assinam um tópico específico. Para obter mais informações sobre mensagens de tópico, consulte [tópico mensagens](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Assinar um tópico

Edite Resources **/layout/Main. axml** e `Button` adicione a seguinte declaração imediatamente `Button` após o elemento anterior:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Esse XML adiciona um botão **assinar à notificação** ao layout.
Edite **MainActivity.cs** e adicione o seguinte código ao final do `OnCreate` método:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Esse código localiza o botão **inscrever-se na notificação** no layout e atribui seu manipulador de cliques ao código que chama `FirebaseMessaging.Instance.SubscribeToTopic`, passando o tópico assinado, _notícias_. Quando o usuário toca no botão inscrever, o aplicativo assina o tópico _notícias_ . Na seção a seguir, uma mensagem de tópico de _notícias_ será enviada da GUI de notificações do console do firebase.

### <a name="send-a-topic-message"></a>Enviar uma mensagem de tópico

Desinstale o aplicativo, recompile-o e execute-o novamente. Clique no botão **assinar notificações** :

[![Botão assinar notificações](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se o aplicativo tiver se inscrito com êxito, você deverá ver o **tópico sincronização bem-sucedida** na janela saída do IDE:

[![Janela de saída mostra a mensagem de sincronização de tópico com êxito](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Use as etapas a seguir para enviar uma mensagem de tópico:

1. No console do firebase, clique em **nova mensagem**.

2. Na página de **composição de mensagem** , insira o texto da mensagem e selecione **tópico**.

3. No menu suspenso do **tópico** , selecione o tópico interno, **notícias**:

    [![Selecionando o tópico de notícias](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. No dispositivo Android (ou emulador), em segundo plano o aplicativo, tocando no botão **visão geral** do Android e tocando na tela inicial.

5. Quando o dispositivo estiver pronto, clique em **Enviar mensagem** no console do firebase.

6. Verifique a janela saída do IDE para ver **/topics/news** na saída do log:

    [![A mensagem de/topic/News é mostrada](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando essa mensagem é vista na janela de saída, o ícone de notificação também deve aparecer na área de notificação no dispositivo Android. Abra o ícone de notificação para exibir a mensagem de tópico:

[![A mensagem de tópico aparece como uma notificação](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se você não receber uma mensagem, tente excluir o aplicativo **FCMClient** no dispositivo (ou emulador) e repita as etapas acima.

## <a name="foreground-notifications"></a>Notificações em primeiro plano

Para receber notificações em aplicativos em primeiro plano, você deve `FirebaseMessagingService`implementar o. Esse serviço também é necessário para o recebimento de cargas de dados e para o envio de mensagens de upstream. Os exemplos a seguir ilustram como implementar um serviço que `FirebaseMessagingService` estende &ndash; o aplicativo resultante será capaz de lidar com notificações remotas enquanto ele estiver em execução em primeiro plano.

### <a name="implement-firebasemessagingservice"></a>Implementar FirebaseMessagingService

O `FirebaseMessagingService` serviço é responsável por receber e processar as mensagens de firebase. Cada aplicativo deve subclasse desse tipo e substituir o `OnMessageReceived` para processar uma mensagem de entrada. Quando um aplicativo estiver em primeiro plano, o `OnMessageReceived` retorno de chamada sempre tratará a mensagem.

> [!NOTE]
> Os aplicativos têm apenas 10 segundos para lidar com uma mensagem de entrada na nuvem firebase. Qualquer trabalho que demore mais do que isso deve ser agendado para execução em segundo plano usando uma biblioteca como o Agendador de [trabalhos do Android](~/android/platform/android-job-scheduler.md) ou o Dispatcher do [trabalho do firebase](~/android/platform/firebase-job-dispatcher.md).

Adicione um novo arquivo chamado **MyFirebaseMessagingService.cs** e substitua seu código de modelo pelo seguinte:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Observe que o `MESSAGING_EVENT` filtro de intenção deve ser declarado para que novas mensagens FCM sejam direcionadas para: `MyFirebaseMessagingService`

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando o aplicativo cliente recebe uma mensagem de FCM, `OnMessageReceived` o extrai o conteúdo da mensagem do `RemoteMessage` objeto passado chamando seu `GetNotification` método. Em seguida, ele registra o conteúdo da mensagem para que ele possa ser exibido na janela saída do IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Se você definir pontos de interrupção `FirebaseMessagingService`no, sua sessão de depuração poderá ou não atingir esses pontos de interrupção devido a como o FCM entrega mensagens.


### <a name="send-another-message"></a>Enviar outra mensagem

Desinstale o aplicativo, recompile-o, execute-o novamente e siga estas etapas para enviar outra mensagem:

1. No console do firebase, clique em **nova mensagem**.

2. Na página da **mensagem de composição** , insira o texto da mensagem e selecione **único dispositivo**.

3. Copie a cadeia de caracteres do token da janela saída do IDE e cole-a no campo **token de registro FCM** do console do firebase como antes.

4. Verifique se o aplicativo está em execução em primeiro plano e clique em **Enviar mensagem** no console do firebase:

    [![Enviando outra mensagem do console](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. Quando a caixa de diálogo de **mensagem de revisão** for exibida, clique em **Enviar**.

6. A mensagem de entrada é registrada na janela de saída do IDE:

    [![Corpo da mensagem impresso na janela de saída](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Adicionar um remetente de notificação local

Neste exemplo restante, a mensagem FCM de entrada será convertida em uma notificação local que é iniciada enquanto o aplicativo está em execução em primeiro plano. Edite **MyFirebaseMessageService.cs** e adicione as `using` seguintes instruções:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Adicione o seguinte método a `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Para distinguir essa notificação das notificações em segundo plano, esse código marca as notificações com um ícone diferente do ícone do aplicativo. Adicione o arquivo [IC\_stat\_IC\_Notification. png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) a **recursos/desenháveis** e inclua-o no projeto **FCMClient** .

O `SendNotification` método usa `NotificationCompat.Builder` para criar a notificação e `NotificationManagerCompat` é usado para iniciar a notificação. A notificação contém um `PendingIntent` que permitirá que o usuário abra o aplicativo e exiba o conteúdo da cadeia de caracteres passada `messageBody`para. Para obter mais informações `NotificationCompat.Builder`sobre o, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

Chame o `SendNotification` método na extremidade `OnMessageReceived` do método:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

Como resultado dessas alterações, `SendNotification` o será executado sempre que uma notificação for recebida enquanto o aplicativo estiver em primeiro plano e a notificação aparecerá na área de notificação.

Quando um aplicativo está em segundo plano, a [carga da mensagem](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determinará como a mensagem é tratada:

* **Notificação** do as mensagens serão enviadas para a **bandeja do sistema.** &ndash; Uma notificação local aparecerá lá. Quando o usuário toca na notificação, o aplicativo será iniciado.
* **Dados** do as mensagens serão tratadas `OnMessageReceived`pelo. &ndash;
* **Ambos** &ndash; mensagens com carga de dados e notificação serão entregues à bandeja do sistema. Quando o aplicativo for iniciado, a carga de dados aparecerá `Extras` no `Intent` do que foi usado para iniciar o aplicativo.

Neste exemplo, se o aplicativo estiver em segundo plano, `SendNotification` será executado se a mensagem tiver uma carga de dados. Caso contrário, será iniciada uma notificação em segundo plano (ilustrada anteriormente neste guia).

### <a name="send-the-last-message"></a>Enviar a última mensagem

Desinstale o aplicativo, recompile-o, execute-o novamente e, em seguida, use as seguintes etapas para enviar a última mensagem:

1. No console do firebase, clique em **nova mensagem**.

2. Na página da **mensagem de composição** , insira o texto da mensagem e selecione **único dispositivo**.

3. Copie a cadeia de caracteres do token da janela saída do IDE e cole-a no campo **token de registro FCM** do console do firebase como antes.

4. Verifique se o aplicativo está em execução em primeiro plano e clique em **Enviar mensagem** no console do firebase:

    [![Enviando a mensagem em primeiro plano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Desta vez, a mensagem que foi registrada na janela de saída também é empacotada em uma nova &ndash; notificação. o ícone de notificação aparece na bandeja de notificação enquanto o aplicativo está em execução em primeiro plano:

[![Ícone de notificação para mensagem em primeiro plano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Ao abrir a notificação, você deverá ver a última mensagem enviada da GUI de notificações do console do firebase:

[![Notificação em primeiro plano mostrada com o ícone de primeiro plano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Desconectando do FCM

Para cancelar a assinatura de um tópico, chame o método [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) na classe [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) . Por exemplo, para cancelar a assinatura do tópico de _notícias_ assinado anteriormente, um botão **cancelar assinatura** pode ser adicionado ao layout com o seguinte código de manipulador:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Para cancelar o registro do dispositivo do FCM totalmente, exclua a ID da instância chamando o método [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) na classe [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) . Por exemplo:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Essa chamada de método exclui a ID da instância e os dados associados a ela. Como resultado, o envio periódico de dados de FCM para o dispositivo é interrompido.


## <a name="troubleshooting"></a>Solução de problemas

A seguir estão descritos os problemas e as soluções alternativas que podem surgir ao usar o firebase Cloud Messaging com o Xamarin. Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp não foi inicializado

Em alguns casos, você poderá ver esta mensagem de erro:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Esse é um problema conhecido que você pode solucionar limpando a solução e recompilando o projeto (**criar > solução limpa**, **criar > solução**de recompilação).

## <a name="summary"></a>Resumo

Este passo a passo detalha as etapas para implementar notificações remotas do firebase Cloud Messaging em um aplicativo Xamarin. Android. Ele descreveu como instalar os pacotes necessários necessários para as comunicações do FCM e explicou como configurar o manifesto do Android para acesso a servidores FCM. Ele forneceu um código de exemplo que ilustra como verificar a presença de Google Play Services. Ele demonstrou como implementar um serviço de ouvinte de ID de instância que negocia com FCM para um token de registro e explicou como esse código cria notificações em segundo plano enquanto o aplicativo está em segundo plano. Ele explicou como assinar mensagens de tópico e forneceu um exemplo de implementação de um serviço de ouvinte de mensagens que é usado para receber e exibir notificações remotas enquanto o aplicativo está em execução em primeiro plano.


## <a name="related-links"></a>Links relacionados

- [FCMNotifications (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Mensagens na nuvem do Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Sobre as mensagens FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
