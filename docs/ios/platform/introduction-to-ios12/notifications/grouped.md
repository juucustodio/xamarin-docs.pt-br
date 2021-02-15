---
title: Notificações agrupadas no Xamarin. iOS
description: Com o iOS 12, é possível agrupar notificações no centro de notificações ou na tela de bloqueio por aplicativo ou por thread. Este documento descreve como enviar notificações segmentadas e não threaddas com o Xamarin. iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 8c440d61a41fc26aa7537dc169be08c2bf413c68
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435220"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notificações agrupadas no Xamarin. iOS

Por padrão, o iOS 12 coloca todas as notificações de um aplicativo em um grupo. A tela de bloqueio e o centro de notificação exibem esse grupo como uma pilha com a notificação mais recente na parte superior. Os usuários podem expandir o grupo para ver todas as notificações que ele contém e ignorar o grupo como um todo.

Os aplicativos também podem agrupar notificações por thread, facilitando para os usuários a localização e a interação com as informações específicas em que estão interessados.

## <a name="sample-app-groupednotifications"></a>Aplicativo de exemplo: GroupedNotifications

Para saber como usar notificações agrupadas com o Xamarin. iOS, dê uma olhada no aplicativo de exemplo [GroupedNotifications](/samples/xamarin/ios-samples/ios12-groupednotifications) .

Este aplicativo de exemplo simula conversas com vários amigos, enviando uma notificação para cada mensagem e agrupando-as por thread. Ele também demonstra como as notificações não encadeadas se estornam em um grupo de nível de aplicativo.

Trechos de código neste guia são provenientes deste aplicativo de exemplo.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Solicitar autorização e permitir notificações em primeiro plano

Antes que um aplicativo possa enviar notificações locais, ele deve solicitar permissão para fazer isso. No aplicativo de exemplo [`AppDelegate`](xref:UIKit.UIApplicationDelegate) , o [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) método solicita essa permissão:

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

O [`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate) (definido acima) para um [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) decide se um aplicativo em primeiro plano deve ou não exibir uma notificação de entrada chamando o manipulador de conclusão passado para [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})) :

```csharp
[Export("userNotificationCenter:willPresentNotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

O [`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions) parâmetro indica que o aplicativo deve mostrar o alerta, mas não reproduzir um som ou atualizar um crachá.

## <a name="threaded-notifications"></a>Notificações encadeadas

Toque na mensagem do aplicativo de exemplo com o botão **Alice** repetidamente para fazer com que ele envie notificações para uma conversa com um amigo chamado Alice.
Como as notificações desta conversa fazem parte do mesmo thread, a tela de bloqueio e o centro de notificação agrupam-se.

Para iniciar uma conversa com um amigo diferente, toque no botão **escolher um novo amigo** . As notificações para essa conversa aparecem em um grupo separado.

### <a name="threadidentifier"></a>ThreadIdentifier

Sempre que o aplicativo de exemplo iniciar um novo thread, ele criará um identificador de thread exclusivo:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Para enviar uma notificação encadeada, o aplicativo de exemplo:

- Verifica se o aplicativo tem autorização para enviar uma notificação.
- Cria um [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
objeto para o conteúdo da notificação e define seu [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
para o identificador de thread criado acima.
- Cria uma solicitação e agenda a notificação:

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

Todas as notificações do mesmo aplicativo com o mesmo identificador de thread aparecerão no mesmo grupo de notificação.

> [!NOTE]
> Para definir um identificador de thread em uma notificação remota, adicione a `thread-id` chave à carga JSON da notificação. Consulte [a criação de um documento de notificação remoto](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) da Apple para obter mais detalhes.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` Especifica como uma notificação afetará o texto de resumo que aparece no canto inferior esquerdo de um grupo de notificação ao qual a notificação pertence. o iOS agrega o texto de Resumo de notificações no mesmo grupo para criar uma descrição geral do resumo.

O aplicativo de exemplo usa o autor da mensagem como o argumento de resumo. Usando essa abordagem, o texto de Resumo de um grupo de seis notificações com Alice pode ser **mais de 5 notificações de Alice e eu**.

## <a name="unthreaded-notifications"></a>Notificações não segmentadas

Cada toque do botão de **lembrete de compromissos** do aplicativo de exemplo envia uma das várias notificações de lembrete de compromissos. Como esses lembretes não são segmentados, eles aparecem no grupo de notificação no nível do aplicativo na tela de bloqueio e no centro de notificações.

Para enviar uma notificação não threaded, o método do aplicativo de exemplo `ScheduleUnthreadedNotification` usa um código semelhante como acima.
No entanto, ele não define o `ThreadIdentifier` no `UNMutableNotificationContent` objeto.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – GroupedNotifications](/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Estrutura de notificações do usuário no Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [O que há de novo nas notificações do usuário (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Usando notificações agrupadas (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Práticas recomendadas e o que há de novo nas notificações do usuário (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Gerando uma notificação remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)