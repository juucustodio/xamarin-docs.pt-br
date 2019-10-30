---
title: Gerenciamento de notificação no Xamarin. iOS
description: Este documento descreve como usar o Xamarin. iOS para aproveitar os novos recursos de gerenciamento de notificação introduzidos no iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 671b6c00a41d719a7ccb8247fd4a7bc008d91adf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031897"
---
# <a name="notification-management-in-xamarinios"></a>Gerenciamento de notificação no Xamarin. iOS

No iOS 12, o sistema operacional pode se vincular do centro de notificações e do aplicativo de configurações à tela de gerenciamento de notificação de um aplicativo. Essa tela deve permitir que os usuários aceitem e reconheçam os vários tipos de notificações que o aplicativo envia.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Para ver um exemplo de como funciona o gerenciamento de notificações, dê uma olhada no aplicativo de exemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) .

Esse aplicativo de exemplo envia dois tipos de notificações – vermelho e verde – e fornece uma tela que permite que os usuários aceitem ou saiam de qualquer um dos tipos.

Trechos de código neste guia são provenientes deste aplicativo de exemplo.

## <a name="notification-management-screen"></a>Tela de gerenciamento de notificação

No aplicativo de exemplo, `ManageNotificationsViewController` define uma interface do usuário que permite aos usuários habilitar e desabilitar de forma independente as notificações vermelhas e as notificações verdes. É um [`UIViewController`](xref:UIKit.UIViewController) padrão
que contém um [`UISwitch`](xref:UIKit.UISwitch) para cada tipo de notificação. Alternar a opção para qualquer tipo de notificação salva, em padrões do usuário, a preferência do usuário para esse tipo de notificação:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> A tela de gerenciamento de notificação também verifica se o usuário desabilitou completamente as notificações para o aplicativo. Nesse caso, ele oculta as alternâncias para os tipos de notificação individuais. Para fazer isso, a tela de gerenciamento de notificação:
>
> - Chama [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) e examina a propriedade [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) .
> - Oculta as alternâncias para os tipos de notificação individuais se as notificações tiverem sido completamente desabilitadas para o aplicativo.
> - Verifica novamente se as notificações foram desabilitadas sempre que o aplicativo passa para o primeiro plano, pois o usuário pode habilitar/desabilitar notificações nas configurações do iOS a qualquer momento.

A classe `ViewController` do aplicativo de exemplo, que envia as notificações, verifica a preferência do usuário antes de enviar uma notificação local para garantir que a notificação seja de um tipo que o usuário realmente deseja receber:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Link profundo

o iOS detalha a tela de gerenciamento de notificação de um aplicativo no centro de notificações e as configurações de notificação do aplicativo no aplicativo configurações. Para facilitar isso, um aplicativo deve:

- Indique que uma tela de gerenciamento de notificação está disponível passando `UNAuthorizationOptions.ProvidesAppNotificationSettings` para a solicitação de autorização de notificação do aplicativo.
- Implemente o método `OpenSettings` de [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate).

### <a name="authorization-request"></a>Solicitação de autorização

Para indicar ao sistema operacional que uma tela de gerenciamento de notificação está disponível, um aplicativo deve passar a opção `UNAuthorizationOptions.ProvidesAppNotificationSettings` (juntamente com quaisquer outras opções de entrega de notificação necessárias) para o método `RequestAuthorization` no `UNUserNotificationCenter`.

Por exemplo, no `AppDelegate`do aplicativo de exemplo:

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

O método `OpenSettings`, chamado pelo sistema para um link profundo para a tela de gerenciamento de notificação de um aplicativo, deve navegar pelo usuário diretamente para essa tela.

No aplicativo de exemplo, esse método executa o transição para o `ManageNotificationsViewController`, se necessário:

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

- [Aplicativo de exemplo – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Usernotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
