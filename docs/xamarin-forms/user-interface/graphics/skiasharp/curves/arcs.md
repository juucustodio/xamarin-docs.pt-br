---
title: Três formas de desenhar um arco
description: Este artigo explica como usar o SkiaSharp para definir arcos de três maneiras diferentes e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6aa52ff13dccbf6c7b65f7006195997971a7cac2
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609775"
---
# <a name="three-ways-to-draw-an-arc"></a>Três formas de desenhar um arco

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba como usar o SkiaSharp para definir arcos de três maneiras diferentes_

Um arco é uma curva na circunferência de uma elipse, como as partes arredondadas desse sinal de infinito:

![Sinal de infinito](arcs-images/arcsample.png)

Apesar da simplicidade dessa definição, não há como definir uma função de desenho de arco que atenda a todas as necessidades e, portanto, não há consenso entre os sistemas gráficos da melhor maneira de desenhar um arco. Por esse motivo, a `SKPath` classe não se restringe a apenas uma abordagem.

`SKPath` define um [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) método, cinco [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) métodos diferentes e dois métodos relativos [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) . Esses métodos se enquadram em três categorias, representando três abordagens muito diferentes para especificar um arco. O que você usa depende das informações disponíveis para definir o arco e de como esse arco se encaixa com os outros elementos gráficos que você está desenhando.

## <a name="the-angle-arc"></a>O arco de ângulo

A abordagem de arco angular para desenhar arcos requer que você especifique um retângulo que limite uma elipse. O arco na circunferência dessa elipse é indicado por ângulos do centro da elipse que indicam o início do arco e seu comprimento. Dois métodos diferentes desenham arcos angulares. Estes são os [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) métodos e o [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) método:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Esses métodos são idênticos aos [`AddArc`](xref:Android.Graphics.Path.AddArc*) métodos Android e [ `ArcTo` ] xref: Android. Graphics. Path. ArcTo *). O [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) método Ios é semelhante, mas é restrito a arcos na circunferência de um círculo em vez de generalizado para uma elipse.

Ambos os métodos começam com um `SKRect` valor que define o local e o tamanho de uma elipse:

![A elipse que começa um arco angular](arcs-images/anglearcoval.png)

O arco é uma parte da circunferência desta elipse.

O `startAngle` argumento é um ângulo no sentido horário em graus em relação a uma linha horizontal desenhada do centro da elipse à direita. O `sweepAngle` argumento é relativo ao `startAngle` . Aqui estão `startAngle` e `sweepAngle` valores de 60 graus e 100 graus, respectivamente:

![Os ângulos que definem um arco angular](arcs-images/anglearcangles.png)

O arco começa no ângulo inicial. Seu comprimento é regido pelo ângulo de flecha. O arco é mostrado aqui em vermelho:

![O arco de ângulo realçado](arcs-images/anglearchighlight.png)

A curva adicionada ao caminho com o `AddArc` método ou `ArcTo` é simplesmente a parte da circunferência da elipse:

![O arco de ângulo por si só](arcs-images/anglearc.png)

Os `startAngle` `sweepAngle` argumentos ou podem ser negativos: o arco é no sentido horário para valores positivos `sweepAngle` e no sentido anti-horário para valores negativos.

No entanto, `AddArc` o *não* define uma delimitação fechada. Se você chamar `LineTo` After `AddArc` , uma linha será desenhada a partir do final do arco até o ponto no `LineTo` método e o mesmo será verdadeiro `ArcTo` .

`AddArc` inicia automaticamente uma nova delimitação e é funcionalmente equivalente a uma chamada para `ArcTo` com um argumento final de `true` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Esse último argumento é chamado `forceMoveTo` e efetivamente causa uma `MoveTo` chamada no início do arco. Isso inicia uma nova delimitação. Esse não é o caso com um último argumento de `false` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Esta versão do `ArcTo` desenha uma linha da posição atual até o início do arco. Isso significa que o arco pode estar em algum lugar no meio de uma delimitação maior.

