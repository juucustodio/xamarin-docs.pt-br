---
title: Caminhos e linhas de SkiaSharp
description: Este artigo explica como usar o SkiaSharp para desenhar linhas e caminhos de gráficos em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 97c7305e59a023e65535186bbbe39a9c2b7d4c26
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138990"
---
# <a name="skiasharp-lines-and-paths"></a>Caminhos e linhas de SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar SkiaSharp para desenhar linhas e caminhos de gráficos_

A [seção anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) demonstrou que a `SKCanvas` classe SkiaSharp inclui vários métodos para desenhar círculos, elipses, retângulos, retângulos arredondados, texto e bitmaps. Esta seção e as seções posteriores abordam as várias classes conectadas com a criação e renderização de *caminhos gráficos*.

O caminho de gráficos é a abordagem mais generalizada para desenhar linhas e curvas em SkiaSharp. Esta seção aborda o uso de um [`SKPath`](xref:SkiaSharp.SKPath) objeto para desenhar linhas retas e usar uma coleção de pequenas linhas retas (chamadas de uma *polilinha*) para desenhar curvas que você pode definir forma algorítmica. Uma seção posterior em [**curvas e caminhos de SkiaSharp**](../curves/index.md) discute os vários tipos de curvas com suporte no `SKPath` .

Todos os programas de exemplo nesta seção aparecem sob as **linhas e caminhos** de cabeçalho na home page do programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) e na pasta [**Paths**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) dessa solução.

## <a name="lines-and-stroke-caps"></a>[limites de linha e de traço](lines.md)

Saiba como usar o SkiaSharp para desenhar linhas com limites diferentes de traço.

## <a name="path-basics"></a>[Noções básicas do caminho](paths.md)

Explore o `SKPath` objeto SkiaSharp para combinar linhas e curvas.

## <a name="the-path-fill-types"></a>[Os tipos de preenchimento do caminho](fill-types.md)

Descubra os diferentes efeitos possíveis com os tipos de preenchimento de caminho SkiaSharp.

## <a name="polylines-and-parametric-equations"></a>[Polilinhas e equações paramétricas](polylines.md)

Use SkiaSharp para renderizar qualquer linha que você possa definir com equações paramétricas.

## <a name="dots-and-dashes"></a>[Pontos e traços](dots.md)

Dominar as complexidades do desenho de linhas pontilhadas e tracejadas em SkiaSharp.

## <a name="finger-painting"></a>[Pintura a dedo](finger-paint.md)

Use seus dedos para pintar na tela.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
