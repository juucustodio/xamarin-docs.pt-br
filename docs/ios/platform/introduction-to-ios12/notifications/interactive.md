---
title: Interfaces do usuário interativo de notificação no xamarin. IOS
description: Com o iOS 12, é possível criar interfaces do usuário interativa para notificações locais e remotas. Este guia descreve como usar esses recursos com o xamarin. IOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: e6dc2f14b36c9d6f67f1df5ad3d118fa423e0d4d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034907"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfaces do usuário interativo de notificação no xamarin. IOS

[Extensões de conteúdo de notificação](~/ios/platform/user-notifications/advanced-user-notifications.md), introduzida no iOS 10, tornam possível criar interfaces do usuário personalizadas para notificações. Começando com o iOS 12, interfaces de usuário de notificação podem conter elementos interativos como botões e controles deslizantes.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

O [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicativo de exemplo contém uma extensão de conteúdo de notificação com uma interface de usuário interativo.

Trechos de código neste guia são provenientes neste exemplo.

## <a name="notification-content-extension-infoplist-file"></a>Arquivo de info. plist da extensão de conteúdo de notificação

No aplicativo de exemplo, o **Info. plist** arquivo na **RedGreenNotificationsContentExtension** projeto contém a seguinte configuração:

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Observe os seguintes recursos:

- O `UNNotificationExtensionCategory` matriz Especifica o tipo de categorias de notificação as alças de extensão de conteúdo.
- Para dar suporte a conteúdo interativo, a extensão de conteúdo da notificação define os `UNNotificationExtensionUserInteractionEnabled` chave `true`.
- O `UNNotificationExtensionInitialContentSizeRatio` chave especifica a taxa de largura/altura inicial para a interface da extensão de conteúdo.

## <a name="interactive-interface"></a>Interface interativa

**MainInterface.storyboard**, que define a interface para uma extensão de conteúdo de notificação, é um padrão storyboard que contém um controlador de exibição único. No aplicativo de exemplo, o controlador de exibição é do tipo `NotificationViewController`, e que ela contém uma exibição de imagem, três botões e um controle deslizante. O storyboard associa esses controles com manipuladores definidos em **NotificationViewController.cs**:

- O **aplicativo inicie** chamadas do manipulador de botão a `PerformNotificationDefaultAction` método de ação na `ExtensionContext`, que inicia o aplicativo:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    No, o Centro de notificação do usuário do aplicativo `Delegate` (no aplicativo de exemplo, o `AppDelegate`) pode responder à interação no `DidReceiveNotificationResponse` método:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- O **descartar notificação** chamadas do manipulador de botão `DismissNotificationContentExtension` em `ExtensionContext`, que fecha a notificação:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- O **remover notificação** manipulador do botão descarta a notificação e a remove do Centro de notificações:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- O método que manipula as alterações de valor no controle deslizante atualiza o alfa da imagem exibida na interface da notificação:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notificações de rich (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
