---
title: Enviar e receber notificações por push com os hubs de notificação do Azure e Xamarin.Forms
description: Este artigo explica como usar os hubs de notificação do Azure para enviar notificações por push de plataforma cruzada para Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- Firebase
ms.openlocfilehash: 721785fe2eeb35f0ef04d1a7854afe4039a66849
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561827"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-no-locxamarinforms"></a>Enviar e receber notificações por push com os hubs de notificação do Azure e Xamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

As notificações por push entregam informações de um sistema de back-end para um aplicativo móvel. A Apple, o Google e outras plataformas têm seu próprio serviço de notificação por push (PNS). Os hubs de notificação do Azure permitem centralizar notificações entre plataformas para que seu aplicativo de back-end possa se comunicar com um único Hub, que cuida da distribuição das notificações para cada PNS específica da plataforma.

Integre os hubs de notificação do Azure em aplicativos móveis seguindo estas etapas:

1. [Configure o Notification Services de envio por push e o Hub de notificação do Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Entenda como usar modelos e marcas](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Crie um Xamarin.Forms aplicativo de plataforma cruzada](#xamarinforms-application-functionality).
1. [Configure o projeto nativo do Android para notificações por push](#configure-the-android-application-for-notifications).
1. [Configure o projeto nativo do IOS para notificações por push](#configure-ios-for-notifications).
1. [Testar as notificações usando o Hub de notificação do Azure](#test-notifications-in-the-azure-portal).
1. [Crie um aplicativo de back-end para enviar notificações](#create-a-notification-dispatcher).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar os Serviços de Notificação por Push nos Hubs de Notificação do Azure

A integração dos hubs de notificação do Azure a um Xamarin.Forms aplicativo móvel é semelhante à integração dos hubs de notificação do Azure com um aplicativo nativo do Xamarin. Configure um Firebase aplicativo de mensagens de nuvem (FCM) seguindo as Firebase etapas do console em [notificações por push para o Xamarin. Android usando os hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Conclua as etapas a seguir usando o tutorial do Xamarin. Android:

1. Defina um nome de pacote do Android `com.xamarin.notifysample` , como, que é usado no exemplo.
1. Baixe `google-services.json` no Firebase console do. Você adicionará esse arquivo ao seu aplicativo Android em uma etapa futura.
1. Crie uma instância do hub de notificação do Azure e dê um nome a ela. Este artigo e o exemplo usam `xdocsnotificationhub` como o nome do Hub.
1. Copie a **chave do servidor** FCM e salve-a como a **chave de API** no **Google (GCM/FCM)** em seu hub de notificação do Azure.

A captura de tela a seguir mostra a configuração da plataforma Google no Hub de notificação do Azure:

![Captura de tela da configuração do Google do hub de notificação do Azure](azure-notification-hub-images/fcm-notification-hub-config.png "Configuração do Google do hub de notificação do Azure")

Você precisará de uma máquina macOS para concluir a configuração para dispositivos iOS. Configure o APNS (Apple Push Notification Services) seguindo as etapas iniciais em [notificações por push para o Xamarin. Ios usando os hubs de notificação do Azure](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Conclua as etapas a seguir usando o tutorial do Xamarin. iOS:

1. Defina um identificador de pacote do iOS. Este artigo e o exemplo usam `com.xamarin.notifysample` como o identificador de pacote.
1. Crie um arquivo CSR (solicitação de assinatura de certificado) e use-o para gerar um certificado de notificação por push.
1. Carregue o certificado de notificação por push em **Apple (APNS)** em seu hub de notificação do Azure.

A captura de tela a seguir mostra a configuração da plataforma Apple no Hub de notificação do Azure:

![Captura de tela da configuração da Apple do hub de notificação do Azure](azure-notification-hub-images/apns-notification-hub-config.png "Configuração da Apple do hub de notificação do Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrar modelos e marcas com o Hub de notificação do Azure

O Hub de notificação do Azure exige que os aplicativos móveis se registrem no Hub, definam modelos e assinem marcas. O registro vincula um identificador PNS específico à plataforma a um identificador no Hub de notificação do Azure. Para saber mais sobre os registros, consulte [Gerenciamento de registro](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Os modelos permitem que os dispositivos especifiquem modelos de mensagem com parâmetros. As mensagens de entrada podem ser personalizadas por dispositivo, por marca. Para saber mais sobre modelos, consulte [modelos](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

As marcas podem ser usadas para assinar categorias de mensagens, como notícias, esportes e clima. Para simplificar, o aplicativo de exemplo define um modelo padrão com um único parâmetro chamado `messageParam` e uma única marca chamada `default` . Em sistemas mais complexos, as marcas específicas do usuário podem ser usadas para mensagens de um usuário em dispositivos para notificações personalizadas. Para saber mais sobre marcas, consulte [expressões de marca e de roteamento](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Para receber mensagens com êxito, cada aplicativo nativo deve executar estas etapas:

1. Obtenha um identificador ou token PNS da plataforma PNS.
1. Registre o identificador PNS com o Hub de notificação do Azure.
1. Especifique um modelo que contenha os mesmos parâmetros de mensagens de saída.
1. Assine a marca direcionada por mensagens de saída.

Essas etapas são descritas em mais detalhes para cada plataforma nas seções [Configurar o aplicativo Android para notificações](#configure-the-android-application-for-notifications) e [Configurar o Ios para notificações](#configure-ios-for-notifications) .

## <a name="no-locxamarinforms-application-functionality"></a>Xamarin.Forms funcionalidade do aplicativo

O aplicativo de exemplo Xamarin.Forms exibe uma lista de mensagens de notificação por push. Isso é obtido com o `AddMessage` método, que adiciona a mensagem de notificação por push especificada à interface do usuário. Esse método também impede que mensagens duplicadas sejam adicionadas à interface do usuário e são executadas no thread principal para que possam ser chamadas de qualquer thread. O código a seguir mostra o `AddMessage` método:

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

O aplicativo de exemplo contém um `AppConstants.cs` arquivo, que define as propriedades usadas pelos projetos da plataforma. Esse arquivo precisa ser personalizado com valores de seu hub de notificação do Azure. O código a seguir mostra o `AppConstants.cs` arquivo:

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

Personalize os seguintes valores no `AppConstants` para conectar o aplicativo de exemplo ao Hub de notificação do Azure:

* `NotificationHubName`: Use o nome do hub de notificação do Azure que você criou em seu portal do Azure.
* `ListenConnectionString`: Esse valor é encontrado no Hub de notificação do Azure em **políticas de acesso**.

A captura de tela a seguir mostra onde esses valores estão localizados na portal do Azure:

![Captura de tela da política de acesso do hub de notificação do Azure](azure-notification-hub-images/notification-hub-access-policy.png "Política de acesso do hub de notificação do Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurar o aplicativo Android para notificações

Conclua as etapas a seguir para configurar o aplicativo Android para receber e processar notificações:

1. Configure o **nome do pacote** do Android para corresponder ao nome do pacote no Firebase console do.
1. Instale os seguintes pacotes NuGet para interagir com Google Play Firebase e os hubs de notificação do Azure:
    1. `Xamarin.GooglePlayServices.Base`
    1. `Xamarin.Firebase.Messaging`
    1. `Xamarin.Azure.NotificationHubs.Android`
1. Copie o `google-services.json` arquivo que você baixou durante a instalação do FCM para o projeto e defina a ação de compilação como `GoogleServicesJson` .
1. [Configurar](#configure-android-manifest) `AndroidManifest.xml` para se comunicar com o Firebase .
1. [Substituir](#override-firebasemessagingservice-to-handle-messages) `FirebaseMessagingService` para lidar com mensagens.
1. [Adicionar](#add-incoming-notifications-to-the-xamarinforms-ui) notificações de entrada à Xamarin.Forms interface do usuário.

> [!NOTE]
> A `GoogleServicesJson` ação de compilação faz parte do `Xamarin.GooglePlayServices.Base` pacote NuGet. O Visual Studio 2019 define as ações de compilação disponíveis durante a inicialização. Se você não vir `GoogleServicesJson` como uma ação de compilação, reinicie o Visual Studio 2019 depois de instalar os pacotes NuGet.

### <a name="configure-android-manifest"></a>Configurar o manifesto do Android

Os `receiver` elementos dentro do `application` elemento permitem que o aplicativo se comunique Firebase . Os `uses-permission` elementos permitem que o aplicativo manipule mensagens e registre-se no Hub de notificação do Azure. A conclusão `AndroidManifest.xml` deve ser semelhante ao exemplo abaixo:

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

### <a name="override-no-locfirebasemessagingservice-to-handle-messages"></a>Substituir `FirebaseMessagingService` para tratar mensagens

Para registrar com Firebase e tratar mensagens, subclasse a `FirebaseMessagingService` classe. O aplicativo de exemplo define uma `FirebaseService` classe que subclasses o `FirebaseMessagingService` . Essa classe é marcada com um `IntentFilter` atributo, que inclui o `com.google.firebase.MESSAGING_EVENT` filtro. Esse filtro permite que o Android passe mensagens de entrada para esta classe para manipulação:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

Quando o aplicativo for iniciado, o Firebase SDK solicitará automaticamente um identificador de token exclusivo do Firebase servidor. Após a solicitação bem-sucedida, o `OnNewToken` método será chamado na `FirebaseService` classe. O projeto de exemplo substitui esse método e registra o token com os hubs de notificação do Azure:

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

O `SendRegistrationToServer` método registra o dispositivo com o Hub de notificação do Azure e assina as marcas com um modelo. O aplicativo de exemplo define uma única marca chamada `default` e um modelo com um único parâmetro chamado `messageParam` no `AppConstants.cs` arquivo. Para obter mais informações sobre registro, marcas e modelos, consulte [registrar modelos e marcas com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub).

Quando uma mensagem for recebida, o `OnMessageReceived` método será chamado na `FirebaseService` classe:

```csharp
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

    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new NotificationCompat.Builder(this, AppConstants.NotificationChannelName)
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
```

As mensagens de entrada são convertidas em uma notificação local com o `SendLocalNotification` método. Esse método cria um novo `Intent` e coloca o conteúdo da mensagem no `Intent` como um `string` `Extra` . Quando o usuário toca na notificação local, se o aplicativo está em primeiro plano ou em segundo plano, o `MainActivity` é iniciado e tem acesso ao conteúdo da mensagem por meio do `Intent` objeto.

A notificação local e o `Intent` exemplo exigem que o usuário execute a ação de tocar na notificação. Isso é desejável quando o usuário deve executar uma ação antes que o estado do aplicativo seja alterado. No entanto, talvez você queira acessar os dados da mensagem sem a necessidade de uma ação do usuário em alguns casos. O exemplo anterior também envia a mensagem diretamente para a `MainPage` instância atual com o `SendMessageToMainPage` método. Em produção, se você implementar os dois métodos para um único tipo de mensagem, o objeto receberá `MainPage` mensagens duplicadas se o usuário tocar na notificação.

> [!NOTE]
> O aplicativo Android receberá notificações por push somente se estiver em execução no plano de fundo ou em primeiro plano. Para receber notificações por push quando a principal `Activity` não está em execução, você deve implementar um serviço, que está além do escopo deste exemplo. Para obter mais informações, consulte [Creating Android Services](../../../android/app-fundamentals/services/index.md)

### <a name="add-incoming-notifications-to-the-no-locxamarinforms-ui"></a>Adicionar notificações de entrada à Xamarin.Forms interface do usuário

A `MainActivity` classe precisa obter permissão para lidar com notificações e gerenciar dados de mensagens de entrada. O código a seguir mostra a `MainActivity` implementação completa:

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

        if (!IsPlayServiceAvailable())
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

O `Activity` atributo define o aplicativo `LaunchMode` como `SingleTop` . Esse modo de inicialização informa ao sistema operacional Android para permitir apenas uma única instância dessa atividade. Com esse modo de inicialização, `Intent` os dados de entrada são roteados para o `OnNewIntent` método, que extrai os dados da mensagem e os envia para a `MainPage` instância por meio do `AddMessage` método. Se seu aplicativo usa um modo de inicialização diferente, ele deve tratar `Intent` os dados de maneira diferente.

O `OnCreate` método usa um método auxiliar chamado `IsPlayServiceAvailable` para garantir que o dispositivo dê suporte ao serviço Google Play. Emuladores ou dispositivos que não dão suporte ao serviço Google Play não podem receber notificações por push do Firebase .

## <a name="configure-ios-for-notifications"></a>Configurar o iOS para notificações

O processo para configurar o aplicativo iOS para receber notificações é:

1. Configure o **identificador do pacote** no `Info.plist` arquivo para corresponder ao valor usado no perfil de provisionamento.
1. Adicione a opção **habilitar notificações por push** ao `Entitlements.plist` arquivo.
1. Adicione o `Xamarin.Azure.NotificationHubs.iOS` pacote NuGet ao seu projeto.
1. [Registre-se](#register-for-notifications-with-apns) para receber notificações com o APNS.
1. [Registre](#register-with-azure-notification-hub-and-subscribe-to-tags) o aplicativo com o Hub de notificação do Azure e assine as marcas.
1. [Adicionar](#add-apns-notifications-to-xamarinforms-ui) Notificações de APNS para a Xamarin.Forms interface do usuário.

A captura de tela a seguir mostra a opção **habilitar notificações por push** selecionada no `Entitlements.plist` arquivo no Visual Studio:

![Captura de tela do direito das notificações por push](azure-notification-hub-images/push-notification-entitlement.png "Direitos de notificações por push")

### <a name="register-for-notifications-with-apns"></a>Registrar-se para notificações com APNS

O `FinishedLaunching` método no `AppDelegate.cs` arquivo deve ser substituído para se registrar para notificações remotas. O registro difere dependendo da versão do iOS que está sendo usada no dispositivo. O projeto do iOS no aplicativo de exemplo substitui o `FinishedLaunching` método a ser chamado `RegisterForRemoteNotifications` , conforme mostrado no exemplo a seguir:

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

Quando o dispositivo tiver se registrado com êxito para notificações remotas durante o `FinishedLaunching` método, o Ios chamará o `RegisteredForRemoteNotifications` método. Esse método deve ser substituído para executar as seguintes ações:

1. Crie uma instância do `SBNotificationHub` .
1. Cancele o registro de quaisquer registros existentes.
1. Registre o dispositivo no Hub de notificação.
1. Assine marcas específicas com um modelo.

Para obter mais informações sobre o registro do dispositivo, modelos e marcas, consulte [registrar modelos e marcas com o Hub de notificação do Azure](#register-templates-and-tags-with-the-azure-notification-hub). O código a seguir demonstra o registro do dispositivo e dos modelos:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
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
> O registro para notificações remotas pode falhar em situações como nenhuma conexão de rede. Você pode optar por substituir o `FailedToRegisterForRemoteNotifications` método para lidar com a falha de registro.

### <a name="add-apns-notifications-to-no-locxamarinforms-ui"></a>Adicionar notificações de APNS à Xamarin.Forms interface do usuário

Quando um dispositivo recebe uma notificação remota, o iOS chama o `ReceivedRemoteNotification` método. A mensagem de entrada JSON é convertida em um `NSDictionary` objeto, e o `ProcessNotification` método extrai valores do dicionário e os envia para a Xamarin.Forms `MainPage` instância. O `ReceivedRemoteNotifications` método é substituído para chamar `ProcessNotification` , conforme mostrado no código a seguir:

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

Os hubs de notificação do Azure permitem que você verifique se o aplicativo pode receber mensagens de teste. A seção de **envio de teste** no Hub de notificação permite que você escolha a plataforma de destino e envie uma mensagem. A configuração da **expressão enviar para marca** para `default` enviará mensagens a aplicativos que registraram um modelo para a `default` marca. Clicar no botão **Enviar** gera um relatório que inclui o número de dispositivos alcançados com a mensagem. A captura de tela a seguir mostra um teste de notificação do Android no portal do Azure:

![Captura de tela de uma mensagem de teste do hub de notificação do Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "Mensagem de teste do hub de notificação do Azure")

### <a name="testing-tips"></a>Dicas de teste

1. Ao testar se um aplicativo pode receber notificações por push, você deve usar um dispositivo físico. Os dispositivos virtuais Android e iOS podem não estar configurados corretamente para receber notificações por push.
1. O aplicativo Android de exemplo registra seu token e modelos uma vez quando o Firebase token é emitido. Durante os testes, talvez seja necessário solicitar um novo token e registrá-lo novamente com o Hub de notificação do Azure. A melhor maneira de forçar isso é limpar o projeto, excluir as `bin` pastas e `obj` e desinstalar o aplicativo do dispositivo antes de recompilar e implantar.
1. Muitas partes do fluxo de notificação por push são executadas de forma assíncrona. Isso pode fazer com que os pontos de interrupção não sejam atingidos ou estejam sendo atingidos em uma ordem inesperada. Use o registro de dispositivo ou de depuração para rastrear a execução sem interromper o fluxo do aplicativo. Filtre o log do dispositivo Android usando o `DebugTag` especificado em `Constants` .
1. Quando a depuração é interrompida no Visual Studio, o aplicativo é forçado a ser fechado. Todos os receptores de mensagens ou outros serviços iniciados como parte do processo de depuração serão fechados e não responderão a eventos de mensagem.

## <a name="create-a-notification-dispatcher"></a>Criar um Dispatcher de notificação

Os hubs de notificação do Azure permitem que seu aplicativo de back-end envie notificações para dispositivos entre plataformas. O exemplo demonstra a expedição de notificação com um aplicativo de console. O aplicativo inclui o `DispatcherConstants.cs` arquivo, que define as seguintes propriedades:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Você deve configurar o `DispatcherConstants.cs` arquivo para corresponder à configuração do hub de notificação do Azure. O valor da `SubscriptionTags` propriedade deve corresponder aos valores usados nos aplicativos cliente. A `NotificationHubName` propriedade é o nome da sua instância do hub de notificação do Azure. A `FullAccessConnectionString` propriedade é a chave de acesso encontrada nas políticas de **acesso**do hub de notificação. A captura de tela a seguir mostra o local das `NotificationHubName` `FullAccessConnectionString` Propriedades e no portal do Azure:

![Captura de tela do nome e FullAccessConnectionString do hub de notificação do Azure](azure-notification-hub-images/notification-hub-full-access-policy.png "Nome e FullAccessConnectionString do hub de notificação do Azure")

O aplicativo de console executa um loop `SubscriptionTags` em cada valor e envia notificações aos assinantes usando uma instância da `NotificationHubClient` classe. O código a seguir mostra a classe de aplicativo de console `Program` :

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