A página de **arco de ângulo** permite que você use dois controles deslizantes para especificar os ângulos de início e de varredura. O arquivo XAML instancia dois `Slider` elementos e um `SKCanvasView` . O `PaintCanvas` manipulador no arquivo [**AngleArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) desenha a oval e o arco usando dois `SKPaint` objetos definidos como campos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Como você pode ver, o ângulo inicial e o ângulo de flecha podem assumir valores negativos:

[![Captura de tela tripla da página de arco do ângulo](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

Essa abordagem para gerar um arco é forma algorítmica a mais simples, e é fácil derivar as equações paramétricas que descrevem o arco. Sabendo o tamanho e o local da elipse e os ângulos de início e de varredura, os pontos inicial e final do arco podem ser calculados usando Trigonometria simples:

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

O `angle` valor é `startAngle` ou `startAngle + sweepAngle` .

O uso de dois ângulos para definir um arco é melhor para casos em que você sabe o comprimento angular do arco que você deseja desenhar, por exemplo, para criar um gráfico de pizza. A página do **gráfico de pizza destacada** demonstra isso. A [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe usa uma classe interna para definir alguns dados e cores do criei:

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

O `PaintSurface` manipulador primeiro percorre os itens para calcular um `totalValues` número. A partir disso, ele pode determinar o tamanho de cada item como a fração do total e convertê-lo em um ângulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

Um novo `SKPath` objeto é criado para cada fatia de pizza. O caminho consiste em uma linha do centro, em seguida, um `ArcTo` para desenhar o arco e outra linha de volta para os resultados do centro da `Close` chamada. Este programa exibe fatias de pizza "explodidas" movendo-as para fora do centro em 50 pixels. Essa tarefa requer um vetor na direção do ponto médio do ângulo de flecha para cada fatia:

[![Captura de tela tripla da página do gráfico de pizza destacada](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

Para ver sua aparência sem a "explosão", basta comentar a `Translate` chamada:

[![Captura de tela tripla da página do gráfico de pizza destacada sem a explosão](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>O arco tangente

O segundo tipo de arco com suporte `SKPath` no é o *arco tangente*, portanto, chamado porque o arco é a circunferência de um círculo que é tangente a duas linhas conectadas.

Um arco tangente é adicionado a um caminho com uma chamada para o  [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método com dois `SKPoint` parâmetros, ou a [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga com `Single` parâmetros separados para os pontos:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Esse `ArcTo` método é semelhante à [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) função PostScript (página 532) e ao [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) método Ios.

O `ArcTo` método envolve três pontos:

- O ponto atual da delimitação ou o ponto (0, 0) se `MoveTo` não tiver sido chamado
- O primeiro argumento de ponto para o `ArcTo` método, chamado de *ponto de canto*
- O segundo argumento de ponto para `ArcTo` , chamado de *ponto de destino*:

![Três pontos que começam um arco tangente](arcs-images/tangentarcthreepoints.png)

Esses três pontos definem duas linhas conectadas:

![Linhas conectando os três pontos de um arco tangente](arcs-images/tangentarcconnectinglines.png)

Se os três pontos forem colineos &mdash; , se eles estiverem na mesma linha reta, &mdash; nenhum arco será desenhado.

O `ArcTo` método também inclui um `radius` parâmetro. Isso define o raio de um círculo:

![O círculo de um arco tangente](arcs-images/tangentarccircle.png)

O arco tangente não é generalizado para uma elipse.

Se as duas linhas forem atendidas em qualquer ângulo, esse círculo poderá ser inserido entre essas linhas para que seja tangente a ambas as linhas:

![O círculo de arco tangente entre as duas linhas](arcs-images/tangentarctangentcircle.png)

A curva que é adicionada à delimitação não toca em nenhum dos pontos especificados no `ArcTo` método. Ele consiste em uma linha reta do ponto atual até o primeiro ponto tangente e um arco que termina no segundo ponto tangente, mostrado aqui em vermelho:

![Diagrama mostra o diagrama anterior anotado com uma linha vermelha que mostra o arco tangente realçado entre as duas linhas.](arcs-images/tangentarchighlight.png)

Aqui está a linha reta final e o arco que é adicionado à delimitação:

![O arco tangente realçado entre as duas linhas](arcs-images/tangentarc.png)

A delimitação pode ser continuada a partir do segundo ponto tangente.

A página **arco tangente** permite que você experimente o arco tangente. Esta é a primeira de várias páginas que derivam de [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) , que definem alguns `SKPaint` objetos práticos e executa o `TouchPoint` processamento:

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

A classe `TangentArcPage` deriva de `InteractivePage`. O Construtor no arquivo [**TangentArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) é responsável por instanciar e inicializar a `touchPoints` matriz e definir `baseCanvasView` (in `InteractivePage` ) como o `SKCanvasView` objeto instanciado no arquivo [**TangentArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) :

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

O `PaintSurface` manipulador usa o `ArcTo` método para desenhar o arco com base nos pontos de toque e um `Slider` , mas também forma algorítmica calcula o círculo no qual o ângulo se baseia:

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }

    // Vector methods
    SKPoint Normalize(SKPoint v)
    {
        float magnitude = Magnitude(v);
        return new SKPoint(v.X / magnitude, v.Y / magnitude);
    }

    float Magnitude(SKPoint v)
    {
        return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
    }
}
```

Esta é a página de **arco tangente** em execução:

[![Captura de tela tripla da página de arco tangente](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

O arco tangente é ideal para criar cantos arredondados, como um retângulo arredondado. Como `SKPath` o já inclui um `AddRoundedRect` método, a página **arredondada heptagon** demonstra como usar `ArcTo` o para arredondar os cantos de um polígono de sete lados. (O código é generalizado para qualquer polígono regular.)

O `PaintSurface` manipulador da [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contém um `for` loop para calcular as coordenadas dos sete vértices do heptagon e um segundo para calcular os pontos médios dos sete lados desses vértices. Esses pontos médios são usados para construir o caminho:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Este é o programa em execução:

[![Captura de tela tripla da página arredondada heptagon](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>O arco elíptico

O arco elíptico é adicionado a um caminho com uma chamada para o [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) método que tem dois `SKPoint` parâmetros, ou a  [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) sobrecarga com coordenadas X e Y separadas:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

O arco elíptico é consistente com o [arco elíptico](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluído em SVG (gráfico vetorial escalonável) e na [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe plataforma universal do Windows.

Esses `ArcTo` métodos desenham um arco entre dois pontos, que são o ponto atual da delimitação e o último parâmetro para o `ArcTo` método (o `xy` parâmetro ou os `x` parâmetros e separados `y` ):

![Os dois pontos que definiram um arco elíptico](arcs-images/ellipticalarcpoints.png)

O primeiro parâmetro de ponto para o `ArcTo` método ( `r` ou `rx` e `ry` ) não é um ponto, mas sim especifica o raios horizontal e vertical de uma elipse;

![A elipse que definiu um arco elíptico](arcs-images/ellipticalarcellipse.png)

O `xAxisRotate` parâmetro é o número de graus no sentido horário para girar esta elipse:

![A elipse inclinada que definiu um arco elíptico](arcs-images/ellipticalarctiltedellipse.png)

Se essa elipse inclinada for posicionada de forma que ela toque nos dois pontos, os pontos serão conectados por dois arcos diferentes:

![O primeiro conjunto de arcos elípticos](arcs-images/ellipticalarcellipse1.png)

Esses dois arcos podem ser diferenciados de duas maneiras: o arco superior é maior do que o arco inferior e, à medida que o arco é desenhado da esquerda para a direita, o arco superior é desenhado em uma direção no sentido horário enquanto o arco inferior é desenhado em uma direção no sentido anti-horário.

Também é possível ajustar a elipse entre os dois pontos de outra maneira:

![O segundo conjunto de arcos elípticos](arcs-images/ellipticalarcellipse2.png)

Agora, há um arco menor na parte superior que é desenhado no sentido horário e um arco maior na parte inferior que é desenhado no sentido anti-horário.

Portanto, esses dois pontos podem ser conectados por um arco definido pela elipse inclinada em um total de quatro maneiras:

![Todos os quatro arcos elípticos](arcs-images/ellipticalarccolors.png)

Esses quatro arcos são diferenciados pelas quatro combinações dos [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) argumentos e do [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) tipo de enumeração para o `ArcTo` método:

- vermelho: SKPathArcSize. Large e SKPathDirection. no sentido horário
- verde: SKPathArcSize. Small e SKPathDirection. no sentido horário
- Blue: SKPathArcSize. Small e SKPathDirection. anti-horário
- magenta: SKPathArcSize. Large e SKPathDirection. anti-horário

Se a elipse inclinada não for grande o suficiente para se ajustar entre os dois pontos, ela será dimensionada uniformemente até que seja grande o suficiente. Somente dois arcos exclusivos conectam os dois pontos nesse caso. Eles podem ser diferenciados com o `SKPathDirection` parâmetro.

Embora essa abordagem para definir um arco pareça complexa na primeira apresentação, é a única abordagem que permite definir um arco com uma elipse girada, e geralmente é a abordagem mais fácil quando você precisa integrar arcos com outras partes da delimitação.

A página de **arco elíptico** permite definir interativamente os dois pontos e o tamanho e a rotação da elipse. A `EllipticalArcPage` classe deriva de `InteractivePage` e o `PaintSurface` manipulador no arquivo code-behind [**EllipticalArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) desenha os quatro arcos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

Aqui está executando:

[![Captura de tela tripla da página de arco elíptico](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

A página **Infinity de arco** usa o arco elíptico para desenhar um sinal de infinito. O sinal de infinito se baseia em dois círculos com raios de 100 unidades separadas por 100 unidades:

![Dois círculos](arcs-images/infinitycircles.png)

Duas linhas que se cruzam entre si são tangentes a ambos os círculos:

![Dois círculos com linhas tangentes](arcs-images/infinitycircleslines.png)

O sinal de infinito é uma combinação de partes desses círculos e das duas linhas. Para usar o arco elíptico para desenhar o sinal de infinito, as coordenadas em que as duas linhas são tangentes para os círculos devem ser determinadas.

Construa um retângulo direito em um dos círculos:

![Dois círculos com linhas tangentes e círculo incorporado](arcs-images/infinitytriangle.png)

O raio do círculo é de 100 unidades e o hipotenusa do triângulo é 150 unidades, portanto, o ângulo α é o arco seno (seno inverso) de 100 dividido por 150 ou 41,8 graus. O comprimento do outro lado do triângulo é 150 vezes o cosseno de 41,8 graus ou 112, que também pode ser calculado pelo teorema Pitágoras.

As coordenadas do ponto tangente podem ser calculadas usando essas informações:

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

Os quatro pontos tangentes são tudo o que é necessário para desenhar um sinal de infinito centralizado no ponto (0, 0) com raios de círculo de 100:

![Dois círculos com linhas e coordenadas da tangente](arcs-images/infinitycoordinates.png)

O `PaintSurface` manipulador na [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe posiciona o sinal de infinito para que o (0, 0) aponte esteja posicionado no centro da página e dimensione o caminho para o tamanho da tela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
                              info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

O código usa a `Bounds` propriedade de `SKPath` para determinar as dimensões do seno infinito para dimensioná-lo para o tamanho da tela:

[![Captura de tela tripla da página de infinito do arco](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

O resultado parece um pouco pequeno, o que sugere que a `Bounds` propriedade de `SKPath` está relatando um tamanho maior do que o caminho.

Internamente, skia aproxima o arco usando várias curvas Bézier quadráticas. Essas curvas (como você verá na próxima seção) contêm pontos de controle que regem como a curva é desenhada, mas não fazem parte da curva renderizada. A `Bounds` propriedade inclui esses pontos de controle.

Para obter um ajuste mais rígido, use a `TightBounds` propriedade, que exclui os pontos de controle. Aqui está o programa em execução no modo paisagem e usando a `TightBounds` propriedade para obter os limites do caminho:

[![Captura de tela tripla da página de infinito do arco com limites apertados](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

Embora as conexões entre os arcos e as linhas retas sejam matematicamente suaves, a alteração de arco para linha reta pode parecer um pouco abrupta. Um sinal de infinito melhor é apresentado no próximo artigo sobre [**três tipos de curvas Bézier**](beziers.md).

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)