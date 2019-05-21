---
title: Renderizadores de xamarin. Forms Fast
description: Este artigo apresenta os renderizadores rápidos, o que reduz os custos de renderização de um controle xamarin. Forms no Android e inflação nivelando a hierarquia de controle nativo resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 861d9e3f898dcd61015d9aca27ae66c3fe72d1a9
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970716"
---
# <a name="xamarinforms-fast-renderers"></a>Renderizadores de xamarin. Forms Fast

Tradicionalmente, a maioria dos renderizadores de controle original no Android é compostas de dois modos de exibição:

- Controlar um nativo, como uma `Button` ou `TextView`.
- Um contêiner `ViewGroup` que lida com algumas das outras tarefas, tratamento de gesto e o trabalho de layout.

No entanto, essa abordagem tem uma implicação de desempenho que dois modos de exibição são criados para cada controle lógico, o que resulta em uma árvore visual mais complexa que exige mais memória e mais processamento para renderizar na tela.

Os renderizadores rápidos reduzem os custos de renderização de um controle xamarin. Forms e inflação em uma única exibição. Portanto, em vez de criar dois modos de exibição e adicioná-los à árvore de exibição, somente um é criado. Isso melhora o desempenho criando menos objetos, que por sua vez, significa que uma árvore de exibição menos complexa, e menos uso de memória (que também resulta em menos pausas na coleta de lixo).

Os renderizadores rápidos estão disponíveis para os seguintes controles no xamarin. Forms no Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Funcionalmente, esses renderizadores rápidos não são diferentes para os renderizadores herdados. Do xamarin. Forms 4.0 e versões posteriores, todos os aplicativos destinados ao `FormsAppCompatActivity` usarão essas renderizadores rápidos por padrão. Os renderizadores para todos os novos controles, incluindo [ `ImageButton` ](xref:Xamarin.Forms.ImageButton) e [ `CollectionView` ](xref:Xamarin.Forms.CollectionView), use a abordagem de renderizador rápida.

Melhorias de desempenho ao usar os renderizadores rápidos irá variar para cada aplicativo, dependendo da complexidade do layout. Por exemplo, melhorias de desempenho de x2 são possíveis ao rolar por um [ `ListView` ](xref:Xamarin.Forms.ListView) que contém milhares de linhas de dados, em que as células em cada linha são compostas de controles que usam os renderizadores rápidos, o que resulta em visivelmente rolagem mais suave.

> [!NOTE]
> Renderizadores personalizados podem ser criados para renderizadores rápidos usando a mesma abordagem usada para os renderizadores herdados. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="backwards-compatibility"></a>Compatibilidade com versões anteriores

Os renderizadores rápidos podem ser substituídos com as seguintes abordagens:

1. Habilitando os renderizadores herdados, adicionando a seguinte linha de código para seus `MainActivity` classe antes de chamar `Forms.Init`:

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Usando renderizadores personalizados que se destinam os renderizadores herdados. Quaisquer renderizadores personalizados existentes continuarão a funcionar com os renderizadores herdados.
1. Especificando uma opção diferente `View.Visual`, tais como `Material`, que usa diferentes processadores. Para obter mais informações sobre o Material Visual, consulte [xamarin. Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
