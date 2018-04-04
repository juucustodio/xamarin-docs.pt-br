---
title: Passo a passo - usando notificações Local no xamarin
description: Nesta seção examinaremos como usar notificações locais em um aplicativo xamarin. Demonstrar os conceitos básicos de criação e publicação de uma notificação que será exibida em um alerta quando recebidas pelo aplicativo.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bb133d16f12249cbd31e4fce2b227162b4b28333
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Passo a passo - usando notificações Local no xamarin

_Nesta seção examinaremos como usar notificações locais em um aplicativo xamarin. Demonstrar os conceitos básicos de criação e publicação de uma notificação que será exibida em um alerta quando recebidas pelo aplicativo._

> [!IMPORTANT]
> As informações nesta seção se refere ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte o [guia estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a Local e remoto de notificação em um dispositivo iOS.

## <a name="walkthrough"></a>Passo a passo

Permitem criar um aplicativo simples que mostrará o locais notificações em ação. Este aplicativo terá um único botão nele. Quando é clicar no botão, ele criará uma notificação de local. Depois que o período de tempo decorrido, veremos a notificação aparecer.


1. No Visual Studio para Mac, crie uma nova solução iOS único modo de exibição e chamá-la `Notifications`.
1. Abra o `Main.storyboard` de arquivo e arraste um botão para o modo de exibição. Nomeie o botão **botão**e dê a ele o título **adicionar notificação**. Talvez você queira definir algumas [restrições](~/ios/user-interface/designer/designer-auto-layout.md) ao botão neste ponto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Definir algumas restrições no botão")
1. Editar o `ViewController` classe e adicione o manipulador de eventos a seguir ao método ViewDidLoad:

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

    Esse código cria uma notificação que usa um som, define o valor da notificação de ícone para 1 e exibe um alerta para o usuário.

1. Em seguida, edite o arquivo `AppDelegate.cs`, primeiro adicione o seguinte código para o `FinishedLaunching` método. Verificamos para ver se o dispositivo está executando o iOS 8, se temos **necessária** para solicitar a permissão do usuário receber notificações:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. Ainda no `AppDelegate.cs`, adicione o seguinte método ser chamado quando a notificação é recebida:

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
            {
                // show an alert
                UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }

    ```

1. Precisamos lidar com o caso em que a notificação foi iniciada devido a uma notificação de local. Editar o método `FinishedLaunching` no `AppDelegate` para incluir o seguinte trecho de código:


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

1. Por fim, execute o aplicativo. No iOS 8, você será solicitado para permitir que as notificações. Clique em **Okey** e, em seguida, clique no **adicionar notificação** botão. Após uma pequena pausa você verá a caixa de diálogo alerta, conforme mostrado nas capturas de tela seguir:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirmar a capacidade de enviar notificações") ![ ] (local-notifications-in-ios-walkthrough-images/image1.png "botão a adicionar notificação") ![ ] (local-notifications-in-ios-walkthrough-images/image2.png "a caixa de diálogo de alerta de notificação")

## <a name="summary"></a>Resumo

Este passo a passo mostrada como usar as várias APIs para criar e publicar as notificações no iOS. Ele também demonstrou como atualizar o ícone do aplicativo com uma notificação para fornecer seus comentários específicos do aplicativo para o usuário.


## <a name="related-links"></a>Links relacionados

- [Notificações de locais (exemplo)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local e o guia de programação de notificações por Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
