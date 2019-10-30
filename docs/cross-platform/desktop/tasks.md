---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
description: Este documento compara como executar várias tarefas comuns no WPF e no Xamarin. Forms. Ele examina os botões, os temporizadores, os tamanhos de fonte, a abertura de um URI e a exibição de uma planilha de ações.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: d1f1430d8044f94c17a19d747334b5ed8a1441cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016416"
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Criar um temporizador|Classe `DispatcherTimer`|`Device.StartTimer` método estático|
|Obter um tamanho de fonte padrão|classe estática `SystemFonts`|`Device.GetNamedSize` método estático|
|Abrir um URI/URL|`Process.Start`|`Device.OpenUri`|
|Exibir folha de ação (lista de botões)|N/D|`Page.DisplayActionSheet`|
