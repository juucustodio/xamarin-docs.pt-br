---
title: Usar um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122108"
---
# <a name="using-a-contentprovider"></a>Usar um ContentProvider

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
ContentProviders permitem que você acesse dados expostos por outros aplicativos (incluindo dados de sistema do Android, como contatos, mídia e informações de calendário).

É a maneira preferencial para acessar um ContentProvider com um CursorLoader usando o LoaderManager. LoaderManager foi introduzido no Android 3.0 (API nível 11, Honeycomb) para mover as tarefas de bloqueio fora do thread principal e usar um CursorLoader permite que os dados a ser carregado em um thread antes que está sendo associada a um ListView para exibição.

Consulte a [Introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.

