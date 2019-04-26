---
title: Usando um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189693"
---
# <a name="using-a-contentprovider"></a>Usando um ContentProvider

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
ContentProviders permitem que você acesse dados expostos por outros aplicativos (incluindo dados de sistema do Android, como contatos, mídia e informações de calendário).

É a maneira preferencial para acessar um ContentProvider com um CursorLoader usando o LoaderManager. LoaderManager foi introduzido no Android 3.0 (API nível 11, Honeycomb) para mover as tarefas de bloqueio fora do thread principal e usar um CursorLoader permite que os dados a ser carregado em um thread antes que está sendo associada a um ListView para exibição.

Consulte a [Introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.

