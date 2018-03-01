---
title: Usando um ContentProvider
ms.topic: article
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 5c90b140719e0dafb36dad1da75f52ba27e75e25
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="using-a-contentprovider"></a>Usando um ContentProvider

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
ContentProviders permitem que você acesse dados expostos por outros aplicativos (incluindo dados de sistema Android como contatos, mídia e informações de calendário).

É a melhor maneira de acessar um ContentProvider com um CursorLoader usando o LoaderManager. LoaderManager foi introduzido no Android 3.0 (API nível 11, Honeycomb) para mover as tarefas de bloqueio do thread principal e usar um CursorLoader permite que os dados a serem carregados em um thread antes que está sendo associada a uma ListView para exibição.

Consulte [Introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.

