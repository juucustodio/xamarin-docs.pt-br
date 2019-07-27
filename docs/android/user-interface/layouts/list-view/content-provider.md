---
title: Usando um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 142541dcc35b55e43b54eeb729c486ac9fc88b54
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510068"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Usando um ContentProvider com o Xamarin. Android

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
O ContentProviders permite que você acesse dados expostos por outros aplicativos (incluindo dados do sistema Android, como contatos, mídia e informações de calendário).

A maneira preferida de acessar um ContentProvider é com um CursorLoader usando o Loadermanager. Loadermanager foi introduzido no Android 3,0 (API nível 11, Honeycomb) para mover tarefas de bloqueio para fora do thread principal, e usar um CursorLoader permite que os dados sejam carregados em um thread antes de serem associados a um ListView para exibição.

Consulte [introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.

