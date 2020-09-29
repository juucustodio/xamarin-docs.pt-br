---
title: Notificações no Xamarin. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: ce19a286a288846a67b73e77d31a5da4c77a2462
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455580"
---
# <a name="notifications-in-xamarinandroid"></a>Notificações no Xamarin. Android

Esta seção explica como implementar notificações no Xamarin. Android. Ele descreve os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação.

## <a name="local-notifications-in-android"></a>[Notificações locais no Android](local-notifications.md)

Esta seção explica como implementar notificações locais no Xamarin. Android. Ele descreve os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação.

## <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>[Walkthrough – usando notificações locais no Xamarin. Android](local-notifications-walkthrough.md)  

Este tutorial explica como usar notificações locais em um aplicativo Xamarin. Android. Ele demonstra as noções básicas de criação e publicação de uma notificação. Quando o usuário clica na notificação na gaveta de notificação, ele inicia uma segunda atividade. 

## <a name="further-reading"></a>Leitura adicional

Mensagens de nuvem [firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; O FCM (firebase Cloud Messaging) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. O firebase Cloud Messaging pode ser usado para implementar notificações remotas (também chamadas de notificações por push) em aplicativos Xamarin. Android.

[Notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; do Este tópico do desenvolvedor do Android é o guia definitivo para notificações do Android. Ele inclui uma seção considerações de design que ajuda você a projetar suas notificações para que elas estejam em conformidade com as diretrizes da interface do usuário do Android. Ele fornece mais informações básicas sobre como preservar a navegação ao iniciar uma atividade e explica como exibir o progresso em uma notificação e controlar a reprodução de mídia na tela de bloqueio.

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; Esse serviço Android possibilita que seu aplicativo Ouça (e interaja com) todas as notificações postadas no dispositivo Android, não apenas as notificações que seu aplicativo está registrado para receber.
Observe que o usuário deve conceder explicitamente permissão ao seu aplicativo para que ele possa escutar notificações no dispositivo.

## <a name="related-links"></a>Links relacionados

- [Notificações locais (exemplo)](/samples/xamarin/monodroid-samples/localnotifications)