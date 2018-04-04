---
title: Três maneiras para desenhar um arco
description: Saiba como usar SkiaSharp para definir arcos de três maneiras diferentes
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: charlespetzold
ms.author: chape
ms.date: 05/10/2017
ms.openlocfilehash: 668b1f437b78535bd4cdf3bb3f80154dbf281a02
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="three-ways-to-draw-an-arc"></a>Três maneiras para desenhar um arco

_Saiba como usar SkiaSharp para definir arcos de três maneiras diferentes_

Um arco é uma curva a circunferência de uma elipse, como as partes arredondadas deste sinal infinito:

![](arcs-images/arcsample.png "Símbolo de infinito")

Apesar da simplicidade dessa definição, não é possível definir uma função de desenho arco que atenda a todas as necessidades e, portanto, nenhum consenso entre sistemas gráficos da melhor forma de desenhar um arco. Por esse motivo, a `SKPath` classe não se restringe a apenas uma abordagem.

`SKPath` define uma `AddArc` método, diferente de cinco `ArcTo` métodos e dois relativo `RArcTo` métodos. Esses métodos se enquadram em três categorias, que representa três abordagens diferentes para especificar um arco. Que você usa depende das informações disponíveis para definir o arco e como esse arco se adapta os outros elementos de gráficos que você estiver desenhando.

## <a name="the-angle-arc"></a>O ângulo em arco

A abordagem de arco do ângulo de desenho arcos requer que você especifique um retângulo que circunda uma elipse. O arco na circunferência dessa elipse é indicado por ângulos do centro da elipse fazendo o início do arco e seu tamanho. Dois métodos diferentes desenham arcos ângulo. Estes são os [ `AddArc` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) método e o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKRect/System.Single/System.Single/System.Boolean/) método:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Esses métodos são idênticos para o Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/) e [ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/) métodos. O iOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) método é semelhante, mas é restrito a arcos na circunferência de um círculo em vez de generalizado para uma elipse.

Ambos os métodos começam com um `SKRect` valor que define o local e o tamanho de uma elipse:

![](arcs-images/anglearcoval.png "A elipse que inicia um arco de ângulo")

O arco é uma parte entre a circunferência dessa elipse.

O `startAngle` é um ângulo em graus em relação a uma linha horizontal desenhada do Centro de elipse à direita de no sentido horário. O `sweepAngle` é relativo a `startAngle`. Aqui estão `startAngle` e `sweepAngle` valores de 100 e 60 graus, respectivamente:

![](arcs-images/anglearcangles.png "Os ângulos que definem um arco de ângulo")

O arco começa o ângulo inicial. Seu comprimento é regido pelo ângulo de flecha:

![](arcs-images/anglearchighlight.png "O arco ângulo realçado")

A curva adicionada ao caminho com o `AddArc` ou `ArcTo` método é simplesmente a parte de circunferência da elipse, mostrada aqui em vermelho:

![](arcs-images/anglearc.png "O arco ângulo sozinho")

O `startAngle` ou `sweepAngle` argumentos podem ser negativos: O arco é no sentido horário para valores positivos de `sweepAngle` e no sentido anti-horário para valores negativos.

No entanto, `AddArc` does *não* definir um contorno fechado. Se você chamar `LineTo` depois `AddArc`, uma linha é desenhada da extremidade do arco até o ponto no `LineTo` método e o mesmo é verdadeiro para `ArcTo`.

`AddArc` inicia uma nova delimitação automaticamente e é funcionalmente equivalente a uma chamada para `ArcTo` com um argumento final de `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Se o último argumento é chamado `forceMoveTo`, e faz com que efetivamente um `MoveTo` chamada no início do arco. Que inicia uma nova delimitação. Este não for o caso com um último argumento de `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Esta versão do `ArcTo` desenha uma linha da posição atual para o início do arco. Isso significa que o arco pode ser em algum lugar no meio de um contorno maior.

O **ângulo arco** página permite que você use dois controles deslizantes para especificar o início e ângulos de varredura. O arquivo XAML instancia dois `Slider` elementos e um `SKCanvasView`. O `PaintCanvas` manipulador no [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) arquivo desenha a elipse e o arco usando duas `SKPaint` objetos definidos como campos:

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

