---
title: Google Messaging
description: Esta seção contém guias que descrevem como implementar aplicativos Xamarin. Android usando os serviços de mensagens do Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/12/2018
ms.openlocfilehash: e0d257cd5d443c77fd0a8132d9cd31ec572182aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023527"
---
# <a name="google-messaging"></a>Google Messaging

_Esta seção contém guias que descrevem como implementar aplicativos Xamarin. Android usando os serviços de mensagens do Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Mensagens na nuvem do Firebase](firebase-cloud-messaging.md)

O FCM (firebase Cloud Messaging) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. FCM é o sucessor do Google para Google Cloud Messaging. Este artigo fornece uma visão geral de como o FCM funciona e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo possa usar os serviços do FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notificações remotas com o firebase Cloud Messaging](remote-notifications-with-fcm.md)

Este tutorial fornece uma explicação passo a passo de como usar o firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo Xamarin. Android. Ele ilustra como implementar as várias classes que são necessárias para comunicações com o firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o sistema de mensagens downstream usando o firebase MMC.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

Esta seção fornece uma visão geral de alto nível de como o Google Cloud Messaging (GCM) roteia mensagens entre seu aplicativo e um servidor de aplicativos e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo possa usar os serviços do GCM. (Observe que o GCM foi substituído por FCM.)

> [!NOTE]
> O GCM foi substituído pelo [firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> As APIs de servidor e cliente do GCM foram [preteridas](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estarão mais disponíveis assim que 11 de abril, 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notificações remotas com Google Cloud Messaging](remote-notifications-with-gcm.md)

Esta seção fornece uma explicação passo a passo de como implementar notificações remotas no Xamarin. Android usando o Google Cloud Messaging.
Ele explica os vários componentes que devem ser aproveitados para habilitar o Google Cloud Messaging em um aplicativo Android.
