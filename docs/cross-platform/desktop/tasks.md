---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4d45ae61c5d7a7093d51c4440d11dd883c157a4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Criar um timer|Classe `DispatcherTimer`|`Device.StartTimer` método estático|
|Obter o tamanho de fonte padrão|`SystemFonts` classe estática|`Device.GetNamedSize` método estático|
|Abrir um URI/URL|`Process.Start`|`Device.OpenUri`|
|Exibir a folha de ação (lista de botões)|N/D|`Page.DisplayActionSheet`|
