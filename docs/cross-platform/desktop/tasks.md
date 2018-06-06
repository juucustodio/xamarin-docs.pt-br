---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
description: Este documento compara a realizar várias tarefas comuns no WPF e xamarin. Forms. Examina a botões, temporizadores, tamanhos de fonte, abrindo um URI e exibir uma folha de ação.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780470"
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Criar um timer|Classe `DispatcherTimer`|`Device.StartTimer` método estático|
|Obter o tamanho de fonte padrão|`SystemFonts` classe estática|`Device.GetNamedSize` método estático|
|Abrir um URI/URL|`Process.Start`|`Device.OpenUri`|
|Exibir a folha de ação (lista de botões)|N/D|`Page.DisplayActionSheet`|
