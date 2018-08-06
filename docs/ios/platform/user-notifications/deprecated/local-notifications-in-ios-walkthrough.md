---
title: 'Passo a passo: usar notificações locais no xamarin. IOS'
description: Nesta seção vamos examinar como usar notificações locais em um aplicativo xamarin. IOS. Ela demonstrará as Noções básicas de criação e publicação de uma notificação que será exibido um alerta quando recebidas pelo aplicativo.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cf1e44ba4176922234fc1b6b9bfe5c463611cc7b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403423"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Passo a passo: usar notificações locais no xamarin. IOS

_Nesta seção vamos examinar como usar notificações locais em um aplicativo xamarin. IOS. Ela demonstrará as Noções básicas de criação e publicação de uma notificação que será exibido um alerta quando recebidas pelo aplicativo._

> [!IMPORTANT]
> As informações nesta seção referem-se ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte a [guia de estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a locais e remotas de notificação em um dispositivo iOS.

## <a name="walkthrough"></a>Passo a passo

Permitem criar um aplicativo simples que mostrará as notificações locais em ação. Este aplicativo terá um único botão nele. Quando clicamos no botão, ele criará uma notificação de local. Depois que o especificado o período de tempo decorrido, veremos a notificação aparecem.


1. No Visual Studio para Mac, crie uma nova solução de iOS do modo de exibição único e chame- `Notifications`.
1. Abra o `Main.storyboard` de arquivo e arraste um botão para o modo de exibição. Nomeie o botão **botão**e dê a ele o título **adicionar notificação**. Talvez você queira definir algumas [restrições](~/ios/user-interface/designer/designer-auto-layout.md) ao botão neste momento: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Definindo algumas restrições sobre o botão")
1. Editar o `ViewController` de classe e adicione o seguinte manipulador de eventos para o método ViewDidLoad:

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

    Esse código cria uma notificação que usa um som, define o valor de notificação de ícone para 1 e exibe um alerta para o usuário.

1. Em seguida, edite o arquivo `AppDelegate.cs`, primeiro adicione o seguinte código para o `FinishedLaunching` método. Verificamos para ver se o dispositivo está executando o iOS 8, se, portanto, estamos **necessária** para pedir permissão do usuário receber notificações:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. Ainda no `AppDelegate.cs`, adicione o seguinte método que será chamado quando uma notificação é recebida:

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

1. Precisamos manipular o caso em que a notificação foi iniciada devido a uma notificação de local. Editar o método `FinishedLaunching` no `AppDelegate` para incluir o seguinte trecho de código:


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

1. Por fim, execute o aplicativo. No iOS 8, você será solicitado para permitir as notificações. Clique em **Okey** e, em seguida, clique no **adicionar notificação** botão. Após uma pequena pausa, você deverá ver a caixa de diálogo de alerta, conforme mostrado nas capturas de tela seguir:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirmando a capacidade de enviar notificações") ![](local-notifications-in-ios-walkthrough-images/image1.png "botão a adicionar notificação") ![](local-notifications-in-ios-walkthrough-images/image2.png "a caixa de diálogo de alerta de notificação")

## <a name="summary"></a>Resumo

Este passo a passo mostrou como usar a API de vários para criar e publicar as notificações no iOS. Ele também demonstrou como atualizar o ícone do aplicativo com uma notificação para fornecer alguns comentários específicos do aplicativo para o usuário.


## <a name="related-links"></a>Links relacionados

- [Notificações locais (amostra)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local e o guia de programação de notificações por Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
