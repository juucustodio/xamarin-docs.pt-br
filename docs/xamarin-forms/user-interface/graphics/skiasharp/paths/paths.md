---
title: Noções básicas de caminho no SkiaSharp
description: Este artigo explora o objeto de SkiaSharp SKPath para combinar linhas e curvas conectadas e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: c892adf2f75ec00c4a9ee171ded78f79bb8227e9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291549"
---
# <a name="path-basics-in-skiasharp"></a>Noções básicas de caminho no SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar o objeto SkiaSharp SKPath para combinar linhas e curvas conectadas_

Um dos recursos mais importantes do caminho gráfico é a capacidade de definir quando várias linhas devem ser conectadas e quando eles não devem estar conectados. A diferença pode ser significativa, como demonstram as partes superiores desses dois triângulos:

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

Um caminho de gráfico é encapsulado pelo objeto de [`SKPath`](xref:SkiaSharp.SKPath) . Um caminho é uma coleção de um ou mais *contornos*. Cada contorno é uma coleção de linhas retas e curvas *conectadas* . Delimitações não estão conectadas entre si, mas eles podem se sobrepor visualmente. Às vezes, uma única delimitação pode se sobrepor em si.

Uma delimitação geralmente começa com uma chamada para o seguinte método de `SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) iniciar uma nova delimitação

O argumento para esse método é um ponto único, que pode ser expresso como um valor `SKPoint` ou como coordenadas X e Y separadas. A chamada `MoveTo` estabelece um ponto no início da delimitação e um *ponto atual*inicial. Você pode chamar os métodos a seguir para continuar a delimitação com uma linha ou curva do ponto atual para um ponto especificado no método, que então se torna o novo ponto atual:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) adicionar uma linha reta ao caminho
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) adicionar um arco, que é uma linha na circunferência de um círculo ou elipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) adicionar um spline Bézier cúbico
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) adicionar um spline de Bezier quadrática
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) adicionar um spline de Bézier quadrática racional, que pode renderizar com precisão as seções de cone (reticências, parabolas e hiperbólicas)

Nenhum desses cinco métodos contêm todas as informações necessárias para descrever a linha ou curva. Cada um desses cinco métodos funciona em conjunto com o ponto atual estabelecido pela chamada de método imediatamente anterior a ele. Por exemplo, o método `LineTo` adiciona uma linha reta à delimitação com base no ponto atual, de modo que o parâmetro para `LineTo` é apenas um único ponto.

A classe `SKPath` também define métodos que têm os mesmos nomes que esses seis métodos, mas com uma `R` no início:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

O `R` significa *relativo*. Esses métodos têm a mesma sintaxe que os métodos correspondentes sem o `R`, mas são relativos ao ponto atual. Essas ferramentas são úteis para desenhar partes semelhantes de um caminho em um método que você chamar várias vezes.

Uma delimitação termina com outra chamada para `MoveTo` ou `RMoveTo`, que inicia uma nova delimitação ou uma chamada para `Close`, que fecha a delimitação. O método `Close` acrescenta automaticamente uma linha reta do ponto atual ao primeiro ponto da delimitação e marca o caminho como fechado, o que significa que ele será renderizado sem qualquer borda do traço.

A diferença entre os contornos aberto e fechado é ilustrada na página de **contornos de dois** triângulos, que usa um objeto `SKPath` com dois contornos para renderizar dois triangulares. A primeira delimitação é aberta e o segundo é fechado. Aqui está a classe [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) :

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

A primeira contorno consiste em uma chamada para [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usando coordenadas X e Y em vez de um valor `SKPoint`, seguido de três chamadas para [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) para desenhar os três lados do triângulo. A segunda delimitação tem apenas duas chamadas para `LineTo`, mas termina a delimitação com uma chamada para [`Close`](xref:SkiaSharp.SKPath.Close), que fecha a delimitação. A diferença é significativa:

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

Como você pode ver, a primeira delimitação é, obviamente, uma série de três linhas conectadas, mas final não se conectar com o início. As duas linhas se sobrepor na parte superior. A segunda delimitação é obviamente fechada e foi realizada com um número menor de chamadas de `LineTo` porque o método `Close` adiciona automaticamente uma linha final para fechar a delimitação.

`SKCanvas` define apenas um método [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) , que nesta demonstração é chamado duas vezes para preencher e traçar o caminho. Todos os contornos são preenchidas, mesmo aqueles que não estão fechados. Para fins de preenchimento de caminhos não fechados, é considerada uma linha reta para existir entre os pontos inicial e final das delimitações. Se você remover a última `LineTo` da primeira delimitação ou remover a chamada de `Close` da segunda delimitação, cada contorno terá apenas dois lados, mas será preenchido como se fosse um triângulo.

`SKPath` define muitos outros métodos e propriedades. Os seguintes métodos adicionam delimitações inteiras para o caminho, que pode ser fechado ou não fechado, dependendo do método:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) adicionar uma curva na circunferência de uma elipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) adicionar outro caminho ao caminho atual
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) adicionar outro caminho na ordem inversa

Tenha em mente que um objeto `SKPath` define apenas uma geometria &mdash; uma série de pontos e conexões. Somente quando um `SKPath` é combinado com um objeto `SKPaint` é o caminho processado com uma cor específica, a largura do traço e assim por diante. Além disso, tenha em mente que o objeto `SKPaint` passado para o método `DrawPath` define características de todo o caminho. Se você quiser desenhar algo que exigem várias cores, você deve usar um caminho separado para cada cor.

Assim como a aparência do início e do fim de uma linha é definida por uma ponta de traço, a aparência da conexão entre duas linhas é definida por uma *junção de traço*. Você especifica isso definindo a propriedade [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) de `SKPaint` como um membro da enumeração [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) :

- `Miter` para uma junção pontual
- `Round` para uma junção arredondada
- `Bevel` para uma junção cortados

A página de **junções de traço** mostra essas três junções de traço com código semelhante à página de **Caps de traço** . Este é o manipulador de eventos `PaintSurface` na classe [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) :

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

Este é o programa em execução:

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

A junção de Malhete consiste em um ponto de curva na qual as linhas de se conectar. Ao unir duas linhas em um ângulo pequeno, a junção de Malhete pode se tornar muito longa. Para evitar junções de Mitre excessivamente longas, o comprimento da junção de Mitre é limitado pelo valor da propriedade [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) de `SKPaint`. Uma junção de Malhete que excede esse comprimento é cortada para se tornar uma junção de bisel.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
