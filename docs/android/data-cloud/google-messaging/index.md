---
title: Sistema de mensagens do Google
description: Esta seção contém as guias que descrevem como implementar os aplicativos xamarin. Android usando os serviços de mensagens do Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: 8c2e3705f0f867b6993d0bdcb22f6672c853498e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121900"
---
# <a name="google-messaging"></a>Sistema de mensagens do Google

_Esta seção contém as guias que descrevem como implementar os aplicativos xamarin. Android usando os serviços de mensagens do Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Mensagens na nuvem do Firebase](firebase-cloud-messaging.md)

O firebase Cloud Messaging (FCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. FCM é o sucessor do Google para o Google Cloud Messaging. Este artigo fornece uma visão geral de como funciona o FCM e fornece um procedimento passo a passo para adquirir as credenciais para que seu aplicativo pode usar os serviços do FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notificações remotas com o Firebase Cloud Messaging](remote-notifications-with-fcm.md)

Este passo a passo fornece uma explicação passo a passo de como usar o Firebase Cloud Messaging para implementar notificações remotas (também chamadas de notificações por push) em um aplicativo xamarin. Android. Ele ilustra como implementar as várias classes que são necessários para comunicações com o Firebase Cloud Messaging (FCM), fornece exemplos de como configurar o manifesto do Android para acesso ao FCM e demonstra o downstream de mensagens usando o Firebase Console.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

Esta seção fornece uma visão geral de como o Google Cloud Messaging (GCM) roteia mensagens entre seu aplicativo e um servidor de aplicativo e fornece um procedimento passo a passo para adquirir as credenciais para que seu aplicativo pode usar os serviços do GCM. (Observe que o GCM foi substituído pelo FCM).

> [!NOTE]
> Foi substituído pelo GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e APIs de cliente [foram preteridos](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estará disponível assim que 11 de abril de 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notificações remotas com o Google Cloud Messaging](remote-notifications-with-gcm.md)

Esta seção fornece uma explicação passo a passo de como implementar notificações remotas no xamarin. Android usando o Google Cloud Messaging.
Ele explica os vários componentes que devem ser utilizados para habilitar o Google Cloud Messaging em um aplicativo Android.


