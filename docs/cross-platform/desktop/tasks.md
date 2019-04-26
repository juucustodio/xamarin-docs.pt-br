---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: Comparação de tarefas comuns
description: Este documento compara como realizar várias tarefas comuns no xamarin. Forms e WPF. Ele examina botões, temporizadores, tamanhos de fonte, um URI de abrindo e exibindo uma folha de ações.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269589"
---
# <a name="common-tasks-comparison"></a>Comparação de tarefas comuns

| Tarefa | WPF | Xamarin.Forms |
|--- |--- |--- |
|Exibir uma mensagem na tela com botões|`MessageBox`|`Page.DisplayAlert`|
|Crie um temporizador|Classe `DispatcherTimer`|`Device.StartTimer` método estático|
|Obter um tamanho de fonte padrão|`SystemFonts` classe estática|`Device.GetNamedSize` método estático|
|Abrir URL/URI|`Process.Start`|`Device.OpenUri`|
|Exibir a folha de ações (lista de botões)|N/D|`Page.DisplayActionSheet`|
