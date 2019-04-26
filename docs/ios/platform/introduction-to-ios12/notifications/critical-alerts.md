---
title: Alertas críticos no xamarin. IOS
description: Este documento descreve como usar os alertas críticos com xamarin. IOS. Alertas críticos, introduzidas com o iOS 12, são notificações de interrupções que tocar um som independentemente se não incomodar é em ou a opção de alternar está desativado.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 264b1935aefe6f5ddf4bfcd17c491f6858bd4ffb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876099"
---
# <a name="critical-alerts-in-xamarinios"></a>Alertas críticos no xamarin. IOS

Com 12 do iOS, aplicativos podem enviar alertas críticos. Alertas críticos tocar um som, independentemente de estarem ou não, não incomodar está habilitado ou a opção de alternar está desativado. Essas notificações são interrompidas e só devem ser usadas quando os usuários devem tomar uma ação imediata.

## <a name="custom-critical-alert-entitlement"></a>Qualificação de alerta crítica personalizada

Exibir alertas críticos em seu aplicativo, primeiro [solicitar um direito personalizado notificações de alerta crítico](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) da Apple.

Depois de receber esse direito da Apple e seguindo as instruções associadas sobre como configurar seu aplicativo usá-la, adicionar personalizado [direito](~/ios/deploy-test/provisioning/entitlements.md) ao seu aplicativo **Entitlements. plist** arquivo (s). Em seguida, configure sua **assinatura do pacote iOS** opções a serem usadas **Entitlements. plist** ao assinar o aplicativo no simulador e dispositivo.

## <a name="request-authorization"></a>Solicitação de autorização

Solicitação de autorização de notificação de um aplicativo solicita ao usuário para permitir ou impedir que notificações do aplicativo. Se a solicitação de autorização de notificação solicita permissão para enviar alertas críticos, o aplicativo também concederá ao usuário a oportunidade de optar por alertas críticos.

O código a seguir solicita permissão para enviar alertas críticos e notificações padrão e sons, passando a apropriado [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
valores de [ `RequestAuthorization` ](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

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

Para enviar um alerta crítico local, crie uma [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
e defina seu `Sound` propriedade para qualquer um:

- `UNNotificationSound.DefaultCriticalSound`, que usa o som de notificação crítica do padrão.
- `UNNotificationSound.GetCriticalSound`, que permite que você especifique um personalizado de som que é fornecido com o seu aplicativo e um volume.

Em seguida, crie um `UNNotificationRequest` a notificação de conteúdo e adicioná-lo para o Centro de notificação:

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
> Alertas críticos não serão entregues se eles não estiverem habilitados para seu aplicativo. Juntamente com o prompt que aparece na primeira vez que um aplicativo solicita permissão para enviar alertas críticos, um usuário também pode habilitar ou desabilitar alertas críticos no seu aplicativo **notificações** seção do iOS **configurações**app.

## <a name="remote-critical-alerts"></a>Alertas críticos remotos

Para obter informações sobre os alertas críticos remotos, consulte a [o que há de novo em notificações de usuário](https://developer.apple.com/videos/play/wwdc2018/710/) sessão de 2018 WWDC e o [gerar uma notificação remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento.

## <a name="related-links"></a>Links relacionados

- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
