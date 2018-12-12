---
title: Renderizadores de xamarin. Forms Fast
description: Este artigo apresenta os renderizadores rápidos, o que reduz os custos de renderização de um controle xamarin. Forms no Android e inflação nivelando a hierarquia de controle nativo resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 42889e3805391e9e586cda61d26103398ba16269
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898518"
---
# <a name="xamarinforms-fast-renderers"></a>Renderizadores de xamarin. Forms Fast

![Visualizar](~/media/shared/preview.png)

_Este artigo apresenta os renderizadores rápidos, o que reduz os custos de renderização de um controle xamarin. Forms no Android e inflação nivelando a hierarquia de controle nativo resultante._

Tradicionalmente, a maioria dos renderizadores de controle original no Android é compostas de dois modos de exibição:

- Controlar um nativo, como uma `Button` ou `TextView`.
- Um contêiner `ViewGroup` que lida com algumas das outras tarefas, tratamento de gesto e o trabalho de layout.

No entanto, essa abordagem tem uma implicação de desempenho que dois modos de exibição são criados para cada controle lógico, o que resulta em uma árvore visual mais complexa que exige mais memória e mais processamento para renderizar na tela.

Os renderizadores rápidos reduzem os custos de renderização de um controle xamarin. Forms e inflação em uma única exibição. Portanto, em vez de criar dois modos de exibição e adicioná-los à árvore de exibição, somente um é criado. Isso melhora o desempenho criando menos objetos, que por sua vez, significa que uma árvore de exibição menos complexa, e menos uso de memória (que também resulta em menos pausas na coleta de lixo).

Os renderizadores rápidos estão disponíveis para os seguintes controles no xamarin. Forms 2.4 no Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Funcionalmente, esses renderizadores rápidos não são diferentes para os renderizadores originais. No entanto, eles atualmente são experimentais e só pode ser usados, adicionando a seguinte linha de código para seus `MainActivity` classe antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> Os renderizadores rápidos só são aplicáveis para o aplicativo compat Android back-end, portanto, essa configuração será ignorada em atividades de compatibilidade de aplicativo pré.

Melhorias de desempenho irá variar para cada aplicativo, dependendo da complexidade do layout. Por exemplo, melhorias de desempenho de x2 são possíveis ao rolar por um [ `ListView` ](xref:Xamarin.Forms.ListView) que contém milhares de linhas de dados, em que as células em cada linha são compostas de controles que usam os renderizadores rápidos, o que resulta em visivelmente rolagem mais suave.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
