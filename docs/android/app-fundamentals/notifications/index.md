---
title: Notificações no Xamarin. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 5d91904e35a658b03d4602567e5a123cafd6926c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025530"
---
# <a name="notifications-in-xamarinandroid"></a>Notificações no Xamarin. Android

Esta seção explica como implementar notificações no Xamarin. Android. Ele descreve os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação.

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notificações locais no Android](local-notifications.md)

Esta seção explica como implementar notificações locais no Xamarin. Android. Ele descreve os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação.

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Walkthrough – usando notificações locais no Xamarin. Android](local-notifications-walkthrough.md)  

Este tutorial explica como usar notificações locais em um aplicativo Xamarin. Android. Ele demonstra as noções básicas de criação e publicação de uma notificação. Quando o usuário clica na notificação na gaveta de notificação, ele inicia uma segunda atividade. 

## <a name="further-reading"></a>Leitura adicional

O [firebase Cloud messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; o FCM (firebase Cloud Messaging) é um serviço que facilita as mensagens entre aplicativos móveis e de servidor. O firebase Cloud Messaging pode ser usado para implementar notificações remotas (também chamadas de notificações por push) em aplicativos Xamarin. Android.

[Notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; este tópico do desenvolvedor do Android é o guia definitivo para notificações do Android. Ele inclui uma seção considerações de design que ajuda você a projetar suas notificações para que elas estejam em conformidade com as diretrizes da interface do usuário do Android. Ele fornece mais informações básicas sobre a navegação preserviing ao iniciar uma atividade e explica como exibir o progresso em uma notificação e controlar a reprodução de mídia na tela de bloqueio.

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; esse serviço Android possibilita que seu aplicativo Ouça (e interaja com) todas as notificações postadas no dispositivo Android, não apenas as notificações que seu aplicativo está registrado para receber.
Observe que o usuário deve conceder explicitamente permissão ao seu aplicativo para que ele possa escutar notificações no dispositivo.

## <a name="related-links"></a>Links relacionados

- [Notificações locais (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Notificações remotas (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
