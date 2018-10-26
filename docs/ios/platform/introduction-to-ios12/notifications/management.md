---
title: Gerenciamento de notificação no xamarin. IOS
description: Este documento descreve como usar o xamarin. IOS para tirar proveito dos novos recursos de gerenciamento de notificação, introduzido no iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: a7a3bb8f720f1c6a2370a2510659693bb28ea09b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111447"
---
# <a name="notification-management-in-xamarinios"></a>Gerenciamento de notificação no xamarin. IOS

No iOS 12, o sistema operacional pode link profundo do Centro de notificação e o aplicativo de configurações para a tela de gerenciamento de notificação do aplicativo. Esta tela deve permitir que usuários optar por participar e fora de vários tipos de notificações que o aplicativo envia.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Para ver um exemplo de como funciona o gerenciamento de notificação, dê uma olhada a [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicativo de exemplo.

Este aplicativo de exemplo envia dois tipos de notificações, vermelho e verde e fornece uma tela que permite aos usuários aceitar ou recusar a qualquer tipo.

Trechos de código neste guia são provenientes este aplicativo de exemplo.

## <a name="notification-management-screen"></a>Tela de gerenciamento de notificação

No aplicativo de exemplo, `ManageNotificationsViewController` define uma interface do usuário que permite aos usuários habilitar e desabilitar notificações de vermelhas e verdes notificações de forma independente. É um padrão [`UIViewController`](https://developer.xamarin.com/api/type/UIKit.UIViewController/)
que contém um [ `UISwitch` ](https://developer.xamarin.com/api/type/UIKit.UISwitch/) para cada tipo de notificação. Ativar/desativar a opção para qualquer tipo de notificação salva, nos padrões do usuário, a preferência do usuário para esse tipo de notificação:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> A tela de gerenciamento de notificação também verifica se o usuário tiver desabilitado completamente as notificações para o aplicativo. Nesse caso, ele oculta os botões para os tipos de notificação individuais. Para fazer isso, a tela de gerenciamento de notificação:
>
> - Chamadas [ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync()/) e examina o [ `AuthorizationStatus` ](https://developer.xamarin.com/api/property/UserNotifications.UNNotificationSettings.AuthorizationStatus/) propriedade.
> - Oculta os botões para os tipos de notificação individuais se as notificações foram totalmente desativadas para o aplicativo.
> - Novamente, verifica se as notificações foram desabilitadas cada vez que o aplicativo é movido para o primeiro plano, uma vez que o usuário pode habilitar/desabilitar notificações em configurações do iOS a qualquer momento.

O aplicativo de exemplo `ViewController` classe, que envia as notificações, verificação de preferência do usuário antes de enviar uma notificação local para garantir que a notificação seja de um tipo de usuário realmente quer receber:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Link profundo

iOS links profundos para a tela de gerenciamento de notificação do aplicativo do Centro de notificação e configurações de notificação do aplicativo no aplicativo configurações. Para facilitar isso, um aplicativo deve:

- Indicar que uma tela de gerenciamento de notificação está disponível, passando `UNAuthorizationOptions.ProvidesAppNotificationSettings` a solicitação de autorização de notificação do aplicativo.
- Implemente a `OpenSettings` método de [ `IUNUserNotificationCenterDelegate` ](https://developer.xamarin.com/api/type/UserNotifications.IUNUserNotificationCenterDelegate/).

### <a name="authorization-request"></a>Solicitação de autorização

Para indicar ao sistema operacional que uma tela de gerenciamento de notificação está disponível, um aplicativo deve passar o `UNAuthorizationOptions.ProvidesAppNotificationSettings` opção (juntamente com qualquer outras notificação opções de entrega precisa) para o `RequestAuthorization` método o `UNUserNotificationCenter`.

Por exemplo, no aplicativo de exemplo `AppDelegate`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Método OpenSettings

O `OpenSettings` método, chamado pelo sistema ao link profundo à tela de gerenciamento de notificação do aplicativo, deverá conduzir o usuário diretamente para a tela.

No aplicativo de exemplo, esse método executa o segue para a `ManageNotificationsViewController` se necessário:

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
