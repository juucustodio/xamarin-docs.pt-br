---
title: 'Passo a passo: usar notificações locais no xamarin. Android'
description: Este passo a passo demonstra como usar notificações locais em aplicativos xamarin. Android. Ele demonstra as Noções básicas de criação e publicação de uma notificação de local. Quando o usuário clicar na notificação na área de notificação, ele inicia uma segunda atividade.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: e60ed6cc49921fc7b6e8e2616a6b0bf6f8abb401
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670084"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Passo a passo: usar notificações locais no xamarin. Android

_Este passo a passo demonstra como usar notificações locais em aplicativos xamarin. Android. Ele demonstra as Noções básicas de criação e publicação de uma notificação de local. Quando o usuário clicar na notificação na área de notificação, ele inicia uma segunda atividade._


## <a name="overview"></a>Visão geral

Neste passo a passo, criaremos um aplicativo do Android que gera uma notificação quando o usuário clica em um botão em uma atividade. Quando o usuário clicar na notificação, ele abre uma segunda atividade que exibe o número de vezes que o usuário tivesse clicado no botão na primeira atividade.

As capturas de tela a seguir ilustram alguns exemplos desse aplicativo:

[![Capturas de tela de exemplo com notificação](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Este guia enfoca as [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) da [biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essas APIs garantirá máximo com versões anteriores a compatibilidade para o Android 4.0 (API nível 14).

## <a name="creating-the-project"></a>Criando o projeto

Para começar, vamos criar um novo projeto Android usando o **aplicativo Android** modelo. Vamos chamar esse projeto **LocalNotifications**. (Se você não estiver familiarizado com a criação de projetos do xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Editar o arquivo de recurso **values/Strings.xml** para que ele contém dois recursos de cadeia de caracteres extras que serão usados quando é hora de criar o canal de notificação:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Adicione o pacote NuGet Android.Support.V4

Neste passo a passo, estamos usando `NotificationCompat.Builder` para criar nossa notificação local. Conforme explicado em [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md), podemos deve incluir o [biblioteca de suporte ao Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet em nosso projeto usar `NotificationCompat.Builder`.

Em seguida, vamos editar **MainActivity.cs** e adicione o seguinte `using` instrução para que os tipos no `Android.Support.V4.App` estão disponíveis para o nosso código:

```csharp
using Android.Support.V4.App;
```

Além disso, podemos deve deixar claro para o compilador que estamos usando o `Android.Support.V4.App` verzi `TaskStackBuilder` em vez de `Android.App` versão. Adicione o seguinte `using` instrução para resolver qualquer ambiguidade:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Criar o canal de notificação

Em seguida, adicione um método para `MainActivity` que criará um canal de notificação (se necessário):

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

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Atualização de `OnCreate` método para chamar esse novo método:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Definir a ID de notificação

Precisaremos uma ID exclusiva para nossa notificação e o canal de notificação. Vamos editar **MainActivity.cs** e adicione a seguinte variável de instância estática para o `MainActivity` classe:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Adicione código para gerar a notificação

Em seguida, precisamos criar um novo manipulador de eventos para o botão `Click` eventos. Adicione o seguinte método à `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

O `OnCreate` método de MainActivity deve fazer a chamada para criar o canal de notificação e atribuir os `ButtonOnClick` método para o `Click` evento do botão (substitua o manipulador de eventos do delegado fornecido pelo modelo):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Crie uma segunda atividade

Agora, precisamos criar uma outra atividade Android será exibido quando o usuário clica em nossa notificação. Adicione outra atividade Android ao seu projeto chamado **SecondActivity**. Abra **SecondActivity.cs** e substitua seu conteúdo com este código:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

Podemos também deve criar um layout de recurso para **SecondActivity**. Adicione um novo **Layout do Android** ao seu projeto chamado **Second.axml**. Edite **Second.axml** e cole no código de layout a seguir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Adicionar um ícone de notificação

Por fim, adicione um ícone pequeno que será exibido na área de notificação quando a notificação é iniciada. Você pode copiar [esse ícone](local-notifications-walkthrough-images/ic-stat-button-click.png) ao seu projeto ou criar seu próprio ícone personalizado. Nomeie o arquivo de ícone **ic\_stat\_botão\_click.png** e copie-o para o **recursos/drawable** pasta. Lembre-se de usar **Adicionar > Item existente...**  para incluir esse arquivo de ícone em seu projeto.


### <a name="run-the-application"></a>Executar o aplicativo

Crie e execute o aplicativo. Você deve ser apresentado com a primeira atividade, semelhante à seguinte captura de tela:

[![Primeira captura de tela de atividade](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Ao clicar no botão, você deve observar que o ícone pequeno para a notificação é exibida na área de notificação:

[![Ícone de notificação é exibida](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se você passa o dedo para baixo e expor a gaveta de notificação, você deve ver a notificação:

[![Mensagem de notificação](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando você clicar na notificação, ele desaparecerá e nossa outra atividade deve ser iniciada &ndash; procurando um pouco como captura de tela a seguir:

[![Segunda captura de tela de atividade](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Parabéns! Neste ponto, você concluiu o passo a passo do Android notificação local e você tem um exemplo funcional que você pode consultar. Há muito mais para notificações que mostramos aqui, portanto, se você quiser obter mais informações, dar uma olhada [documentação do Google sobre notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="summary"></a>Resumo

Este passo a passo usada `NotificationCompat.Builder` para criar e exibir as notificações. Ele mostrou um exemplo básico de como iniciar uma segunda atividade como uma maneira de responder à interação do usuário com a notificação, e ele demonstrou a transferência de dados da primeira atividade para a segunda atividade.


## <a name="related-links"></a>Links relacionados

- [LocalNotifications (amostra)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Canais de notificação do Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notificação](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
