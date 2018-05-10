---
title: Desenho 2D
description: Cross Platform de desenho 2D com SkiaSharp
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: f1a40b48ecfb7244aae77cf1b4110ae53490d98c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="2d-drawing"></a>Desenho 2D

SkiaSharp fornece uma API c# poderosa para fazer gráficos 2D. Ele é alimentado por [biblioteca de Skia do Google](http://skia.org), da mesma biblioteca que habilita o Google Chrome, Firefox e do Android pilhas de gráfico.

[![](images/ide-sml.png "SkiaSharp fornece uma API c# poderosa para fazer gráficos 2D")](images/ide.png#lightbox)

SkiaSharp é uma biblioteca portátil e convenientemente é fornecido como um [pacote de NuGet de plataforma cruzada](https://www.nuget.org/packages/SkiaSharp)e suporta as seguintes plataformas predefinido: macOS, xamarin, xamarin e a área de trabalho do Windows.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introdução ao SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Uma visão geral dos principais conceitos do exemplo e SkiaSharp código para renderizar elementos gráficos, texto, bitmaps e usar filtros de imagem.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Tutoriais de SkiaSharp para xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Aprenda a trabalhar com cruzada gráficos de plataforma que processam em xamarin. Forms:

- [Noções básicas de desenho](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Desenho de um círculo simple](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integração com Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Unidades independentes de dispositivo e pixels](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animação básica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [Integração de texto e elementos gráficos](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Noções básicas de bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Linhas e caminhos](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Linhas e limites de traço](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Noções básicas de caminho](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [Os tipos de preenchimento do caminho](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Polilinhas e equações paramétricas](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Pontos e traços](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Pintura a dedo](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Transformações](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [A transformação de conversão](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [A transformação de escala](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [A transformação de rotação](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [A transformação de distorção](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Transformações de matriz](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Manipulações de toque](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Transformações não afins](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Rotação 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Curvas e caminhos](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Três formas de desenhar um arco](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Três tipos de curvas de Bézier](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Dados de caminho SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Recorte com caminhos de regiões](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Efeitos de caminho](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Caminhos e texto](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Enumeração e informações de caminho](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Notas específicas à plataforma](~/graphics-games/skiasharp/platform.md)

Esta página descreve as instruções de instalação para SkiaSharp em diferentes plataformas, inclusive Windows, iOS, Android e macOS.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentação da API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

Você pode procurar o [documentação da API](https://developer.xamarin.com/api/namespace/SkiaSharp/) para SkiaSharp no site da web.

## <a name="work-in-progress"></a>Trabalho em andamento

SkiaSharp é um trabalho em andamento estão compartilhando com nossa comunidade. Enquanto estamos ter associados partes importantes da API Skia, o trabalho restante a ser feito. Estamos usando a API de C estável abordados pela Skia e nosso plano é continuar contribuindo com nosso trabalho para as associações de C de Skia para fornecer a cobertura completa para as APIs.

Para ajudar a orientar nossos esforços de associação, deixe os comentários e sugestões como problemas no repositório do GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
