---
title: Enviar e receber notificações por push com hubs de notificação do Azure e Xamarin. Forms
description: Este artigo explica como usar os hubs de notificação do Azure para enviar notificações por push de plataforma cruzada para aplicativos Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: c4237e9315ccc095abc72fdec24d58ffe1faebdf
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "68739223"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Enviar e receber notificações por push com hubs de notificação do Azure e Xamarin. Forms

[![Download amostra ](~/media/shared/download.png)Download exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

As notificações por push entregam informações de um sistema de back-end para um aplicativo móvel. A Apple, o Google e outras plataformas têm seu próprio serviço de notificação por push (PNS). Os hubs de notificação do Azure permitem centralizar notificações entre plataformas para que seu aplicativo de back-end possa se comunicar com um único Hub, que cuida da distribuição das notificações para cada PNS específica da plataforma.

Integre os hubs de notificação do Azure em aplicativos móveis seguindo estas etapas:

1. [Configure o Notification Services de envio por push e o Hub de notificação do Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Entenda como usar modelos e marcas](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Crie um aplicativo Xamarin. Forms de plataforma cruzada](#xamarinforms-application-functionality).
1. [Configure o projeto nativo do Android para notificações por push](#configure-the-android-application-for-notifications).
1. [Configure o projeto nativo do IOS para notificações por push](#configure-ios-for-notifications).
1. [Testar as notificações usando o Hub de notificação do Azure](#test-notifications-in-the-azure-portal).
1. [Crie um aplicativo de back-end para enviar notificações](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar o Notification Services de envio por push e o Hub de notificação do Azure

A integração dos hubs de notificação do Azure a um aplicativo móvel Xamarin. Forms é semelhante à integração dos hubs de notificação do Azure com um aplicativo nativo do Xamarin. Configure um **aplicativo FCM** seguindo as etapas do console firebase em [notificações por push para o Xamarin. Android usando os hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Conclua as etapas a seguir usando o tutorial do Xamarin. Android:

1. Defina um nome de pacote do Android, como `com.xamarin.notifysample`, que é usado no exemplo.
1. Baixe o **Google-Services. JSON** no console do firebase. Você adicionará esse arquivo ao seu aplicativo Android em uma etapa futura.
1. Crie uma instância do hub de notificação do Azure e dê um nome a ela. Este artigo e exemplo usam `xdocsnotificationhub` como o nome do Hub.
1. Copie a **chave do servidor** FCM e salve-a como a **chave de API** no **Google (GCM/FCM)** em seu hub de notificação do Azure.

A captura de tela a seguir mostra a configuração da plataforma Google no Hub de notificação do Azure:

![Captura de tela da configuração do Google do hub de notificação do Azure](azure-notification-hub-images/fcm-notification-hub-config.png "Configuração do Google do hub de notificação do Azure")

Você precisará de uma máquina macOS para concluir a configuração para dispositivos iOS. Configure o APNS seguindo as etapas iniciais em [notificações por push para o Xamarin. Ios usando os hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Conclua as etapas a seguir usando o tutorial do Xamarin. iOS:

1. Defina um identificador de pacote do iOS. Este artigo e exemplo usam `com.xamarin.notifysample` como o identificador de pacote.
1. Crie um arquivo CSR (solicitação de assinatura de certificado) e use-o para gerar um certificado de notificação por push.
1. Carregue o certificado de notificação por push em **Apple (APNS)** em seu hub de notificação do Azure.

A captura de tela a seguir mostra a configuração da plataforma Apple no Hub de notificação do Azure:

![Captura de tela da configuração da Apple do hub de notificação do Azure](azure-notification-hub-images/apns-notification-hub-config.png "Configuração da Apple do hub de notificação do Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrar modelos e marcas com o Hub de notificação do Azure

O Hub de notificação do Azure exige que os aplicativos móveis se registrem no Hub, definam modelos e assinem marcas. O registro vincula um identificador PNS específico à plataforma a um identificador no Hub de notificação do Azure. Para saber mais sobre os registros, consulte [Gerenciamento de registro](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Os modelos permitem que os dispositivos especifiquem modelos de mensagem com parâmetros. As mensagens de entrada podem ser personalizadas por dispositivo, por marca. Para saber mais sobre modelos, consulte [modelos](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

As marcas podem ser usadas para assinar categorias de mensagens, como notícias, esportes e clima. Para simplificar, o aplicativo de exemplo define um modelo padrão com um único parâmetro chamado `messageParam` e uma única marca chamada `default`. Em sistemas mais complexos, as marcas específicas do usuário podem ser usadas para mensagens de um usuário em dispositivos para notificações personalizadas. Para saber mais sobre marcas, consulte [expressões de marca e de roteamento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para receber mensagens com êxito, cada aplicativo nativo deve executar estas etapas:

1. Obtenha um identificador ou token PNS da plataforma PNS.
1. Registre o identificador PNS com o Hub de notificação do Azure.
1. Especifique um modelo que contenha os mesmos parâmetros de mensagens de saída.
1. Assine a marca direcionada por mensagens de saída.

Essas etapas são descritas em mais detalhes para cada plataforma nas seções [Configurar o aplicativo Android para notificações](#configure-the-android-application-for-notifications) e [Configurar o Ios para notificações](#configure-ios-for-notifications) .

## <a name="xamarinforms-application-functionality"></a>Funcionalidade do aplicativo Xamarin. Forms

O aplicativo Xamarin. Forms de exemplo exibe uma lista de mensagens de notificação por push. Isso é feito com o método `AddMessage`, que adiciona a mensagem de notificação por push especificada à interface do usuário. Esse método também impede que mensagens duplicadas sejam adicionadas à interface do usuário e são executadas no thread principal para que possam ser chamadas de qualquer thread. O código a seguir mostra o método `AddMessage`:

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

O aplicativo de exemplo contém um arquivo **AppConstants.cs** , que define as propriedades usadas pelos projetos da plataforma. Esse arquivo precisa ser personalizado com valores de seu hub de notificação do Azure. O código a seguir mostra o arquivo **AppConstants.cs** :

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

Personalize os seguintes valores em `AppConstants` para conectar o aplicativo de exemplo ao Hub de notificação do Azure:

* `NotificationHubName`: Use o nome do hub de notificação do Azure que você criou em seu portal do Azure.
* `ListenConnectionString`: esse valor é encontrado no Hub de notificação do Azure em **políticas de acesso**.

A captura de tela a seguir mostra onde esses valores estão localizados na portal do Azure:

![Captura de tela da política de acesso do hub de notificação do Azure](azure-notification-hub-images/notification-hub-access-policy.png "Política de acesso do hub de notificação do Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurar o aplicativo Android para notificações

Conclua as etapas a seguir para configurar o aplicativo Android para receber e processar notificações:

1. Configure o **nome do pacote** do Android para corresponder ao nome do pacote no console do firebase.
1. Instale os seguintes pacotes NuGet para interagir com os hubs de notificação Google Play, firebase e Azure:
    1. Xamarin. GooglePlayServices. base.
    1. Xamarin. firebase. Messaging.
    1. Xamarin. Azure. NotificationHubs. Android.
1. Copie o arquivo de `google-services.json` que você baixou durante a instalação do FCM para o projeto e defina a ação de compilação como `GoogleServicesJson`.
1. [Configure o AndroidManifest. xml para se comunicar com o firebase](#configure-android-manifest).
1. [Registre o aplicativo com o firebase e o Hub de notificação do Azure usando um `FirebaseInstanceIdService`](#register-using-a-custom-firebaseinstanceidservice).
1. [Processar mensagens com um `FirebaseMessagingService`](#process-messages-with-a-firebasemessagingservice).
1. [Adicione notificações de entrada à interface do usuário do Xamarin. Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> A ação de compilação **GoogleServicesJson** faz parte do pacote NuGet **Xamarin. GooglePlayServices. base** . O Visual Studio 2019 define as ações de compilação disponíveis durante a inicialização. Se você não vir **GoogleServicesJson** como uma ação de compilação, reinicie o Visual Studio 2019 depois de instalar os pacotes NuGet.

### <a name="configure-android-manifest"></a>Configurar o manifesto do Android

Os elementos de `receiver` dentro do elemento `application` permitem que o aplicativo se comunique com o firebase. Os elementos de `uses-permission` permitem que o aplicativo manipule mensagens e registre-se no Hub de notificação do Azure. O **AndroidManifest. xml** completo deve ser semelhante ao exemplo abaixo:

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

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>Registrar usando um FirebaseInstanceIdService personalizado

Firebase emite tokens que identificam exclusivamente um dispositivo no PNS. Os tokens têm um longo tempo de vida, mas são ocasionalmente atualizados. Quando um token é emitido ou atualizado, o aplicativo precisa registrar seu novo token com o Hub de notificação do Azure. O registro é tratado por uma instância de uma classe derivada de `FirebaseInstanceIdService`.

No aplicativo de exemplo, `FirebaseRegistrationService` classe herda de `FirebaseInstanceIdService`. Essa classe tem um `IntentFilter` que inclui `com.google.firebase.INSTANCE_ID_EVENT`, permitindo que o sistema operacional Android chame automaticamente `OnTokenRefresh` quando um token é emitido pelo firebase.

O código a seguir mostra o `FirebaseInstanceIdService` personalizado do aplicativo de exemplo:

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

O método `SendRegistrationToServer` no `FirebaseRegistrationClass` registra o dispositivo com o Hub de notificação do Azure e assina as marcas com um modelo. O aplicativo de exemplo define uma única marca chamada `default` e um modelo com um único parâmetro chamado `messageParam` no arquivo **AppConstants.cs** . Para obter mais informações sobre registro, marcas e modelos, consulte [registrar modelos e marcas com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>Processar mensagens com um FirebaseMessagingService

As mensagens de entrada são roteadas para uma instância de `FirebaseMessagingService`, onde podem ser convertidas em uma notificação local. O projeto do Android no aplicativo de exemplo contém uma classe chamada `FirebaseService` que herda de `FirebaseMessagingService`. Essa classe tem um `IntentFilter` que inclui `com.google.firebase.MESSAGING_EVENT`, permitindo que o sistema operacional Android chame automaticamente `OnMessageReceived` quando uma mensagem de notificação por push é recebida.

O exemplo a seguir mostra a `FirebaseService` do aplicativo de exemplo:

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

As mensagens de entrada são convertidas em uma notificação local com o método `SendLocalNotification`. Esse método cria um novo `Intent` e coloca o conteúdo da mensagem no `Intent` como um `string` `Extra`. Quando o usuário toca na notificação local, se o aplicativo está em primeiro plano ou em segundo plano, o `MainActivity` é iniciado e tem acesso ao conteúdo da mensagem por meio do objeto `Intent`.

O exemplo de notificação local e `Intent` requer que o usuário tome a ação de tocar na notificação. Isso é desejável quando o usuário deve executar uma ação antes que o estado do aplicativo seja alterado. No entanto, talvez você queira acessar os dados da mensagem sem a necessidade de uma ação do usuário em alguns casos. O exemplo anterior também envia a mensagem diretamente para a instância de `MainPage` atual com o método `SendMessageToMainPage`. Em produção, se você implementar os dois métodos para um único tipo de mensagem, o objeto `MainPage` receberá mensagens duplicadas se o usuário tocar na notificação.

> [!NOTE]
> O aplicativo Android receberá notificações por push somente se estiver em execução no plano de fundo ou em primeiro plano. Para receber notificações por push quando o `Activity` principal não estiver em execução, você deve implementar um serviço, que está além do escopo deste exemplo. Para obter mais informações, consulte [Creating Android Services](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Adicionar notificações de entrada à interface do usuário do Xamarin. Forms

A classe `MainActivity` precisa obter permissão para lidar com notificações e gerenciar dados de mensagens de entrada. O código a seguir mostra a implementação completa de `MainActivity`:

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

O atributo `Activity` define o `LaunchMode` do aplicativo como `SingleTop`. Esse modo de inicialização informa ao sistema operacional Android para permitir apenas uma única instância dessa atividade. Com esse modo de inicialização, os dados de entrada `Intent` são roteados para o método `OnNewIntent`, que extrai os dados da mensagem e os envia para a instância `MainPage` por meio do método `AddMessage`. Se seu aplicativo usa um modo de inicialização diferente, ele deve tratar `Intent` dados de maneira diferente.

O método `OnCreate` usa um método auxiliar chamado `IsPlayServiceAvailable` para garantir que o dispositivo dê suporte ao serviço Google Play. Emuladores ou dispositivos que não dão suporte ao serviço Google Play não podem receber notificações por push de firebase.

## <a name="configure-ios-for-notifications"></a>Configurar o iOS para notificações

O processo para configurar o aplicativo iOS para receber notificações é:

1. Configure o **identificador do pacote** no arquivo **info. plist** para corresponder ao valor usado no perfil de provisionamento.
1. Adicione a opção **habilitar notificações por push** ao arquivo de **direitos. plist** .
1. Adicione o pacote NuGet **Xamarin. Azure. NotificationHubs. Ios** ao seu projeto.
1. [Registre-se para receber notificações com o APNS](#register-for-notifications-with-apns).
1. [Registre o aplicativo com o Hub de notificação do Azure e assine as marcas](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Adicione notificações de APNS à interface do usuário do Xamarin. Forms](#add-apns-notifications-to-xamarinforms-ui).

A captura de tela a seguir mostra a opção **habilitar notificações por push** selecionada no arquivo de **direitos. plist** no Visual Studio:

![Captura de tela do direito das notificações por push](azure-notification-hub-images/push-notification-entitlement.png "Direitos de notificações por push")

### <a name="register-for-notifications-with-apns"></a>Registrar-se para notificações com APNS

O método `FinishedLaunching` no arquivo **AppDelegate.cs** deve ser substituído para se registrar para notificações remotas. O registro difere dependendo da versão do iOS que está sendo usada no dispositivo. O projeto do iOS no aplicativo de exemplo substitui o método `FinishedLaunching` para chamar `RegisterForRemoteNotifications` conforme mostrado no exemplo a seguir:

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

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registrar-se no Hub de notificação do Azure e assinar marcas

Quando o dispositivo for registrado com êxito para notificações remotas durante o método `FinishedLaunching`, o iOS chamará o método `RegisteredForRemoteNotifications`. Esse método deve ser substituído para executar as seguintes ações:

1. Crie uma instância do `SBNotificationHub`.
1. Cancele o registro de quaisquer registros existentes.
1. Registre o dispositivo no Hub de notificação.
1. Assine marcas específicas com um modelo.

Para obter mais informações sobre o registro do dispositivo, modelos e marcas, consulte [registrar modelos e marcas com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub). O código a seguir demonstra o registro do dispositivo e dos modelos:

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
> O registro para notificações remotas pode falhar em situações como nenhuma conexão de rede. Você pode optar por substituir o método `FailedToRegisterForRemoveNotifications` para manipular a falha de registro.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Adicionar notificações de APNS à interface do usuário do Xamarin. Forms

Quando um dispositivo recebe uma notificação remota, o iOS chama o método `ReceivedRemoteNotification`. A mensagem de entrada JSON é convertida em um objeto `NSDictionary`, e o método `ProcessNotification` extrai valores do dicionário e os envia para a instância `MainPage` do Xamarin. Forms. O método `ReceivedRemoteNotifications` é substituído para chamar `ProcessNotification` conforme mostrado no código a seguir:

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

## <a name="test-notifications-in-the-azure-portal"></a>Notificações de teste no portal do Azure

Os hubs de notificação do Azure permitem que você verifique se o aplicativo pode receber mensagens de teste. A seção de **envio de teste** no Hub de notificação permite que você escolha a plataforma de destino e envie uma mensagem. Definir a **expressão enviar para marca** como `default` enviará mensagens para aplicativos que registraram um modelo para a marca de `default`. Clicar no botão **Enviar** gera um relatório que inclui o número de dispositivos alcançados com a mensagem. A captura de tela a seguir mostra um teste de notificação do Android no portal do Azure:

![Captura de tela de uma mensagem de teste do hub de notificação do Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "Mensagem de teste do hub de notificação do Azure")

### <a name="testing-tips"></a>Dicas de teste

1. Ao testar se um aplicativo pode receber notificações por push, você deve usar um dispositivo físico. Os dispositivos virtuais Android e iOS podem não estar configurados corretamente para receber notificações por push.
1. O aplicativo Android de exemplo registra seu token e modelos uma vez quando o token firebase é emitido. Durante os testes, talvez seja necessário solicitar um novo token e registrá-lo novamente com o Hub de notificação do Azure. A melhor maneira de forçar isso é limpar o projeto, excluir as pastas `bin` e `obj` e desinstalar o aplicativo do dispositivo antes de recompilar e implantar.
1. Muitas partes do fluxo de notificação por push são executadas de forma assíncrona. Isso pode fazer com que os pontos de interrupção não sejam atingidos ou estejam sendo atingidos em uma ordem inesperada. Use o registro de dispositivo ou de depuração para rastrear a execução sem interromper o fluxo do aplicativo. Filtre o log do dispositivo Android usando o `DebugTag` especificado em `Constants`.

## <a name="create-a-notification-dispatcher"></a>Criar um Dispatcher de notificação

Os hubs de notificação do Azure permitem que seu aplicativo de back-end envie notificações para dispositivos entre plataformas. O exemplo demonstra a expedição de notificação com o aplicativo de console **NotificationDispatcher** . O aplicativo inclui o arquivo **DispatcherConstants.cs** , que define as seguintes propriedades:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Você deve configurar o **DispatcherConstants.cs** para corresponder à configuração do hub de notificação do Azure. O valor da propriedade `SubscriptionTags` deve corresponder aos valores usados nos aplicativos cliente. A propriedade `NotificationHubName` é o nome da sua instância do hub de notificação do Azure. A propriedade `FullAccessConnectionString` é a chave de acesso encontrada nas políticas de **acesso**do hub de notificação. A captura de tela a seguir mostra o local das propriedades `NotificationHubName` e `FullAccessConnectionString` no portal do Azure:

![Captura de tela do nome e FullAccessConnectionString do hub de notificação do Azure](azure-notification-hub-images/notification-hub-full-access-policy.png "Nome e FullAccessConnectionString do hub de notificação do Azure")

O aplicativo de console faz um loop por cada `SubscriptionTags` valor e envia notificações aos assinantes usando uma instância da classe `NotificationHubClient`. O código a seguir mostra o aplicativo de console `Program` classe:

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

Quando o aplicativo de console de exemplo é executado, a barra de espaços pode ser pressionada para enviar mensagens. Os dispositivos que executam os aplicativos cliente devem receber notificações numeradas, desde que estejam configurados corretamente.

## <a name="related-links"></a>Links relacionados

* [Modelos de notificação por push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gerenciamento de registro de dispositivo](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Expressões de marcação e de roteamento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Tutorial de hubs de notificação do Azure para Xamarin. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Tutorial dos hubs de notificação do Azure para Xamarin. Ios](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
