---
title: Xamarin.Forms notificações locais
description: Este artigo explica como enviar, agendar e receber notificações locais no Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/03/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1dad280ee8253d4ef627c5ab7ec9c8dcfa0408a2
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940441"
---
# <a name="local-notifications-in-no-locxamarinforms"></a>Notificações locais em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/local-notifications)

As notificações locais são alertas enviados por aplicativos instalados em um dispositivo móvel. As notificações locais geralmente são usadas para recursos como:

- Eventos do calendário
- Lembretes
- Gatilhos baseados em local

Cada plataforma manipula a criação, a exibição e o consumo de notificações locais de forma diferente. Este artigo explica como criar uma abstração de plataforma cruzada para enviar, agendar e receber notificações locais com o Xamarin.Forms .

[![Aplicativo de notificações locais no iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Criar uma interface de plataforma cruzada

O Xamarin.Forms aplicativo deve criar e consumir notificações sem preocupação com as implementações de plataforma subjacentes. A interface a seguir `INotificationManager` é implementada na biblioteca de código compartilhada e define uma API de plataforma cruzada que o aplicativo pode usar para interagir com as notificações:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;
    void Initialize();
    void SendNotification(string title, string message, DateTime? notifyTime = null);
    void ReceiveNotification(string title, string message);
}
```

Essa interface será implementada em cada projeto de plataforma. O `NotificationReceived` evento permite que o aplicativo manipule notificações de entrada. O `Initialize` método deve executar qualquer lógica de plataforma nativa necessária para preparar o sistema de notificação. O `SendNotification` método deve enviar uma notificação, em um opcional `DateTime` . O `ReceiveNotification` método deve ser chamado pela plataforma subjacente quando uma mensagem é recebida.

## <a name="consume-the-interface-in-no-locxamarinforms"></a>Consuma a interface em Xamarin.Forms

Depois que uma interface é criada, ela pode ser consumida no Xamarin.Forms projeto compartilhado, embora as implementações de plataforma ainda não tenham sido criadas. O aplicativo de exemplo contém um `ContentPage` chamado **MainPage. XAML** com o seguinte conteúdo:

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button below to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnSendClick" />
    <Label Text="Click the button below to schedule a local notification for in 10 seconds time."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick" />
</StackLayout>
```

O layout contém `Label` elementos que explicam instruções e `Button` elementos que enviam ou agendam uma notificação quando tocadas.

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

    void OnSendClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message);
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message, DateTime.Now.AddSeconds(10));
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

O `MainPage` Construtor de classe usa o Xamarin.Forms `DependencyService` para recuperar uma instância específica da plataforma do `INotificationManager` . Os `OnSendClick` `OnScheduleClicked` métodos e usam a `INotificationManager` instância para enviar e agendar novas notificações. O `ShowNotification` método é chamado a partir do manipulador de eventos anexado ao `NotificationReceived` evento e irá inserir um novo `Label` na página quando o evento for invocado.

O `NotificationReceived` manipulador de eventos converte seus argumentos de evento para `NotificationEventArgs` . Esse tipo é definido no projeto compartilhado Xamarin.Forms :

```csharp
public class NotificationEventArgs : EventArgs
{
    public string Title { get; set; }
    public string Message { get; set; }
}
```

