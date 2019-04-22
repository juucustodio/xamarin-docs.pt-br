---
title: Botões de ação de notificação dinâmico no xamarin. IOS
description: Com o iOS 12, uma extensão de conteúdo de notificação pode adicionar, remover e atualizar os botões de ação exibidos ao lado de uma notificação. Este documento descreve como usar os botões de ação de notificação dinâmico com xamarin. IOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 5611d673ecc7af896fd3a9e566e184e408b6b367
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870086"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Botões de ação de notificação dinâmico no xamarin. IOS

No iOS 12, notificações dinamicamente podem adicionar, remover e atualizar seus botões de ação associada. Tal personalização torna possível fornecer aos usuários com as ações que são diretamente relevantes para o conteúdo da notificação e a interação do usuário com ele.

## <a name="sample-app-redgreennotifications"></a>Aplicativo de exemplo: RedGreenNotifications

Os trechos de código neste guia são provenientes de [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) aplicativo de exemplo que demonstra como usar o xamarin. IOS para trabalhar com os botões de ação de notificação no iOS 12.

Este aplicativo de exemplo envia dois tipos de notificações locais: vermelho e verde.
Depois de ter o aplicativo enviar uma notificação, use o 3D Touch para exibir sua interface de usuário personalizada. Em seguida, use os botões de ação da notificação para girar a imagem que ele exibe. Como gira a imagem, uma **redefinir rotação** botão aparece e desaparece conforme necessário.

Trechos de código neste guia são provenientes este aplicativo de exemplo.

## <a name="default-action-buttons"></a>Botões de ação padrão

Categoria de uma notificação determina seus botões de ação padrão.

Crie e registre as categorias de notificação enquanto um aplicativo é iniciado.
Por exemplo, nos [aplicativo de exemplo](#sample-app-redgreennotifications), o `FinishedLaunching` método `AppDelegate` faz o seguinte:

- Define uma categoria para notificações vermelhas e outro para notificações verdes
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

Com base nesse código, qualquer notificação cuja [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
será "vermelho-categoria" ou "verde-", por padrão, mostrar um **girar 20°** botão de ação.

## <a name="in-app-handling-of-notification-action-buttons"></a>No aplicativo tratamento dos botões de ação de notificação

`UNUserNotificationCenter` tem um `Delegate` propriedade do tipo [ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate).

No aplicativo de exemplo, `AppDelegate` se define como representante da Central de notificação do usuário em `FinishedLaunching`:

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
para lidar com o botão de ação toca:

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

Essa implementação do `DidReceiveNotificationResponse` não processa a notificação **girar 20°** botão de ação. Em vez disso, a extensão de conteúdo da notificação manipula toques neste botão. Ainda mais a próxima seção aborda o tratamento do botão de ação de notificação.

## <a name="action-buttons-in-the-notification-content-extension"></a>Botões de ação na extensão de conteúdo de notificação

Uma extensão de conteúdo de notificação contém um controlador de exibição que define a interface personalizada para uma notificação.

Esse controlador de exibição pode usar o `GetNotificationActions` e `SetNotificationActions` métodos em seu [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
propriedade para acessar e modificar os botões de ação da notificação.

No aplicativo de exemplo, o controlador de exibição da extensão de conteúdo de notificação modifica os botões de ação somente ao responder a um toque de um botão de ação já existente.

> [!NOTE]
> Uma extensão de conteúdo pode responder a um toque de botão de ação em seu controlador de exibição de notificação [ `DidReceiveNotificationResponse` ](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) método, declarado como parte da [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Embora ele compartilha um nome com o `DidReceiveNotificationResponse` método [descrito acima](#in-app-handling-of-notification-action-buttons), esse é um método diferente.
>
> Depois que uma extensão de conteúdo de notificação termina de processar um toque de botão, ele pode escolher se deseja ou não informar o aplicativo principal para lidar com esse mesmo toque de botão. Para fazer isso, ele deve passar um valor apropriado igual [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) para seu manipulador de conclusão:
>
> - `Dismiss` indica que a interface de notificação deve ser descartada, e que o aplicativo principal não precisa lidar com o toque de botão.
> - `DismissAndForwardAction` indica que a interface de notificação deve ser descartada, e que o aplicativo principal também deve lidar com o toque de botão.
> - `DoNotDismiss` indica que a interface de notificação não deve ser ignorada, e que o aplicativo principal não precisa lidar com o toque de botão.

A extensão de conteúdo `DidReceiveNotificationResponse` método determina qual botão de ação foi tocada, gira a imagem da interface da notificação e mostra ou oculta uma **redefinir** botão de ação:

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

Nesse caso, o método passa `UNNotificationContentExtensionResponseOption.DoNotDismiss` para seu manipulador de conclusão. Isso que significa que a interface da notificação permanecerá aberta.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [Declarando tipos sua notificação acionáveis](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
