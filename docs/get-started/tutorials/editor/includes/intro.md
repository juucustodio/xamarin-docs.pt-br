---
ms.openlocfilehash: d00ddd66c6b609131c363d8c13c0490b08eff8f0
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689778"
---
Antes de tentar realizar este tutorial, você deve ter concluído com sucesso:

- Início rápido [Compilar seu primeiro aplicativo Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial de [StackLayout](~/get-started/tutorials/stacklayout/index.yml).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um [`Editor`](xref:Xamarin.Forms.Editor) do Xamarin.Forms em XAML.
> - Responder a alterações no texto no `Editor`.
> - Personalizar o comportamento do `Editor`.

Você usará o Visual Studio 2019 ou Visual Studio para Mac para criar um aplicativo simples que demonstra como personalizar o comportamento de um [`Editor`](xref:Xamarin.Forms.Editor). Estas capturas de tela mostram o aplicativo final:

[![Captura de tela de um editor de dimensionamento automático, no iOS e no Android](../images/customize-behavior.png "Editor de dimensionamento automático")](../images/customize-behavior-large.png#lightbox "Editor de dimensionamento automático")

Você também usará a [Recarga Dinâmica de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) a fim de conferir alterações da interface do usuário sem a necessidade de recompilar seu aplicativo.
