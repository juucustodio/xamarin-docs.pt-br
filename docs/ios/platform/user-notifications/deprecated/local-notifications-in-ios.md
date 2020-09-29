---
title: Notificações no Xamarin. iOS
description: Esta seção mostra como implementar notificações locais no Xamarin. iOS. Ele explicará os vários elementos da interface do usuário de uma notificação do iOS e discutirá as APIs envolvidas na criação e exibição de uma notificação.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: f99776a0e6a84c193362d0e5a4efb68e70717ad0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436114"
---
# <a name="notifications-in-xamarinios"></a>Notificações no Xamarin. iOS

> [!IMPORTANT]
> As informações contidas nesta seção pertencem ao iOS 9 e anteriores. Para o iOS 10 e posterior, consulte o [guia da estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md).

o iOS tem três maneiras de indicar ao usuário que uma notificação foi recebida:

- **Som ou vibração** – o Ios pode emitir um som para notificar os usuários. Se o som estiver desabilitado, o dispositivo poderá ser configurado para vibrar.
- **Alertas** -é possível exibir uma caixa de diálogo na tela com informações sobre a notificação.
- **Notificações** – quando uma notificação é publicada, um número pode ser exibido (com notificações) no ícone do aplicativo.

o iOS também fornece um *centro de notificação* que exibirá todas as notificações, locais e remotas, para o usuário. Os usuários podem acessá-lo passando o dedo para baixo na parte superior da tela:

![O centro de notificações](local-notifications-in-ios-images/image13.png "O centro de notificações")

## <a name="creating-local-notifications-in-ios"></a>Criando notificações locais no iOS

o iOS torna bastante simples criar e manipular notificações locais.
Primeiro, o iOS 8 exige que os aplicativos solicitem a permissão do usuário para exibir notificações. Adicione o código a seguir ao seu aplicativo antes de tentar enviar uma notificação local-a [amostra anexada](/samples/xamarin/ios-samples/localnotifications) a coloca no método **FinishedLaunching** do **AppDelegate**.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmando a capacidade de enviar uma notificação local](local-notifications-in-ios-images/image0-sml.png "Confirmando a capacidade de enviar uma notificação local")](local-notifications-in-ios-images/image0.png#lightbox)

Para agendar uma notificação local, crie um `UILocalNotification` objeto, defina `FireDate` e agende-o por meio do `ScheduleLocalNotification` método no `UIApplication.SharedApplication` objeto. O trecho de código a seguir mostra como agendar uma notificação que irá disparar um minuto no futuro e exibir um alerta com uma mensagem:

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

A captura de tela a seguir mostra a aparência desse alerta:

[![Um alerta de exemplo](local-notifications-in-ios-images/image2-sml.png)](local-notifications-in-ios-images/image2.png#lightbox)

Observe que, se o usuário optar por *não permitir* notificações, nada será exibido.

Se você quiser aplicar uma notificação ao ícone do aplicativo com um número, poderá defini-lo conforme mostrado no seguinte código de linha:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Na ordem, reproduza um som com o ícone, defina a propriedade soundName na notificação, conforme mostrado no trecho de código a seguir:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Se o som da notificação for maior que 30 segundos, o iOS tocará o som padrão.

> [!IMPORTANT]
> Há um bug no simulador de iOS que acionará a notificação de delegado duas vezes. Esse problema não deve ocorrer ao executar o aplicativo em um dispositivo.

## <a name="handling-notifications"></a>Manipulando notificações

os aplicativos iOS lidam com notificações remotas e locais quase exatamente da mesma maneira. Quando um aplicativo estiver em execução, o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método na `AppDelegate` classe será chamado e as informações de notificação serão passadas como um parâmetro.

Um aplicativo pode lidar com uma notificação de maneiras diferentes. Por exemplo, o aplicativo pode apenas exibir um alerta para lembrar os usuários sobre algum evento. Ou a notificação pode ser usada para exibir um alerta ao usuário de que um processo foi concluído, como a sincronização de arquivos para um servidor.

O código a seguir mostra como lidar com uma notificação local e exibir um alerta e redefinir o número de crachá para zero:

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

Se o aplicativo não estiver em execução, o iOS reproduzirá o som e/ou atualizará o selo do ícone conforme aplicável. Quando o usuário inicia o aplicativo associado ao alerta, o aplicativo será iniciado e o `FinishedLaunching` método no delegado do aplicativo será chamado e as informações de notificação serão passadas por meio do `launchOptions` parâmetro. Se o dicionário Options contiver a chave `UIApplication.LaunchOptionsLocalNotificationKey` , o `AppDelegate` saberá que o aplicativo foi iniciado a partir de uma notificação local. O trecho de código a seguir demonstra esse processo:

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

Para uma notificação remota, `launchOptions` o terá um `LaunchOptionsRemoteNotificationKey` com um associado `NSDictionary` que contém a carga de notificação remota. Você pode extrair a carga de notificação pelas `alert` `badge` chaves, e `sound` . O trecho de código a seguir mostra como obter notificações remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumo

Esta seção mostrou como criar e publicar uma notificação no Xamarin. iOS. Ele mostra como um aplicativo pode reagir às notificações substituindo o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método no `AppDelegate` .

## <a name="related-links"></a>Links relacionados

- [Notificações locais (exemplo)](/samples/xamarin/ios-samples/localnotifications)
- [Notificações locais e por push para desenvolvedores](https://developer.apple.com/notifications/)
- [Guia de Programação Local e de Notificação por Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](/dotnet/api/uikit.uiapplication)
- [UILocalNotification](/dotnet/api/uikit.UILocalNotification)