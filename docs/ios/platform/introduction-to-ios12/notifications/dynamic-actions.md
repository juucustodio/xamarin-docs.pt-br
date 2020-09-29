---
title: Botões de ação de notificação dinâmica no Xamarin. iOS
description: Com o iOS 12, uma extensão de conteúdo de notificação pode adicionar, remover e atualizar os botões de ação exibidos junto com uma notificação. Este documento descreve como usar botões de ação de notificação dinâmica com o Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 88eb3dec0c72acd6984b226eae5acee4feb3eaac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433824"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Botões de ação de notificação dinâmica no Xamarin. iOS

No iOS 12, as notificações podem adicionar, remover e atualizar dinamicamente seus botões de ação associados. Essa personalização possibilita fornecer aos usuários ações que são diretamente relevantes para o conteúdo da notificação e a interação do usuário com ele.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Os trechos de código neste guia são provenientes do aplicativo de exemplo [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) , que demonstra como usar o Xamarin. Ios para trabalhar com botões de ação de notificação no IOS 12.

Este aplicativo de exemplo envia dois tipos de notificações locais: vermelho e verde.
Depois de fazer com que o aplicativo envie uma notificação, use o 3D Touch para exibir sua interface do usuário personalizada. Em seguida, use os botões de ação da notificação para girar a imagem exibida. À medida que a imagem gira, um botão **Redefinir rotação** é exibido e desaparece conforme necessário.

Trechos de código neste guia são provenientes deste aplicativo de exemplo.

## <a name="default-action-buttons"></a>Botões de ação padrão

A categoria de uma notificação determina seus botões de ação padrão.

Crie e registre categorias de notificação enquanto um aplicativo é iniciado.
Por exemplo, no [aplicativo de exemplo](#sample-app-redgreennotifications), o `FinishedLaunching` método de `AppDelegate` faz o seguinte:

- Define uma categoria para notificações vermelhas e outra para notificações verdes
- Registra essas categorias chamando o [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
método de `UNUserNotificationCenter`
- Anexa um único [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
para cada categoria

O código de exemplo a seguir mostra como isso funciona:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

Com base nesse código, qualquer notificação cujo [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
é "Red-category" ou "Green-category", por padrão, mostrará um botão de ação **girar 20 °** .

## <a name="in-app-handling-of-notification-action-buttons"></a>Manipulação no aplicativo de botões de ação de notificação

`UNUserNotificationCenter` tem uma `Delegate` Propriedade do tipo [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) .

No aplicativo de exemplo, ele `AppDelegate` se define como o delegado do centro de notificação do usuário em `FinishedLaunching` :

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

Em seguida, `AppDelegate` implementa [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
para lidar com toques de botão de ação:

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Essa implementação do não `DidReceiveNotificationResponse` manipula o botão de ação **girar 20 °** da notificação. Em vez disso, a extensão de conteúdo da notificação lida com toques nesse botão. A próxima seção aborda ainda mais o tratamento do botão de ação de notificação.

## <a name="action-buttons-in-the-notification-content-extension"></a>Botões de ação na extensão de conteúdo de notificação

Uma extensão de conteúdo de notificação contém um controlador de exibição que define a interface personalizada para uma notificação.

Este controlador de exibição pode usar `GetNotificationActions` os `SetNotificationActions` métodos e em seu [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
Propriedade para acessar e modificar os botões de ação da notificação.

No aplicativo de exemplo, o controlador de exibição da extensão de conteúdo de notificação modifica os botões de ação somente ao responder a um toque em um botão de ação já existente.

> [!NOTE]
> Uma extensão de conteúdo de notificação pode responder a um botão de ação tocar no método do controlador de exibição [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) , declarado como parte de [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Embora Compartilhe um nome com o `DidReceiveNotificationResponse` método [descrito acima](#in-app-handling-of-notification-action-buttons), esse é um método diferente.
>
> Depois que uma extensão de conteúdo de notificação termina de processar um toque de botão, ela pode escolher se quer ou não instruir o aplicativo principal a lidar com o mesmo toque de botão. Para fazer isso, ele deve passar um valor apropriado de [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) para seu manipulador de conclusão:
>
> - `Dismiss` indica que a interface de notificação deve ser ignorada e que o aplicativo principal não precisa manipular o toque do botão.
> - `DismissAndForwardAction` indica que a interface de notificação deve ser ignorada e que o aplicativo principal também deve lidar com o toque do botão.
> - `DoNotDismiss` indica que a interface de notificação não deve ser ignorada e que o aplicativo principal não precisa manipular o toque do botão.

O método da extensão de conteúdo `DidReceiveNotificationResponse` determina qual botão de ação foi tocado, gira a imagem na interface da notificação e mostra ou oculta um botão de ação de **redefinição** :

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

Nesse caso, o método passa `UNNotificationContentExtensionResponseOption.DoNotDismiss` para seu manipulador de conclusão. Isso significa que a interface da notificação permanecerá aberta.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Declarando seus tipos de notificação acionáveis](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [Usernotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)