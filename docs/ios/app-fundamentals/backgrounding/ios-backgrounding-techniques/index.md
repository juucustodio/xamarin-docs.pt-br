---
title: Técnicas de operação em segundo plano no iOS
description: 'Este documento contém links para guias que descrevem várias técnicas de plano de fundo no iOS: tarefas em segundo plano, serviço de transferência em segundo plano, busca em segundo plano e notificações remotas.'
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: a21ce124e20a421be40d41af5f066e611152317b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281116"
---
# <a name="ios-backgrounding-techniques"></a>Técnicas de operação em segundo plano no iOS

Nas seções a seguir, exploraremos os seguintes recursos do iOS juntamente com as opções de plano de fundo existentes:

- [Tarefas de plano de fundo oportunista](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7) -preserve a vida útil da bateria executando tarefas em segundo plano em partes oportunistas quando o dispositivo estiver ativo para outro processamento.
- [Serviço de transferência em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers) – carregue e baixe arquivos de forma confiável, independentemente do status da rede ou do tamanho do arquivo.
- [Busca em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch) – atualize um aplicativo de segundo plano em intervalos determinados pelo sistema.
- [Notificações remotas](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications) – use notificações por push para disparar atualizações de conteúdo em segundo plano antes que o usuário abra o aplicativo, com uma opção para notificar o usuário ou atualizar silenciosamente.
- **Atualizações de IU em segundo plano** -Prepare a interface do usuário do aplicativo e atualize o instantâneo do aplicativo, tudo a partir do plano de fundo.
