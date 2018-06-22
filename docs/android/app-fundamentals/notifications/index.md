---
title: Notificações no xamarin
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: d321c3eb3fe1c882ef9cea6ed1aace3aa1dd953e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762357"
---
# <a name="notifications-in-xamarinandroid"></a>Notificações no xamarin


## <a name="overview"></a>Visão geral

Esta seção explica como implementar notificações em xamarin. Descreve os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvidos com a criação e exibindo uma notificação.


## <a name="sections"></a>Seções

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notificações de locais no Android](local-notifications.md)

Esta seção explica como implementar notificações de locais em xamarin. Descreve os vários elementos de interface do usuário de uma notificação de Android e discutem a API do envolvidos com a criação e exibindo uma notificação. 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Passo a passo - usando notificações Local no xamarin](local-notifications-walkthrough.md)  
 
Este passo a passo aborda como usar notificações locais em um aplicativo xamarin. Ele demonstra os conceitos básicos de criação e publicação de uma notificação. Quando o usuário clica na notificação na gaveta de notificação é iniciado uma segunda atividade. 


## <a name="for-further-reading"></a>Para leitura adicional

[Mensagens de nuvem firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase nuvem mensagens (FCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. Mensagens de nuvem firebase pode ser usado para implementar notificações remotas (também chamadas de notificações por push) em aplicativos xamarin.

[Notificações](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; tópico desenvolvedor Android este é o guia definitivo para notificações do Android. Ele inclui um design de seção considerações que ajuda você a criar as notificações de modo que estão em conformidade com as diretrizes da interface do usuário do Android. Ele fornece mais informações básicas sobre a navegação preserviing ao iniciar uma atividade e explica como exibir o progresso em uma notificação e controle de reprodução de mídia na tela de bloqueio. 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android este serviço permite que seu aplicativo escutar (e interagir com) todas as notificações geradas no dispositivo Android, não apenas as notificações que seu aplicativo está registrado para receber. Observe que o usuário deve fornecer explicitamente permissão ao seu aplicativo para que ele possa ouvir para as notificações no dispositivo.





## <a name="related-links"></a>Links relacionados

- [Notificações de locais (exemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificações remotas (exemplo)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
