---
title: Noções básicas de caminho em SkiaSharp
description: Este artigo explora o objeto SkiaSharp SKPath para combinar linhas e curvas conectadas e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6387da5ffa240c2509a2942a1e721def8f8d39b9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555626"
---
# <a name="path-basics-in-skiasharp"></a>Noções básicas de caminho em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar o objeto SkiaSharp SKPath para combinar linhas e curvas conectadas_

Um dos recursos mais importantes do caminho gráfico é a capacidade de definir quando várias linhas devem ser conectadas e quando elas não devem ser conectadas. A diferença pode ser significativa, pois a parte superior desses dois triângulos demonstra:

![Dois triângulos que mostram a diferença entre as linhas conectadas e desconectadas](paths-images/connectedlinesexample.png)

Um caminho de gráfico é encapsulado pelo [`SKPath`](xref:SkiaSharp.SKPath) objeto. Um caminho é uma coleção de um ou mais *contornos*. Cada contorno é uma coleção de linhas retas e curvas *conectadas* . Os contornos não estão conectados entre si, mas podem se sobrepor visualmente. Às vezes, uma única delimitação pode se sobrepor.

Uma delimitação geralmente começa com uma chamada para o seguinte método de `SKPath` :

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) para iniciar uma nova delimitação

O argumento para esse método é um ponto único, que pode ser expresso como um `SKPoint` valor ou como coordenadas X e Y separadas. A `MoveTo` chamada estabelece um ponto no início da delimitação e um *ponto atual*inicial. Você pode chamar os seguintes métodos para continuar a delimitação com uma linha ou curva do ponto atual para um ponto especificado no método, que, em seguida, se torna o novo ponto atual:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) para adicionar uma linha reta ao caminho
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) para adicionar um arco, que é uma linha na circunferência de um círculo ou elipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) para adicionar um spline Bézier cúbico
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) para adicionar um spline de Bezier quadrática
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) para adicionar um spline de Bézier quadrática racional, que pode renderizar com precisão seções de cone (reticências, parabolas e hiperbólicas)

Nenhum desses cinco métodos contém todas as informações necessárias para descrever a linha ou a curva. Cada um desses cinco métodos funciona em conjunto com o ponto atual estabelecido pela chamada de método imediatamente antes dele. Por exemplo, o `LineTo` método adiciona uma linha reta à delimitação com base no ponto atual, portanto, o parâmetro para `LineTo` é apenas um único ponto.

A `SKPath` classe também define métodos que têm os mesmos nomes que esses seis métodos, mas com um `R` no início:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

O `R` significa *relativo*. Esses métodos têm a mesma sintaxe que os métodos correspondentes sem o `R` , mas são relativos ao ponto atual. Elas são úteis para desenhar partes semelhantes de um caminho em um método que você chama várias vezes.

Uma delimitação termina com outra chamada para `MoveTo` ou `RMoveTo` , que inicia uma nova delimitação ou uma chamada para `Close` , que fecha a delimitação. O `Close` método acrescenta automaticamente uma linha reta do ponto atual ao primeiro ponto da delimitação e marca o caminho como fechado, o que significa que ele será renderizado sem qualquer borda do traço.

A diferença entre os contornos aberto e fechado é ilustrada na página de **contornos de dois** triângulos, que usa um `SKPath` objeto com dois contornos para renderizar dois triangulares. A primeira delimitação é aberta e a segunda é fechada. Esta é a [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) classe:

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

A primeira contorno consiste em uma chamada para [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usar coordenadas X e Y em vez de um `SKPoint` valor, seguido por três chamadas para [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) para desenhar os três lados do triângulo. A segunda delimitação tem apenas duas chamadas para `LineTo` , mas termina a delimitação com uma chamada para [`Close`](xref:SkiaSharp.SKPath.Close) , que fecha a delimitação. A diferença é significativa:

[![Captura de tela tripla da página de contornos de dois triângulos](paths-images/twotrianglecontours-small.png)](paths-images/twotrianglecontours-large.png#lightbox "Captura de tela tripla da página de contornos de dois triângulos")

Como você pode ver, a primeira delimitação é, obviamente, uma série de três linhas conectadas, mas o final não se conecta com o início. As duas linhas se sobrepõem na parte superior. A segunda delimitação é obviamente fechada e foi realizada com uma quantidade menor de `LineTo` chamadas porque o `Close` método adiciona automaticamente uma linha final para fechar a delimitação.

`SKCanvas` define apenas um [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) método, que nesta demonstração é chamado duas vezes para preencher e traçar o caminho. Todos os contornos são preenchidos, mesmo aqueles que não estão fechados. Para fins de preenchimento de caminhos não fechados, presume-se que uma linha reta exista entre os pontos inicial e final dos contornos. Se você remover o último `LineTo` da primeira delimitação ou remover a `Close` chamada da segunda delimitação, cada contorno terá apenas dois lados, mas será preenchido como se fosse um triângulo.

`SKPath` define muitos outros métodos e propriedades. Os métodos a seguir adicionam contornos inteiros ao caminho, que podem ser fechados ou não fechados, dependendo do método:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) para adicionar uma curva na circunferência de uma elipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) para adicionar outro caminho ao caminho atual
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) para adicionar outro caminho na ordem inversa

Tenha em mente que um `SKPath` objeto define apenas um Geometry &mdash; de uma série de pontos e conexões. Somente quando um `SKPath` é combinado com um `SKPaint` objeto, o caminho é processado com uma cor específica, a largura do traço e assim por diante. Além disso, tenha em mente que o `SKPaint` objeto passado para o `DrawPath` método define características de todo o caminho. Se você quiser desenhar algo que exija várias cores, deverá usar um caminho separado para cada cor.

Assim como a aparência do início e do fim de uma linha é definida por uma ponta de traço, a aparência da conexão entre duas linhas é definida por uma *junção de traço*. Você especifica isso definindo a [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) propriedade de `SKPaint` como um membro da [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) enumeração:

- `Miter` para uma junção pontual
- `Round` para uma junção arredondada
- `Bevel` para uma junção cortados

A página de **junções de traço** mostra essas três junções de traço com código semelhante à página de **Caps de traço** . Esse é o `PaintSurface` manipulador de eventos na [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) classe:

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

[![Captura de tela tripla da página de junções de traço](paths-images/strokejoins-small.png)](paths-images/strokejoins-large.png#lightbox "Captura de tela tripla da página de junções de traço")

A junção de mitra consiste em um ponto nítido onde as linhas se conectam. Quando duas linhas entram em um ângulo pequeno, a junção de mitre pode se tornar muito longa. Para evitar junções de mitra excessivamente longas, o comprimento da junção de Mitre é limitado pelo valor da [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) propriedade de `SKPaint` . Uma junção de mitra que excede esse comprimento é cortados para se tornar uma junção chanfrada.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)