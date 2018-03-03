---
title: Caminhos e SkiaSharp linhas
description: "Use SkiaSharp para desenhar linhas e elementos gráficos caminhos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: b94091afc459866d072bd3c4adc3947f6be258b1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="skiasharp-lines-and-paths"></a>Caminhos e SkiaSharp linhas

_Use SkiaSharp para desenhar linhas e elementos gráficos caminhos_

O [seção anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) demonstrado que o SkiaSharp `SKCanvas` classe inclui vários métodos para desenhar retângulos, elipses e retângulos arredondados. Esta seção e seções posteriores abrangem as várias classes conectadas com a criação e renderização *caminhos de gráficos*.

O caminho do gráfico é a abordagem mais generalizada para desenho linhas e curvas em SkiaSharp. Esta seção abrange o uso uma `SKPath` objeto para desenhar linhas retas e usar um conjunto pequeno de linhas retas (chamado um *polilinha*) para desenhar curvas que você pode definir matematicamente. Uma seção posterior será aborda os vários tipos de curvas suportados pelo `SKPath`.

Todos os programas de exemplo nesta seção aparecem sob o título **linhas e caminhos** na home page do [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa e no [ **Caminhos** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Paths) pasta da solução.

## <a name="lines-and-stroke-capslinesmd"></a>[Linhas e limites de traço](lines.md)

Saiba como usar SkiaSharp para desenhar linhas com extremidades de traço diferentes.

## <a name="path-basicspathsmd"></a>[Noções básicas de caminho](paths.md)

Explore o objeto SkiaSharp SKPath para a combinação de linhas e curvas.

## <a name="the-path-fill-typesfill-typesmd"></a>[Os tipos de preenchimento do caminho](fill-types.md)

Descobrir os efeitos de diferentes possíveis com tipos de preenchimento do caminho SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polilinhas e equações paramétricas](polylines.md)

Use SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas.

## <a name="dots-and-dashesdotsmd"></a>[Pontos e traços](dots.md)

A complexidade do desenho linhas pontilhadas e tracejadas no SkiaSharp do mestre.

## <a name="finger-paintingfinger-paintmd"></a>[Pintura a dedo](finger-paint.md)

Use seus dedos para pintar na tela.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
