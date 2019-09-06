---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
description: Este documento compara como executar várias tarefas comuns no WPF e no Xamarin. Forms. Ele examina os botões, os temporizadores, os tamanhos de fonte, a abertura de um URI e a exibição de uma planilha de ações.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290325"
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Criar um temporizador|Classe `DispatcherTimer`|`Device.StartTimer`método estático|
|Obter um tamanho de fonte padrão|`SystemFonts`classe estática|`Device.GetNamedSize`método estático|
|Abrir um URI/URL|`Process.Start`|`Device.OpenUri`|
|Exibir folha de ação (lista de botões)|N/D|`Page.DisplayActionSheet`|
