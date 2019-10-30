---
title: Alertas críticos no Xamarin. iOS
description: Este documento descreve como usar alertas críticos com o Xamarin. iOS. Alertas críticos, introduzidos com o iOS 12, são notificações de interrupção que reproduzem um som, independentemente de não incomodar estarem ou se o interruptor de toque está desligado.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 43b810b95e4da2927030617e68c0ade824a0beaa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031972"
---
# <a name="critical-alerts-in-xamarinios"></a>Alertas críticos no Xamarin. iOS

Com o iOS 12, os aplicativos podem enviar alertas críticos. Alertas críticos desempenham um som, independentemente de se não incomodar ou não estar habilitado ou se o interruptor de toque está desativado. Essas notificações são interrompidas e só devem ser usadas quando os usuários precisam executar uma ação imediata.

## <a name="custom-critical-alert-entitlement"></a>Direito de alerta crítico personalizado

Para exibir alertas críticos em seu aplicativo, primeiro [solicite um direito de notificações de alerta crítico personalizado](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) da Apple.

Depois de receber esse direito da Apple e seguir todas as instruções associadas sobre como configurar seu aplicativo para usá-lo, adicione o [direito](~/ios/deploy-test/provisioning/entitlements.md) personalizado aos arquivos **. plist de direitos** do seu aplicativo. Em seguida, configure suas opções de **assinatura de pacote do IOS** para usar os **direitos. plist** ao assinar o aplicativo no simulador e no dispositivo.

## <a name="request-authorization"></a>Solicitar autorização

A solicitação de autorização de notificação de um aplicativo solicita que o usuário permita ou proíba as notificações de um aplicativo. Se a solicitação de autorização de notificação solicitar permissão para enviar alertas críticos, o aplicativo também dará ao usuário a oportunidade de aceitar alertas críticos.

O código a seguir solicita permissão para enviar alertas críticos e notificações padrão e sons passando o [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions) apropriado
valores para [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Alertas críticos locais

Para enviar um alerta crítico local, crie um [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
e defina sua propriedade `Sound` como:

- `UNNotificationSound.DefaultCriticalSound`, que usa o som de notificação crítico padrão.
- `UNNotificationSound.GetCriticalSound`, que permite que você especifique um som personalizado que é agrupado com seu aplicativo e um volume.

Em seguida, crie uma `UNNotificationRequest` do conteúdo da notificação e adicione-a ao centro de notificações:

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> Alertas críticos não serão entregues se não estiverem habilitados para seu aplicativo. Junto com o prompt que aparece na primeira vez que um aplicativo solicita permissão para enviar alertas críticos, um usuário também pode habilitar ou Desabilitar alertas críticos na seção **notificações** do aplicativo do aplicativo de **configurações** do Ios.

## <a name="remote-critical-alerts"></a>Alertas críticos remotos

Para obter informações sobre alertas críticos remotos, consulte a sessão [What ' s New in User Notifications](https://developer.apple.com/videos/play/wwdc2018/710/) from WWDC 2018 e [gerando um documento de notificação remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) .

## <a name="related-links"></a>Links relacionados

- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Usernotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
