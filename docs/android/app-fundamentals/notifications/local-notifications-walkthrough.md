---
title: Walkthrough – usando notificações locais no Xamarin. Android
description: Este tutorial demonstra como usar notificações locais em aplicativos Xamarin. Android. Ele demonstra as noções básicas de criação e publicação de uma notificação local. Quando o usuário clica na notificação na área de notificação, ele inicia uma segunda atividade.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 6d48d650b0900e71b7d3d4d5e1ff1ac919dcb948
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025546"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Walkthrough – usando notificações locais no Xamarin. Android

_Este tutorial demonstra como usar notificações locais em aplicativos Xamarin. Android. Ele demonstra as noções básicas de criação e publicação de uma notificação local. Quando o usuário clica na notificação na área de notificação, ele inicia uma segunda atividade._

## <a name="overview"></a>Visão Geral

Neste tutorial, criaremos um aplicativo Android que gera uma notificação quando o usuário clica em um botão em uma atividade. Quando o usuário clica na notificação, ele inicia uma segunda atividade que exibe o número de vezes que o usuário clicou no botão na primeira atividade.

As capturas de tela a seguir ilustram alguns exemplos desse aplicativo:

[![capturas de tela de exemplo com notificação](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Este guia concentra-se nas [APIs do NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) da [biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essas APIs garantirão a compatibilidade máxima com versões anteriores do Android 4,0 (nível de API 14).

## <a name="creating-the-project"></a>Criando o projeto

Para começar, vamos criar um novo projeto do Android usando o modelo de **aplicativo do Android** . Vamos chamar este projeto de **LocalNotifications**. (Se você não estiver familiarizado com a criação de projetos do Xamarin. Android, consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Edite os valores de arquivo de recurso **/Strings. xml** para que ele contenha dois recursos de cadeia de caracteres extras que serão usados quando for hora de criar o canal de notificação:

```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Adicionar o pacote NuGet do Android. support. v4

Neste tutorial, estamos usando `NotificationCompat.Builder` para criar nossa notificação local. Conforme explicado em [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md), devemos incluir a [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet em nosso projeto para usar `NotificationCompat.Builder`.

Em seguida, vamos editar **MainActivity.cs** e adicionar a instrução `using` a seguir para que os tipos em `Android.Support.V4.App` estejam disponíveis para nosso código:

```csharp
using Android.Support.V4.App;
```

Além disso, devemos deixar claro para o compilador que estamos usando a versão `Android.Support.V4.App` do `TaskStackBuilder` em vez da versão `Android.App`. Adicione a seguinte instrução de `using` para resolver qualquer ambiguidade:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Criar o canal de notificação

Em seguida, adicione um método a `MainActivity` que criará um canal de notificação (se necessário):

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

Atualize o método `OnCreate` para chamar esse novo método:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```

### <a name="define-the-notification-id"></a>Definir a ID da notificação

Precisaremos de uma ID exclusiva para nosso canal de notificação e notificação. Vamos editar **MainActivity.cs** e adicionar a seguinte variável de instância estática à classe `MainActivity`:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Adicionar código para gerar a notificação

Em seguida, precisamos criar um novo manipulador de eventos para o botão `Click` evento. Adicione o seguinte método a `MainActivity`:

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

O método `OnCreate` de MainActivity deve fazer a chamada para criar o canal de notificação e atribuir o método `ButtonOnClick` ao evento `Click` do botão (substitua o manipulador de eventos de Representante fornecido pelo modelo):

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

### <a name="create-a-second-activity"></a>Criar uma segunda atividade

Agora, precisamos criar outra atividade que o Android exibirá quando o usuário clicar em nossa notificação. Adicione outra atividade do Android ao seu projeto chamada **SecondActivity**. Abra **SecondActivity.cs** e substitua seu conteúdo por este código:

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

Também devemos criar um layout de recurso para **SecondActivity**. Adicione um novo arquivo de **layout do Android** ao seu projeto chamado **Second. axml**. Edite o **segundo. axml** e cole o seguinte código de layout:

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

Por fim, adicione um pequeno ícone que será exibido na área de notificação quando a notificação for iniciada. Você pode copiar [esse ícone](local-notifications-walkthrough-images/ic-stat-button-click.png) para seu projeto ou criar seu próprio ícone personalizado. Nomeie o arquivo de ícone **ic\_\_botão\_clique em. png** e copie-o para a pasta **recursos/empates** . Lembre-se de usar **adicionar > item existente...** para incluir esse arquivo de ícone em seu projeto.

### <a name="run-the-application"></a>Executar o aplicativo

Crie e execute o aplicativo. Você deve receber a primeira atividade, semelhante à captura de tela a seguir:

[captura de tela da primeira atividade![](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Ao clicar no botão, você deve observar que o ícone pequeno da notificação aparece na área de notificação:

[![ícone de notificação aparece](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se você passar o dedo para baixo e expor a gaveta de notificação, verá a notificação:

[![mensagem de notificação](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando você clica na notificação, ela deve desaparecer e nossa outra atividade deve ser iniciada &ndash; com uma aparência semelhante à captura de tela a seguir:

[captura de tela da segunda atividade![](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Parabéns! Neste ponto, você concluiu a explicação do Android em instruções de notificação local e tem um exemplo funcional para o qual você pode se referir. Há muito mais para notificações do que mostramos aqui, portanto, se você quiser mais informações, confira a [documentação do Google sobre notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="summary"></a>Resumo

Este passo a passos usou `NotificationCompat.Builder` para criar e exibir notificações. Ele mostrou um exemplo básico de como iniciar uma segunda atividade como uma maneira de responder à interação do usuário com a notificação e demonstrou a transferência de dados da primeira atividade para a segunda atividade.

## <a name="related-links"></a>Links relacionados

- [LocalNotifications (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Canais de notificação do Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notification](xref:Android.App.Notification)
- [Notificationmanager](xref:Android.App.NotificationManager)
- [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