[![](arcs-images/anglearc-small.png "Tripla captura da página do ângulo arco")](arcs-images/anglearc-large.png#lightbox "tripla captura da página do arco do ângulo")

Essa abordagem para gerar um arco é o algoritmo mais simples e é fácil derivar as equações paramétricos que descrevem o arco. Saber o tamanho e o local da elipse e os ângulos de início e de varredura, os pontos inicial e final do arco pode ser calculados usando trigonometria simple:

x = oval. MidX + (oval. Largura / 2) * cos(angle)

y = oval. MidY + (oval. Altura / 2) * sin(angle)

O `angle` valor seja `startAngle` ou `startAngle + sweepAngle`.

O uso de dois ângulos para definir um arco é melhor para casos em que você sabe que o comprimento angular do arco que você deseja para desenhar, por exemplo, para criar um gráfico de pizza. O **gráfico de pizza destacada** página demonstra isso. O [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe usa uma classe interna para definir alguns dados fabricados e cores:

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

O `PaintSurface` manipulador primeiro percorre os itens para calcular um `totalValues` número. De que, ele pode determinar o tamanho de cada item como a fração do total e converter isso em um ângulo:

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

Um novo `SKPath` objeto é criado para cada fatia da pizza. O caminho consiste em uma linha do centro, então um `ArcTo` para desenhar o arco e outra linha de volta para os resultados do centro do `Close` chamar. Este programa exibe fatias da pizza "destacado", movendo-os de tudo no Centro por 50 pixels. Essa tarefa requer um vetor de direção do ponto central do ângulo de flecha de cada fatia:

[![](arcs-images/explodedpiechart-small.png "Captura de tela da página de gráfico de pizza destacada tripla")](arcs-images/explodedpiechart-large.png#lightbox "tripla captura da página do gráfico de pizza destacada")

Para ver sua aparência sem "explosão", simplesmente comentar o `Translate` chamar:

[![](arcs-images/explodedpiechartunexploded-small.png "Tripla captura da página do gráfico de pizza destacada sem explosão")](arcs-images/explodedpiechartunexploded-large.png#lightbox "tripla captura da página do gráfico de pizza destacada sem o detalhamento")

## <a name="the-tangent-arc"></a>O arco tangente

O segundo tipo de suporte de arco `SKPath` é o *arco tangente*, assim chamada porque o arco é a circunferência de um círculo tangente em duas linhas conectadas.

Um arco tangente é adicionado a um caminho com uma chamada para o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/) método com dois `SKPoint` parâmetros, ou o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/) sobrecarga com separado `Single` parâmetros para o pontos:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Isso `ArcTo` método é semelhante do PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) função (página 532 no documento PDF) e o iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) método.

O `ArcTo` método envolve três pontos:

- Atual do ponto do contorno, ou o ponto (0, 0) se `MoveTo` não foi chamado
- O primeiro argumento de ponto para o `ArcTo` método, chamado de *ponto do canto*
- O segundo argumento de ponto para `ArcTo`, chamado de *ponto de destino*:

![](arcs-images/tangentarcthreepoints.png "Três pontos que iniciam um arco tangente")

Esses três pontos definem duas linhas conectadas:

![](arcs-images/tangentarcconnectinglines.png "Linhas que conectam os três pontos de um arco tangente")

Se os três pontos são colinear &mdash; ou seja, se eles residem na mesma linha reta &mdash; nenhum arco será desenhado.

O `ArcTo` método também inclui um `radius` parâmetro. Isso define o raio de um círculo:

![](arcs-images/tangentarccircle.png "O círculo de um arco tangente")

O arco tangente não é generalizado para uma elipse.

Se as duas linhas atendem em qualquer ângulo, que círculo pode ser inserido entre as linhas para que ele seja tangente para as duas linhas:

![](arcs-images/tangentarctangentcircle.png "O círculo arco tangente entre as duas linhas")

A curva é adicionada para o contorno não toca em qualquer um dos pontos de especificado no `ArcTo` método. Ele consiste em uma linha reta do ponto atual para o primeiro ponto de tangente e um arco que termina o segundo ponto de tangente:

![](arcs-images/tangentarchighlight.png "O arco tangente realçado entre as duas linhas")

Aqui está a linha reta final e arco é adicionado para o contorno:

![](arcs-images/tangentarc.png "O arco tangente realçado entre as duas linhas")

É possível continuar o contorno do segundo ponto de tangente.

O **arco tangente** página permite fazer experiências com o arco tangente. Este é o primeiro de várias páginas que derivam de [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/InteractivePage.cs), que define algumas útil `SKPaint` objetos e executa `TouchPoint` processamento:

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

