---
title: Gerenciamento de notificação no Xamarin. iOS
description: Este documento descreve como usar o Xamarin. iOS para aproveitar os novos recursos de gerenciamento de notificação introduzidos no iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: b6c6baad2cbd923bde4dab3766040b5df4351787
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282124"
---
# <a name="notification-management-in-xamarinios"></a>Gerenciamento de notificação no Xamarin. iOS

No iOS 12, o sistema operacional pode se vincular do centro de notificações e do aplicativo de configurações à tela de gerenciamento de notificação de um aplicativo. Essa tela deve permitir que os usuários aceitem e reconheçam os vários tipos de notificações que o aplicativo envia.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Para ver um exemplo de como funciona o gerenciamento de notificações, dê uma olhada no aplicativo de exemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) .

Esse aplicativo de exemplo envia dois tipos de notificações – vermelho e verde – e fornece uma tela que permite que os usuários aceitem ou saiam de qualquer um dos tipos.

Trechos de código neste guia são provenientes deste aplicativo de exemplo.

## <a name="notification-management-screen"></a>Tela de gerenciamento de notificação

No aplicativo de exemplo, `ManageNotificationsViewController` define uma interface do usuário que permite aos usuários habilitar e desabilitar de forma independente as notificações vermelhas e as notificações verdes. É um padrão[`UIViewController`](xref:UIKit.UIViewController)
que contém [`UISwitch`](xref:UIKit.UISwitch) um para cada tipo de notificação. Alternar a opção para qualquer tipo de notificação salva, em padrões do usuário, a preferência do usuário para esse tipo de notificação:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> A tela de gerenciamento de notificação também verifica se o usuário desabilitou completamente as notificações para o aplicativo. Nesse caso, ele oculta as alternâncias para os tipos de notificação individuais. Para fazer isso, a tela de gerenciamento de notificação:
>
> - Chama [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) e examina a [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) propriedade.
> - Oculta as alternâncias para os tipos de notificação individuais se as notificações tiverem sido completamente desabilitadas para o aplicativo.
> - Verifica novamente se as notificações foram desabilitadas sempre que o aplicativo passa para o primeiro plano, pois o usuário pode habilitar/desabilitar notificações nas configurações do iOS a qualquer momento.

A classe do `ViewController` aplicativo de exemplo, que envia as notificações, verifica a preferência do usuário antes de enviar uma notificação local para garantir que a notificação seja de um tipo que o usuário realmente deseja receber:

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

- Indique que uma tela de gerenciamento de notificação está `UNAuthorizationOptions.ProvidesAppNotificationSettings` disponível passando para a solicitação de autorização de notificação do aplicativo.
- Implemente `OpenSettings` o método [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate)de.

### <a name="authorization-request"></a>Solicitação de autorização

Para indicar ao sistema operacional que uma tela de gerenciamento de notificação está disponível, um aplicativo deve passar `UNAuthorizationOptions.ProvidesAppNotificationSettings` a opção (juntamente com quaisquer outras opções de entrega de notificação necessárias) `RequestAuthorization` para o método `UNUserNotificationCenter`no.

Por exemplo, no aplicativo `AppDelegate`de exemplo:

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

O `OpenSettings` método, chamado pelo sistema para o link profundo para a tela de gerenciamento de notificação de um aplicativo, deve navegar pelo usuário diretamente para essa tela.

No aplicativo de exemplo, esse método executa o transição para o `ManageNotificationsViewController` , se necessário:

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
