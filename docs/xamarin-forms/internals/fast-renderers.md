---
title: Xamarin.FormsRenderizadores rápidos
description: Este artigo apresenta renderizadores rápidos, que reduzem os custos de inflação e renderização de um Xamarin.Forms controle no Android, nivelando a hierarquia de controle nativo resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 29f79e4aed0314fe1590fa26c8e4b052e14a94d6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198060"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.FormsRenderizadores rápidos

Tradicionalmente, a maioria dos renderizadores de controle original no Android é composta por duas exibições:

- Um controle nativo, como um `Button` ou `TextView` .
- Um contêiner `ViewGroup` que lida com parte do trabalho de layout, tratamento de gestos e outras tarefas.

No entanto, essa abordagem tem uma implicação de desempenho no momento em que duas exibições são criadas para cada controle lógico, o que resulta em uma árvore visual mais complexa que exige mais memória e mais processamento para renderizar na tela.

Renderizadores rápidos reduzem os custos de inflação e renderização de um Xamarin.Forms controle em uma única exibição. Portanto, em vez de criar duas exibições e adicioná-las à árvore de exibição, apenas uma é criada. Isso melhora o desempenho criando menos objetos, o que, por sua vez, significa uma árvore de exibição menos complexa e menos uso de memória (o que também resulta em menos pausas de coleta de lixo).

Renderizadores rápidos estão disponíveis para os seguintes controles no Xamarin.Forms Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`MediaElement`](xref:Xamarin.Forms.MediaElement)

Funcionalmente, esses renderizadores rápidos não são diferentes dos renderizadores herdados. De Xamarin.Forms 4,0 em diante, todos os aplicativos direcionados usarão `FormsAppCompatActivity` esses renderizadores rápidos por padrão. Renderizadores para todos os novos controles, incluindo [`ImageButton`](xref:Xamarin.Forms.ImageButton) e [`CollectionView`](xref:Xamarin.Forms.CollectionView) , usam a abordagem de processador rápido.

As melhorias de desempenho ao usar renderizadores rápidos variam para cada aplicativo, dependendo da complexidade do layout. Por exemplo, os aprimoramentos de desempenho do X2 são possíveis ao percorrer um [`ListView`](xref:Xamarin.Forms.ListView) contendo milhares de linhas de dados, onde as células em cada linha são feitas de controles que usam renderizadores rápidos, o que resulta em uma rolagem mais suave.

> [!NOTE]
> Renderizadores personalizados podem ser criados para renderizadores rápidos usando a mesma abordagem usada para os renderizadores herdados. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="backwards-compatibility"></a>Compatibilidade com versões anteriores

Renderizadores rápidos podem ser substituídos pelas seguintes abordagens:

1. Habilitando os renderizadores herdados adicionando a seguinte linha de código à sua `MainActivity` classe antes de chamar `Forms.Init` :

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Usando renderizadores personalizados que destinam-se aos renderizadores herdados. Todos os renderizadores personalizados existentes continuarão a funcionar com os renderizadores herdados.
1. Especificar um diferente `View.Visual` , como `Material` , que usa renderizadores diferentes. Para obter mais informações sobre o Visual material, consulte [ Xamarin.Forms material Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
