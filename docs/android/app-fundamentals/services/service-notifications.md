---
title: Notificações de serviço
description: Este guia discute como um serviço Android pode usar notificações locais para enviar informações a um usuário.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b02785863f89ef6a273c52c09f45a99c17cb6242
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024527"
---
# <a name="service-notifications"></a>Notificações de serviço

_Este guia discute como um serviço Android pode usar notificações locais para enviar informações a um usuário._

## <a name="service-notifications-overview"></a>Visão geral das notificações de serviço

As notificações de serviço permitem que um aplicativo exiba informações para o usuário, mesmo que o aplicativo do Android não esteja em primeiro plano. É possível que uma notificação forneça ações para o usuário, como exibir uma atividade de um aplicativo. O exemplo de código a seguir demonstra como um serviço pode enviar uma notificação para um usuário:

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

Esta captura de tela é um exemplo da notificação que é exibida:

[![ícone de notificação exibido na barra de status](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Quando o usuário desliza para baixo na tela de notificação na parte superior, a notificação completa é exibida:

![Exibição exibida na bandeja de notificação](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>Atualizando uma notificação

Para atualizar uma notificação, o serviço republicará a notificação usando a mesma ID de notificação. O Android exibirá ou atualizará a notificação na barra de status, conforme necessário.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Mais informações sobre notificações estão disponíveis na seção [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md) do guia de [notificações do Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="related-links"></a>Links relacionados

- [Notificações locais no Android](~/android/app-fundamentals/notifications/local-notifications.md)
