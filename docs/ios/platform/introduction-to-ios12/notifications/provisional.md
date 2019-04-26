---
title: Notificações provisória no xamarin. IOS
description: Este documento descreve como usar o xamarin. IOS para funcionar com notificações provisória. Notificações provisória, introduzidas no iOS 12, permitir que os aplicativos enviar notificações silenciosa sem a permissão explícita do usuário.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 434dd2e2ee7a0064b706872a228070c5114078c4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034766"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notificações provisória no xamarin. IOS

Notificações provisória permitem que os aplicativos entregar notificações sem o consentimento explícito de inicial do usuário. Essas notificações chegam no modo silencioso e mostram somente no Centro de notificações, que permite aos usuários visualizá-las antes de aceitar ou recusar o fornecimento contínuo.

No Centro de notificação, os usuários podem especificar que um aplicativo deve interromper a entrega de notificações provisória, continuar entregá-los provisoriamente ou iniciar entregá-los de modo mais visível.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Examine os [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicativo de exemplo que envia notificações provisória.

## <a name="sending-provisional-notifications"></a>Envio de notificações provisionais

Para enviar notificações provisória, fornecer `UNAuthorizationOptions.Provisional` como uma opção para o [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
método de `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Se o usuário promove notificações provisória para entrega proeminente, o `UNAuthorizationOptions` valores passados para `RequestAuthorization` determinará as novas configurações de entrega de notificação (no código acima, `UNAuthorizationOptions.Alert` e `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