Para obter mais informações sobre o Xamarin.Forms `DependencyService` , consulte [ Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Criar a implementação da interface do Android

Para que o Xamarin.Forms aplicativo envie e receba notificações no Android, o aplicativo deve fornecer uma implementação da `INotificationManager` interface.

### <a name="create-the-androidnotificationmanager-class"></a>Criar a classe AndroidNotificationManager

A `AndroidNotificationManager` classe implementa a `INotificationManager` interface:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Graphics;
using Android.OS;
using AndroidX.Core.App;
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

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = 0;
        int pendingIntentId = 0;

        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public static AndroidNotificationManager Instance { get; private set; }

        public void Initialize()
        {
            CreateNotificationChannel();
            Instance = this;
        }

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            if (notifyTime != null)
            {
                Intent intent = new Intent(AndroidApp.Context, typeof(AlarmHandler));
                intent.PutExtra(TitleKey, title);
                intent.PutExtra(MessageKey, message);

                PendingIntent pendingIntent = PendingIntent.GetBroadcast(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.CancelCurrent);
                long triggerTime = GetNotifyTime(notifyTime.Value);
                AlarmManager alarmManager = AndroidApp.Context.GetSystemService(Context.AlarmService) as AlarmManager;
                alarmManager.Set(AlarmType.RtcWakeup, triggerTime, pendingIntent);
            }
            else
            {
                Show(title, message);
            }
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

        public void Show(string title, string message)
        {
            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.UpdateCurrent);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            Notification notification = builder.Build();
            manager.Notify(messageId++, notification);
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

        long GetNotifyTime(DateTime notifyTime)
        {
            DateTime utcTime = TimeZoneInfo.ConvertTimeToUtc(notifyTime);
            double epochDiff = (new DateTime(1970, 1, 1) - DateTime.MinValue).TotalSeconds;
            long utcAlarmTime = utcTime.AddSeconds(-epochDiff).Ticks / 10000;
            return utcAlarmTime; // milliseconds
        }
    }
}
```

O `assembly` atributo acima do namespace registra a `INotificationManager` implementação da interface com o `DependencyService` .

O Android permite que os aplicativos definam vários canais para notificações. O `Initialize` método cria um canal básico que o aplicativo de exemplo usa para enviar notificações. O `SendNotification` método define a lógica específica da plataforma necessária para criar e enviar uma notificação. O `ReceiveNotification` método é chamado pelo sistema operacional Android quando uma mensagem é recebida e invoca o manipulador de eventos.

O `SendNotification` método cria uma notificação local imediatamente ou em um exato `DateTime` . Uma notificação pode ser agendada para um `DateTime` uso exato da `AlarmManager` classe, e a notificação será recebida por um objeto derivado da `BroadcastReceiver` classe:

```csharp
[BroadcastReceiver(Enabled = true, Label = "Local Notifications Broadcast Receiver")]
public class AlarmHandler : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        if (intent?.Extras != null)
        {
            string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
            string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);

            AndroidNotificationManager.Instance.Show(title, message);
        }
    }
}
```

> [!IMPORTANT]
> Por padrão, as notificações agendadas usando a `AlarmManager` classe não irão sobreviver à reinicialização do dispositivo. No entanto, você pode criar seu aplicativo para reagendar automaticamente as notificações se o dispositivo for reiniciado. Para obter mais informações, consulte [Iniciar um alarme quando o dispositivo for reiniciado](https://developer.android.com/training/scheduling/alarms#boot) em [agendar alarmes repetitivos](https://developer.android.com/training/scheduling/alarms) em developer.Android.com. Para obter informações sobre o processamento em segundo plano no Android, consulte [guia para processamento em segundo plano](https://developer.android.com/guide/background) em developer.Android.com.

Para obter mais informações sobre receptores de transmissão, consulte [receptores de transmissão no Xamarin. Android](~/android/app-fundamentals/broadcast-receivers.md).

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
        string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
        string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);
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
using System;
using Foundation;
using UserNotifications;
using Xamarin.Forms;

[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = 0;
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

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            // EARLY OUT: app doesn't have permissions
            if (!hasNotificationsPermission)
            {
                return;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };            

            UNNotificationTrigger trigger;
            if (notifyTime != null)
            {
                // Create a calendar-based trigger.
                trigger = UNCalendarNotificationTrigger.CreateTrigger(GetNSDateComponents(notifyTime.Value), false);
            }
            else
            {
                // Create a time-based trigger, interval is in seconds and must be greater than 0.
                trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);
            }                      

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });
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

        NSDateComponents GetNSDateComponents(DateTime dateTime)
        {
            return new NSDateComponents
            {
                Month = dateTime.Month,
                Day = dateTime.Day,
                Year = dateTime.Year,
                Hour = dateTime.Hour,
                Minute = dateTime.Minute,
                Second = dateTime.Second
            };
        }
    }
}
```

O `assembly` atributo acima do namespace registra a `INotificationManager` implementação da interface com o `DependencyService` .

No iOS, você deve solicitar permissão para usar notificações antes de tentar agendar uma notificação. O `Initialize` método solicita autorização para usar notificações locais. O `SendNotification` método define a lógica necessária para criar e enviar uma notificação. O `ReceiveNotification` método será chamado pelo Ios quando uma mensagem for recebida e invocará o manipulador de eventos.

> [!NOTE]
> O `SendNotification` método cria uma notificação local imediatamente, usando um `UNTimeIntervalNotificationTrigger` objeto ou, em um exato, `DateTime` usando um `UNCalendarNotificationTrigger` objeto.

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

Depois que os projetos de plataforma contiverem uma implementação registrada da `INotificationManager` interface, o aplicativo poderá ser testado em ambas as plataformas. Execute o aplicativo e clique em um dos botões **criar notificação** para criar uma notificação.

No Android, as notificações serão exibidas na área de notificação. Quando a notificação é tocada, o aplicativo recebe a notificação e exibe uma mensagem:

![Notificações locais no Android](local-notifications-images/local-notifications-android.png)

No iOS, as notificações de entrada são automaticamente recebidas pelo aplicativo sem a necessidade de entrada do usuário. O aplicativo recebe a notificação e exibe uma mensagem:

![Notificações locais no iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Links relacionados

- [Projeto de exemplo](/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notificações no Xamarin. Android](~/android/app-fundamentals/notifications/index.md)
- [Receptores de difusão no Xamarin. Android](~/android/app-fundamentals/broadcast-receivers.md)
- [Notificações no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
