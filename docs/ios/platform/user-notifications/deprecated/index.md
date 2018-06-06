---
title: Tecnologias de notificação preterido no xamarin
description: Este documento descreve as tecnologias de notificação do iOS que foram substituídas em favor da estrutura de notificações do usuário, introduzida no iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2016
ms.openlocfilehash: 4becc5e296fb6e2496d9ffd863f7137419480262
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788547"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologias de notificação preterido no xamarin

Esta seção mostra como implementar local e notificações por push xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutem a API do envolvidos com a criação e exibindo uma notificação.

> [!IMPORTANT]
> As informações nesta seção se refere ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte o [guia estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a Local e remoto de notificação em um dispositivo iOS.

## <a name="sections"></a>Seções

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notificações de locais no iOS](local-notifications-in-ios.md)

Esta seção aborda como implementar notificações de locais em xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de iOS e discutem a API do envolvidos com a criação e exibindo uma notificação.

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Passo a passo: usar notificações locais no Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

Nesta seção examinaremos como usar notificações locais em um aplicativo xamarin. Demonstrar os conceitos básicos de criação e publicação de uma notificação que será exibida em um alerta quando recebidas pelo aplicativo.

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notificações remotas no iOS](remote-notifications-in-ios.md)

Esta seção abordará as notificações por push no iOS. Ele apresenta o Apple Push notificações Gateway Service (APNS) e a função que tem na publicação notificações a aplicativos iOS. Ele explica como criar os certificados de segurança necessárias para habilitar notificações por push e discutir. Por fim esta seção aborda algumas das tarefas de manutenção que servidores de aplicativo devem executar para controlar os dispositivos móveis do cliente.

## <a name="related-links"></a>Links relacionados

- [Notificações (exemplo)](https://developer.xamarin.com/samples/monotouch/Notifications/)
