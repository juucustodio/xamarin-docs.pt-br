---
title: Enviar e receber notificações por push com Hubs de notificação do Azure e o xamarin. Forms
description: Este artigo explica como usar os Hubs de notificação do Azure para enviar notificações por push de plataforma cruzada para aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: 474398922bf00e3a430166d8b2e073d200e6ed6e
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659323"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Enviar e receber notificações por push com Hubs de notificação do Azure e o xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png)baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

Envie notificações enviar informações de um sistema de back-end para um aplicativo móvel. Apple, Google e outras plataformas tem seu próprio serviço de notificação por Push (PNS). Os Hubs de notificação do Azure permitem que você centralize notificações entre plataformas para que seu aplicativo de back-end que possa se comunicar com um único hub cuida de distribuir as notificações para cada PNS específico da plataforma.

Integre os Hubs de notificação do Azure em aplicativos móveis, seguindo estas etapas:

1. [Configurar serviços de notificação por Push e o Hub de notificação do Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Entenda como usar marcas e modelos](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Criar um aplicativo do xamarin. Forms multiplataforma](#xamarinforms-application-functionality).
1. [Configurar o projeto Android nativo para notificações por push](#configure-the-android-application-for-notifications).
1. [Configurar o projeto do iOS nativo para notificações por push](#configure-ios-for-notifications).
1. [Testar as notificações usando o Hub de notificação do Azure](#test-notifications-in-the-azure-portal).
1. [Criar um aplicativo de back-end para enviar notificações](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar serviços de notificação por Push e o Hub de notificação do Azure

A integração dos Hubs de notificação do Azure com um aplicativo móvel do xamarin. Forms é semelhante ao integrar os Hubs de notificação do Azure com um aplicativo nativo do Xamarin. Configurar uma **aplicativo FCM** seguindo estas etapas de Console do Firebase em [notificações por Push para o xamarin. Android usando os Hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Conclua as etapas a seguir usando o tutorial do xamarin. Android:

1. Definir um nome de pacote do Android, como `com.xamarin.notifysample`, que é usado no exemplo.
1. Baixe **google-Services. JSON** do console Firebase. Você adicionará esse arquivo ao seu aplicativo Android em uma etapa futura.
1. Criar uma instância de Hub de notificação do Azure e dê a ele um nome. Neste artigo e exemplo, use `xdocsnotificationhub` como o nome do hub.
1. Copie o FCM **chave do servidor** e salve-o como o **chave de API** sob **Google (GCM/FCM)** no Hub de notificação do Azure.

Captura de tela a seguir mostra a configuração da plataforma Google no Hub de notificação do Azure:

![Captura de tela da configuração do Google de Hub de notificação do Azure](azure-notification-hub-images/fcm-notification-hub-config.png "configuração do Google de Hub de notificação do Azure")

Você precisará de um computador macOS para concluir a configuração para dispositivos iOS. Configurar o APNS seguindo inicial etapas em [notificações por Push para o xamarin. IOS usando os Hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Conclua as etapas a seguir usando o tutorial do xamarin. IOS:

1. Defina um identificador de pacote do iOS. Neste artigo e exemplo, use `com.xamarin.notifysample` como o identificador de pacote.
1. Crie um arquivo de solicitação de assinatura de certificado (CSR) e usá-lo para gerar um certificado de notificação por push.
1. Carregue o certificado de notificação por push sob **da Apple (APNS)** no Hub de notificação do Azure.

Captura de tela a seguir mostra a configuração da plataforma da Apple no Hub de notificação do Azure:

![Captura de tela da configuração da Apple de Hub de notificação do Azure](azure-notification-hub-images/apns-notification-hub-config.png "configuração da Apple de Hub de notificação do Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrar marcas e modelos com o Hub de notificação do Azure

O Hub de notificação do Azure requer aplicativos móveis para registrar com o hub, definir modelos e inscrever-se às marcas. Registro vincula um identificador PNS específico da plataforma para um identificador no Hub de notificação do Azure. Para saber mais sobre os registros, consulte [gerenciamento de registro](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Modelos permitem que dispositivos especificar modelos de mensagem com parâmetros. Mensagens de entrada podem ser personalizadas por dispositivo, por marca. Para saber mais sobre modelos, consulte [modelos](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Marcas podem ser usadas para se inscrever em categorias de mensagem, como notícias, esportes e clima. Para simplificar, o aplicativo de exemplo define um modelo padrão com um parâmetro único chamado `messageParam` e uma única marca chamada `default`. Em sistemas mais complexos, marcas de usuário específicas podem ser usadas para a mensagem a um usuário em todos os dispositivos para notificações personalizadas. Para saber mais sobre marcas, consulte [expressões de marca e roteamento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para receber com êxito mensagens, cada aplicativo nativo deve executar estas etapas:

1. Para obter um token ou indicador PNS da plataforma PNS.
1. Registre o identificador PNS com o Hub de notificação do Azure.
1. Especifique um modelo que contém os mesmos parâmetros que as mensagens de saída.
1. Inscrever-se a marca direcionada por mensagens de saída.

Essas etapas são descritas em mais detalhes para cada plataforma na [configurar o aplicativo Android para notificações](#configure-the-android-application-for-notifications) e [configurar notificações do iOS](#configure-ios-for-notifications) seções.

## <a name="xamarinforms-application-functionality"></a>Funcionalidade do aplicativo xamarin. Forms

O exemplo de aplicativo do xamarin. Forms exibe uma lista de mensagens de notificação por push. Isso é feito com o `AddMessage` método, que adiciona a mensagem de notificação por push especificada na interface do usuário. Esse método também impede que mensagens duplicadas sejam adicionados à interface do usuário e é executado no thread principal, de modo que ele possa ser chamado de qualquer thread. O seguinte código mostra o `AddMessage` método:

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

O aplicativo de exemplo contém um **AppConstants.cs** arquivo, que define as propriedades usadas pelos projetos de plataforma. Esse arquivo precisa ser personalizado com os valores do Hub de notificação do Azure. O código a seguir mostra a **AppConstants.cs** arquivo:

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

Personalizar os seguintes valores no `AppConstants` para conectar o aplicativo de exemplo para o Hub de notificação do Azure:

* `NotificationHubName`: Use o nome do Hub de notificação do Azure criada no portal do Azure.
* `ListenConnectionString`: Esse valor é encontrado no Hub de notificação do Azure sob **políticas de acesso**.

A captura de tela a seguir mostra onde esses valores estão localizados no portal do Azure:

![Captura de tela da política de acesso do Hub de notificação do Azure](azure-notification-hub-images/notification-hub-access-policy.png "política de acesso do Hub de notificação do Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurar o aplicativo Android para notificações

Conclua as seguintes etapas para configurar o aplicativo Android para receber e processar notificações:

1. Configurar o Android **nome do pacote** para corresponder ao nome do pacote no console do Firebase.
1. Instale os seguintes pacotes NuGet para interagir com o Google Play, Firebase e Hubs de notificação do Azure:
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Cópia de `google-services.json` arquivo que você baixou durante a instalação do FCM ao projeto e defina a Build Action `GoogleServicesJson`.
1. [Configurar androidmanifest. XML para se comunicar com o Firebase](#configure-android-manifest).
1. [Registrar o aplicativo com o Firebase e o Hub de notificação do Azure usando um `FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice).
1. [Processar mensagens com um `FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice).
1. [Adicionar notificações de entrada à interface do usuário do xamarin. Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> O **GoogleServicesJson** ação de compilação faz parte do **Xamarin.GooglePlayServices.Base** pacote do NuGet. 2019 do Visual Studio define as ações de Build disponíveis durante a inicialização. Se você não vir **GoogleServicesJson** como uma ação de compilação, reinicie 2019 do Visual Studio depois de instalar os pacotes do NuGet.

### <a name="configure-android-manifest"></a>Configurar o manifesto do Android

O `receiver` elementos dentro de `application` elemento permitir que o aplicativo para se comunicar com o Firebase. O `uses-permission` elementos permitem que o aplicativo lidar com mensagens e registrar com o Hub de notificação do Azure. A conclusão **androidmanifest. XML** deve ser semelhante ao exemplo a seguir:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>Registrar-se usando um FirebaseInstanceIdService personalizado

O firebase emite tokens que identificam exclusivamente um dispositivo em que o PNS. Tokens têm um tempo de vida longa, mas são atualizados ocasionalmente. Quando um token é emitido ou atualizado, o aplicativo precisa registrar seu novo token com o Hub de notificação do Azure. Registro é tratado por uma instância de uma classe que deriva de `FirebaseInstanceIdService`.

No aplicativo de exemplo, `FirebaseRegistrationService` classe herda de `FirebaseInstanceIdService`. Essa classe tem um `IntentFilter` que inclui `com.google.firebase.INSTANCE_ID_EVENT`, permitindo que o sistema operacional Android automaticamente chamar `OnTokenRefresh` quando um token é emitido pelo Firebase.

O código a seguir mostra o personalizado `FirebaseInstanceIdService` do aplicativo de exemplo:

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

O `SendRegistrationToServer` método no `FirebaseRegistrationClass` registra o dispositivo com o Hub de notificação do Azure e assina marcas com um modelo. O aplicativo de exemplo define uma única marca denominada `default` e um modelo com um único parâmetro chamado `messageParam` na **AppConstants.cs** arquivo. Para obter mais informações sobre registro, marcas e modelos, consulte [registrar marcas e modelos com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>Processar as mensagens com um FirebaseMessagingService

Mensagens de entrada são roteadas para um `FirebaseMessagingService` instância, onde pode ser convertidos em uma notificação de local. O projeto do Android no aplicativo de exemplo contém uma classe chamada `FirebaseService` que herda de `FirebaseMessagingService`. Essa classe tem um `IntentFilter` que inclui `com.google.firebase.MESSAGING_EVENT`, permitindo que o sistema operacional Android automaticamente chamar `OnMessageReceived` quando uma mensagem de notificação por push é recebida.

A exemplo a seguir mostra o `FirebaseService` do aplicativo de exemplo:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

Mensagens de entrada são convertidas em uma notificação local com o `SendLocalNotification` método. Esse método cria uma nova `Intent` e coloca a mensagem de conteúdo para o `Intent` como um `string` `Extra`. Quando o usuário toca a notificação local, se o aplicativo está em primeiro plano ou segundo plano, o `MainActivity` é iniciado e tem acesso ao conteúdo da mensagem por meio de `Intent` objeto.

A notificação local e `Intent` exemplo exige que o usuário executar a ação de tocar na notificação. Isso é desejável quando o usuário deve agir antes das alterações de estado do aplicativo. No entanto, você talvez queira acessar os dados da mensagem sem a necessidade de uma ação do usuário em alguns casos. O exemplo anterior também envia a mensagem diretamente para o atual `MainPage` de instância com o `SendMessageToMainPage` método. Em produção, se você implementar os dois métodos para um tipo de mensagem único, o `MainPage` objeto receberá mensagens duplicadas, se o usuário toca na notificação.

> [!NOTE]
> O aplicativo Android receberá notificações por push apenas se ele estiver em execução no plano de fundo ou primeiro plano. Para receber notificações por push ao principal `Activity` é não em execução, você deve implementar um serviço, que está além do escopo deste exemplo. Para obter mais informações, consulte [criando serviços do Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Adicionar notificações de entrada na interface do usuário do xamarin. Forms

O `MainActivity` classe precisa obter permissão para lidar com notificações e gerenciar dados de mensagem de entrada. O código a seguir mostra todo `MainActivity` implementação:

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

O `Activity` atributo define o aplicativo `LaunchMode` para `SingleTop`. Esse modo de inicialização informa o sistema operacional Android para permitir que somente uma única instância dessa atividade. Com esse modo de inicialização, entrado `Intent` os dados são roteados para o `OnNewIntent` método, que extrai dados de mensagem e o envia para o `MainPage` por meio da instância a `AddMessage` método. Se seu aplicativo usa um modo de inicialização diferente, ele deve tratar `Intent` dados de maneira diferente.

O `OnCreate` método usa um método auxiliar chamado `IsPlayServiceAvailable` para garantir que o dispositivo dá suporte a Google Play Service. Emuladores ou dispositivos que não dão suporte a Google Play Service não podem receber notificações por push do Firebase.

## <a name="configure-ios-for-notifications"></a>Configurar notificações do iOS

O processo de configuração do aplicativo do iOS para receber notificações é:

1. Configurar o **identificador de pacote** na **Info. plist** arquivo para coincidir com o valor usado no perfil de provisionamento.
1. Adicione a **habilitar notificações por Push** opção para o **Entitlements. plist** arquivo.
1. Adicione a **notificationhubs** pacote NuGet ao seu projeto.
1. [Se registrar para notificações com o APNS](#register-for-notifications-with-apns).
1. [Registrar o aplicativo com o Hub de notificação do Azure e inscrever-se às marcas](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Adicionar notificações do APNS à interface do usuário do xamarin. Forms](#add-apns-notifications-to-xamarinforms-ui).

A captura de tela a seguir mostra a **habilitar notificações por Push** opção selecionada na **Entitlements. plist** arquivo dentro do Visual Studio:

![Captura de tela da qualificação de notificações por Push](azure-notification-hub-images/push-notification-entitlement.png "enviar por Push o direito de notificações")

### <a name="register-for-notifications-with-apns"></a>Registre-se para notificações com o APNS

O `FinishedLaunching` método na **AppDelegate.cs** arquivo precisa ser substituído para se registrar para notificações remotas. Registro é diferente dependendo da versão do iOS que está sendo usada no dispositivo. O projeto do iOS no aplicativo de exemplo substitui o `FinishedLaunching` método a ser chamado `RegisterForRemoteNotifications` conforme mostrado no exemplo a seguir:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registrar no Hub de notificação do Azure e inscrever-se às marcas

Quando o dispositivo foi registrado com êxito para notificações remotas durante o `FinishedLaunching` método, o iOS chamará o `RegisteredForRemoteNotifications` método. Esse método deve ser substituído para executar as seguintes ações:

1. Criar uma instância de `SBNotificationHub`.
1. Cancelar o registro de todos os registros existentes.
1. Registre o dispositivo com hub de notificação.
1. Inscrever-se marcas específicas com um modelo.

Para obter mais informações sobre o registro do dispositivo, modelos e marcas, consulte [registrar marcas e modelos com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub). O código a seguir demonstra o registro do dispositivo e modelos:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> Se registrar para notificações remotas pode falhar em situações como nenhuma conexão de rede. Você pode optar por substituir o `FailedToRegisterForRemoveNotifications` método para lidar com falha no registro.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Adicionar notificações do APNS à interface do usuário do xamarin. Forms

Quando um dispositivo recebe uma notificação remota, o iOS chama o `ReceivedRemoteNotification` método. Mensagem de entrada JSON é convertida em uma `NSDictionary` objeto e o `ProcessNotification` método extrai os valores do dicionário e as envia para o xamarin. Forms `MainPage` instância. O `ReceivedRemoteNotifications` método é substituído para chamar `ProcessNotification` conforme mostrado no código a seguir:

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Testar as notificações no portal do Azure

Os Hubs de notificação do Azure permitem que você verificar que seu aplicativo pode receber mensagens de teste. O **envio de teste** seção no hub de notificação permite que você escolha a plataforma de destino e enviar uma mensagem. Definindo o **enviar a expressão de marca** à `default` enviarão mensagens para aplicativos que registraram um modelo para o `default` marca. Clicar a **enviar** botão gera um relatório que inclui o número de dispositivos atingido com a mensagem. Captura de tela a seguir mostra um teste de notificação do Android no portal do Azure:

![Captura de tela de uma mensagem de teste do Hub de notificação do Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "mensagem de teste do Hub de notificação do Azure")

### <a name="testing-tips"></a>Dicas de testes

1. Durante o teste que um aplicativo pode receber notificações por push, você deve usar um dispositivo físico. Dispositivos virtuais Android e iOS não podem ser configurados corretamente para receber notificações por push.
1. O aplicativo Android de exemplo registra seu token e os modelos depois que o token de Firebase é emitido. Durante o teste, você precisará solicitar um novo token e registrar novamente com o Hub de notificação do Azure. A melhor maneira de forçar isso é limpar seu projeto, exclua o `bin` e `obj` pastas e desinstalar o aplicativo do dispositivo antes de recompilar e implantar.
1. Muitas partes do fluxo de notificação por push são executadas de forma assíncrona. Isso pode resultar em pontos de interrupção não sendo visita ou atingido em uma ordem inesperada. Use o log de depuração ou de dispositivo para rastrear a execução sem interromper o fluxo do aplicativo. Filtre o log de dispositivo Android usando o `DebugTag` especificado no `Constants`.

## <a name="create-a-notification-dispatcher"></a>Criar um dispatcher de notificação

Os Hubs de notificação do Azure permitem que seu aplicativo de back-end enviar notificações para dispositivos entre plataformas. O exemplo demonstra a expedição de notificação com o **NotificationDispatcher** aplicativo de console. O aplicativo inclui o **DispatcherConstants.cs** arquivo, que define as propriedades a seguir:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Você deve configurar o **DispatcherConstants.cs** para corresponder ao corresponder à configuração de seu Hub de notificação do Azure. O valor da `SubscriptionTags` propriedade deve corresponder aos valores usados nos aplicativos do cliente. O `NotificationHubName` propriedade é o nome da sua instância do Hub de notificação do Azure. O `FullAccessConnectionString` propriedade é a chave de acesso encontrada em seu hub de notificação **políticas de acesso**. Captura de tela a seguir mostra o local do `NotificationHubName` e `FullAccessConnectionString` propriedades no portal do Azure:

![Captura de tela do nome do Hub de notificação do Azure e FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "FullAccessConnectionString e nome do Hub de notificação do Azure")

O aplicativo de console executa loops em cada `SubscriptionTags` de valor e envia notificações para os assinantes usando uma instância da `NotificationHubClient` classe. O código a seguir mostra o aplicativo de console `Program` classe:

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

Quando o aplicativo de console de exemplo é executado, a barra de espaço pode ser pressionada para enviar mensagens. Dispositivos que executam o cliente de aplicativos devem receber notificações numeradas, fornecidos estão configurados corretamente.

## <a name="related-links"></a>Links relacionados

* [Modelos de notificação de push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gerenciamento de registro de dispositivo](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Roteamento e expressões de marca](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Tutorial de Hubs de notificação do Azure do xamarin. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Tutorial de Hubs de notificação do Azure do xamarin. IOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
