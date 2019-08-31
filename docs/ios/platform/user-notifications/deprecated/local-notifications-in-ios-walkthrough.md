---
title: Walkthrough – usando notificações locais no Xamarin. iOS
description: Nesta seção, vamos examinar como usar as notificações locais em um aplicativo Xamarin. iOS. Ele demonstrará as noções básicas de criação e publicação de uma notificação que exibirá um alerta quando recebido pelo aplicativo.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 376f046769e27586d9611d634aef89811c7b3be8
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200377"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Walkthrough – usando notificações locais no Xamarin. iOS

_Nesta seção, vamos examinar como usar as notificações locais em um aplicativo Xamarin. iOS. Ele demonstrará as noções básicas de criação e publicação de uma notificação que exibirá um alerta quando recebido pelo aplicativo._

> [!IMPORTANT]
> As informações contidas nesta seção pertencem ao iOS 9 e anteriores, foram deixadas aqui para dar suporte a versões mais antigas do iOS. Para o iOS 10 e posterior, consulte o [guia da estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a notificações locais e remotas em um dispositivo IOS.

## <a name="walkthrough"></a>Passo a passo

Permita criar um aplicativo simples que mostrará notificações locais em ação. Esse aplicativo terá um único botão. Quando clicamos no botão, ele cria uma notificação local. Depois que o período de tempo especificado tiver decorrido, veremos que a notificação será exibida.


1. No Visual Studio para Mac, crie uma nova solução de exibição única do iOS e `Notifications`chame-a.
1. Abra o `Main.storyboard` arquivo e arraste um botão para a exibição. Nomeie o **botão**botão e dê a ele o título **Adicionar notificação**. Talvez você também queira definir algumas [restrições](~/ios/user-interface/designer/designer-auto-layout.md) para o botão neste ponto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Definindo algumas restrições no botão")
1. Edite `ViewController` a classe e adicione o seguinte manipulador de eventos ao método ViewDidLoad:

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

1. Em seguida, edite o arquivo `AppDelegate.cs`, primeiro adicione o seguinte código `FinishedLaunching` ao método. Verificamos se o dispositivo está executando o iOS 8, se for **necessário** solicitar a permissão do usuário para receber notificações:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. Ainda em `AppDelegate.cs`, adicione o seguinte método que será chamado quando uma notificação for recebida:

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

1. Precisamos lidar com o caso em que a notificação foi iniciada devido a uma notificação local. Edite o `FinishedLaunching` método `AppDelegate` no para incluir o seguinte trecho de código:


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

1. Por fim, execute o aplicativo. No iOS 8, você será solicitado a permitir notificações. Clique em **OK** e, em seguida, clique no botão **Adicionar notificação** . Após uma breve pausa, você deverá ver a caixa de diálogo de alerta, conforme mostrado nas capturas de tela a seguir:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Confirmando a capacidade de enviar notificações") ![](local-notifications-in-ios-walkthrough-images/image1.png "botão a adicionar notificação") ![](local-notifications-in-ios-walkthrough-images/image2.png "a caixa de diálogo de alerta de notificação")

## <a name="summary"></a>Resumo

Este tutorial mostrou como usar as várias APIs para criar e publicar notificações no iOS. Ele também demonstrou como atualizar o ícone do aplicativo com uma notificação para fornecer alguns comentários específicos do aplicativo para o usuário.


## <a name="related-links"></a>Links relacionados

- [Notificações locais (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Guia de programação de notificações por push e locais](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
