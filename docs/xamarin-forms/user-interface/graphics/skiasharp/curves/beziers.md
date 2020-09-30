---
title: Três tipos de curvas de bézier
description: Este artigo explica como usar SkiaSharp para renderizar curvas Bézier cúbicas, quadráticas e cônicas em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9193cef76a5f474f3681b15a1315e5840b41d88a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562971"
---
# <a name="three-types-of-bzier-curves"></a>Três tipos de curvas de bézier

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore como usar SkiaSharp para renderizar curvas Bézier cúbicas, quadráticas e cônicas_

A curva de Bézier é nomeada após Pierre Bézier (1910 – 1999), um engenheiro francês na Renault da empresa automotiva, que usou a curva para o design assistido por computador de corpos de carros.

As curvas Bézier são conhecidas por serem adequadas para o design interativo: elas estão bem comparadas &mdash; em outras palavras, não há singularidades que façam com que a curva se torne infinita ou incômodo &mdash; e, em geral, é bastante agradável:

![Uma curva de Bézier de exemplo](beziers-images/beziersample.png)

Os contornos de caracteres das fontes baseadas em computador geralmente são definidos com curvas Bézier.

O artigo da Wikipédia na [**curva de Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contém algumas informações úteis do plano de fundo. O termo *curva Bézier* na verdade se refere a uma família de curvas semelhantes. O SkiaSharp dá suporte a três tipos de curvas Bézier, chamadas *cúbica*, *quadrática*e *cone*. O cone também é conhecido como o *quadrática racional*.

## <a name="the-cubic-bzier-curve"></a>A curva Bézier cúbica

O cúbico é o tipo de curva de Bézier que a maioria dos desenvolvedores imagina quando o assunto das curvas Bézier é exibido.

Você pode adicionar uma curva Bézier cúbica a um `SKPath` objeto usando o [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método com três `SKPoint` parâmetros ou a [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga com `x` parâmetros e separados `y` :

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

A curva começa no ponto atual da delimitação. A curva de Bézier cúbica completa é definida por quatro pontos:

- ponto de início: ponto atual na delimitação ou (0, 0) se `MoveTo` não tiver sido chamado
- primeiro ponto de controle: `point1` na `CubicTo` chamada
- segundo ponto de controle: `point2` na `CubicTo` chamada
- ponto de extremidade: `point3` na `CubicTo` chamada

A curva resultante começa no ponto inicial e termina no ponto de extremidade. Em geral, a curva não passa pelos dois pontos de controle; em vez disso, os pontos de controle funcionam muito como os ímãs para puxar a curva em direção a eles.

A melhor maneira de ter uma ideia para a curva Bézier cúbica é por experimentação. Essa é a finalidade da página de **curva de Bézier** , que deriva de `InteractivePage` . O arquivo [**BezierCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) instancia o `SKCanvasView` e um `TouchEffect` . O arquivo code-behind [**BezierCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) cria quatro `TouchPoint` objetos em seu construtor. O `PaintSurface` manipulador de eventos cria um `SKPath` para renderizar uma curva de Bézier com base nos quatro `TouchPoint` objetos e também desenha linhas tangentes pontilhadas dos pontos de controle para os pontos de extremidade:

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

Aqui está executando:

[![Captura de tela tripla da página de curva de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matematicamente, a curva é um polinomial cúbico. A curva intersecciona uma linha reta em três pontos no máximo. No ponto de partida, a curva sempre é a tangente e, na mesma direção, uma linha reta do ponto inicial para o primeiro ponto de controle. No ponto de extremidade, a curva sempre é a tangente e, na mesma direção, uma linha reta do segundo ponto de controle para o ponto de extremidade.

A curva Bézier cúbica sempre é limitada por um convexa diamante conectando os quatro pontos. Isso é chamado de *convexa envoltória*. Se os pontos de controle estiverem na linha reta entre o ponto inicial e o final, a curva de Bézier será renderizada como uma linha reta. Mas a curva também pode se cruzar, como demonstra a terceira captura de tela.

Uma delimitação de caminho pode conter várias curvas Bézier cúbicas conectadas, mas a conexão entre duas curvas Bézier cúbica será suave apenas se os três pontos a seguir forem colineares (ou seja, estiverem em uma linha reta):

- o segundo ponto de controle da primeira curva
- o ponto de extremidade da primeira curva, que também é o ponto inicial da segunda curva
- o primeiro ponto de controle da segunda curva

No próximo artigo sobre [**dados de caminho SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), você descobrirá uma instalação para facilitar a definição de curvas Bézier suaves conectadas.

Às vezes, é útil conhecer as equações paramétricas subjacentes que renderizam uma curva Bézier cúbica. Para *t* variando de 0 a 1, as equações paramétricas são as seguintes:

x (t) = (1 – t) ³ x ₀ + 3T (1 – t) ² x ₁ + 3T ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3T (1 – t) ² y ₁ + 3T ² (1 – t) y ₂ + t ³ y ₃

O expoente mais alto de 3 confirma que esses são polinomiais cúbicos. É fácil verificar se, quando é `t` igual a 0, o ponto é (x ₀, y ₀), que é o ponto de partida e quando `t` é igual a 1, o ponto é (x ₃, y ₃), que é o ponto de extremidade. Próximo ao ponto de partida (para valores baixos `t` ), o primeiro ponto de controle (x ₁, y ₁) tem um efeito forte e, perto do ponto de extremidade (valores altos de ' T'), o segundo ponto de controle (x ₂, y ₂) tem um efeito forte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximação da curva de Bézier para arcos circulares

Às vezes, é conveniente usar uma curva de Bézier para renderizar um arco circular. Uma curva Bézier cúbica pode aproximar um arco circular muito bem para um quarto de círculo, de modo que quatro curvas Bézier conectadas podem definir um círculo inteiro. Essa aproximação é discutida em dois artigos publicados há mais de 25 anos:

> Tor Dokken, et al, "boa aproximação de círculos por curvatura, curvas de Bézier contínuas", *design de geométrico auxiliado por computador 7* (1990), 33-41.

> Michael Goldapp, "aproximação de arcos circulares por polinomiais cúbicos", *design de geométrico auxiliado por computador 8* (1991), 227-238.

O diagrama a seguir mostra quatro pontos rotulados `pto` , `pt1` , `pt2` e `pt3` a definição de uma curva de Bézier (mostrada em vermelho) que aproxima um arco circular:

![Aproximação de um arco circular com uma curva de Bézier](beziers-images/bezierarc45.png)

As linhas dos pontos inicial e final para os pontos de controle são tangentes ao círculo e à curva Bézier, e têm um comprimento de *L*. O primeiro artigo citado acima indica que a curva de Bézier melhor aproxima um arco circular quando esse comprimento *L* é calculado da seguinte maneira:

L = 4 × tan (α/4)/3

A ilustração mostra um ângulo de 45 graus, portanto, L é igual a 0,265. No código, esse valor seria multiplicado pelo raio desejado do círculo.

A página de **arco circular de Bézier** permite que você experimente definir uma curva de Bézier para aproximar um arco circular para ângulos variando até 180 graus. O arquivo [**BezierCircularArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) instancia o `SKCanvasView` e um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador de eventos no arquivo code-behind [**BezierCircularArgPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa uma transformação para definir o ponto (0, 0) para o centro da tela. Ele desenha um círculo centralizado nesse ponto para comparação e, em seguida, calcula os dois pontos de controle para a curva de Bézier:

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

Os pontos inicial e final ( `point0` e `point3` ) são calculados com base nas equações paramétricas normais do círculo. Como o círculo está centralizado em (0, 0), esses pontos também podem ser tratados como vetores radiais do centro do círculo para a circunferência. Os pontos de controle estão em linhas que são tangentes ao círculo, portanto, eles estão em ângulos retos para esses vetores radiais. Um vetor em um ângulo direito para outro é simplesmente o vetor original com as coordenadas X e Y trocadas e uma delas se tornou negativa.

Aqui está o programa em execução com diferentes ângulos:

[![Captura de tela tripla da página de arco circular de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Examine com mais detalhes a terceira captura de tela, e você verá que a curva de Bézier notavelmente se desvia de um semicírculo quando o ângulo é de 180 graus, mas a tela do iOS mostra que parece se ajustar a um quarto de círculo quando o ângulo é 90 graus.

Calcular as coordenadas dos dois pontos de controle é muito fácil quando o círculo do trimestre é orientado da seguinte forma:

![Aproximação de um círculo trimestral com uma curva Bézier](beziers-images/bezierarc90.png)

Se o raio do círculo for 100, o *L* será 55, e esse é um número fácil de lembrar.

O **elevar a página de círculo** anima uma figura entre um círculo e um quadrado. O círculo é aproximado por quatro curvas Bézier cujas coordenadas são mostradas na primeira coluna dessa definição de matriz na [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe:

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

A segunda coluna contém as coordenadas de quatro curvas Bézier que definem um quadrado cuja área é aproximadamente igual à área do círculo. (Desenhar um quadrado com a área *exata* como um determinado círculo é o problema geométrico não resolvido clássico de [elevar o círculo](https://en.wikipedia.org/wiki/Squaring_the_circle).) Para renderizar um quadrado com curvas Bézier, os dois pontos de controle para cada curva são os mesmos e são colineares com os pontos inicial e final, portanto, a curva de Bézier é renderizada como uma linha reta.

A terceira coluna da matriz é para valores interpolados para uma animação. A página define um temporizador para 16 milissegundos e o `PaintSurface` manipulador é chamado com essa taxa:

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

Os pontos são interpolados com base em um valor de oscillating sinusoidally de `t` . Os pontos interpolados são então usados para construir uma série de quatro curvas Bézier conectadas. Esta é a animação em execução:

[![Captura de tela tripla de elevar a página de círculo](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Essa animação seria impossível sem curvas que são forma algorítmica flexíveis o suficiente para serem renderizadas como arcos circulares e linhas retas.

A página **infinito de Bézier** também aproveita a capacidade de uma curva Bézier para aproximar um arco circular. Aqui está o `PaintSurface` manipulador da [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

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

Pode ser um bom exercício para plotar essas coordenadas no documento do grafo para ver como elas estão relacionadas. O sinal de infinito é centralizado em todo o ponto (0, 0) e os dois loops têm centros de (– 150, 0) e (150, 0) e raios de 100. Na série de `CubicTo` comandos, você pode ver as coordenadas X dos pontos de controle utilizando os valores de – 95 e – 205 (esses valores são – 150 mais e menos 55), 205 e 95 (150 Plus e menos 55), bem como 250 e – 250 para os lados direito e esquerdo. A única exceção é quando o próprio sinal de infinito cruza no centro. Nesse caso, os pontos de controle têm coordenadas com uma combinação de 50 e – 50 para endireitar a curva perto do centro.

Eis o sinal de infinito:

[![Captura de tela tripla da página de infinitos de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

É um pouco mais suave em relação ao centro do que o sinal de infinito renderizado pela página de **infinito de arco** das [**três maneiras de desenhar um**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo de arco.

## <a name="the-quadratic-bzier-curve"></a>A curva Bézier quadrática

A curva Bézier quadrática tem apenas um ponto de controle, e a curva é definida por apenas três pontos: o ponto inicial, o ponto de controle e o ponto de extremidade. As equações paramétricas são muito semelhantes à curva Bézier cúbica, exceto que o expoente mais alto é 2, portanto, a curva é um polinomial quadrática:

x (t) = (1 – t) ² x ₀ + 2T (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + 2T (1 – t) y ₁ + t ² y ₂

Para adicionar uma curva Bézier quadrática a um caminho, use o [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) método ou a [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) sobrecarga com `x` coordenadas e separadas `y` :

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Os métodos adicionam uma curva da posição atual para `point2` com `point1` como o ponto de controle.

Você pode experimentar as curvas Bézier quadráticas com a página de **curva quadrática** , que é muito semelhante à página de **curva de Bézier** , exceto que ela tem apenas três pontos de toque. Aqui está o `PaintSurface` manipulador no arquivo de código-behind [**QuadraticCurve.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

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

E aqui está executando:

[![Captura de tela tripla da página de curva quadrática](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

As linhas pontilhadas são tangentes à curva no ponto de partida e no ponto de extremidade e encontram-se no ponto de controle.

O Bézier quadrática será bom se você precisar de uma curva de forma geral, mas preferir a conveniência de apenas um ponto de controle em vez de dois. O Bézier quadrática renderiza com mais eficiência do que qualquer outra curva, que é o motivo pelo qual ele é usado internamente em skia para renderizar arcos elípticos.

No entanto, a forma de uma curva Bézier quadrática não é elíptica, motivo pelo qual várias Béziers quadráticas são necessárias para aproximar um arco elíptico. O Bézier quadrática é, em vez disso, um segmento de um Parabola.

## <a name="the-conic-bzier-curve"></a>A curva de Bézier do cone

A curva Bézier de cone &mdash; também conhecida como a curva Bézier de quadrática racional &mdash; é uma adição relativamente recente à família de curvas Bézier. Como a curva Bézier quadrática, a curva Bézier de quadrática racional envolve um ponto de partida, um ponto de extremidade e um ponto de controle. Mas a curva Bézier de quadrática racional também requer um valor de *peso* . Ele é chamado de quadrática *racional* porque as fórmulas paramétricas envolvem proporções.

As equações paramétricas para X e Y são taxas que compartilham o mesmo denominador. Aqui está a equação para o denominador para *t* que varia de 0 a 1 e um valor de peso de *w*:

d (t) = (1 – t) ² + 2WT (1 – t) + t ²

Teoricamente, um quadrática racional pode envolver três valores de peso separados, um para cada um dos três termos, mas eles podem ser simplificados para apenas um valor de peso no termo médio.

As equações paramétricas para as coordenadas X e Y são semelhantes às equações paramétricas para o Bézier quadrática, exceto que o termo médio também inclui o valor de peso e a expressão é dividida pelo denominador:

x (t) = ((1 – t) ² x ₀ + 2WT (1 – t) x ₁ + t ² x ₂)) ÷ d (t)

y (t) = ((1 – t) ² y ₀ + 2WT (1 – t) y ₁ + t ² y ₂)) ÷ d (t)

As curvas Bézier de quadrática racional também são chamadas de *cônicas* porque podem representar exatamente os segmentos de qualquer seção de cone &mdash; hiperbólico, parabolas, elipses e círculos.

Para adicionar uma curva Bézier de quadrática racional a um caminho, use o [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) método ou a [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) sobrecarga com `x` coordenadas e separadas `y` :

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe o `weight` parâmetro final.

A página **curva de cone** permite que você experimente essas curvas. A classe `ConicCurvePage` deriva de `InteractivePage`. O arquivo [**ConicCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) instancia um `Slider` para selecionar um valor de peso entre – 2 e 2. O arquivo code-behind [**ConicCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) cria três `TouchPoint` objetos, e o `PaintSurface` manipulador simplesmente renderiza a curva resultante com as linhas tangentes para os pontos de controle:

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

Aqui está executando:

[![Captura de tela tripla da página de curva de cone](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Como você pode ver, o ponto de controle parece efetuar pull da curva em direção a mais quando o peso é maior. Quando o peso for zero, a curva se tornará uma linha reta do ponto inicial até o ponto de extremidade.

Teoricamente, os pesos negativos são permitidos e fazem com que a curva dobre para *fora* do ponto de controle. No entanto, os pesos de – 1 ou abaixo fazem com que o denominador nas equações paramétricas se torne negativo para valores específicos de *t*. Provavelmente, por esse motivo, os pesos negativos são ignorados nos `ConicTo` métodos. O programa de **curva de cone** permite definir pesos negativos, mas como você pode ver experimentando, os pesos negativos têm o mesmo efeito que um peso zero e fazem com que uma linha reta seja renderizada.

É muito fácil derivar o ponto de controle e o peso para usar o `ConicTo` método para desenhar um arco circular até (mas não incluindo) um semicírculo. No diagrama a seguir, as linhas tangentes dos pontos inicial e final se encontram no ponto de controle.

![Uma renderização de arco de cone de um arco circular](beziers-images/conicarc.png)

Você pode usar a trigonometria para determinar a distância do ponto de controle do centro do círculo: é o raio do círculo dividido pelo cosseno da metade do ângulo α. Para desenhar um arco circular entre os pontos inicial e final, defina o peso para o mesmo cosseno da metade do ângulo. Observe que, se o ângulo for de 180 graus, as linhas tangentes nunca serão atendidas e o peso será zero. Mas para ângulos inferiores a 180 graus, a matemática funciona bem.

A página de **arco circular de cone** demonstra isso. O arquivo [**ConicCircularArc. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) instancia um `Slider` para selecionar o ângulo. O `PaintSurface` manipulador no arquivo code-behind  [**ConicCircularArc.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcula o ponto de controle e o peso:

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

Como você pode ver, não há nenhuma diferença visual entre o `ConicTo` caminho mostrado em vermelho e o círculo subjacente exibido para referência:

[![Captura de tela tripla da página de arco circular do cone](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Mas defina o ângulo como 180 graus e a matemática falhará.

Infelizmente, nesse caso, isso `ConicTo` não dá suporte a pesos negativos, porque na teoria (com base nas equações paramétricas), o círculo pode ser concluído com outra chamada para `ConicTo` com os mesmos pontos, mas um valor negativo do peso. Isso permitiria a criação de um círculo inteiro com apenas duas `ConicTo` curvas com base em qualquer ângulo entre (mas não incluindo) zero graus e 180 graus.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)