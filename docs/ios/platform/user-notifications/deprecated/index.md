---
title: Tecnologias de notificação preteridas no Xamarin. iOS
description: Este documento descreve as tecnologias de notificação do iOS que foram preteridas em favor da estrutura de notificações do usuário, introduzida no iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2016
ms.openlocfilehash: a97885518c0646d2d8315b5da5eb12c71d693cf6
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574074"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologias de notificação preteridas no Xamarin. iOS

Esta seção mostra como implementar notificações locais e por push no Xamarin. iOS. Ele explicará os vários elementos da interface do usuário de uma notificação do iOS e discutirá as APIs envolvidas na criação e exibição de uma notificação.

> [!IMPORTANT]
> As informações contidas nesta seção pertencem ao iOS 9 e anteriores, foram deixadas aqui para dar suporte a versões mais antigas do iOS. Para o iOS 10 e posterior, consulte o [guia da estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a notificações locais e remotas em um dispositivo IOS.

## <a name="sections"></a>Seções

<a name="Local Notifications In iOS"></a>

## <a name="local-notifications-in-ios"></a>[Notificações locais no iOS](local-notifications-in-ios.md)

Esta seção explicará como implementar notificações locais no Xamarin. iOS. Ele explicará os vários elementos da interface do usuário de uma notificação do iOS e discutirá as APIs envolvidas na criação e exibição de uma notificação.

<a name="Local Notifications Walkthrough"></a>

## <a name="walkthrough---using-local-notifications-in-xamarinios"></a>[Walkthrough – usando notificações locais no Xamarin. iOS](local-notifications-in-ios-walkthrough.md)

Nesta seção, vamos examinar como usar as notificações locais em um aplicativo Xamarin. iOS. Ele demonstrará as noções básicas de criação e publicação de uma notificação que exibirá um alerta quando recebido pelo aplicativo.

<a name="Remote Notifications In iOS"></a>

## <a name="remote-notifications-in-ios"></a>[Notificações remotas no iOS](remote-notifications-in-ios.md)

Esta seção abordará as notificações por push no iOS. Ele apresenta o serviço de gateway de notificações por push da Apple (APNS) e a função que ele desempenha na publicação de notificações em aplicativos iOS. Ele explicará como criar os certificados de segurança necessários para habilitar notificações por push e discutir. Por fim, esta seção discutirá algumas das tarefas de manutenção que os servidores de aplicativos devem executar para manter o controle dos dispositivos móveis do cliente.

## <a name="related-links"></a>Links relacionados

- [Notificações (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
