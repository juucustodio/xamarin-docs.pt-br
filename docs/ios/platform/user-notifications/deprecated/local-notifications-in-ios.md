---
title: Notificações no xamarin. IOS
description: Esta seção mostra como implementar notificações locais no xamarin. IOS. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutir a API do envolvido com a criação e exibindo uma notificação.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: f31490a683adfb46c609f14adf08b68de0abc375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082962"
---
# <a name="notifications-in-xamarinios"></a>Notificações no xamarin. IOS

> [!IMPORTANT]
> As informações nesta seção se refere ao iOS 9 e anterior. Para iOS 10 e posterior, consulte a [guia de estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md).

iOS tem três maneiras de indicar ao usuário que uma notificação foi recebida:

- **Som ou vibração** -iOS pode reproduzir um som para notificar os usuários. Se o som estiver desabilitado, o dispositivo pode ser configurado para Vibrar.
- **Alertas** -é possível exibir uma caixa de diálogo na tela com informações sobre a notificação.
- **Selos** - quando uma notificação é publicada, um número pode ser exibido (com selo) o ícone do aplicativo.

iOS também fornece um *Centro de notificações* exibirá todas as notificações, locais e remotas, para o usuário. Os usuários podem acessar isso passando o dedo para baixo da parte superior da tela:

![Centro de notificação](local-notifications-in-ios-images/image13.png "o Centro de notificações")

## <a name="creating-local-notifications-in-ios"></a>Criando notificações locais no iOS

iOS torna bastante simples criar e manipular notificações locais.
Primeiro, o iOS 8 requer aplicativos para pedir permissão do usuário exibir as notificações. Adicione o seguinte código ao seu aplicativo antes de tentar enviar uma notificação local – o [anexados a amostra](https://developer.xamarin.com/samples/monotouch/LocalNotifications/) coloca na **AppDelegate**do **FinishedLaunching** método.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmando a capacidade de enviar uma notificação local](local-notifications-in-ios-images/image0-sml.png "confirmando a capacidade de enviar uma notificação de local")](local-notifications-in-ios-images/image0.png#lightbox)

Para agendar uma notificação de local, crie uma `UILocalNotification` do objeto, defina a `FireDate`e programá-lo por meio do `ScheduleLocalNotification` método no `UIApplication.SharedApplication` objeto. O trecho de código a seguir mostra como agendar uma notificação que acionará um minuto no futuro e exibir um alerta com uma mensagem:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Captura de tela a seguir mostra a aparência desse alerta:

[![](local-notifications-in-ios-images/image2-sml.png "Um exemplo de alerta")](local-notifications-in-ios-images/image2.png#lightbox)

Observe que, se o usuário optou *não permitir* notificações e em seguida, nada será exibido.

Se você deseja aplicar uma notificação para o ícone do aplicativo com um número, você pode defini-lo, conforme mostrado no código de linha a seguir:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Na play de ordem um som com o ícone, defina a propriedade de SoundName na notificação, conforme mostrado no trecho de código a seguir:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Se o som de notificação for maior que 30 segundos, iOS reproduzirá o padrão som em vez disso.

> [!IMPORTANT]
> Há um bug no simulador de iOS que irá disparar a notificação de delegado duas vezes. Esse problema não deveria ocorrer ao executar o aplicativo em um dispositivo.

## <a name="handling-notifications"></a>Manipulando notificações

aplicativos iOS manipulam notificações locais e remotas quase exatamente da mesma maneira. Quando um aplicativo está em execução, o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método o `AppDelegate` classe será chamada e as informações de notificação serão passadas como um parâmetro.

Um aplicativo pode manipular uma notificação de maneiras diferentes. Por exemplo, o aplicativo pode simplesmente exibir um alerta para lembrar os usuários de algum evento. Ou a notificação pode ser usada para exibir um alerta para o usuário que um processo foi concluído, como arquivos de sincronização para um servidor.

O código a seguir mostra como lidar com uma notificação local e exibir um alerta e redefinir o número de notificação para zero:

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

Se o aplicativo não está em execução, iOS será reproduzir o som e/ou atualizar o selo do ícone conforme aplicável. Quando o usuário inicia o aplicativo associado ao alerta, o aplicativo será iniciado e o `FinishedLaunching` método de representante do aplicativo será chamado e as informações de notificação serão passadas por meio de `launchOptions` parâmetro. Se o dicionário de opções contém a chave `UIApplication.LaunchOptionsLocalNotificationKey`, em seguida, a `AppDelegate` sabe que o aplicativo foi iniciado de uma notificação de local. O trecho de código a seguir demonstra esse processo:

```csharp
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
```

Uma notificação de remoto `launchOptions` terá uma `LaunchOptionsRemoteNotificationKey` com um tipo de `NSDictionary` que contém a carga de notificação remota. Você pode extrair a carga de notificação por meio de `alert`, `badge`, e `sound` chaves. O trecho de código a seguir mostra como obter notificações remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumo

Esta seção mostrou como criar e publicar uma notificação no xamarin. IOS. Ele mostra como um aplicativo pode reagir a notificações, substituindo o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método no `AppDelegate`.

## <a name="related-links"></a>Links relacionados

- [Notificações locais (amostra)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local e notificações por Push para desenvolvedores](https://developer.apple.com/notifications/)
- [Local e enviar por Push o guia de programação de notificação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
