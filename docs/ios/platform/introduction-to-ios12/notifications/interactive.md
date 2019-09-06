---
title: Interfaces de usuário de notificação interativa no Xamarin. iOS
description: Com o iOS 12, é possível criar interfaces de usuário interativas para notificações locais e remotas. Este guia descreve como usar esses recursos com o Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 572b369755e37f123fbfdf5850a635e7ada12a9b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291232"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfaces de usuário de notificação interativa no Xamarin. iOS

[As extensões de conteúdo de notificação](~/ios/platform/user-notifications/advanced-user-notifications.md), introduzidas no Ios 10, possibilitam a criação de interfaces de usuário personalizadas para notificações. A partir do iOS 12, as interfaces de usuário de notificação podem conter elementos interativos, como botões e controles deslizantes.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

O aplicativo de exemplo [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) contém uma extensão de conteúdo de notificação com uma interface de usuário interativa.

Trechos de código neste guia vêm deste exemplo.

## <a name="notification-content-extension-infoplist-file"></a>Arquivo info. plist da extensão de conteúdo de notificação

No aplicativo de exemplo, o arquivo **info. plist** no projeto **RedGreenNotificationsContentExtension** contém a seguinte configuração:

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

- A `UNNotificationExtensionCategory` matriz Especifica o tipo de categorias de notificação que a extensão de conteúdo manipula.
- Para oferecer suporte a conteúdo interativo, a extensão de conteúdo de notificação `UNNotificationExtensionUserInteractionEnabled` define a `true`chave como.
- A `UNNotificationExtensionInitialContentSizeRatio` chave especifica a taxa inicial de altura/largura para a interface da extensão de conteúdo.

## <a name="interactive-interface"></a>Interface interativa

**MainInterface. Storyboard**, que define a interface de uma extensão de conteúdo de notificação, é um storyboard padrão que contém um único controlador de exibição. No aplicativo de exemplo, o controlador de exibição é do `NotificationViewController`tipo e contém uma exibição de imagem, três botões e um controle deslizante. O storyboard associa esses controles aos manipuladores definidos em **NotificationViewController.cs**:

- O manipulador do botão **Iniciar aplicativo** chama `PerformNotificationDefaultAction` o método de `ExtensionContext`ação em, que inicia o aplicativo:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    No aplicativo, a central de notificações do `Delegate` usuário (no aplicativo de exemplo, o `AppDelegate`) pode responder `DidReceiveNotificationResponse` à interação no método:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- O manipulador do botão **ignorar notificação** `DismissNotificationContentExtension` chama `ExtensionContext`o, que fecha a notificação:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- O manipulador do botão **Remover notificação** ignora a notificação e a remove da central de notificações:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- O método que manipula alterações de valor no controle deslizante atualiza a alfa da imagem exibida na interface da notificação:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Usernotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notificações avançadas (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
