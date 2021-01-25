---
ms.openlocfilehash: 1ff81c0399a0abb321831f8a72dfb2a8560816c4
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689996"
---
Antes de tentar realizar este tutorial, você deve ter concluído com sucesso:

- Início rápido [Compilar seu primeiro aplicativo Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial de [StackLayout](~/get-started/tutorials/stacklayout/index.yml).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um [`Entry`](xref:Xamarin.Forms.Entry) do Xamarin.Forms em XAML.
> - Responder a alterações no texto no `Entry`.
> - Personalizar o comportamento do `Entry`.

Você usará o Visual Studio 2019 ou Visual Studio para Mac para criar um aplicativo simples que demonstra como personalizar o comportamento de um [`Entry`](xref:Xamarin.Forms.Entry). Estas capturas de tela mostram o aplicativo final:

[![Captura de tela de uma entrada com texto mascarado por caracteres de senha, no iOS e no Android](../images/customize-behavior.png "Entrada com caracteres de senha mascarados")](../images/customize-behavior-large.png#lightbox "Entrada com caracteres de senha mascarados")

Você também usará a [Recarga Dinâmica de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md) a fim de conferir alterações da interface do usuário sem a necessidade de recompilar seu aplicativo.
