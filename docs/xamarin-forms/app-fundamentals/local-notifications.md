---
title: Xamarin.Formsnotificações locais
description: Este artigo explica como enviar e receber notificações locais no Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 40e040f216ddda40931273f4e7f5614964862fe8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137586"
---
# <a name="local-notifications-in-xamarinforms"></a>Notificações locais emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

As notificações locais são alertas enviados por aplicativos instalados em um dispositivo móvel. As notificações locais geralmente são usadas para recursos como:

- Eventos do calendário
- Lembretes
- Gatilhos baseados em local

Cada plataforma manipula a criação, a exibição e o consumo de notificações locais de forma diferente. Este artigo explica como criar uma abstração de plataforma cruzada para enviar e receber notificações locais com o Xamarin.Forms .

[![Aplicativo de notificações locais no iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Criar uma interface de plataforma cruzada

O Xamarin.Forms aplicativo deve criar e consumir notificações sem preocupação com as implementações de plataforma subjacentes. A interface a seguir `INotificationManager` é implementada na biblioteca de código compartilhada e define uma API de plataforma cruzada que o aplicativo pode usar para interagir com as notificações:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Essa interface será implementada em cada projeto de plataforma. O `NotificationReceived` evento permite que o aplicativo manipule notificações de entrada. O `Initialize` método deve executar qualquer lógica de plataforma nativa necessária para preparar o sistema de notificação. O `ScheduleNotification` método deve enviar uma notificação. O `ReceiveNotification` método deve ser chamado pela plataforma subjacente quando uma mensagem é recebida.

## <a name="consume-the-interface-in-xamarinforms"></a>Consuma a interface emXamarin.Forms

Depois que uma interface é criada, ela pode ser consumida no Xamarin.Forms projeto compartilhado, embora as implementações de plataforma ainda não tenham sido criadas. O aplicativo de exemplo contém um `ContentPage` chamado **MainPage. XAML** com o seguinte conteúdo:

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

O layout contém um `Label` elemento com instruções para o usuário e um `Button` que deve agendar uma notificação quando tocado.

A `MainPage` classe code-behind lida com o envio e recebimento de notificações:

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

O `MainPage` Construtor de classe usa o Xamarin.Forms `DependencyService` para recuperar uma instância específica da plataforma do `INotificationManager` . O `OnScheduleClicked` método usa a `INotificationManager` instância para agendar uma nova notificação. O `ShowNotification` método é chamado a partir do manipulador de eventos anexado ao `NotificationReceived` evento e irá inserir um novo `Label` na página quando o evento for invocado.

Para obter mais informações sobre o Xamarin.Forms `DependencyService` , consulte [ Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Criar a implementação da interface do Android

Para que o Xamarin.Forms aplicativo envie e receba notificações no Android, o aplicativo deve fornecer uma implementação da `INotificationManager` interface.

### <a name="create-the-androidnotificationmanager-class"></a>Criar a classe AndroidNotificationManager

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

O `assembly` atributo acima do namespace registra a `INotificationManager` implementação da interface com o `DependencyService` .

O Android permite que os aplicativos definam vários canais para notificações. O `Initialize` método cria um canal básico que o aplicativo de exemplo usa para enviar notificações. O `ScheduleNotification` método define a lógica específica da plataforma necessária para criar e enviar uma notificação. Por fim, o `ReceiveNotification` método é chamado pelo sistema operacional Android quando uma mensagem é recebida e invoca o manipulador de eventos.

> [!NOTE]
> A `Application` classe é definida nos `Xamarin.Forms` `Android.App` namespaces e, portanto, o `AndroidApp` alias é definido nas `using` instruções para diferenciar os dois.

### <a name="handle-incoming-notifications-on-android"></a>Manipular notificações de entrada no Android

A `MainActivity` classe deve detectar notificações de entrada e notificar a `AndroidNotificationManager` instância. O `Activity` atributo na `MainActivity` classe deve especificar um `LaunchMode` valor de `LaunchMode.SingleTop` :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

O `SingleTop` modo impede que várias instâncias de um sejam `Activity` iniciadas enquanto o aplicativo está em primeiro plano. Isso `LaunchMode` pode não ser apropriado para aplicativos que iniciam várias atividades em cenários de notificação mais complexos. Para obter mais informações sobre `LaunchMode` valores de enumeração, consulte [inicialização de atividade do Android](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Na `MainActivity` classe é modificado para receber notificações de entrada:

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

O `CreateNotificationFromIntent` método extrai dados de notificação do `intent` argumento e fornece-os para o `AndroidNotificationManager` usando o `ReceiveNotification` método. O `CreateNotificationFromIntent` método é chamado tanto do `OnCreate` método quanto do `OnNewIntent` método:

- Quando o aplicativo for iniciado pelos dados de notificação, os `Intent` dados serão passados para o `OnCreate` método.
- Se o aplicativo já estiver em primeiro plano, os `Intent` dados serão passados para o `OnNewIntent` método.

O Android oferece muitas opções avançadas para notificações. Para obter mais informações, consulte [notificações no Xamarin. Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Criar a implementação da interface do iOS

Para que o Xamarin.Forms aplicativo envie e receba notificações no Ios, o aplicativo deve fornecer uma implementação do `INotificationManager` .

### <a name="create-the-iosnotificationmanager-class"></a>Criar a classe iOSNotificationManager

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

O `assembly` atributo acima do namespace registra a `INotificationManager` implementação da interface com o `DependencyService` .

No iOS, você deve solicitar permissão para usar notificações antes de tentar agendar uma notificação. O `Initialize` método solicita autorização para usar notificações locais. O `ScheduleNotification` método define a lógica necessária para criar e enviar uma notificação. Por fim, o `ReceiveNotification` método será chamado pelo Ios quando uma mensagem for recebida e invocará o manipulador de eventos.

### <a name="handle-incoming-notifications-on-ios"></a>Manipular notificações de entrada no iOS

No iOS, você deve criar um delegado que subclasses `UNUserNotificationCenterDelegate` para tratar as mensagens de entrada. O aplicativo de exemplo define uma `iOSNotificationReceiver` classe:

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

Essa classe usa o `DependencyService` para obter uma instância da `iOSNotificationManager` classe e fornece dados de notificação de entrada para o `ReceiveNotification` método.

A `AppDelegate` classe deve especificar o delegado personalizado durante a inicialização do aplicativo. A `AppDelegate` classe deve especificar um `iOSNotificationReceiver` objeto como o `UNUserNotificationCenter` delegado durante a inicialização do aplicativo. Isso ocorre no `FinishedLaunching` método:

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

Depois que os projetos de plataforma contiverem uma implementação registrada da `INotificationManager` interface, o aplicativo poderá ser testado em ambas as plataformas. Execute o aplicativo e clique no botão **agendar notificação** para criar uma notificação.

No Android, a notificação aparecerá na área de notificação. Quando a notificação é tocada, o aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **agendar notificação** :

![Notificações locais no Android](local-notifications-images/local-notifications-android.png)

No iOS, as notificações de entrada são automaticamente recebidas pelo aplicativo sem a necessidade de entrada do usuário. O aplicativo recebe a notificação e exibe uma mensagem abaixo do botão **agendar notificação** :

![Notificações locais no iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Links relacionados

- [Projeto de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notificações no Xamarin. Android](~/android/app-fundamentals/notifications/index.md)
- [Notificações no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.FormsDependência. serviço](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