O `TangentArcPage` classe deriva de `InteractivePage`. O construtor o [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) arquivo é responsável por criar uma instância e inicializar o `touchPoints` matriz e configuração `baseCanvasView` (em `InteractivePage`) para o `SKCanvasView` objeto instanciado no [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) arquivo:

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

O `PaintSurface` manipulador utiliza o `ArcTo` método para desenhar o arco com base nos pontos de toque e um `Slider`, mas também o algoritmo calcula o círculo o ângulo com base em:

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

Aqui está o **arco tangente** página em execução em todas as três plataformas:

[![](arcs-images/tangentarc-small.png "Captura de tela da página arco tangente tripla")](arcs-images/tangentarc-large.png#lightbox "tripla captura da página do arco tangente")

No dispositivo móvel do Windows, os três pontos são quase colinear e o arco é muito pequeno.

O arco tangente é ideal para a criação de cantos arredondados, como um retângulo arredondado. Porque `SKPath` já inclui um `AddRoundedRect` método, o **arredondado Heptágono** página demonstra como usar `ArcTo` para arredondar os cantos de um polígono sete lados. (O código é generalizado para qualquer polígono regular).

O `PaintSurface` manipulador do [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contém um `for` loop para calcular as coordenadas dos sete vértices o Heptágono e um segundo para calcular os pontos médios dos sete lados com esses vértices. Esses pontos médios são usados para construir o caminho:

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

Aqui está o programa em execução em plataformas de três:

[![](arcs-images/roundedheptagon-small.png "Captura de tela da página Heptágono arredondado tripla")](arcs-images/roundedheptagon-large.png#lightbox "tripla captura de tela da página Heptágono arredondado")

## <a name="the-elliptical-arc"></a>O arco elíptico

O arco elíptico é adicionado a um caminho com uma chamada para o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/SkiaSharp.SKPoint/) método que tem duas `SKPoint` parâmetros, ou o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/System.Single/System.Single/) coordenadas de sobrecarga com separado X e Y:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

O arco elíptico é consistente com o [arco elíptico](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluídos em gráficos escalonáveis de vetor (SVG) e a plataforma Universal do Windows [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe.

Essas `ArcTo` métodos desenham um arco entre dois pontos, que é o ponto atual do contorno, e o último parâmetro para o `ArcTo` método (o `xy` parâmetro ou separadas `x` e `y` parâmetros):

![](arcs-images/ellipticalarcpoints.png "Os dois pontos que definiu um arco elíptico")

O primeiro parâmetro de ponto para o `ArcTo` método (`r`, ou `rx` e `ry`) não é um ponto em todos os mas em vez disso, especifica os raios horizontais e verticais de uma elipse;

![](arcs-images/ellipticalarcellipse.png "A elipse que definiu um arco elíptico")

O `xAxisRotate` parâmetro é o número de graus no sentido horário para girar essa elipse:

![](arcs-images/ellipticalarctiltedellipse.png "Elipse inclinada que definiu um arco elíptico")

Se essa elipse inclinada é posicionado, em seguida, para que ele toque os dois pontos, os pontos são conectados por dois arcos diferentes:

![](arcs-images/ellipticalarcellipse1.png "O primeiro conjunto de arcos elípticos")

Esses dois arcos podem ser distinguidos de duas maneiras: O arco superior é maior do que o arco inferior, e como o arco é desenhado da esquerda para a direita, o arco superior é desenhado no sentido horário, enquanto o arco inferior é desenhado no sentido anti-horário.

Também é possível ajustar a elipse entre os dois pontos de outra maneira:

![](arcs-images/ellipticalarcellipse2.png "O segundo conjunto de arcos elípticos")

Agora há um arco menor na parte superior que é desenhada no sentido horário e um arco maior na parte inferior que é desenhada no sentido anti-horário.

Esses dois pontos, portanto, podem ser conectados por um arco definido por elipse inclinada em um total de quatro maneiras:

![](arcs-images/ellipticalarccolors.png "Todos os quatro arcos elípticos")

Essas quatro arcos são diferenciados por quatro combinações do [ `SKPathArcSize` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathArcSize/) e [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/) argumentos de tipo de enumeração para o `ArcTo` método:

- vermelho: SKPathArcSize.Large e SKPathDirection.Clockwise
- verde: SKPathArcSize.Small e SKPathDirection.Clockwise
- azul: SKPathArcSize.Small e SKPathDirection.CounterClockwise
- magenta: SKPathArcSize.Large e SKPathDirection.CounterClockwise

Se a elipse inclinada não é grande o suficiente para caber entre os dois pontos, em seguida, ele é dimensionado uniformemente até que seja grande o suficiente. Somente dois arcos exclusivos conectam os dois pontos nesse caso. Eles podem ser distinguidos com o `SKPathDirection` parâmetro.

Embora essa abordagem para definir um arco parece complexa em encontrada pela primeira vez, essa é a abordagem única que permite definir um arco com uma elipse girada e geralmente é a abordagem mais fácil quando você precisa integrar arcos com outras partes do contorno.

O **arco elíptico** página permite que você defina interativamente os dois pontos e o tamanho e a rotação da elipse. O `EllipticalArcPage` classe derivada de `InteractivePage`e o `PaintSurface` manipulador no [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) arquivo code-behind desenha quatro arcos:

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

Aqui, ele é executado em três plataformas:

[![](arcs-images/ellipticalarc-small.png "Captura de tela da página arco elíptico tripla")](arcs-images/ellipticalarc-large.png#lightbox "tripla captura da página do arco elíptico")

O **infinito arco** página usa o arco elíptico para desenhar um símbolo de infinito. O símbolo de infinito se baseia em dois círculos com raios dos 100 unidades separadas por 100 unidades:

![](arcs-images/infinitycircles.png "Dois círculos")

Cruzando entre si de duas linhas são tangente para ambos os círculos:

![](arcs-images/infinitycircleslines.png "Dois círculos com linhas tangentes")

O símbolo de infinito é uma combinação de partes desses círculos e as duas linhas. Para usar o arco elíptico para desenhar o símbolo de infinito, as coordenadas em que as duas linhas são tangente para os círculos devem ser determinadas.

Construa um retângulo à direita em um dos círculos:

![](arcs-images/infinitytriangle.png "Dois círculos com linhas tangentes e círculo incorporado")

O raio do círculo é de 100 unidades e hipotenusa do triângulo é 150 unidades, portanto, o ângulo α é o arco seno (seno inverso) de 100 dividido por 150 ou 41.8 graus. O comprimento do lado do triângulo é 150 vezes o cosseno de graus 41.8 ou 112, que também pode ser calculado pelo Teorema de Pitágoras.

As coordenadas de ponto de tangente, em seguida, podem ser calculadas usando essas informações:

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

Os quatro pontos tangentes são tudo o que é necessário desenhar um sinal de infinito centralizado no ponto (0, 0) com raios círculo de 100:

![](arcs-images/infinitycoordinates.png "Dois círculos com coordenadas e linhas tangentes")

O `PaintSurface` manipulador no [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe posiciona o símbolo de infinito para que o (0, 0) ponto é posicionado no centro da página e dimensiona o caminho para o tamanho de tela:

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

O código usa o `Bounds` propriedade `SKPath` para determinar as dimensões do seno de infinito para dimensioná-lo ao tamanho da tela:

[![](arcs-images/arcinfinity-small.png "Tripla captura da página do arco infinito")](arcs-images/arcinfinity-large.png#lightbox "tripla captura de tela da página infinito de arco")

O resultado parece um pouco pequeno, o que sugere que a `Bounds` propriedade `SKPath` está relatando um tamanho maior que o caminho.

Internamente, Skia aproxima o arco usando várias curvas de Bézier quadráticas. Esses curvas (como você verá na próxima seção) contêm pontos de controle que controlam como a curva é desenhada, mas que não fazem parte da curva renderizada. O `Bounds` propriedade inclui os pontos de controle.

Para obter um ajuste maior, use o `TightBounds` propriedade, que exclui os pontos de controle. Aqui está o programa em execução no modo paisagem e usando o `TightBounds` propriedade para obter os limites de caminho:

[![](arcs-images/arcinfinitytightbounds-small.png "Tripla captura da página do arco infinito com limites rígidos")](arcs-images/arcinfinitytightbounds-large.png#lightbox "tripla captura de tela da página com limites rígidos infinito de arco")

Embora as conexões entre os arcos e linhas retas são matematicamente suaves, a alteração do arco linear pode parecer um pouco abrupta. Um símbolo de infinito melhor é apresentado na próxima página.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
