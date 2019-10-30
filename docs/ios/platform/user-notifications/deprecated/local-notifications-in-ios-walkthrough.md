---
title: Walkthrough – usando notificações locais no Xamarin. iOS
description: Nesta seção, vamos examinar como usar as notificações locais em um aplicativo Xamarin. iOS. Ele demonstrará as noções básicas de criação e publicação de uma notificação que exibirá um alerta quando recebido pelo aplicativo.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 764be6319e95b16dc043bebd2abfb27ba0696457
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031399"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Walkthrough – usando notificações locais no Xamarin. iOS

_Nesta seção, vamos examinar como usar as notificações locais em um aplicativo Xamarin. iOS. Ele demonstrará as noções básicas de criação e publicação de uma notificação que exibirá um alerta quando recebido pelo aplicativo._

> [!IMPORTANT]
> As informações contidas nesta seção pertencem ao iOS 9 e anteriores, foram deixadas aqui para dar suporte a versões mais antigas do iOS. Para o iOS 10 e posterior, consulte o [guia da estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a notificações locais e remotas em um dispositivo IOS.

## <a name="walkthrough"></a>Passo a passo

Permita criar um aplicativo simples que mostrará notificações locais em ação. Esse aplicativo terá um único botão. Quando clicamos no botão, ele cria uma notificação local. Depois que o período de tempo especificado tiver decorrido, veremos que a notificação será exibida.

1. No Visual Studio para Mac, crie uma nova solução de exibição única do iOS e chame-a `Notifications`.
1. Abra o arquivo `Main.storyboard` e arraste um botão para a exibição. Nomeie o **botão**botão e dê a ele o título **Adicionar notificação**. Talvez você também queira definir algumas [restrições](~/ios/user-interface/designer/designer-auto-layout.md) para o botão neste ponto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Setting some constraints on the button")
1. Edite a classe `ViewController` e adicione o seguinte manipulador de eventos ao método ViewDidLoad:

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Esse código criará uma notificação que usa um som, define o valor do selo do ícone como 1 e exibe um alerta para o usuário.

1. Next edit the file `AppDelegate.cs`, first add the following code to the `FinishedLaunching` method. We have checked to see if the device is running iOS 8, if so we are **required** to ask for the user's permission to receive notifications:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. Still in `AppDelegate.cs`, add the following method which will be called  when a notification is received:

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
    {
        // show an alert
        UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
    ```

1. We need to handle the case where the notification was launched because of a local notification. Edit the method `FinishedLaunching` in the `AppDelegate` to include the following snippet of code:

    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
        // check for a local notification
        if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
        {
            var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
            if (localNotification != null)
            {
                UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }
        }
    }
    ```

1. Finally, run the application. On iOS 8 you will be prompted to allow notifications. Click **OK** and then click the **Add notification** button. After a short pause you should see the alert dialog, as shown in the following screenshots:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirming the ability to send notifications") ![](local-notifications-in-ios-walkthrough-images/image1.png "The Add Notification button")
    ![](local-notifications-in-ios-walkthrough-images/image2.png "The notification alert dialog")

## <a name="summary"></a>Resumo

This walkthrough showed how to use the various API's for creating and publishing notifications in iOS. It also demonstrated how to update the application icon with a badge to provide some application specific feedback to the user.

## <a name="related-links"></a>Links relacionados

- [Local Notifications (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Local and Push Notifications Programming Guide](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
