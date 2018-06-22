---
title: Notificações no xamarin
description: Esta seção mostra como implementar notificações de locais em xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutem a API do envolvidos com a criação e exibindo uma notificação.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d4dd759f52e52f417441f69e6417fab536daf6d3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777906"
---
# <a name="notifications-in-xamarinios"></a>Notificações no xamarin

_Esta seção mostra como implementar notificações de locais em xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutem a API do envolvidos com a criação e exibindo uma notificação._

> [!IMPORTANT]
> As informações nesta seção se refere ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte o [guia estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a Local e remoto de notificação em um dispositivo iOS.

iOS tem três modos para indicar ao usuário que uma notificação foi recebida:

-  **Som ou vibração** -iOS pode tocar um som para notificar os usuários. Se o som estiver desabilitado, o dispositivo pode ser configurado para Vibrar.
-  **Alertas** -é possível exibir uma caixa de diálogo na tela com informações sobre a notificação.
-  **Notificações** - quando uma notificação é publicada, um número pode ser exibido (com selo) o ícone do aplicativo.


iOS também fornece um *Central de notificações* que exibirá todas as notificações, locais e remotas, para o usuário. Os usuários podem acessar este passando da parte superior da tela:

 ![](local-notifications-in-ios-images/image13.png "O Centro de notificação")

## <a name="creating-local-notifications-in-ios"></a>Criar notificações de Local no iOS

iOS facilita bastante simples para criar e manipular notificações locais.
Primeiro, o iOS 8 requer aplicativos para solicitar a permissão do usuário exibir as notificações. Adicione o seguinte código ao seu aplicativo antes de tentar enviar uma notificação local – o exemplo anexado coloca no **AppDelegate**do **FinishedLaunching** método.

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [![](local-notifications-in-ios-images/image0-sml.png "Confirmar a capacidade de enviar uma notificação de local")](local-notifications-in-ios-images/image0.png#lightbox)

Para agendar uma notificação de local que você criar um `UILocalNotification` do objeto, defina o `FireDate`e agendá-lo por meio de `ScheduleLocalNotification` método no `UIApplication.SharedApplication` objeto. O trecho de código a seguir mostra como agendar uma notificação que irá disparar um minuto no futuro e exibir um alerta com uma mensagem:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Captura de tela a seguir mostram como esse alerta aparece:

  [![](local-notifications-in-ios-images/image2-sml.png "Um exemplo de alerta")](local-notifications-in-ios-images/image2.png#lightbox)

Observe que, se o usuário optou por *não permitir* notificações e em seguida, nada será exibido.

Se você deseja aplicar uma notificação para o ícone do aplicativo com um número, você pode definir como mostrado no código de linha a seguir:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Na play ordem um som com o ícone, defina a propriedade de SoundName na notificação, conforme mostrado no trecho de código a seguir:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Por que as diretrizes de Interface Humana Apple, se uma notificação toca um som, ele deve também ser acompanhado por um alerta para ajudar o usuário a identificar o aplicativo que gerou o alerta ou uma notificação. Além disso, se o som tiver mais de 30 segundos, iOS reproduzirá o padrão som em vez disso.

> [!IMPORTANT]
> Há um bug no simulador iOS que irá disparar a notificação de delegado duas vezes. Esse problema não deve ocorrer ao executar o aplicativo em um dispositivo.

## <a name="handling-notifications"></a>Manipulando notificações

aplicativos iOS tratar notificações locais e remotas quase exatamente da mesma maneira. Quando um aplicativo é executado, o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método na classe AppDelegate será chamado e as informações de notificação serão passadas como um parâmetro.

Um aplicativo pode manipular uma notificação de maneiras diferentes. Por exemplo, o aplicativo pode apenas exibir um alerta para lembrar os usuários de um evento. Ou a notificação pode ser usada para exibir um alerta para o usuário que um processo for concluído, como arquivos sincronizando a um servidor.

O código a seguir mostra como tratar uma notificação de local e exibir um alerta e redefinir o número de notificação para zero:

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

Se o aplicativo não está em execução, iOS será reprodução do som e/ou atualizar a notificação de ícone conforme aplicável. Quando o usuário inicia o aplicativo associado com o alerta, o aplicativo será iniciado e o método FinishedLaunching sobre o delegado de aplicativo será chamado e as informações de notificação serão passadas por meio do parâmetro de opções. Se o dicionário de opções contém a chave `UIApplication.LaunchOptionsLocalNotificationKey`, o AppDelegate saberá que o aplicativo foi iniciado em uma notificação de local. O trecho de código a seguir demonstra esse processo:

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

Se for uma notificação remota do dicionário de opções em vez disso, contém um `LaunchOptionsRemoteNotificationKey`, e o valor resultante para essa chave um `NSDictionary` objeto com a carga de notificação remoto. Você pode extrair a carga de notificação por meio de chaves de som, alerta e notificação. O trecho de código a seguir mostra como obter notificações remotas:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Resumo

Esta seção mostrou como criar e publicar uma notificação no xamarin. Ele mostra como um aplicativo pode reagir a notificações, substituindo o `ReceivedLocalNotification` método ou o `ReceivedRemoteNotification` método o `AppDelegate`.


## <a name="related-links"></a>Links relacionados

- [Notificações de locais (exemplo)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local e enviar notificações para desenvolvedores](https://developer.apple.com/notifications/)
- [Local e enviar por Push o guia de programação de notificação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
