---
title: Caminhos e linhas de SkiaSharp
description: Este artigo explica como usar SkiaSharp para desenhar linhas e demarcadores de elementos gráficos em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 3f2597c67459e407ac066ee19d54d134d60f3076
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615129"
---
# <a name="skiasharp-lines-and-paths"></a>Caminhos e linhas de SkiaSharp

_Usar SkiaSharp para desenhar linhas e elementos gráficos caminhos_

O [seção anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) demonstrado que as SkiaSharp `SKCanvas` classe inclui vários métodos para desenhar círculos, retângulos, elipses, retângulos arredondados, texto e bitmaps. Nesta seção e as próximas seções abordam as diversas classes conectadas com a criação e renderização *demarcadores de elementos gráficos*.

O caminho de gráficos é a abordagem mais generalizada para desenho de linhas e curvas em SkiaSharp. Esta seção aborda o uso um [ `SKPath` ](xref:SkiaSharp.SKPath) objeto para desenhar linhas retas e usar uma coleção de linhas retas pequenas (chamado um *polilinha*) para desenhar curvas que você pode definir de forma algorítmica. Uma seção posterior sobre [ **SkiaSharp curvas e caminhos** ](../curves/index.md) discute os vários tipos de curvas com suporte pelo `SKPath`.

Todos os programas de exemplo nesta seção são exibidos sob o título **linhas e caminhos** na home page do [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa e, no [ **Caminhos** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) pasta da solução.

## <a name="lines-and-stroke-capslinesmd"></a>[limites de linha e de traço](lines.md)

Saiba como usar SkiaSharp para desenhar linhas com extremidades diferentes do traço.

## <a name="path-basicspathsmd"></a>[Noções básicas do caminho](paths.md)

Explore o SkiaSharp `SKPath` objeto para a combinação de linhas e curvas.

## <a name="the-path-fill-typesfill-typesmd"></a>[Os tipos de preenchimento do caminho](fill-types.md)

Descubra os diferentes efeitos possíveis com tipos de preenchimento do caminho de SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polilinhas e equações paramétricas](polylines.md)

Use SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas.

## <a name="dots-and-dashesdotsmd"></a>[Pontos e traços](dots.md)

Dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp.

## <a name="finger-paintingfinger-paintmd"></a>[Pintura a dedo](finger-paint.md)

Use os dedos para pintar na tela.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
