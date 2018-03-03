---
title: "Enviar notificações por Push de aplicativos móveis do Azure"
description: "Hubs de notificação do Azure oferecem uma infraestrutura de push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, eliminando a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente. Este artigo explica como usar Hubs de notificação do Azure para enviar notificações por push de uma instância dos aplicativos móveis do Azure para um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: cccbe64f69b926ced77403bcf85540ef1060dbac
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>Enviar notificações por Push de aplicativos móveis do Azure

_Hubs de notificação do Azure oferecem uma infraestrutura de push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, eliminando a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente. Este artigo explica como usar Hubs de notificação do Azure para enviar notificações por push de uma instância dos aplicativos móveis do Azure para um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

Uma notificação por push é usada para fornecer informações, como uma mensagem de um sistema de back-end a um aplicativo em um dispositivo móvel para aumentar o uso e o compromisso do aplicativo. A notificação pode ser enviada em qualquer momento, mesmo quando o usuário não está usando ativamente o aplicativo de destino.

Sistemas de back-end enviar notificações por push para dispositivos móveis por meio de sistemas de notificação de plataforma (PNS), conforme mostrado no diagrama a seguir:

[![](azure-images/pns.png "Sistemas de notificação de plataforma")](azure-images/pns-large.png "sistemas de notificação de plataforma")

Para enviar uma notificação por push, o sistema de back-end entra em contato com o PNS específico da plataforma para enviar uma notificação para uma instância do aplicativo cliente. Isso aumenta significativamente a complexidade de back-end quando as notificações por push da plataforma cruzada são necessárias, porque o back-end deve usar cada API PNS e um protocolo específico de plataforma.

Hubs de notificação do Azure eliminar essa complexidade, abstraindo os detalhes dos sistemas de notificação de plataforma diferente, permitindo que uma notificação de plataforma cruzada para ser enviada com uma única chamada de API, conforme mostrado no diagrama a seguir:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png)

Para enviar uma notificação por push, os back-end sistema apenas os contatos do Azure Hub de notificação, que por sua vez, se comunica com os sistemas de notificação de plataforma diferente, diminuindo, portanto, a complexidade de back-end que as notificações por push de envios de código.

Aplicativos móveis do Azure tem suporte interno para notificações por push usando os hubs de notificação. O processo para enviar uma notificação por push de uma instância dos aplicativos móveis do Azure para um aplicativo xamarin. Forms é da seguinte maneira:

1. O aplicativo xamarin. Forms registra com o PNS, que retorna um identificador.
1. A instância os aplicativos móveis do Azure envia uma notificação ao seu Hub de notificação do Azure, especifica o identificador do dispositivo a ser direcionada.
1. O Hub de notificação do Azure envia a notificação ao PNS apropriado para o dispositivo.
1. O PNS envia a notificação para o dispositivo especificado.
1. O aplicativo xamarin. Forms processa a notificação e o exibe.

O aplicativo de exemplo demonstra um aplicativo de lista de tarefas cujos dados são armazenados em uma instância dos aplicativos móveis do Azure. Sempre que um novo item é adicionado à instância os aplicativos móveis do Azure, uma notificação por push é enviada para o aplicativo xamarin. Forms. As capturas de tela a seguir mostram a cada plataforma exibindo a notificação por push recebido:

[![](azure-images/screenshots.png "Exemplo de aplicativo para receber uma notificação por Push")](azure-images/screenshots-large.png "receber uma notificação por Push do aplicativo de exemplo")

