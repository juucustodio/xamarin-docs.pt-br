---
title: Técnicas de operação em segundo plano no iOS
description: 'Este documento contém links para guias que descrevem várias técnicas de backgrounding no iOS: notificações remotas, serviço de transferência em segundo plano, busca em segundo plano e tarefas em segundo plano.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 00cca0c75cc79858f6edda5d6fb954611d81161b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169600"
---
# <a name="ios-backgrounding-techniques"></a>Técnicas de operação em segundo plano no iOS

As seções a seguir, vamos explorar os seguintes recursos do iOS junto com as opções de backgrounding existentes:

-  [Tarefas de plano de fundo oportunista](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) -preservar a vida útil da bateria, executando tarefas em segundo plano em partes oportunistas quando o dispositivo está ativo para outros tipos de processamento.
-  [O serviço de transferência do plano de fundo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) - confiável carregar e baixar arquivos, independentemente do tamanho de arquivo ou o status de rede.
-  [Busca de plano de fundo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) -atualizar um aplicativo do plano de fundo em intervalos determinados pelo sistema.
-  [Notificações remotas](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) -notificações por push de uso para disparar as atualizações de conteúdo em segundo plano antes do usuário abre o aplicativo, com uma opção para notificar o usuário ou atualizar silenciosamente.
-  **Atualizações da interface do usuário do plano de fundo** - preparar o aplicativo da interface do usuário para o usuário e atualizar o instantâneo do aplicativo, tudo a partir de segundo plano.
