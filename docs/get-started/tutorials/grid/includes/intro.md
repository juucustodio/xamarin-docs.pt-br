---
ms.openlocfilehash: 24fdf5849e94fb80cad40635a44cc8da434d83cc
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634851"
---
Antes de tentar realizar este tutorial, você deve ter concluído com sucesso:

- Início rápido [Compilar seu primeiro aplicativo Xamarin.Forms](~/get-started/first-app/index.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um [`Grid`](xref:Xamarin.Forms.Grid) do Xamarin.Forms em XAML.
> - Especificar colunas e linhas para `Grid`.
> - Distribuir o conteúdo em diversas colunas ou linhas em `Grid`.

Você usará o Visual Studio 2019 ou Visual Studio para Mac para criar um aplicativo simples que demonstra como dispor controles em um [`Grid`](xref:Xamarin.Forms.Grid). Estas capturas de tela mostram o aplicativo final:

[![Captura de tela de uma grade que tem conteúdo que abrange várias colunas e linhas, no iOS e no Android](../images/span-columns-rows.png "Grade com conteúdo que abrange colunas e linhas")](../images/span-columns-rows-large.png#lightbox "Grade com conteúdo que abrange colunas e linhas")

Você também usará a [Recarga Dinâmica de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) a fim de conferir alterações da interface do usuário sem a necessidade de recompilar seu aplicativo.
