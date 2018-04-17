---
title: Mensagens do Google
description: Esta seção contém guias que descrevem como implementar aplicativos xamarin usando serviços de mensagens do Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: cf1eaec3dfee7c3457a4614147c9b5564843b2a7
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="google-messaging"></a>Mensagens do Google

_Esta seção contém guias que descrevem como implementar aplicativos xamarin usando serviços de mensagens do Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Mensagens na nuvem do Firebase](firebase-cloud-messaging.md)

Mensagens de nuvem firebase (FCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. FCM é o sucessor do Google Google Cloud Messaging. Este artigo fornece uma visão geral de como funciona o FCM e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo pode usar serviços FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notificações remotas com Firebase mensagens de nuvem](remote-notifications-with-fcm.md)

Este passo a passo fornece uma explicação passo a passo de como usar mensagens de nuvem Firebase para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Ele ilustra como implementar várias classes que são necessários para as comunicações com o sistema de mensagens de nuvem com a Firebase (FCM), fornece exemplos de como configurar o manifesto do Android para acessar FCM e demonstra o downstream de mensagens usando o Firebase Console.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

Esta seção fornece uma visão geral de como o Google Cloud Messaging (GCM) encaminha mensagens entre seu aplicativo e um servidor de aplicativo, e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo pode usar os serviços do GCM. (Observe que o GCM foi substituído por FCM).

> [!NOTE]
> GCM foi substituído por [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM APIs de cliente e servidor [foram preteridos](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estará disponível assim que 11 de abril de 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notificações remotas com o Google Cloud Messaging](remote-notifications-with-gcm.md)

Esta seção fornece uma explicação passo a passo de como implementar notificações remotas em xamarin usando o Google Cloud Messaging.
Explica os vários componentes que devem ser utilizados para habilitar o Google Cloud Messaging em um aplicativo do Android.


