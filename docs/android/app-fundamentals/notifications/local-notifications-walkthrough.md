---
title: Passo a passo - usando notificações Local no xamarin
description: Este passo a passo demonstra como usar notificações locais em aplicativos xamarin. Ele demonstra os conceitos básicos de criação e publicação de uma notificação de local. Quando o usuário clica na notificação na área de notificação, ele inicia uma segunda atividade.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766576"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Passo a passo - usando notificações Local no xamarin

_Este passo a passo demonstra como usar notificações locais em aplicativos xamarin. Ele demonstra os conceitos básicos de criação e publicação de uma notificação de local. Quando o usuário clica na notificação na área de notificação, ele inicia uma segunda atividade._


## <a name="overview"></a>Visão geral

Neste passo a passo, criaremos um aplicativo do Android que emite uma notificação quando o usuário clica no botão em uma atividade. Quando o usuário clica a notificação, ele abre uma segunda atividade que exibe o número de vezes que o usuário tivesse clicado no botão na primeira atividade.

As capturas de tela a seguir ilustram alguns exemplos desse aplicativo:

[![Capturas de tela de exemplo com notificação](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>Passo a passo

Para começar, vamos criar um novo projeto Android usando o **aplicativo Android** modelo. Vamos chamar esse projeto **LocalNotifications**. (Se você não estiver familiarizado com a criação de projetos de xamarin, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)


### <a name="add-the-androidsupportv4app-component"></a>Adicionar o componente Android.Support.V4.App

Neste passo a passo estamos usando `NotificationCompat.Builder` para criar nossa notificação local. Conforme explicado em [notificações Local](~/android/app-fundamentals/notifications/local-notifications.md), deve incluir o [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) usar o NuGet em nosso projeto `NotificationCompat.Builder`.

Em seguida, vamos editar **MainActivity.cs** e adicione o seguinte `using` instrução para que os tipos em `Android.Support.V4.App` estão disponíveis para o nosso código:

```csharp
using Android.Support.V4.App;
```

Além disso, podemos deverá torná-lo claro para o compilador que estamos usando o `Android.Support.V4.App` versão do `TaskStackBuilder` em vez de `Android.App` versão. Adicione o seguinte `using` instrução para resolver qualquer ambiguidade:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>Definir a ID de notificação

Será necessário uma ID exclusiva para nossa notificação. Vamos editar **MainActivity.cs** e adicione a seguinte variável de instância estático para o `MainActivity` classe:

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>Adicione código para gerar a notificação

Em seguida, precisamos criar um novo manipulador de eventos para o botão `Click` eventos. Adicione o seguinte método para `MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

No `OnCreate` método, atribua `ButtonOnClick` método para o `Click` eventos do botão (substitua o manipulador de eventos delegate fornecida pelo modelo):

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>Criar uma segunda atividade

Agora, precisamos criar outra atividade Android será exibido quando o usuário clica em nossa notificação. Adicione outra atividade Android ao seu projeto chamado **SecondActivity**. Abra **SecondActivity.cs** e substitua o seu conteúdo com este código:

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
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

Também é necessário criar um layout de recurso para **SecondActivity**. Adicionar um novo **Layout Android** ao seu projeto chamado **Second.axml**. Editar **Second.axml** e cole o seguinte código de layout:

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
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Adicionar um ícone de notificação

Por fim, vamos adicionar um pequeno ícone será exibido na área de notificação quando nossa notificação é iniciada. Você pode copiar [esse ícone](local-notifications-walkthrough-images/ic-stat-button-click.png) ao seu projeto ou criar seu próprio ícone personalizado. Chamaremos o arquivo de ícone **ic\_stat\_botão\_click.png** e copie-o para o **recursos/drawable** pasta. Lembre-se de usar **Adicionar > Item existente...**  para incluir esse arquivo de ícone em seu projeto.


### <a name="run-the-application"></a>Executar o aplicativo

Vamos criar e executar o aplicativo. Você deve ser apresentada com a primeira atividade, semelhante à captura de tela a seguir:

[![Captura de tela de atividade primeiro](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Ao clicar no botão, você deve observar o ícone pequeno para a notificação aparecem na área de notificação:

[![Ícone de notificação é exibida](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se você passa o dedo para baixo e expor a gaveta de notificação, você verá a notificação:

[![Mensagem de notificação](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando você clicar na notificação, deverá desaparecer e nossa outra atividade deve ser iniciada &ndash; aparência semelhante a captura de tela a seguir:

[![Segunda tela de atividade](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Parabéns! Agora você concluiu o passo a passo do Android notificação local e você tem uma amostra funcional que você pode consultar. Há muito mais notificações que mostramos aqui, então se você quiser obter mais informações, dê uma olhada [documentação do Google em notificações](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) e o Android [notificações](http://developer.android.com/design/patterns/notifications.html) guia de design.



## <a name="summary"></a>Resumo

Neste passo a passo usamos `NotificationCompat.Builder` para criar e exibir as notificações. Vimos um exemplo básico de como iniciar uma segunda atividade como uma maneira para responder à interação do usuário com a notificação e demonstramos a transferência de dados da primeira atividade para a segunda atividade.


## <a name="related-links"></a>Links relacionados

- [LocalNotifications (exemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Padrões de design da guia em notificações](http://developer.android.com/design/patterns/notifications.html)
- [notificação](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
