---
ms.openlocfilehash: d5acf27dfbda0dcd31b00600cee2f16b98aaa70a
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634736"
---
Antes de tentar realizar este tutorial, você deve ter concluído com sucesso:

- Início rápido [Compilar seu primeiro aplicativo Xamarin.Forms](~/get-started/first-app/index.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) do Xamarin.Forms em XAML.
> - Especificar a orientação de `StackLayout`.
> - Controlar a expansão e o alinhamento da exibição filho dentro de `StackLayout`.

Você usará o Visual Studio 2019 ou Visual Studio para Mac para criar um aplicativo simples que demonstra como alinhar controles em um [`StackLayout`](xref:Xamarin.Forms.StackLayout). Estas capturas de tela mostram o aplicativo final:

[![Captura de tela de exibições filho em um StackLayout, com opções de alinhamento e expansão definidas, no iOS e no Android](../images/alignment-expansion-reduced.png "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")](../images/alignment-expansion-large.png#lightbox "StackLayout contendo instâncias de rótulo, com alinhamento e expansão definidos")

Você também usará a [Recarga Dinâmica de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) a fim de conferir alterações da interface do usuário sem a necessidade de recompilar seu aplicativo.
