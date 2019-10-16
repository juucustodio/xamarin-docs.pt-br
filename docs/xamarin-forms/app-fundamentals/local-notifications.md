---
title: Notificações locais do Xamarin. Forms
description: Este artigo explica como enviar e receber notificações locais no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: 49afc8c6484c465bf5c9e1d64a8c9122a0bcf506
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72371567"
---
# <a name="local-notifications-in-xamarinforms"></a>Notificações locais no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

As notificações locais são alertas enviados por aplicativos instalados em um dispositivo móvel. As notificações locais geralmente são usadas para recursos como:

- Eventos de calendário
- Lembretes
- Gatilhos baseados em local

Cada plataforma manipula a criação, a exibição e o consumo de notificações locais de forma diferente. Este artigo explica como criar uma abstração de plataforma cruzada para enviar e receber notificações locais com o Xamarin. Forms.

[aplicativo de notificações ![Local no iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Criar uma interface de plataforma cruzada

O aplicativo Xamarin. Forms deve criar e consumir notificações sem preocupação com as implementações de plataforma subjacentes. A interface `INotificationManager` a seguir é implementada na biblioteca de código compartilhada e define uma API de plataforma cruzada que o aplicativo pode usar para interagir com as notificações:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Essa interface será implementada em cada projeto de plataforma. O evento `NotificationReceived` permite que o aplicativo manipule notificações de entrada. O método `Initialize` deve executar qualquer lógica de plataforma nativa necessária para preparar o sistema de notificação. O método `ScheduleNotification` deve enviar uma notificação. O método `ReceiveNotification` deve ser chamado pela plataforma subjacente quando uma mensagem é recebida.

## <a name="consume-the-interface-in-xamarinforms"></a>Consumir a interface no Xamarin. Forms

Depois que uma interface é criada, ela pode ser consumida no projeto do Xamarin. Forms compartilhado, embora as implementações de plataforma ainda não tenham sido criadas. O aplicativo de exemplo contém um `ContentPage` chamado **MainPage. XAML** com o seguinte conteúdo:

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

O layout contém um elemento `Label` com instruções para o usuário e um `Button` que deve agendar uma notificação quando tocado.

O code-behind da classe `MainPage` lida com o envio e recebimento de notificações:

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

O construtor da classe `MainPage` usa o Xamarin. Forms `DependencyService` para recuperar uma instância específica da plataforma do `INotificationManager`. O método `OnScheduleClicked` usa a instância `INotificationManager` para agendar uma nova notificação. O método `ShowNotification` é chamado do manipulador de eventos anexado ao evento `NotificationReceived` e inserirá um novo `Label` na página quando o evento for invocado.

Para obter mais informações sobre o Xamarin. Forms `DependencyService`, consulte [xamarin. Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Criar a implementação da interface do Android

Para que o aplicativo Xamarin. Forms envie e receba notificações no Android, o aplicativo deve fornecer uma implementação da interface `INotificationManager`.

### <a name="create-the-androidnotificationmanager-class"></a>Criar a classe AndroidNotificationManager

A classe `AndroidNotificationManager` implementa a interface `INotificationManager`:

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

O atributo `assembly` acima do namespace registra a implementação da interface `INotificationManager` com o `DependencyService`.

O Android permite que os aplicativos definam vários canais para notificações. O método `Initialize` cria um canal básico que o aplicativo de exemplo usa para enviar notificações. O método `ScheduleNotification` define a lógica específica da plataforma necessária para criar e enviar uma notificação. Por fim, o método `ReceiveNotification` é chamado pelo sistema operacional Android quando uma mensagem é recebida e invoca o manipulador de eventos.

> [!NOTE]
> A classe `Application` é definida nos namespaces `Xamarin.Forms` e `Android.App` para que o alias `AndroidApp` seja definido nas instruções `using` para diferenciar os dois.

### <a name="handle-incoming-notifications-on-android"></a>Manipular notificações de entrada no Android

A classe `MainActivity` deve detectar notificações de entrada e notificar a instância de `AndroidNotificationManager`. O atributo `Activity` na classe `MainActivity` deve especificar um valor `LaunchMode` de `LaunchMode.SingleTop`:

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

O modo `SingleTop` impede que várias instâncias de um `Activity` sejam iniciadas enquanto o aplicativo está em primeiro plano. Esse `LaunchMode` pode não ser apropriado para aplicativos que iniciam várias atividades em cenários de notificação mais complexos. Para obter mais informações sobre valores de enumeração `LaunchMode`, consulte [inicialização de atividade do Android](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Na classe `MainActivity` é modificada para receber notificações de entrada:

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

O método `CreateNotificationFromIntent` extrai dados de notificação do argumento `intent` e o fornece ao `AndroidNotificationManager` usando o método `ReceiveNotification`. O método `CreateNotificationFromIntent` é chamado do método `OnCreate` e do método `OnNewIntent`:

- Quando o aplicativo for iniciado pelos dados de notificação, os dados `Intent` serão passados para o método `OnCreate`.
- Se o aplicativo já estiver em primeiro plano, os dados `Intent` serão passados para o método `OnNewIntent`.

O Android oferece muitas opções avançadas para notificações. Para obter mais informações, consulte [notificações no Xamarin. Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Criar a implementação da interface do iOS

Para que o aplicativo Xamarin. Forms envie e receba notificações no iOS, o aplicativo deve fornecer uma implementação do `INotificationManager`.

### <a name="create-the-iosnotificationmanager-class"></a>Criar a classe iOSNotificationManager

A classe `iOSNotificationManager` implementa a interface `INotificationManager`:

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

O atributo `assembly` acima do namespace registra a implementação da interface `INotificationManager` com o `DependencyService`.

No iOS, você deve solicitar permissão para usar notificações antes de tentar agendar uma notificação. O método `Initialize` solicita autorização para usar notificações locais. O método `ScheduleNotification` define a lógica necessária para criar e enviar uma notificação. Por fim, o método `ReceiveNotification` será chamado pelo iOS quando uma mensagem for recebida e invocará o manipulador de eventos.

### <a name="handle-incoming-notifications-on-ios"></a>Manipular notificações de entrada no iOS

No iOS, você deve criar um delegado que subclasses `UNUserNotificationCenterDelegate` para tratar as mensagens de entrada. O aplicativo de exemplo define uma classe `iOSNotificationReceiver`:

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

Essa classe usa o `DependencyService` para obter uma instância da classe `iOSNotificationManager` e fornece dados de notificação de entrada para o método `ReceiveNotification`.

A classe `AppDelegate` deve especificar o delegado personalizado durante a inicialização do aplicativo. A classe `AppDelegate` deve especificar um objeto `iOSNotificationReceiver` como o delegado `UNUserNotificationCenter` durante a inicialização do aplicativo. Isso ocorre no método `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

o iOS oferece muitas opções avançadas para notificações. Para obter mais informações, consulte [notificações no Xamarin. Ios](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Testar o aplicativo

Depois que os projetos de plataforma contiverem uma implementação registrada da interface `INotificationManager`, o aplicativo poderá ser testado em ambas as plataformas. Execute o aplicativo e clique no botão **agendar notificação** para criar uma notificação.

No Android, a notificação aparecerá na área de notificação. Quando a notificação é tocada, o aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **agendar notificação** :

![Notificações locais no Android](local-notifications-images/local-notifications-android.png)

No iOS, as notificações de entrada são automaticamente recebidas pelo aplicativo sem a necessidade de entrada do usuário. O aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **agendar notificação** :

![Notificações locais no iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Links relacionados

- [Projeto de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notificações no Xamarin. Android](~/android/app-fundamentals/notifications/index.md)
- [Notificações no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Dependência do Xamarin. Forms. Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
