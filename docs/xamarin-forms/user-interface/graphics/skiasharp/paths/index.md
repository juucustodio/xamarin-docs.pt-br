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
ms.openlocfilehash: 380c9d5b8159a1a142ebf005955e4345dbc6e6d8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562893"
---
# <a name="skiasharp-lines-and-paths"></a>Caminhos e linhas de SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar SkiaSharp para desenhar linhas e caminhos de gráficos_

A [seção anterior](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) demonstrou que a `SKCanvas` classe SkiaSharp inclui vários métodos para desenhar círculos, elipses, retângulos, retângulos arredondados, texto e bitmaps. Esta seção e as seções posteriores abordam as várias classes conectadas com a criação e renderização de *caminhos gráficos*.

O caminho de gráficos é a abordagem mais generalizada para desenhar linhas e curvas em SkiaSharp. Esta seção aborda o uso de um [`SKPath`](xref:SkiaSharp.SKPath) objeto para desenhar linhas retas e usar uma coleção de pequenas linhas retas (chamadas de uma *polilinha*) para desenhar curvas que você pode definir forma algorítmica. Uma seção posterior em [**curvas e caminhos de SkiaSharp**](../curves/index.md) discute os vários tipos de curvas com suporte no `SKPath` .

Todos os programas de exemplo nesta seção aparecem sob as **linhas e caminhos** de cabeçalho na home page do programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) e na pasta [**Paths**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) dessa solução.

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

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)