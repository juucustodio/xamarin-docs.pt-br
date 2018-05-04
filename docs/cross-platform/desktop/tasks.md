---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 84b3edc1a8cbc9ad642d94b457321786fae5fe70
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Criar um timer|Classe `DispatcherTimer`|`Device.StartTimer` método estático|
|Obter o tamanho de fonte padrão|`SystemFonts` classe estática|`Device.GetNamedSize` método estático|
|Abrir um URI/URL|`Process.Start`|`Device.OpenUri`|
|Exibir a folha de ação (lista de botões)|N/D|`Page.DisplayActionSheet`|
