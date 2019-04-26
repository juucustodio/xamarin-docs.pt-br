---
title: Tecnologias de notificação preteridas no xamarin. IOS
description: Este documento descreve as tecnologias de notificação do iOS que foram preteridas em favor a estrutura de notificações do usuário, introduzida no iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: 7b0c2d99412f7a34c0e9c95a282d5d74dbe8dd78
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085624"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologias de notificação preteridas no xamarin. IOS

Esta seção mostra como implementar um local e enviar notificações por push no xamarin. IOS. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutir a API do envolvido com a criação e exibindo uma notificação.

> [!IMPORTANT]
> As informações nesta seção referem-se ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte a [guia de estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a locais e remotas de notificação em um dispositivo iOS.

## <a name="sections"></a>Seções

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notificações locais no iOS](local-notifications-in-ios.md)

Esta seção aborda como implementar notificações locais no xamarin. IOS. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutir a API do envolvido com a criação e exibindo uma notificação.

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Passo a passo: usar notificações locais no Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

Nesta seção vamos examinar como usar notificações locais em um aplicativo xamarin. IOS. Ela demonstrará as Noções básicas de criação e publicação de uma notificação que será exibido um alerta quando recebidas pelo aplicativo.

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notificações remotas no iOS](remote-notifications-in-ios.md)

Esta seção abordará as notificações por push no iOS. Ele apresenta as notificações de Gateway de serviço APNS (Apple Push) e a função que desempenha em notificações de publicação para aplicativos do iOS. Ele explicará como criar os certificados de segurança necessárias para habilitar notificações por push e discutir. Por fim esta seção aborda algumas das tarefas de manutenção do sistema que servidores de aplicativos devem executar para controlar os dispositivos móveis de cliente.

## <a name="related-links"></a>Links relacionados

- [Notificações (amostra)](https://developer.xamarin.com/samples/monotouch/Notifications/)
