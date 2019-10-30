---
title: Usando um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c99b3d64f4f5033240c086af8677d31485a44f01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028926"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Usando um ContentProvider com o Xamarin. Android

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
O ContentProviders permite que você acesse dados expostos por outros aplicativos (incluindo dados do sistema Android, como contatos, mídia e informações de calendário).

A maneira preferida de acessar um ContentProvider é com um CursorLoader usando o Loadermanager. Loadermanager foi introduzido no Android 3,0 (API nível 11, Honeycomb) para mover tarefas de bloqueio para fora do thread principal, e usar um CursorLoader permite que os dados sejam carregados em um thread antes de serem associados a um ListView para exibição.

Consulte [introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.
