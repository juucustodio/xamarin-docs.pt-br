---
title: Uma introdução ao SkiaSharp
description: Isso fornece uma breve introdução aos conceitos por trás de SkiaSharp
ms.prod: xamarin
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: 0e5a0cbbf5490b0fa0ffaca9be40d8e9357fce5a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-skiasharp"></a>Uma introdução ao SkiaSharp

_Isso fornece uma breve introdução aos conceitos por trás de SkiaSharp_

SkiaSharp fornece uma gráfico 2D avançada e poderosa API que pode ser usada para renderizar em buffers 2D.  Você pode usá-los para implementar os elementos da interface do usuário personalizada e gráficos 2D que podem ser incorporados em seu aplicativo.  SkiaSharp é uma associação de .NET para o [Skia](https://skia.org) biblioteca e herda a recursos e a capacidade desta biblioteca.

A biblioteca está disponível como uma plataforma cruzada [pacote NuGet](https://www.nuget.org/packages/SkiaSharp), você pode adicioná-lo ao seu projeto, adicionando a referência do NuGet.

Para desenhar, seu código criará um `SkCanvas` que descreve a superfície de onde as operações de desenho ocorrerá.

## <a name="obtaining-an-skcanvas"></a>Obtendo um SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>Desenhando em SKCanvas

O `SKCanvas` usa um modelo de desenho semelhante em espírito para outro desenho modelos que você pode estar familiarizado, ele usa cores com um canal de transparência opcional e pode desenhar linhas, arcos, texto e imagens.

Abaixo estão alguns dos muitos recursos diferentes que podem ser realizados com SkiaSharp.  Os exemplos abaixo a variável `canvas` é do tipo SKCanvas.

### <a name="drawing-xamagon"></a>Xamagon de desenho

Este exemplo desenha logotipo do Xamarin o Xamagon:

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>Desenhando texto

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>Bitmaps de desenho

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>Desenhando com filtros de imagem

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

# <a name="more-information"></a>Mais informações

Para obter mais informações sobre como usar SkiaSharp podem ser encontradas no [online documentação da API](https://developer.xamarin.com/api/namespace/SkiaSharp/)


## <a name="related-links"></a>Links relacionados

- [IOS SkiaSharp pasta de trabalho](https://developer.xamarin.com/workbooks/graphics/skiasharp/logo/skialogo-ios.workbook)
