---
title: "Renderizadores Rápidos"
description: "Este artigo apresenta renderizadores rápidos, que reduzem os custos de processamento de um controle xamarin. Forms no Android e inflação por mesclar a hierarquia resultante do controle nativo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 92a11ebe983840270d3679fd11f5faa0b8222cfe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="fast-renderers"></a>Renderizadores Rápidos

_Este artigo apresenta renderizadores rápidos, que reduzem os custos de processamento de um controle xamarin. Forms no Android e inflação por mesclar a hierarquia resultante do controle nativo._

Tradicionalmente, a maioria dos renderizadores do controle original no Android é compostas de dois modos de exibição:

- Controle de um nativo, como um `Button` ou `TextView`.
- Um contêiner `ViewGroup` que lida com algumas do trabalho de layout, manipulação de gesto e outras tarefas.

No entanto, essa abordagem tem uma implicação de desempenho, em que dois modos de exibição são criados para cada controle lógico, o que resulta em uma árvore visual mais complexa que não exige mais memória e mais processamento para renderizar na tela.

Processadores rápidos reduzem os custos de processamento de um controle xamarin. Forms e inflação em uma única exibição. Portanto, em vez de criar dois modos de exibição e adicioná-los à árvore de exibição, somente um é criado. Isso melhora o desempenho criando menos objetos, que por sua vez significa uma árvore de exibição menos complexa, e menos uso de memória (que também resulta em menos pausas de coleta de lixo).

Processadores rápidos estão disponíveis para os seguintes controles em xamarin. Forms 2.4 no Android:

- [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)
- [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)
- [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)

Funcionalmente, esses processadores rápidos não são diferentes para os renderizadores originais. No entanto, eles são atualmente experimentais e só pode ser usados ao adicionar a seguinte linha de código para o `MainActivity` classe antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> **Observação**: renderizadores rápidos só são aplicáveis ao aplicativo compat Android back-end, para que essa configuração será ignorada em atividades de compatibilidade de aplicativo pré.

Melhorias de desempenho irá variar para cada aplicativo, dependendo da complexidade do layout. Por exemplo, melhorias de desempenho de x2 são possíveis ao rolar por um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) contendo milhares de linhas de dados, onde as células em cada linha são feitas dos controles que usam processadores rápidas, o que resulta em visivelmente rolagem mais suave.


## <a name="related-links"></a>Links relacionados

- [Processadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
