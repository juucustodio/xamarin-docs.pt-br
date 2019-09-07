---
title: Noções básicas de caminho no SkiaSharp
description: Este artigo explora o objeto de SkiaSharp SKPath para combinar linhas e curvas conectadas e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: eee338461593ad131f679d32cadf63fe3b1a4c40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759341"
---
# <a name="path-basics-in-skiasharp"></a>Noções básicas de caminho no SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar o objeto de SkiaSharp SKPath para combinar linhas e curvas conectadas_

Um dos recursos mais importantes do caminho gráfico é a capacidade de definir quando várias linhas devem ser conectadas e quando eles não devem estar conectados. A diferença pode ser significativa, como demonstram as partes superiores desses dois triângulos:

![](paths-images/connectedlinesexample.png "Dois triângulos que mostra a diferença entre as linhas conectadas e desconectadas")

Um caminho gráfico é encapsulado pela [ `SKPath` ](xref:SkiaSharp.SKPath) objeto. Um caminho é uma coleção de um ou mais *delimitações*. Cada contorno é uma coleção de *conectados* linhas retas e curvas. Delimitações não estão conectadas entre si, mas eles podem se sobrepor visualmente. Às vezes, uma única delimitação pode se sobrepor em si.

Um contorno geralmente começa com uma chamada para o método a seguir `SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) para iniciar uma nova delimitação

O argumento para esse método é um único ponto, você pode expressar como um `SKPoint` de valor ou como separado de X e Y coordena. O `MoveTo` chamada estabelece um ponto no início de uma inicial e da delimitação *ponto atual*. Você pode chamar os métodos a seguir para continuar a delimitação com uma linha ou curva do ponto atual para um ponto especificado no método, que então se torna o novo ponto atual:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) Para adicionar uma linha reta ao caminho
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) Para adicionar um arco, que é uma linha na circunferência de um círculo ou elipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) Para adicionar uma spline de Bézier cúbica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) Para adicionar uma spline de Bézier quadrática
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) Para adicionar um racional spline de Bézier quadrático, que pode processar com precisão as seções de conic (elipses, parábolas e hipérboles)

Nenhum desses cinco métodos contêm todas as informações necessárias para descrever a linha ou curva. Cada um desses cinco métodos funciona em conjunto com o ponto atual estabelecido pela chamada de método imediatamente anterior a ele. Por exemplo, o `LineTo` método adiciona uma linha reta para a delimitação com base no ponto atual, portanto, o parâmetro para `LineTo` é apenas um único ponto.

O `SKPath` classe também define métodos que têm os mesmos nomes que esses seis métodos, mas com um `R` no início:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

O `R` signifca *relativo*. Esses métodos têm a mesma sintaxe que os métodos correspondentes sem o `R` , mas são relativos ao ponto atual. Essas ferramentas são úteis para desenhar partes semelhantes de um caminho em um método que você chamar várias vezes.

Um contorno termina com outra chamada para `MoveTo` ou `RMoveTo`, que inicia uma nova delimitação ou uma chamada para `Close`, que fecha a delimitação. O `Close` método automaticamente acrescenta uma linha reta do ponto atual para o primeiro ponto da delimitação e marca o caminho como fechado, o que significa que ele será renderizado sem qualquer caps do traço.

A diferença entre delimitações abertas e fechadas é ilustrada na **dois contornos de triângulo** página, que usa um `SKPath` objeto com dois contornos para renderizar dois triângulos. A primeira delimitação é aberta e o segundo é fechado. Aqui está o [ `TwoTriangleContoursPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) classe:

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

A delimitação primeiro consiste em uma chamada para [ `MoveTo` ](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usando coordenadas X e Y em vez de uma `SKPoint` valor, seguido por três chamadas para [ `LineTo` ](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) para desenhar os três lados das triângulo. A segunda delimitação tem apenas duas chamadas para `LineTo` , mas ela é concluída a delimitação com uma chamada para [ `Close` ](xref:SkiaSharp.SKPath.Close), que fecha a delimitação. A diferença é significativa:

[![](paths-images/twotrianglecontours-small.png "Tripla captura de tela da página de dois contornos de triângulo")](paths-images/twotrianglecontours-large.png#lightbox "tripla captura de tela da página de dois contornos de triângulo")

Como você pode ver, a primeira delimitação é, obviamente, uma série de três linhas conectadas, mas final não se conectar com o início. As duas linhas se sobrepor na parte superior. A segunda delimitação, obviamente, foi fechada e era realizada com um número menor `LineTo` chama porque o `Close` método adiciona automaticamente uma linha final para fechar a delimitação.

`SKCanvas` define apenas um [ `DrawPath` ](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) método, que, nesta demonstração, é chamado duas vezes para preencher e traçar o demarcador. Todos os contornos são preenchidas, mesmo aqueles que não estão fechados. Para fins de preenchimento de caminhos não fechados, é considerada uma linha reta para existir entre os pontos inicial e final das delimitações. Se você remover a última `LineTo` da primeira delimitação ou remover o `Close` chamada da delimitação segundo, cada delimitação terão apenas dois lados, mas serão ser preenchido como se fosse um triângulo.

`SKPath` define vários métodos e propriedades. Os seguintes métodos adicionam delimitações inteiras para o caminho, que pode ser fechado ou não fechado, dependendo do método:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) Para adicionar uma curva na circunferência de uma elipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) Para adicionar outro caminho para o caminho atual
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) Para adicionar outro caminho na ordem inversa

Tenha em mente que um `SKPath` objeto define apenas uma geometria &mdash; uma série de pontos e conexões. Somente quando um `SKPath` combinada com um `SKPaint` objeto é o caminho renderizado com uma determinada cor, largura do traço e assim por diante. Além disso, tenha em mente que o `SKPaint` objeto passado para o `DrawPath` método define as características de todo o caminho. Se você quiser desenhar algo que exigem várias cores, você deve usar um caminho separado para cada cor.

Assim como a aparência de início e no final de uma linha é definida por um limite do traço, a aparência da conexão entre duas linhas é definida por uma *junção traço*. Você pode especificar isso definindo a [ `StrokeJoin` ](xref:SkiaSharp.SKPaint.StrokeJoin) propriedade do `SKPaint` a um membro da [ `SKStrokeJoin` ](xref:SkiaSharp.SKStrokeJoin) enumeração:

- `Miter` para uma junção pontuda
- `Round` para uma junção de cantos arredondada
- `Bevel` para uma junção waited-off

O **junções de traço** página mostra os três traçar as junções com código semelhante do **traço Caps** página. Esse é o `PaintSurface` manipulador de eventos em de [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) classe:

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

Aqui está o programa em execução:

[![](paths-images/strokejoins-small.png "Tripla captura de tela da página traço une")](paths-images/strokejoins-large.png#lightbox "tripla captura de tela da página de junções de traço")

A junção de Malhete consiste em um ponto de curva na qual as linhas de se conectar. Ao unir duas linhas em um ângulo pequeno, a junção de Malhete pode se tornar muito longa. Para evitar esquadrias excessivamente longo, o comprimento da junção de Malhete é limitado pelo valor da [ `StrokeMiter` ](xref:SkiaSharp.SKPaint.StrokeMiter) propriedade `SKPaint`. Uma junção de Malhete que excede esse comprimento é cortada para se tornar uma junção de bisel.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