Para obter mais informações sobre Hubs de notificação do Azure, consulte [Hubs de notificação do Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) e [adicionar notificações de push para seu aplicativo xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure e configuração do sistema de notificação de plataforma

O processo para integrar um Hub de notificação do Azure em uma instância de aplicativos do Azure Mobile é o seguinte:

1. Crie uma instância dos aplicativos móveis do Azure. Para obter mais informações, consulte [consumindo um aplicativo do Azure Mobile](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Configure um hub de notificação. Para obter mais informações, consulte [configurar um hub de notificação](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub).
1. Atualize a instância do Azure os aplicativos móveis para enviar notificações por push. Para obter mais informações, consulte [atualizar o projeto do servidor para enviar notificações por push](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Registrar com cada PNS.
1. Configure o hub de notificação para se comunicar com cada PNS.

As seções a seguir fornecem instruções de configuração adicional para cada plataforma.

### <a name="ios"></a>iOS

As seguintes etapas adicionais devem ser realizadas para usar APNS Apple Push Notification Service () de um Hub de notificação do Azure:

1. Gere uma solicitação para o certificado de push com a ferramenta de acesso do conjunto de chaves de assinatura de certificado. Para obter mais informações, consulte [gerar o arquivo de solicitação de assinatura de certificado para o certificado de push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) no Centro de documentação do Azure.
1. Registre o aplicativo xamarin. Forms para suporte de notificação por push no centro do desenvolvedor da Apple. Para obter mais informações, consulte [registrar seu aplicativo para notificações por push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) no Centro de documentação do Azure.
1. Crie um push notificações habilitado perfil de provisionamento para o aplicativo xamarin. Forms no centro do desenvolvedor da Apple. Para obter mais informações, consulte [criar um perfil de provisionamento para o aplicativo](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) no Centro de documentação do Azure.
1. Configure o hub de notificação para se comunicar com APNS. Para obter mais informações, consulte [configurar hub de notificação de APNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Configure o aplicativo xamarin. Forms para usar a nova ID de aplicativo e o perfil de provisionamento. Para obter mais informações, consulte [Configurando o projeto do iOS no Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) ou [Configurando o projeto do iOS no Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) no Centro de documentação do Azure.

### <a name="android"></a>Android

As seguintes etapas adicionais devem ser realizadas para usar Firebase nuvem mensagens (FCM) de um Hub de notificação do Azure:

1. Registrar para FCM. Uma chave de API de servidor e uma ID de cliente são automaticamente gerados e compactadas em um `google-services.json` arquivo baixado. Para obter mais informações, consulte [habilitar Firebase nuvem mensagens (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Configure o hub de notificação para se comunicar com FCM. Para obter mais informações, consulte [configurar os aplicativos móveis volta terminar para enviar solicitações de envio usando FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

As seguintes etapas adicionais devem ser realizadas para usar o serviço de notificação de Windows (WNS) de um Hub de notificação do Azure:

1. Registre-se para o serviço de notificação do Windows (WNS). Para obter mais informações, consulte [registrar seu aplicativo do Windows para notificações por push com WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) no Centro de documentação do Azure.
1. Configure o hub de notificação para se comunicar com WNS. Para obter mais informações, consulte [configurar hub de notificação para o WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) no Centro de documentação do Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Adicionando suporte a notificação por Push para o aplicativo xamarin. Forms

As seções a seguir discutem a implementação necessária em cada projeto específico da plataforma para oferecer suporte a notificações por push.

### <a name="ios"></a>iOS

O processo de implementação de suporte de notificação por push em um aplicativo do iOS é o seguinte:

1. Registrar com o Apple APNS Push Notification Service () no `AppDelegate.FinishedLaunching` método. Para obter mais informações, consulte [registrar com o sistema de notificação por Push da Apple](#ios_register).
1. Implementar o `AppDelegate.RegisteredForRemoteNotifications` método para controlar a resposta de registro. Para obter mais informações, consulte [manipulando a resposta de registro](#ios_registration_response).
1. Implementar o `AppDelegate.DidReceiveRemoteNotification` método para processar notificações de push de entrada. Para obter mais informações, consulte [processamento de notificações por Push entrada](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>Registrar com o serviço de notificação por Push da Apple

Antes que um aplicativo iOS pode receber notificações por push, deve ser registrado com o Apple Push serviços de notificação (APNS), que irá gerar um token de dispositivo exclusivo e retorná-la para o aplicativo. Registro é chamado de `FinishedLaunching` substituir no `AppDelegate` classe:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

Quando um aplicativo do iOS registra com APNS deve especificar os tipos de notificações por push gostaria de receber. O `RegisterUserNotificationSettings` método registra os tipos de notificações que o aplicativo pode receber, com o `RegisterForRemoteNotifications` método registrar para receber notificações por push de APNS.

> [!NOTE]
> **Observação**: Falha ao chamar o `RegisterUserNotificationSettings` método resultará em notificações por push silenciosamente sendo recebidas pelo aplicativo.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>Manipulando a resposta de registro

A solicitação de registro APNS ocorre em segundo plano. Quando a resposta é recebida, o iOS chamará o `RegisteredForRemoteNotifications` substituir no `AppDelegate` classe:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

Esse método cria um modelo de mensagem de notificação simples como JSON e registra o dispositivo para receber notificações de modelo do hub de notificação.

> [!NOTE]
> **Observação**: O `FailedToRegisterForRemoteNotifications` substituição deve ser implementada para lidar com situações como nenhuma conexão de rede. Isso é importante porque os usuários podem iniciar o aplicativo ao offline.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Processando notificações de Push de entrada

O `DidReceiveRemoteNotification` substituir no `AppDelegate` classe é usada para processar as notificações de envio de entrada quando o aplicativo está em execução e é invocado quando a notificação é recebida:

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

O `userInfo` dicionário contém o `aps` chave, cujo valor é o `alert` dicionário com os dados restantes de notificação. Esse dicionário é recuperado, com o `string` que está sendo exibida na caixa de diálogo de mensagem de notificação.

> [!NOTE]
> **Observação**: se um aplicativo não está em execução quando chega uma notificação por push, o aplicativo será iniciado, mas o `DidReceiveRemoteNotification` método não processa a notificação. Em vez disso, obtenha a carga de notificação e responder adequadamente a partir de `WillFinishLaunching` ou `FinishedLaunching` substituições.

Para obter mais informações sobre APNS, consulte [notificações por Push no iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

O processo de implementação de suporte de notificação por push em um aplicativo do Android é o seguinte:

1. Adicionar o [Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet do pacote para o projeto Android e defina a versão de destino do aplicativo para Android 7.0 ou superior.
1. Adicionar o `google-services.json` arquivo baixado do console Firebase, para a raiz do projeto Android e defina a ação de compilação como **GoogleServicesJson**. Para obter mais informações, consulte [adicionar o arquivo de JSON de serviços do Google](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Registrar com o sistema de mensagens de nuvem com a Firebase (FCM) declarando um destinatário no manifesto do Android do arquivo e por implementar o `FirebaseRegistrationService.OnTokenRefresh` método. Para obter mais informações, consulte [registrando mensagens de nuvem Firebase](#android_register_fcm).
1. Registrar com o Hub de notificação do Azure no `AzureNotificationHubService.RegisterAsync` método. Para obter mais informações, consulte [registrar com o Hub de notificação do Azure](#android_register_azure).
1. Implementar o `FirebaseNotificationService.OnMessageReceived` método para processar notificações de push de entrada. Para obter mais informações, consulte [exibindo o conteúdo de uma notificação por Push](#android_displaying_notification).

Para obter mais informações sobre as mensagens de nuvem Firebase, consulte [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) e [remoto notificações com mensagens de nuvem Firebase](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrando Firebase mensagens de nuvem

Antes que um aplicativo do Android pode receber notificações por push, deve ser registrado com FCM, que irá gerar um token de registro e retorná-la para o aplicativo. Para obter mais informações sobre tokens de registro, consulte [registro com FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

Isso é feito por:

- Declarando um destinatário no manifesto do Android. Para obter mais informações, consulte [declarando o receptor no manifesto do Android](#declaring_a_receiver).
- Implementando o serviço de ID de instância Firebase. Para obter mais informações, consulte [implementar o serviço de ID de instância Firebase](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Declarando o receptor no manifesto do Android

Editar **AndroidManifest.xml** e insira o seguinte `<receiver>` elementos para o `<application>` elemento:

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

Esse XML executa as seguintes operações:

- Declara um interno `FirebaseInstanceIdInternalReceiver` implementação que é usada para iniciar os serviços com segurança.
- Declara um `FirebaseInstanceIdReceiver` implementação que fornece um identificador exclusivo para cada instância do aplicativo. Esse destinatário também autentica e autoriza ações.

O `FirebaseInstanceIdReceiver` recebe `FirebaseInstanceId` e `FirebaseMessaging` eventos e entrega para a classe que é derivada de `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Implementando o serviço de ID de instância Firebase

Registrando o aplicativo FCM é obtida derivando uma classe a partir de `FirebaseInstanceIdService` classe. Essa classe é responsável por gerar tokens de segurança que autorizam o aplicativo cliente acessem FCM. No aplicativo de amostra a `FirebaseRegistrationService` classe deriva de `FirebaseInstanceIdService` classe e é mostrado no exemplo de código a seguir:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

O `OnTokenRefresh` método é invocado quando o aplicativo recebe um token de registro de FCM. O método retorna o token do `FirebaseInstanceId.Instance.Token` propriedade, que é atualizada de forma assíncrona por FCM. O `OnTokenRefresh` método é invocado com pouca frequência, porque o token é atualizado somente quando o aplicativo é instalado ou desinstalado, quando o usuário exclui dados de aplicativos, quando o aplicativo apaga a ID da instância, ou quando a segurança do token tiver sido comprometida. Além disso, o serviço de ID da instância FCM solicita que o aplicativo atualiza seu token periodicamente, normalmente a cada 6 meses.

O `OnTokenRefresh` método também chama o `SendRegistrationTokenToAzureNotificationHub` método, que é usado para associar um token de registro do usuário com o Hub de notificação do Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Registrando com o Hub de notificação do Azure

O `AzureNotificationHubService` classe fornece a `RegisterAsync` método, que associa um token de registro do usuário com o Hub de notificação do Azure. O seguinte exemplo de código mostra o `RegisterAsync` método, que é invocado com o `FirebaseRegistrationService` classe quando alterações de token de registro do usuário:

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

Esse método cria um modelo de mensagem de notificação simples como JSON e registra para receber notificações de modelo do hub de notificação, usando o token de registro Firebase. Isso garante que as notificações enviadas do Hub de notificação do Azure têm como destino o representada pelo token de registro de dispositivo.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Exibir o conteúdo de uma notificação por Push

Exibir o conteúdo de uma notificação por push é obtido derivando uma classe a partir de `FirebaseMessagingService` classe. Essa classe inclui o substituível `OnMessageReceived` método, que é invocado quando o aplicativo recebe uma notificação de FCM, fornecido se o aplicativo está em execução em primeiro plano. No aplicativo de amostra a `FirebaseNotificationService` classe deriva de `FirebaseMessagingService` classe e é mostrado no exemplo de código a seguir:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

Quando o aplicativo recebe uma notificação de FCM, o `OnMessageReceived` método extrai o conteúdo da mensagem e chama o `SendNotification` método. Este método converte o conteúdo da mensagem em uma notificação de local que é iniciada enquanto o aplicativo está em execução, com a notificação de que aparecem na área de notificação.

##### <a name="handling-notification-intents"></a>Tratamento de tentativas de notificação

Quando um usuário toca uma notificação, quaisquer dados que acompanha a mensagem de notificação são disponibilizados no `Intent` extras. Esses dados podem ser extraídos com o código a seguir:

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

Iniciador do aplicativo `Intent` é acionado quando o usuário toca em sua mensagem de notificação, portanto esse código registrará seus dados no `Intent` a janela de saída.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Antes de um Windows UWP (plataforma Universal) o aplicativo pode receber notificações por push que deve ser registrado com a notificação de serviços Windows (WNS), que retornará um canal de notificação. Registro é invocado com o `InitNotificationsAsync` método o `App` classe:

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

Esse método obtém o canal de notificação por push, cria um modelo de mensagem de notificação como JSON e registra o dispositivo para receber notificações de modelo do hub de notificação.

O `InitNotificationsAsync` método é chamado a partir de `OnLaunched` substituir no `App` classe:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Isso garante que o registro de notificação por push é criado ou atualizado sempre que o aplicativo é iniciado, e, portanto, garantindo que o canal de push WNS sempre está ativo.

Após o recebimento de uma notificação por push ele automaticamente será exibido como um *torrada* – uma janela não restrita que contém a mensagem.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar Hubs de notificação do Azure para enviar notificações por push de uma instância dos aplicativos móveis do Azure para um aplicativo xamarin. Forms. Hubs de notificação do Azure oferecem uma infraestrutura de push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, eliminando a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente.


## <a name="related-links"></a>Links relacionados

- [Consumo de um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Hubs de notificação do Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Adicionar notificações por push para seu aplicativo xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Notificações por push no iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase mensagens de nuvem](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
