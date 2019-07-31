---
title: Efeitos de SkiaSharp
description: Saiba como alterar a exibição normal dos elementos gráficos com gradientes, bitmaps lado a lado, modos de mesclagem, Desfoque e outros efeitos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: da923a3542a57b6150e536ecb6649140e57c81e1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655351"
---
# <a name="skiasharp-effects"></a>Efeitos de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

O SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe define seis propriedades que podem ser classificadas sob o termo geral de _efeitos_. Essas são propriedades que alteram a exibição normal dos gráficos de alguma forma. Os efeitos de SkiaSharp se enquadram em seis categorias:

## <a name="path-effectscurveseffectsmd"></a>[Efeitos de caminho](../curves/effects.md)

Defina a [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propriedade do `SKPaint` a um objeto do tipo [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) para exibir linhas tracejadas, ou para traçar ou preencher uma área com um padrão criado de caminhos. O efeito de caminho foi abordado anteriormente nesta série no artigo [ **efeitos de caminho em SkiaSharp**](../curves/effects.md).

## <a name="shadersshadersindexmd"></a>[Sombreadores](shaders/index.md)

Defina a [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) propriedade do `SKPaint` a um objeto do tipo [ `SKShader` ](xref:SkiaSharp.SKShader) para exibir gradientes lineares ou circulares, bitmaps lado a lado e padrões de ruído de Perlin.

## <a name="blend-modesblend-modesindexmd"></a>[Modos de mesclagem](blend-modes/index.md)

Defina a [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) propriedade do `SKPaint` a um membro da [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) enumeração para controlar o que acontece quando um gráfico de origem é exibido em um destino. SkiaSharp dá suporte a todos os CSS composição e blend modos, incluindo modos Duff Porter, modos de mesclagem separáveis e modos de mesclagem não separáveis.

## <a name="mask-filtersmask-filtersmd"></a>[Filtros de máscara](mask-filters.md)

Defina a [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) propriedade do `SKPaint` a um objeto do tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) para Desfoca e outros efeitos alfabéticos.

## <a name="image-filtersimage-filtersmd"></a>[Filtros de imagem](image-filters.md)

Defina a [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) propriedade do `SKPaint` a um objeto do tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) para bitmaps de desfoque e criar sombras, alto-relevo ou baixo-relevo efeitos.

## <a name="color-filterscolor-filtersmd"></a>[Filtros de cores](color-filters.md)

Defina a [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) propriedade do `SKPaint` para um objeto do tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) alterar o uso de tabelas de cores ou transformações de matriz.

Todos os o exemplo de código para estes artigos estão na [ **SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos). Na home page, selecione **SkiaSharp efeitos**.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
