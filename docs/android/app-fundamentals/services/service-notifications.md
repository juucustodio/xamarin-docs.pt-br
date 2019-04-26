---
title: Notificações de serviço
description: Este guia aborda como um serviço do Android pode usar notificações locais para enviar informações para um usuário.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012266"
---
# <a name="service-notifications"></a>Notificações de serviço

_Este guia aborda como um serviço do Android pode usar notificações locais para enviar informações para um usuário._


## <a name="service-notifications-overview"></a>Visão geral de notificações de serviço

Notificações de serviço permitem que um aplicativo para exibir informações ao usuário, mesmo se o aplicativo Android não está em primeiro plano. É possível que uma notificação fornecer ações do usuário, como a exibição de uma atividade de um aplicativo. O exemplo de código a seguir demonstra como um serviço pode enviar uma notificação a um usuário:

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

Esta captura de tela é um exemplo de notificação que é exibido:

[![Ícone de notificação exibida na barra de status](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Quando os slides do usuário para baixo a tela de notificação na parte superior, a notificação completa é exibida:

![Notificação para exibido na bandeja de notificação](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>Atualizando uma notificação

Para atualizar uma notificação, o serviço será republicar a notificação usando a mesma ID de notificação. Android exibirá ou atualizar a notificação na barra de status conforme necessário.

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

Para obter mais informações sobre notificações estão disponíveis na [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md) seção o [notificações do Android](~/android/app-fundamentals/notifications/index.md) guia.


## <a name="related-links"></a>Links relacionados

- [Notificações locais no Android](~/android/app-fundamentals/notifications/local-notifications.md)
