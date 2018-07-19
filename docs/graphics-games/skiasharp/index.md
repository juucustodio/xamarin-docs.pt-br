---
title: Desenho 2D com SkiaSharp
description: Este documento fornece uma visão geral de 2D de plataforma cruzada com SkiaSharp de desenho. Ele vincula vários guias que descrevem SkiaSharp e suas várias APIs.
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 0c8cbc14308c8c4131e5aaa2bcc0ddfa798af610
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130914"
---
# <a name="2d-drawing-with-skiasharp"></a>Desenho 2D com SkiaSharp

SkiaSharp fornece uma API eficiente do c# para fazer gráficos 2D. Ele é alimentado por [biblioteca de Skia do Google](http://skia.org), a mesma biblioteca que alimenta o Google Chrome, Firefox e do Android pilhas de gráficos.

[![](images/ide-sml.png "SkiaSharp fornece uma API eficiente do c# para fazer gráficos 2D")](images/ide.png#lightbox)

SkiaSharp é uma biblioteca portátil e convenientemente fornecido como uma [pacote do NuGet de multiplataforma](https://www.nuget.org/packages/SkiaSharp)e suporta as seguintes plataformas prontos: macOS, a área de trabalho do Windows, xamarin. IOS e xamarin. Android.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introdução ao SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Uma visão geral dos conceitos básicos de SkiaSharp e exemplo de código para renderizar elementos gráficos, texto, bitmaps e usar filtros de imagem.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Tutoriais de SkiaSharp para xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Saiba como trabalhar com entre elementos gráficos de plataforma que são renderizadas no xamarin. Forms:

- [Noções básicas de desenho](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Desenhando um círculo simple](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integração com Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Pixels e unidades independentes de dispositivo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animação básica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [A integração do texto e elementos gráficos](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Noções básicas de bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Caminhos e linhas](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Linhas e limites do traço](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Noções básicas do caminho](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
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
- [Bitmaps](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [Exibição de Bitmaps](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [Criando e Bitmaps de desenho](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [Corte de Bitmaps](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [Exibição segmentada de Bitmaps](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [Salvando Bitmaps em arquivos](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [Acessando os Bits de Pixel de Bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [Animando Bitmaps](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Notas específicas à plataforma](~/graphics-games/skiasharp/platform.md)

Esta página descreve as instruções de instalação para SkiaSharp em diferentes plataformas, incluindo iOS, Android, macOS e Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentação da API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

Você pode procurar o [documentação da API](https://developer.xamarin.com/api/namespace/SkiaSharp/) para SkiaSharp em nosso site.

## <a name="work-in-progress"></a>Trabalho em andamento

SkiaSharp é um trabalho em andamento que estamos compartilhando nossa comunidade. Enquanto estamos tiver vinculado partes importantes da API Skia, trabalho restante a ser feito. Estamos usando a API de C estável fornecido pela Skia e nosso plano é continuar contribuindo com nosso trabalho às associações de Skia para fornecer cobertura completa para as APIs de C.

Para nos ajudar a orientar os nossos esforços de associação, deixe comentários ou sugestões de como problemas no repositório do GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
