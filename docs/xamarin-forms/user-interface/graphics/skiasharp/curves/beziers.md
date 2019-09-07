---
title: Três tipos de curvas de bézier
description: Este artigo explica como usar SkiaSharp para renderizar as curvas de Bézier cúbicas, quadráticas e conic em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 1cf061f2ff27720ad78567bc26f00d99c5456f04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759425"
---
# <a name="three-types-of-bzier-curves"></a>Três tipos de curvas de bézier

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorar como usar SkiaSharp para renderizar as curvas de Bézier cúbicas, quadráticas e conic_

A curva de Bézier é nomeada após Pierre Bézier (1910 – 1999), um engenheiro de francês da empresa automotiva Renault, que usaram a curva para o design auxiliada por computador de corpos de carro.

As curvas Bézier são conhecidas por serem adequadas ao design interativo: Eles estão bem comparados &mdash; em outras palavras, não há singularidades que façam com que a curva se torne infinita ou &mdash; complicada e, em geral, são bastante agradáveis:

![Uma curva de Bézier de exemplo](beziers-images/beziersample.png)

Contornos de caracteres de fontes baseadas em computador normalmente são definidos com curvas de Bézier.

O artigo da Wikipedia sobre [ **curva de Bézier** ](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contém algumas informações úteis em segundo plano. O termo *curva de Bézier* realmente se refere a uma família de curvas semelhantes. SkiaSharp dá suporte a três tipos de curvas de Bézier, chamados de *cúbica*, o *quadrática*e o *conic*. O conic também é conhecido como o *quadrática racional*.

## <a name="the-cubic-bzier-curve"></a>A curva de Bézier cúbica

O cúbico é o tipo de curva de Bézier que a maioria dos desenvolvedores pensam quando o assunto de curvas de Bézier é exibido.

Você pode adicionar uma curva de Bézier cúbica a um `SKPath` do objeto usando o [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método com três `SKPoint` parâmetros, ou o [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga com separado `x` e `y` parâmetros:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

A curva começa no ponto atual da delimitação. A curva de Bézier cúbica completa é definida por quatro pontos:

- ponto de partida: atual do ponto no contorno, ou (0, 0) se `MoveTo` não foi chamado
- primeiro ponto de controle: `point1` no `CubicTo` chamar
- segundo ponto de controle: `point2` no `CubicTo` chamar
- ponto de extremidade: `point3` no `CubicTo` chamar

A curva resultante começa no ponto de início e termina no ponto de extremidade. A curva geralmente não passa pelos pontos de controle de dois; em vez disso, o controle de pontos de função como ímãs para efetuar pull de curva em direção eles.

É a melhor maneira de ter uma noção da curva de Bézier cúbica por experimentação. Essa é a finalidade do **curva de Bézier** página, que deriva de `InteractivePage`. O [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) arquivo XAML instancia o `SKCanvasView` e um `TouchEffect`. O [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) arquivo code-behind cria quatro `TouchPoint` objetos em seu construtor. O `PaintSurface` manipulador de eventos cria um `SKPath` para processar uma curva de Bézier com base em quatro `TouchPoint` objetos e também desenha linhas pontilhadas em tangente dos pontos de controle para os pontos de extremidade:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

Aqui ele está em execução:

[![Captura de tela tripla da página de curva de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matematicamente, a curva é uma polinomial cúbica. A curva cruza uma linha reta em três pontos no máximo. No ponto de início, a curva é sempre aponte tangente e, em que a mesma direção que, de uma linha reta a partir do início até o primeiro ponto de controle. No ponto de extremidade, a curva é sempre uma linha reta do segundo controle de tangente e, em que a mesma direção, aponte para o ponto de extremidade.

A curva de Bézier cúbica sempre é delimitada por um diamante convexo que conecta os quatro pontos. Isso é chamado de um *envoltória convexa*. Se os pontos de controle residem na linha reta entre o início e o ponto de extremidade, a curva de Bézier é renderizado como uma linha reta. Mas a curva também pode cruzar-em si, como demonstra a captura de tela de terceiro.

Um contorno de caminho pode conter várias curvas de Bézier cúbicas conectadas, mas a conexão entre duas curvas de Bézier cúbicas será suave somente se os três pontos a seguir são colinear (ou seja, se encontram em uma linha reta):

- o segundo ponto de controle da curva primeiro
- o ponto de extremidade da curva primeiro, que também é o ponto de início da segunda curva
- o primeiro ponto de controle da curva de segundo

No próximo artigo sobre [ **dados de caminho SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), você vai descobrir um recurso para facilitar a definição de curvas de Bézier conectadas suaves.

Às vezes é útil saber equações paramétricas subjacentes que renderizam uma curva de Bézier cúbica. Para *t* variando de 0 a 1, equações paramétricas são da seguinte maneira:

x (t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

O expoente mais alto de 3 confirma que esses são polynomials cúbicas. É fácil de verificar que, quando `t` for igual a 0, o ponto é (x₀ y₀), que é o ponto inicial e quando `t` for igual a 1, o ponto é (x₃ y₃), que é o ponto de extremidade. Quase o ponto inicial (para valores baixos de `t`), o primeiro ponto de controle (x₁, y₁) tem um forte efeito e perto o ponto de extremidade (valores altos de T' ') segundo ponto de controle (x₂, y₂) tem um efeito muito forte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximação da curva de Bézier arcos circulares

Às vezes é conveniente usar uma curva de Bézier para renderizar um arco circular. Uma curva de Bézier cúbica pode aproximar um arco circular muito bem até um quarto de círculo, assim, quatro curvas de Bézier conectadas podem definir um círculo completo. Essa aproximação é aborda em dois artigos publicados mais de 25 anos atrás:

> Tor Dokken, et al, "Boa aproximação dos círculos por curvas de Bézier curvatura contínua," *computador Aided Geométrico Design 7* (1990), 33 41.

> "Aproximação de arcos circulares por Polynomials cúbicas,", de Michael Goldapp *computador Aided Design Geométrico 8* (1991), 227 238.

O diagrama a seguir mostra quatro pontos rotulados `pto`, `pt1`, `pt2`, e `pt3` definindo uma curva de Bézier (mostrada em vermelho) que se aproxima de um arco circular:

![Aproximação de um arco circular com uma curva de Bézier](beziers-images/bezierarc45.png)

As linhas do início e pontos de extremidade para os pontos de controle são tangente para um círculo e a curva de Bézier e têm um comprimento de *L*. O primeiro artigo citado acima indica que a curva de Bézier melhor aproxima um arco circular quando esse comprimento *L* é calculada da seguinte forma:

L = 4 × tan(α / 4) / 3

A ilustração mostra um ângulo de 45 graus, portanto, L é igual a 0.265. No código, esse valor seria multiplicada pelo raio do círculo desejado.

O **arco Circular de Bezier** página permite que você pode experimentar com a definição de uma curva de Bézier para aproximar um arco circular para ângulos que vão até 180 graus. O [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) arquivo XAML instancia o `SKCanvasView` e um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador de eventos em de [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) arquivo code-behind usa uma transformação para definir o ponto (0, 0) para o centro da tela. Ele desenha um círculo centralizado naquele ponto para comparação e, em seguida, calcula os dois pontos de controle da curva de Bézier:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
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

```

Os pontos inicial e final (`point0` e `point3`) são calculados com base nas equações paramétricas normais para o círculo. Porque o círculo é centralizado em (0, 0), esses pontos também podem ser tratados como vetores radiais do centro do círculo para a circunferência. Os pontos de controle estão nas linhas que são tangente no círculo, portanto, eles são ângulos retos para esses vetores radiais. Um vetor em um ângulo reto para outro é simplesmente o vetor original com as coordenadas X e Y trocadas e um deles feitas negativo.

Aqui está o programa em execução com ângulos diferentes:

[![Captura de tela tripla da página de arco circular de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Examinar de perto a captura de tela de terceiro, e você verá que a curva de Bézier notavelmente se desvia de um semicírculo quando o ângulo de 180 graus, mas a tela do iOS mostra que ele parece se ajustar a um trimestre-círculo bem quando o ângulo é 90 graus.

Calcular as coordenadas dos pontos de controle é muito fácil quando o quarto de círculo é orientado como este:

![Aproximação de um círculo trimestral com uma curva Bézier](beziers-images/bezierarc90.png)

Se o raio do círculo é 100, então *L* é 55 e que é um número fácil de lembrar.

O **elevar o círculo** página anima uma figura entre um círculo e um quadrado. O círculo é aproximado por quatro curvas de Bézier cujas coordenadas são mostradas na primeira coluna desta definição de matriz na [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe:

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

A segunda coluna contém as coordenadas dos quatro curvas de Bézier que definem um quadrado cuja área é aproximadamente o mesmo que a área do círculo. (Desenhando um quadrado com o *exata* área como um círculo determinado é o clássico problema de Geométrico insolúvel da [elevar o círculo](https://en.wikipedia.org/wiki/Squaring_the_circle).) Para a renderização de um quadrado com curvas de Bézier, os dois pontos de controle para cada curva são os mesmos e eles são colinear com os pontos inicial e final, portanto, a curva de Bézier é renderizada como uma linha reta.

A terceira coluna da matriz é interpolada valores para uma animação. A página define um temporizador para 16 milissegundos e o `PaintSurface` manipulador é chamado nessa taxa:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

Os pontos são interpolados com base no valor sinusoidally oscilantes `t`. Os pontos interpolados, em seguida, são usados para construir uma série de quatro curvas de Bézier conectadas. Aqui está a animação em execução:

[![Captura de tela tripla de elevar a página de círculo](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Uma animação desse tipo seria impossível sem as curvas que estão algoritmicamente é flexíveis o suficiente para ser processado como linhas retas e arcos circulares.

O **infinito de Bezier** página também aproveita a capacidade de uma curva de Bézier para aproximar um arco circular. Aqui está o `PaintSurface` manipulador do [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
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

Pode ser um bom exercício para plotar essas coordenadas no papel de gráfico para ver como eles estão relacionados. O símbolo de infinito é centralizado em torno do ponto (0, 0), e os dois loops tem centros de (–150, 0) e (150, 0) e o raio de 100. Na série de `CubicTo` comandos, você pode ver coordenadas dos pontos de controle assumir valores de –95 e –205 X (esses valores são –150 de adição e subtração 55), 205 e 95 (150 de adição e subtração 55), bem como 250 e –250 para os lados esquerdos e direito. A única exceção é quando o símbolo de infinito cruza em si no centro. Nesse caso, os pontos de controle têm coordenadas com uma combinação de 50 e – 50 alinhe a curva próximo ao centro.

Aqui está o símbolo de infinito:

[![Captura de tela tripla da página de infinitos de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

É um pouco mais suave em direção ao centro do que o símbolo de infinito renderizado pelo **arco infinito** página da [ **três formas para desenhar um arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo.

## <a name="the-quadratic-bzier-curve"></a>A curva de Bézier quadrática

A curva de Bézier quadrática tem o ponto de controle de apenas um, e a curva é definida por apenas três pontos: o ponto inicial, o ponto de controle e o ponto de extremidade. Equações paramétricas são muito semelhantes para a curva de Bézier cúbica, exceto que o expoente mais alto é 2, portanto, a curva é uma polinomial quadrática:

x (t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Para adicionar uma curva de Bézier quadrática para um caminho, use o [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método ou o [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) sobrecarga com separado `x` e `y` coordenadas:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Os métodos adicionam uma curva da posição atual para `point2` com `point1` como o ponto de controle.

Você pode experimentar com curvas de Bézier quadráticas com o **curva quadrática** página, que é muito semelhante ao **curva de Bézier** página, exceto que ele tem apenas três pontos de toque. Aqui está o `PaintSurface` manipulador na [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) arquivo code-behind:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

E aqui está em execução:

[![Captura de tela tripla da página de curva quadrática](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

As linhas pontilhadas são tangente da curva no ponto inicial e o ponto de extremidade e atender a no ponto de controle.

O Bézier quadrática é bom se você precisa de uma curva de uma forma geral, mas você prefere a conveniência de ponto de apenas um controle em vez de dois. O Bézier quadrática renderiza mais eficiente do que qualquer outra curva, que é por isso que ele é usado internamente no Skia para renderizar elípticos.

No entanto, a forma de uma curva de Bézier quadrática não é elíptica, que é o motivo pelo qual vários Béziers quadráticas são necessários para aproximar um arco elíptico. O Bézier quadrática em vez disso, é um segmento de um parábola.

## <a name="the-conic-bzier-curve"></a>A curva de Bézier Conic

A curva de Bézier conic &mdash; também conhecido como a racional curva Bézier quadrática &mdash; é uma adição relativamente recente para a família de curvas de Bézier. Como a curva de Bézier quadrática, a curva de Bézier quadrática racional envolve um ponto inicial, um ponto de extremidade e o ponto de um controle. Mas a curva de Bézier quadrática racional também requer um *peso* valor. Ele é chamado uma *racional* quadrática porque as fórmulas paramétricas envolvem proporções.

As equações paramétricas para X e Y são razões que compartilham o mesmo denominador. Aqui está a equação para o denominador para *t* variando de 0 a 1 e um valor de peso dos *w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

Em teoria, uma quadrática racional pode envolver três valores de peso separado, um para cada um dos três termos, mas eles podem ser simplificados para apenas um valor de peso no meio termo.

As equações paramétricas para as coordenadas X e Y são semelhantes às equações paramétricas para de Bézier quadrática, exceto que o meio termo também inclui o valor de peso e a expressão é dividida pelo denominador:

x (t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

Também são chamadas de curvas de Bézier quadráticas racionais *conics* porque eles podem representar exatamente os segmentos de qualquer seção conic &mdash; hipérboles, parábolas, elipses e círculos.

Para adicionar uma curva de Bézier quadrática racional para um caminho, use o [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método ou o [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga com separado `x` e `y` coordenadas:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe o último `weight` parâmetro.

O **curva Conic** página permite que você pode experimentar com esses curvas. O `ConicCurvePage` classe deriva de `InteractivePage`. O [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) arquivo XAML instancia um `Slider` para selecionar um valor de peso entre – 2 e 2. O [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) arquivo code-behind cria três `TouchPoint` objetos e o `PaintSurface` manipulador simplesmente renderiza a curva resultante com as linhas tangentes ao controle pontos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Aqui ele está em execução:

[![Captura de tela tripla da página de curva de cone](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Como você pode ver, o ponto de controle parece puxe a curva em direção ele mais quando o peso é mais alto. Quando o peso é zero, a curva se torna uma linha reta do ponto de início para o ponto de extremidade.

Em teoria, pesos negativos são permitidos, em fazer com que a curva a ser curvar *distância* do ponto de controle. No entanto, pesos de -1 ou abaixo causa o denominador em equações paramétricas para se tornar negativo para valores específicos de *t*. Provavelmente por esse motivo, pesos negativos são ignorados no `ConicTo` métodos. O **curva Conic** programa permite que você defina pesos negativos, mas como você pode ver por meio da experimentação, pesos negativos têm o mesmo efeito de um peso zero e fazer com que uma linha reta a ser renderizado.

É muito fácil derivar o ponto de controle e o peso para usar o `ConicTo` método para desenhar um arco circular até (mas não incluindo) um semicírculo. No diagrama a seguir, atender às linhas tangentes do início e pontos de extremidade no ponto de controle.

![Uma renderização de arco de cone de um arco circular](beziers-images/conicarc.png)

Você pode usar a trigonometria para determinar a distância do ponto de controle no centro do círculo: É o raio do círculo dividido pelo cosseno da metade do ângulo α. Para desenhar um arco circular entre os pontos inicial e final, defina o peso para esse mesmo cosseno metade do ângulo. Observe que se o ângulo de 180 graus, em seguida, as linhas tangentes nunca se encontram e o peso é zero. Mas para ângulos menor que 180 graus, a matemática funciona bem.

O **Conic arco Circular** página demonstra isso. O [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) arquivo XAML instancia um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador na [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) arquivo code-behind calcula o ponto de controle e o peso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

Como você pode ver, não há nenhuma diferença visual entre o `ConicTo` caminho mostrado em vermelho e o círculo subjacente exibidos para referência:

[![Captura de tela tripla da página de arco circular do cone](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Mas, definir o ângulo de 180 graus e a matemática falhar.

É uma pena nesse caso, que `ConicTo` não oferece suporte para pesos negativos, como em teoria (com base em equações paramétricas), o círculo pode ser concluído com outra chamada para `ConicTo` com os mesmos pontos, mas um valor negativo do peso. Isso permitiria que a criação de um círculo completo com apenas dois `ConicTo` curvas com base em qualquer ângulo entre (mas não incluindo) zero graus e 180 graus.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
