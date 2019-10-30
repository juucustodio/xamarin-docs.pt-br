---
title: Notificações provisórias no Xamarin. iOS
description: Este documento descreve como usar o Xamarin. iOS para trabalhar com notificações provisórias. As notificações provisionas, introduzidas no iOS 12, permitem que os aplicativos enviem notificações silenciosas sem permissão explícita do usuário.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 7d9fe2a651d8d75d8dd9d8c0dd1225350a58373d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031882"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notificações provisórias no Xamarin. iOS

As notificações provisórias permitem que os aplicativos forneçam notificações sem o consentimento inicial explícito de um usuário. Essas notificações chegam discretamente e mostram apenas no centro de notificações, que permite aos usuários visualizá-las antes de optar pela entrega contínua.

No centro de notificações, os usuários podem especificar que um aplicativo deve parar de fornecer notificações provisionas, continuar entregando-as provisionas ou começar a fornecê-las com mais destaque.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Dê uma olhada no aplicativo de exemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , que envia notificações provisionas.

## <a name="sending-provisional-notifications"></a>Enviando notificações provisórias

Para enviar notificações provisionas, forneça `UNAuthorizationOptions.Provisional` como uma opção para a [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
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

Se o usuário promover notificações provisionas para entrega proeminente, os valores `UNAuthorizationOptions` passados para `RequestAuthorization` determinarão as novas configurações de entrega de notificação (no código acima, `UNAuthorizationOptions.Alert` e `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Usernotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
