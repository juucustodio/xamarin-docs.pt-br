---
title: Noções básicas de caminho
description: Explorar o objeto SkiaSharp SKPath para a combinação de linhas e curvas conectadas
ms.topic: article
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e083a9d6a97181b4e726d4553b14671f06157f80
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="path-basics"></a>Noções básicas de caminho

_Explorar o objeto SkiaSharp SKPath para a combinação de linhas e curvas conectadas_

Um dos recursos mais importantes do caminho do gráfico é a capacidade de definir quando várias linhas devem ser conectadas e quando eles não devem ser conectados. A diferença pode ser bastante visível, como as partes superiores desses dois triângulos demonstram:

![](paths-images/connectedlinesexample.png "Dois triângulos mostrando a diferença entre linhas conectadas e desconectadas")

Um caminho gráfico é encapsulado pelo [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto. Um caminho é uma coleção de um ou mais *delimitações*. Cada contorno é uma coleção de *conectado* linhas retas e curvas. Delimitações não estão conectadas entre si, mas eles podem se sobrepor visualmente. Às vezes, um contorno único pode se sobrepor.

Um contorno geralmente começa com uma chamada para o método a seguir `SKPath`:

- `MoveTo` para iniciar uma nova delimitação

O argumento para esse método é um único ponto, você pode expressar como um `SKPoint` valor ou como separado X e Y coordenadas. O `MoveTo` chamada estabelece um ponto no início do contorno e um inicial *ponto atual*. Você pode chamar os métodos seguintes para continuar o contorno com uma linha ou curva de ponto atual em um ponto especificado no método, que se torna o novo ponto atual:

- `LineTo` Para adicionar uma linha reta para o caminho
- `ArcTo` Para adicionar um arco, que é uma linha a circunferência de um círculo ou elipse
- `CubicTo` Para adicionar um cúbica spline de Bézier
- `QuadTo` Para adicionar um quadrática spline de Bézier
- `ConicTo` Para adicionar um racional spline de Bézier quadrática, que pode processar com precisão as seções de conic (elipses, parábolas e hipérboles)

Nenhum desses métodos cinco contém todas as informações necessárias para descrever a linha ou curva. Cada um desses métodos cinco funciona em conjunto com o ponto atual estabelecido pela chamada de método imediatamente anteriores a ele. Por exemplo, o `LineTo` método adiciona uma linha reta para o contorno com base no ponto atual, portanto o parâmetro para `LineTo` é apenas um único ponto.

O `SKPath` classe também define métodos que têm os mesmos nomes que esses seis métodos, mas com um `R` no início:

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

O `R` significa *relativo*. Eles têm a mesma sintaxe que os métodos correspondentes sem o `R` , mas são relativas ao ponto atual. Eles são úteis para desenhar semelhantes partes de um caminho em um método que você chamar várias vezes.

Um contorno termina com outra chamada para `MoveTo` ou `RMoveTo`, que inicia uma nova delimitação ou uma chamada para `Close`, que fecha o contorno. O `Close` método automaticamente acrescenta uma linha reta do ponto atual para o primeiro ponto do contorno e marca o caminho como fechado, o que significa que ele será renderizado sem qualquer caps traçado.

A diferença entre delimitações abertas e fechadas é ilustrada no **dois contornos triângulo** página, que usa um `SKPath` objeto com dois contornos para processar dois triângulos. O contorno primeiro é aberto e o segundo é fechado. Aqui está o [ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

O contorno primeiro consiste em uma chamada para [ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/) usando coordenadas X e Y em vez de um `SKPoint` valor, seguido de três chamadas ao [ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/) para desenhar três lados das triângulo. O segundo contorno tem apenas duas chamadas para `LineTo` mas termina o contorno com uma chamada para [ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/), que fecha o contorno. A diferença é significativa:

[![](paths-images/twotrianglecontours-small.png "Tripla captura de tela da página de dois contornos do triângulo")](paths-images/twotrianglecontours-large.png#lightbox "tripla captura de tela da página de dois contornos do triângulo")

Como você pode ver, a primeira delimitação obviamente é uma série de três linhas conectadas, mas final não se conectar com o início. As duas linhas se sobrepor na parte superior. O segundo contorno obviamente é fechado e foi realizado com um número menor `LineTo` chama porque o `Close` método adiciona automaticamente uma linha final para fechar o contorno.

`SKCanvas` define apenas um [ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/) método, que é chamado duas vezes nesta demonstração para preencher e traçar o caminho. Todos os contornos são preenchidas, mesmo aqueles que não estão fechados. Para fins de preenchimento caminhos não fechados, uma linha reta deve para existir entre os pontos inicial e final das delimitações. Se você remover a última `LineTo` de delimitação primeiro, ou remova o `Close` chamada do segundo contorno, cada delimitação terá apenas dois lados mas será preenchido como se fosse um triângulo.

`SKPath` define vários métodos e propriedades. Os métodos a seguir adicionam delimitações inteiras para o caminho, o que pode ser fechado ou não fechado dependendo do método:

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) Para adicionar uma curva a circunferência de uma elipse
- `AddPath` Para adicionar outro caminho para o caminho atual
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) Para adicionar outro caminho na ordem inversa

Lembre-se de que uma `SKPath` objeto define apenas uma geometria &mdash; uma série de pontos e conexões. Somente quando um `SKPath` é combinado com um `SKPaint` objeto é o caminho renderizado com uma cor específica, largura do traço e assim por diante. Além disso, tenha em mente que o `SKPaint` objeto passado para o `DrawPath` método define características de todo o caminho. Se você quiser desenhar algo que exigem várias cores, você deve usar um caminho separado para cada cor.

Assim como a aparência de início e de término de uma linha é definida por um limite de traço, a aparência da conexão entre duas linhas é definida por um *junção traço*. Você pode especificar isso definindo o [ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/) propriedade de `SKPaint` a um membro do [ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/) enumeração:

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) uma junção pontudo
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) uma junção arredondada
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) uma junção embaralhados desativado

O **traço junções** página mostra essas três traçar junções com código semelhante do **traço Caps** página. Este é o `PaintSurface` manipulador de eventos de [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Aqui está o programa em execução em plataformas de três:

[![](paths-images/strokejoins-small.png "Tripla captura de tela da página traço une")](paths-images/strokejoins-large.png#lightbox "tripla captura de tela da página de junções de traço")

A junção de esquadria consiste em um ponto de curva em que as linhas se conectar. Ao unir duas linhas em um ângulo pequeno, a junção de esquadria pode se tornar muito longa. Para evitar que juntas de esquadrias excessivamente longo, o comprimento da junção esquadria é limitado pelo valor da [ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/) propriedade `SKPaint`. Uma junção de esquadria que excede esse comprimento é cortada para se tornar uma junção de inclinação.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
