---
title: Usando um ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5948d4b5db53db97c4e76cb7568c109b5faf90fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762207"
---
# <a name="using-a-contentprovider-with-xamarinandroid"></a>Usando um ContentProvider com o Xamarin. Android

CursorAdapters também pode ser usado para exibir dados de um ContentProvider.
O ContentProviders permite que você acesse dados expostos por outros aplicativos (incluindo dados do sistema Android, como contatos, mídia e informações de calendário).

A maneira preferida de acessar um ContentProvider é com um CursorLoader usando o Loadermanager. Loadermanager foi introduzido no Android 3,0 (API nível 11, Honeycomb) para mover tarefas de bloqueio para fora do thread principal, e usar um CursorLoader permite que os dados sejam carregados em um thread antes de serem associados a um ListView para exibição.

Consulte [introdução ao ContentProviders](~/android/platform/content-providers/index.md) para obter mais informações.
