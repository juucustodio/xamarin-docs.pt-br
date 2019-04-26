---
title: Notificações agrupadas no xamarin. IOS
description: Com o iOS 12, é possível as notificações de grupo no Centro de notificação ou tela de bloqueio por aplicativo ou pelo thread. Este documento descreve como enviar multithread e notificações não encadeadas com xamarin. IOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 6798c4c5fa7502ba5e99cb8bc209468acaa4a9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402415"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notificações agrupadas no xamarin. IOS

Por padrão, o iOS 12 coloca todas as notificações de um aplicativo em um grupo. O Centro de notificação e tela de bloqueio exibem este grupo como uma pilha com a notificação mais recente na parte superior. Os usuários podem expandir o grupo para ver todas as notificações que ele contém e descartar o grupo como um todo.

Aplicativos podem também notificações de grupo por thread, tornando mais fácil para os usuários encontrem e interagir com as informações específicas que eles estão interessados.

## <a name="sample-app-groupednotifications"></a>Aplicativo de exemplo: GroupedNotifications

Para saber como usar notificações agrupadas com xamarin. IOS, dê uma olhada a [GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications) aplicativo de exemplo.

Este aplicativo de exemplo simula conversas com vários amigos, enviando uma notificação para cada mensagem e agrupá-los por thread. Ele também demonstra como não encadeado land notificações em um grupo de nível de aplicativo.

Trechos de código neste guia são provenientes este aplicativo de exemplo.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Solicitar autorização e permitir as notificações de primeiro plano

Antes que um aplicativo pode enviar notificações locais, ele deve solicitar permissão para fazer isso. No aplicativo de exemplo [ `AppDelegate` ](xref:UIKit.UIApplicationDelegate), o [ `FinishedLaunching` ](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) método solicita essa permissão:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

O [ `Delegate` ](xref:UserNotifications.UNUserNotificationCenter.Delegate) (definido acima) para um [ `UNUserNotificationCenter` ](xref:UserNotifications.UNUserNotificationCenter) decide se um aplicativo em primeiro plano deve exibir uma notificação de entrada chamando o manipulador de conclusão passado para [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

O [ `UNNotificationPresentationOptions.Alert` ](xref:UserNotifications.UNNotificationPresentationOptions) parâmetro indica que o aplicativo deve mostrar o alerta, mas não reproduzir um som ou atualizar uma notificação.

## <a name="threaded-notifications"></a>Notificações de threads

Toque no aplicativo de exemplo **mensagem de Alice** botão repetidamente para fazê-lo enviar notificações para uma conversa com um amigo chamado Alice.
Como notificações dessa conversa fazem parte do mesmo thread, o Centro de notificação e tela de bloqueio agrupá-las.

Para iniciar uma conversa com um amigo diferente, toque o **escolher um novo amigo** botão. As notificações para esta conversa aparecem em um grupo separado.

### <a name="threadidentifier"></a>ThreadIdentifier

Sempre que o aplicativo de exemplo inicia um novo thread, ele cria um identificador exclusivo do thread:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Para enviar uma notificação multithread, o aplicativo de exemplo:

- Verifica se o aplicativo tem autorização para enviar uma notificação.
- Cria um [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
objeto para a notificação de conteúdo e define seu [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
para o identificador de thread criado acima.
- Cria uma solicitação e a notificação de agenda:

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Todas as notificações do mesmo aplicativo com o mesmo identificador de thread serão exibido no mesmo grupo de notificação.

> [!NOTE]
> Para definir um identificador de thread em uma notificação remota, adicione o `thread-id` chave para a carga JSON da notificação. Consulte da Apple [gerar uma notificação remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento para obter mais detalhes.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` Especifica como uma notificação afetará o texto do resumo que aparece no canto inferior esquerdo de um grupo de notificação à qual pertence a notificação. iOS agrega o texto do resumo de notificações no mesmo grupo para criar uma descrição geral resumida.

O aplicativo de exemplo usa o autor da mensagem como o argumento de resumo. Usando essa abordagem, o texto do resumo de um grupo de seis notificações com Alice pode ser **5 mais notificações de Alice e Me**.

## <a name="unthreaded-notifications"></a>Notificações não encadeadas

Cada toque do aplicativo de exemplo **lembrete de compromisso** botão envia uma das várias notificações de lembrete de compromisso. Uma vez que esses lembretes não estão em segmentos, eles aparecem no grupo de notificação em nível de aplicativo na tela de bloqueio e no Centro de notificações.

Para enviar uma não encadeada, o aplicativo de exemplo da notificação `ScheduleUnthreadedNotification` método usa um código semelhante como acima.
No entanto, ele não define o `ThreadIdentifier` sobre o `UNMutableNotificationContent` objeto.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [Estrutura de notificações de usuário no xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [Quais são as novidades em notificações de usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Usando notificações agrupadas (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Práticas recomendadas e quais são as novidades em notificações de usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerar uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
