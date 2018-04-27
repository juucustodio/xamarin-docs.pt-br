---
title: Três tipos de curvas de Bézier
description: Explorar como usar SkiaSharp para renderizar cúbicas, quadráticas e conic curvas de Bézier
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: charlespetzold
ms.author: chape
ms.date: 05/25/2017
ms.openlocfilehash: 7b7bd83c474c7e0d32a693e06b5f12696ec5efa2
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="three-types-of-bzier-curves"></a>Três tipos de curvas de Bézier

_Explorar como usar SkiaSharp para renderizar cúbicas, quadráticas e conic curvas de Bézier_

A curva de Bézier é nomeada após Pierre Bézier (1910 – 1999), um engenheiro de francês na empresa automotivo Renault, que usou a curva para o design auxiliado por computador de corpos de carro.

Curvas de Bézier são conhecidas por ser adequada para design interativo: estiverem com bom comportamento &mdash; em outras palavras, não existem singularities que causam a curva para se tornar complicada ou infinita &mdash; e geralmente são satisfatória esteticamente . Contornos de caracteres de fontes com base em computador geralmente são definidos com curvas de Bézier:

![](beziers-images/beziersample.png "Uma curva de Bézier de exemplo")

O artigo da Wikipedia sobre [curva de Bézier](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contém algumas informações úteis. O termo *curva de Bézier* realmente se refere a uma família de curvas semelhantes. SkiaSharp oferece suporte a três tipos de curvas de Bézier, chamados de *cúbica*, o *quadrática*e o *conic*. O conic também é conhecido como o *quadrática racional*.

## <a name="the-cubic-bzier-curve"></a>A curva cúbica de Bézier

O cúbico é o tipo de curva de Bézier que a maioria dos desenvolvedores pensar quando o assunto das curvas de Bézier é exibida.

Você pode adicionar uma curva cúbica de Bézier em uma `SKPath` objeto usando o [ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/SkiaSharp.SKPoint/) método com três `SKPoint` parâmetros, ou o [ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/System.Single/) sobrecarga com separado `x` e `y` parâmetros:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

A curva começa no ponto atual do contorno. A curva cúbica de Bezier completa é definida por quatro pontos:

- ponto inicial: atual aponte o contorno, ou (0, 0) se `MoveTo` não foi chamado
- primeiro ponto de controle: `point1` no `CubicTo` chamar
- segundo ponto de controle: `point2` no `CubicTo` chamar
- ponto de extremidade: `point3` no `CubicTo` chamar

A curva resultante começa no ponto de início e termina no ponto de extremidade. A curva geralmente não passa por meio de dois pontos de controle; em vez disso, eles funcionam muito atração nossa like para receber a curva em direção eles.

É a melhor maneira de conhecer a curva de Bézier cúbica por experimentação. Essa é a finalidade do **curva de Bézier** página, que deriva de `InteractivePage`. O [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) arquivo instancia o `SKCanvasView` e um `TouchEffect`. O [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) arquivo code-behind cria quatro `TouchPoint` objetos em seu construtor. O `PaintSurface` manipulador de eventos cria um `SKPath` para renderizar uma curva de Bézier com base em quatro `TouchPoint` objetos e também desenha linhas pontilhadas em tangente dos pontos de controle para os pontos de extremidade:

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

Aqui, ele é executado em todas as três plataformas:

[![](beziers-images/beziercurve-small.png "Captura de tela da página da curva de Bézier tripla")](beziers-images/beziercurve-large.png#lightbox "tripla captura de tela da página da curva de Bézier")

Matematicamente, a curva é um polinômio cúbico. A curva cruza uma linha reta em três pontos no máximo. No ponto de início, a curva é sempre uma linha reta desde o início de tangente para e, na mesma direção, aponte para o primeiro ponto de controle. No ponto de extremidade, a curva é sempre uma linha reta do segundo controle de tangente para e, na mesma direção, aponte para o ponto de extremidade.

A curva de Bézier cúbica sempre é delimitada por um quadrilátero convexo conectando os quatro pontos. Isso é chamado de um *forma convexa*. Se os pontos de controle residem na linha reta entre o início e o ponto de extremidade, a curva de Bézier processa como uma linha reta. Mas a curva também pode cruzar com, como mostra a captura de tela de terceira.

Um contorno de caminho pode conter várias curvas de Bézier cúbicas conectadas, mas a conexão entre duas curvas de Bézier cúbicas será suave somente se os seguintes três pontos são colinear (ou seja, permanecer em uma linha reta):

- o segundo ponto de controle da curva primeiro
- o ponto de extremidade da curva primeiro, que também é o ponto de início da segunda curva
- o primeiro ponto de controle da curva de segundo

No próximo artigo em [ **SVG caminho dados** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) você vai descobrir um recurso para facilitar a definição das curvas de Bézier conectadas suaves.

Às vezes, é útil saber subjacentes equações paramétricas que renderizam uma curva cúbica de Bézier. Para *t* variando de 0 a 1, equações paramétricas são da seguinte maneira:

x(t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

O expoente mais alto de 3 confirma que esses são polynomials cúbicos. É fácil verificar que, quando `t` é igual a 0, o ponto (x₀, y₀), que é o ponto de início e quando `t` é igual a 1, o ponto (x₃, y₃), que é o ponto de extremidade. Perto o ponto inicial (para valores baixos de `t`), o primeiro ponto de controle (x₁, y₁) tem um forte efeito e perto o ponto de extremidade (valores altos de T' ') do segundo ponto de controle (x₂, y₂) tem um forte efeito.

## <a name="bzier-curve-approximation-to-circular-arcs"></a>Aproximação da curva de Bézier em arcos circulares

Às vezes, é conveniente usar uma curva de Bézier para renderizar um arco circular. Uma curva cúbica de Bézier puder aproximar um arco circular muito bem até um quarto de círculo, portanto quatro curvas de Bézier conectadas podem definir um círculo completo. Essa aproximação é discutida em dois artigos publicados mais de 25 anos atrás:

> Tor Dokken, et al, "Boa aproximação dos círculos por curvas de Bézier curvatura contínua," *computador auxiliado por Geométrico Design 7* (1990), 33 41.

> Michael Goldapp, "Aproximação de arcos circulares por cúbicas Polynomials," *computador ideal para Design Geométrico 8* (1991), 227 238.

O diagrama a seguir mostra quatro pontos identificados `pto`, `pt1`, `pt2`, e `pt3` definindo uma curva de Bézier (mostrada em vermelho) que se aproxima um arco circular:

![](beziers-images/bezierarc45.png "Aproximação de um arco circular com uma curva de Bézier")

As linhas a partir de pontos de início e término para os pontos de controle são tangente para um círculo e a curva de Bézier, e eles têm um comprimento de *L*. O primeiro artigo citado acima indica que a curva de Bézier melhor aproxima um arco circular quando esse comprimento *L* é calculada da seguinte forma:

L = 4 × tan(α / 4) / 3

A ilustração mostra um ângulo de 45 graus, portanto L igual a 0.265. No código, esse valor será multiplicado por desejado raio do círculo.

O **Bézier em arco Circular** página permite fazer experiências com a definição de uma curva de Bézier para aproximar um arco circular para ângulos que vão até 180 graus. O [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) arquivo instancia o `SKCanvasView` e um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador de eventos de [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) arquivo code-behind usa uma transformação para definir o ponto (0, 0) para o centro da tela. Ele desenha um círculo centralizado no ponto de comparação e, em seguida, calcula os pontos de controle da curva de Bézier:

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

Os pontos inicial e final (`point0` e `point3`) são calculadas com base em equações paramétricas normais para o círculo. Porque o círculo é centralizado em (0, 0), esses pontos também podem ser tratados como vetores radiais do centro do círculo para a circunferência. Os pontos de controle estão em linhas de tangente no círculo, para que fiquem em ângulo reto para esses vetores radiais. Um vetor em um ângulo direito é simplesmente o vetor original com as coordenadas X e Y trocadas e um deles feitas negativo.

Aqui está o programa em execução em plataformas de três com três ângulos diferentes:

[![](beziers-images/beziercirculararc-small.png "Captura de tela da página de Bézier em arco Circular tripla")](beziers-images/beziercirculararc-large.png#lightbox "tripla captura de tela da página de Bézier em arco Circular")

Examinar atentamente a captura de tela de terceira, e você verá que a curva de Bézier em especial do desvio de um semicírculo quando o ângulo é 180 graus, mas a tela de iOS mostra que parece se ajustar um trimestre-círculo bem quando o ângulo é 90 graus.

Calcular as coordenadas dos pontos de controle é muito fácil quando o círculo trimestre é orientado por assim:

![](beziers-images/bezierarc90.png "Aproximação de um quarto de círculo com uma curva de Bézier")

Se o raio do círculo é 100, em seguida, *L* é 55 e que é um número fácil de lembrar.

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

A segunda coluna contém as coordenadas de quatro curvas de Bézier que definem um quadrado cuja área é aproximadamente o mesmo que a área do círculo. (Desenho a um quadrado com o *exata* área como um círculo fornecido é o problema clássico de Geométrico insolúvel de [elevar o círculo](https://en.wikipedia.org/wiki/Squaring_the_circle).) Para renderizar um quadrado com curvas de Bézier, os dois pontos de controle para cada curva são os mesmos, e são colinear com os pontos inicial e final, para a curva de Bézier é renderizada como uma linha reta.

A terceira coluna da matriz é interpolada valores de uma animação. A página define um timer para 16 milissegundos e o `PaintSurface` manipulador é chamado em que a taxa de:

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

Os pontos são interpolados com base em um valor sinusoidally oscilantes `t`. Os pontos de interpolada são usados para construir uma série de quatro curvas de Bézier conectadas. Aqui está a animação em execução nas três plataformas mostrando o andamento de um círculo a um quadrado:

[![](beziers-images/squaringthecircle-small.png "Tripla captura de tela do Squaring a página de círculo")](beziers-images/squaringthecircle-large.png#lightbox "tripla captura de tela do Squaring a página de círculo")

Tal uma animação seria impossível sem curvas de maneira algorítmica é flexíveis o suficiente para ser processado como linhas retas e arcos circulares.

O **Bézier infinito** página também aproveita a capacidade de uma curva de Bézier aproximar um arco circular. Aqui está o `PaintSurface` manipulador do [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

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

Pode ser um bom exercício para plotar essas coordenadas no papel de gráfico para ver como eles estão relacionados. O símbolo de infinito é centralizado em torno do ponto (0, 0) e os dois loops tem centros de (–150, 0) e (150, 0) e raios de 100. Na série de `CubicTo` comandos, você pode ver coordenadas dos pontos de controle a execução em valores de –95 e –205 X (esses valores são –150 de adição e subtração 55), 205 e 95 (150 adição e subtração 55), bem como 250 e –250 para os lados esquerdos e direito. A única exceção é quando o símbolo de infinito cruza em si no centro. Nesse caso, os pontos de controle têm coordenadas com uma combinação de – 50 e 50 alinhe a curva próximo ao centro.

Aqui está o símbolo de infinito em todas as três plataformas:

[![](beziers-images/bezierinfinity-small.png "Captura de tela da página infinito de Bézier tripla")](beziers-images/bezierinfinity-large.png#lightbox "tripla captura de tela da página infinito de Bézier")

É um pouco mais suave para o centro do que o símbolo de infinito processado pelo **infinito arco** página do [ **três maneiras para desenhar um arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo.

## <a name="the-quadratic-bzier-curve"></a>A curva de Bézier quadrática

A curva de Bézier quadrática tem o ponto de controle de apenas um, e a curva é definida por apenas três pontos: o ponto inicial, o ponto de controle e o ponto de extremidade. Equações paramétricas são muito semelhantes a curva cúbica de Bézier, exceto que o expoente mais alto é 2, portanto, a curva é um polinômio quadrático:

x(t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Para adicionar uma curva de Bézier quadrática a um caminho, use o [ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/) método ou o [ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/System.Single/System.Single/System.Single/System.Single/) sobrecarga com separado `x` e `y` coordenadas:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Os métodos de adicionam uma curva da posição atual para `point2` com `point1` como o ponto de controle.

Você pode fazer experiências com curvas de Bézier quadráticas com o **curva quadrática** página, que é muito semelhante do **curva de Bézier** página exceto que ele tem somente três pontos de toque. Aqui está o `PaintSurface` manipulador no [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) arquivo code-behind:

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

E aqui está em execução em todas as três plataformas:

[![](beziers-images/quadraticcurve-small.png "Captura de tela da página curva quadrática tripla")](beziers-images/quadraticcurve-large.png#lightbox "tripla captura de tela da página curva quadrática")

As linhas pontilhadas são tangente curva no ponto inicial e no ponto de extremidade e atender no ponto de controle.

O Bézier quadrática é boa se você precisa de uma curva de uma forma geral, mas você prefere a conveniência de ponto de controle de apenas um em vez de dois. O Bézier quadrática renderiza com mais eficiência do que qualquer outra curva, motivo pelo qual ele é usado internamente no Skia para renderizar elípticos arcos.

No entanto, a forma de uma curva de Bézier quadrática não é elíptica, por isso, vários Béziers quadráticas são necessários para aproximar o arco elíptico. O Bézier quadrática em vez disso, é um segmento de um parábola.

## <a name="the-conic-bzier-curve"></a>A curva de Bézier Conic

A curva de Bézier conic &mdash; também conhecido como a racional curva Bézier quadrática &mdash; é uma adição relativamente recente para a família de curvas de Bézier. Como a curva de Bézier quadrática, a curva de Bézier quadrática racional envolve um ponto inicial, um ponto de extremidade e o ponto de um controle. Mas a curva de Bézier quadrática racional também exige um *peso* valor. Ele é chamado um *racional* quadrática porque as fórmulas paramétricos envolvem taxas.

As equações paramétricos para X e Y são taxas que compartilham o mesmo denominador. Aqui está a equação para o denominador para *t* variando de 0 a 1 e um valor de peso de *w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

Em teoria, uma quadrática racional pode envolver três valores de peso separado, um para cada uma das três condições, mas eles podem ser simplificados para apenas um valor de peso o termo intermediária.

Equações paramétricas para as coordenadas X e Y são semelhantes a equações paramétricas para o Bézier quadrática, exceto que o termo intermediária também inclui o valor de peso, e a expressão é dividida pelo denominador:

x(t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

Também são chamadas de curvas de Bézier quadráticas racional *conics* porque podem representar exatamente segmentos de qualquer seção conic &mdash; hipérboles, parábolas, elipses e círculos.

Para adicionar uma curva de Bézier quadrática razoável em um caminho, use o [ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/) método ou o [ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/) sobrecarga com separado `x` e `y` coordenadas:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe o último `weight` parâmetro.

O **curva Conic** página permite fazer experiências com esses curvas. O `ConicCurvePage` classe deriva de `InteractivePage`. O [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) arquivo instancia um `Slider` para selecionar um valor de peso entre – 2 e 2. O [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) arquivo code-behind cria três `TouchPoint` objetos e o `PaintSurface` manipulador simplesmente renderiza a curva resultante com as linhas tangentes ao controle pontos:

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

Aqui, ele é executado em todas as três plataformas:

[![](beziers-images/coniccurve-small.png "Captura de tela da página curva Conic tripla")](beziers-images/coniccurve-large.png#lightbox "tripla captura de tela da página Conic curva")

Como você pode ver, o ponto de controle parece puxe a curva em direção mais quando o peso é mais alto. Quando o peso é zero, a curva torna-se uma linha reta do ponto de início para o ponto de extremidade.

Em teoria, pesos negativos são permitidos em fazer com que a curva em dobre *ausente* do ponto de controle. No entanto, pondera de-1 ou abaixo causa o denominador no equações paramétricas para se tornar negativos para valores específicos de *t*. Provavelmente por esse motivo, pesos negativos são ignorados no `ConicTo` métodos. O **Conic curva** programa permite que você defina pesos negativos, mas como você pode ver experimentando, pesos negativos têm o mesmo efeito de um peso de zero e fazer com que uma linha reta ser processado.

É muito fácil derivar o ponto de controle e o peso para usar o `ConicTo` método para desenhar um arco circular até (mas não incluindo) um semicírculo. No diagrama a seguir, linhas tangentes dos pontos inicial e final atendem no ponto de controle.

![](beziers-images/conicarc.png "Renderização de um arco conic de um arco circular")

Você pode usar trigonométricas para determinar a distância do ponto de controle do centro do círculo: é o raio do círculo dividido pelo cosseno metade do ângulo α. Para desenhar um arco circular entre os pontos inicial e final, defina o peso para esse mesmo cosseno metade do ângulo. Observe que se o ângulo estiver 180 graus, em seguida, as linhas tangentes nunca atendem e o peso é zero. Mas para ângulos menor que 180 graus, o cálculo funciona bem.

O **Conic arco Circular** página demonstra isso. O [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) arquivo instancia um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador no [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) arquivo code-behind calcula o ponto de controle e o peso:

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

Como você pode ver, não há nenhuma diferença visual entre o `ConicTo` caminho mostrada em vermelho e o círculo subjacente exibidos para referência:

[![](beziers-images/coniccirculararc-small.png "Captura de tela da página Conic arco Circular tripla")](beziers-images/coniccirculararc-large.png#lightbox "tripla captura de tela da página Conic arco Circular")

Mas, definir o ângulo de 180 graus, e a falha de matemática.

É ruim nesse caso que `ConicTo` não oferece suporte para pesos negativos, como em teoria (com base em equações paramétricas), o círculo pode ser concluído com outra chamada para `ConicTo` com os mesmos pontos, mas com um valor negativo do peso. Isso permitirá a criação de um círculo completo com apenas duas `ConicTo` curvas com base em qualquer ângulo entre (mas não incluindo) zero graus e 180 graus.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
