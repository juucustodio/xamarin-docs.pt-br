---
title: Usando um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b9b6340d4aaf386c7b4be8ebf366589582771be2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763287"
---
# <a name="using-a-contentprovider"></a>Usando um ContentProvider

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
ContentProviders permitem que você acesse dados expostos por outros aplicativos (incluindo dados de sistema Android como contatos, mídia e informações de calendário).

É a melhor maneira de acessar um ContentProvider com um CursorLoader usando o LoaderManager. LoaderManager foi introduzido no Android 3.0 (API nível 11, Honeycomb) para mover as tarefas de bloqueio do thread principal e usar um CursorLoader permite que os dados a serem carregados em um thread antes que está sendo associada a uma ListView para exibição.

Consulte [Introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.

