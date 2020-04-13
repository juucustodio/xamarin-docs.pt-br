---
title: Xamarin.Forms notificações locais
description: Este artigo explica como enviar e receber notificações locais em Formulários Xamarin..
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72371567"
---
# <a name="local-notifications-in-xamarinforms"></a>Notificações locais em Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Notificações locais são alertas enviados por aplicativos instalados em um dispositivo móvel. Notificações locais são frequentemente usadas para recursos como:

- Eventos do calendário
- Lembretes
- Gatilhos baseados em localização

Cada plataforma lida com a criação, exibição e consumo de notificações locais de forma diferente. Este artigo explica como criar uma abstração multiplataforma para enviar e receber notificações locais com Xamarin.Forms.

[![Aplicativo de notificações locais no iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Crie uma interface multiplataforma

O aplicativo Xamarin.Forms deve criar e consumir notificações sem preocupação com as implementações subjacentes da plataforma. A `INotificationManager` interface a seguir é implementada na biblioteca de código compartilhado e define uma API multiplataforma que o aplicativo pode usar para interagir com notificações:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Essa interface será implementada em cada projeto de plataforma. O `NotificationReceived` evento permite que o aplicativo manuseie notificações recebidas. O `Initialize` método deve executar qualquer lógica de plataforma nativa necessária para preparar o sistema de notificação. O `ScheduleNotification` método deve enviar uma notificação. O `ReceiveNotification` método deve ser chamado pela plataforma subjacente quando uma mensagem é recebida.

## <a name="consume-the-interface-in-xamarinforms"></a>Consumir a interface em Xamarin.Forms

Uma vez que uma interface tenha sido criada, ela pode ser consumida no projeto Xamarin.Forms compartilhado, mesmo que as implementações da plataforma ainda não tenham sido criadas. O aplicativo de `ContentPage` exemplo contém um chamado **MainPage.xaml** com o seguinte conteúdo:

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

O layout `Label` contém um elemento com `Button` instruções para o usuário e um que deve agendar uma notificação quando tocado.

O `MainPage` código de classe atrás lida com o envio e recebimento de notificações:

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

O `MainPage` construtor de classe usa o `DependencyService` Xamarin.Forms para `INotificationManager`recuperar uma instância específica da plataforma do . O `OnScheduleClicked` método `INotificationManager` usa a instância para agendar uma nova notificação. O `ShowNotification` método é chamado do manipulador `NotificationReceived` de eventos anexado `Label` ao evento e inserirá um novo na página quando o evento for invocado.

Para obter mais informações sobre `DependencyService`o Xamarin.Forms, consulte [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Crie a implementação da interface Android

Para que o aplicativo Xamarin.Forms envie e receba notificações no `INotificationManager` Android, o aplicativo deve fornecer uma implementação da interface.

### <a name="create-the-androidnotificationmanager-class"></a>Crie a classe AndroidNotificationManager

A `AndroidNotificationManager` classe implementa a `INotificationManager` interface:

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

O `assembly` atributo acima do `INotificationManager` namespace registra `DependencyService`a implementação da interface com o .

O Android permite que os aplicativos definam vários canais para notificações. O `Initialize` método cria um canal básico que o aplicativo de amostra usa para enviar notificações. O `ScheduleNotification` método define a lógica específica da plataforma necessária para criar e enviar uma notificação. Finalmente, `ReceiveNotification` o método é chamado pelo sistema operacional Android quando uma mensagem é recebida, e invoca o manipulador de eventos.

> [!NOTE]
> A `Application` classe é definida `Xamarin.Forms` `Android.App` tanto nos espaços `AndroidApp` de nome quanto `using` nos espaços de nome para que o alias seja definido nas instruções para diferenciar os dois.

### <a name="handle-incoming-notifications-on-android"></a>Lidar com notificações recebidas no Android

A `MainActivity` classe deve detectar notificações `AndroidNotificationManager` recebidas e notificar a ocorrência. O `Activity` atributo `MainActivity` na classe `LaunchMode` deve `LaunchMode.SingleTop`especificar um valor de:

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

O `SingleTop` modo impede que `Activity` várias instâncias de um seja iniciado enquanto o aplicativo está em primeiro plano. Isso `LaunchMode` pode não ser apropriado para aplicativos que lançam várias atividades em cenários de notificação mais complexos. Para obter `LaunchMode` mais informações sobre valores de enumeração, consulte [Android Activity LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Na `MainActivity` classe é modificado para receber notificações recebidas:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

O `CreateNotificationFromIntent` método extrai dados `intent` de notificação do `AndroidNotificationManager` argumento `ReceiveNotification` e fornece-os ao uso do método. O `CreateNotificationFromIntent` método é chamado `OnCreate` tanto `OnNewIntent` do método quanto do método:

- Quando o aplicativo é iniciado `Intent` pelos dados de `OnCreate` notificação, os dados serão passados para o método.
- Se a aplicação já estiver em `Intent` primeiro plano, os `OnNewIntent` dados serão passados para o método.

O Android oferece muitas opções avançadas para notificações. Para obter mais informações, consulte [Notificações em Xamarin.Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Crie a implementação da interface do iOS

Para que o aplicativo Xamarin.Forms envie e receba notificações no `INotificationManager`iOS, o aplicativo deve fornecer uma implementação do .

### <a name="create-the-iosnotificationmanager-class"></a>Crie a classe iOSNotificationManager

A `iOSNotificationManager` classe implementa a `INotificationManager` interface:

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

O `assembly` atributo acima do `INotificationManager` namespace registra `DependencyService`a implementação da interface com o .

No iOS, você deve solicitar permissão para usar notificações antes de tentar agendar uma notificação. O `Initialize` método solicita autorização para uso de notificações locais. O `ScheduleNotification` método define a lógica necessária para criar e enviar uma notificação. Finalmente, `ReceiveNotification` o método será chamado pelo iOS quando uma mensagem é recebida e invoca o manipulador de eventos.

### <a name="handle-incoming-notifications-on-ios"></a>Lidar com notificações recebidas no iOS

No iOS, você deve criar `UNUserNotificationCenterDelegate` um delegado que subclasses para lidar com mensagens recebidas. O aplicativo de `iOSNotificationReceiver` exemplo define uma classe:

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

Esta classe `DependencyService` usa o para `iOSNotificationManager` obter uma instância da classe `ReceiveNotification` e fornece dados de notificação recebidas para o método.

A `AppDelegate` classe deve especificar o delegado personalizado durante a inicialização do aplicativo. A `AppDelegate` classe deve `iOSNotificationReceiver` especificar `UNUserNotificationCenter` um objeto como o delegado durante a inicialização do aplicativo. Isso ocorre `FinishedLaunching` no método:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

O iOS oferece muitas opções avançadas para notificações. Para obter mais informações, consulte [Notificações em Xamarin.iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Testar o aplicativo

Uma vez que os projetos da `INotificationManager` plataforma contenham uma implementação registrada da interface, o aplicativo pode ser testado em ambas as plataformas. Execute o aplicativo e clique no botão **Notificação de agendamento** para criar uma notificação.

No Android, a notificação aparecerá na área de notificação. Quando a notificação é grampeada, o aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **Notificação de agendamento:**

![Notificações locais no Android](local-notifications-images/local-notifications-android.png)

No iOS, as notificações recebidas são recebidas automaticamente pelo aplicativo sem exigir a entrada do usuário. O aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **Notificação de agendamento:**

![Notificações locais no iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Links relacionados

- [Projeto de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notificações em Xamarin.Android](~/android/app-fundamentals/notifications/index.md)
- [Notificações em Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms Dependency.Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
