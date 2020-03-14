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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305391"
---
# <a name="three-types-of-bzier-curves"></a>Três tipos de curvas de bézier

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore como usar SkiaSharp para renderizar curvas Bézier cúbicas, quadráticas e cônicas_

A curva de Bézier é nomeada após Pierre Bézier (1910 – 1999), um engenheiro de francês da empresa automotiva Renault, que usaram a curva para o design auxiliada por computador de corpos de carro.

As curvas Bézier são conhecidas por serem adequadas para o design interativo: elas têm um bom desempenho &mdash; em outras palavras, não há singularidades que façam com que a curva se torne infinita ou incômodo &mdash; e, em geral, é bastante agradável:

![Uma curva de Bézier de exemplo](beziers-images/beziersample.png)

Contornos de caracteres de fontes baseadas em computador normalmente são definidos com curvas de Bézier.

O artigo da Wikipédia na [**curva de Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contém algumas informações úteis do plano de fundo. O termo *curva Bézier* na verdade se refere a uma família de curvas semelhantes. O SkiaSharp dá suporte a três tipos de curvas Bézier, chamadas *cúbica*, *quadrática*e *cone*. O cone também é conhecido como o *quadrática racional*.

## <a name="the-cubic-bzier-curve"></a>A curva de Bézier cúbica

O cúbico é o tipo de curva de Bézier que a maioria dos desenvolvedores pensam quando o assunto de curvas de Bézier é exibido.

Você pode adicionar uma curva Bézier cúbica a um objeto `SKPath` usando o método [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) com três parâmetros `SKPoint` ou a sobrecarga de [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) com os parâmetros `x` e `y` separados:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

A curva começa no ponto atual da delimitação. A curva de Bézier cúbica completa é definida por quatro pontos:

- ponto de início: ponto atual na delimitação ou (0, 0) se `MoveTo` não tiver sido chamado
- primeiro ponto de controle: `point1` na chamada `CubicTo`
- segundo ponto de controle: `point2` na chamada `CubicTo`
- ponto de extremidade: `point3` na chamada `CubicTo`

A curva resultante começa no ponto de início e termina no ponto de extremidade. A curva geralmente não passa pelos pontos de controle de dois; em vez disso, o controle de pontos de função como ímãs para efetuar pull de curva em direção eles.

É a melhor maneira de ter uma noção da curva de Bézier cúbica por experimentação. Essa é a finalidade da página de **curva de Bézier** , que deriva de `InteractivePage`. O arquivo [**BezierCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) instancia a `SKCanvasView` e uma `TouchEffect`. O arquivo code-behind [**BezierCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) cria quatro objetos `TouchPoint` em seu construtor. O manipulador de eventos `PaintSurface` cria uma `SKPath` para renderizar uma curva de Bézier com base nos quatro objetos `TouchPoint` e também desenha linhas tangentes pontilhadas dos pontos de controle para os pontos de extremidade:

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

[![captura de tela tripla da página de curva de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matematicamente, a curva é uma polinomial cúbica. A curva cruza uma linha reta em três pontos no máximo. No ponto de início, a curva é sempre aponte tangente e, em que a mesma direção que, de uma linha reta a partir do início até o primeiro ponto de controle. No ponto de extremidade, a curva é sempre uma linha reta do segundo controle de tangente e, em que a mesma direção, aponte para o ponto de extremidade.

A curva de Bézier cúbica sempre é delimitada por um diamante convexo que conecta os quatro pontos. Isso é chamado de *convexa envoltória*. Se os pontos de controle residem na linha reta entre o início e o ponto de extremidade, a curva de Bézier é renderizado como uma linha reta. Mas a curva também pode cruzar-em si, como demonstra a captura de tela de terceiro.

Um contorno de caminho pode conter várias curvas de Bézier cúbicas conectadas, mas a conexão entre duas curvas de Bézier cúbicas será suave somente se os três pontos a seguir são colinear (ou seja, se encontram em uma linha reta):

- o segundo ponto de controle da curva primeiro
- o ponto de extremidade da curva primeiro, que também é o ponto de início da segunda curva
- o primeiro ponto de controle da curva de segundo

No próximo artigo sobre [**dados de caminho SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), você descobrirá uma instalação para facilitar a definição de curvas Bézier suaves conectadas.

Às vezes é útil saber equações paramétricas subjacentes que renderizam uma curva de Bézier cúbica. Para *t* variando de 0 a 1, as equações paramétricas são as seguintes:

x (t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

O expoente mais alto de 3 confirma que esses são polynomials cúbicas. É fácil verificar se, quando `t` é igual a 0, o ponto é (x ₀, y ₀), que é o ponto de partida e quando `t` é igual a 1, o ponto é (x ₃, y ₃), que é o ponto de extremidade. Próximo ao ponto de início (para valores baixos de `t`), o primeiro ponto de controle (x ₁, y ₁) tem um efeito forte e, perto do ponto de extremidade (valores altos de ' T'), o segundo ponto de controle (x ₂, y ₂) tem um efeito forte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Aproximação da curva de Bézier arcos circulares

Às vezes, é conveniente usar uma curva de Bézier para renderizar um arco circular. Uma curva Bézier cúbica pode aproximar um arco circular muito bem para um quarto de círculo, de modo que quatro curvas Bézier conectadas podem definir um círculo inteiro. Essa aproximação é aborda em dois artigos publicados mais de 25 anos atrás:

> Tor Dokken, et al, "boa aproximação de círculos por curvatura, curvas de Bézier contínuas", *design de geométrico auxiliado por computador 7* (1990), 33-41.

> Michael Goldapp, "aproximação de arcos circulares por polinomiais cúbicos", *design de geométrico auxiliado por computador 8* (1991), 227-238.

O diagrama a seguir mostra quatro pontos rotulados `pto`, `pt1`, `pt2`e `pt3` definir uma curva de Bézier (mostrada em vermelho) que aproxima um arco circular:

![Aproximação de um arco circular com uma curva de Bézier](beziers-images/bezierarc45.png)

As linhas dos pontos inicial e final para os pontos de controle são tangentes ao círculo e à curva Bézier, e têm um comprimento de *L*. O primeiro artigo citado acima indica que a curva de Bézier melhor aproxima um arco circular quando esse comprimento *L* é calculado da seguinte maneira:

L = 4 × tan(α / 4) / 3

A ilustração mostra um ângulo de 45 graus, portanto, L é igual a 0.265. No código, esse valor seria multiplicada pelo raio do círculo desejado.

A página de **arco circular de Bézier** permite que você experimente definir uma curva de Bézier para aproximar um arco circular para ângulos variando até 180 graus. O arquivo [**BezierCircularArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) instancia a `SKCanvasView` e uma `Slider` para selecionar o ângulo. O manipulador de eventos `PaintSurface` no arquivo code-behind [**BezierCircularArgPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa uma transformação para definir o ponto (0, 0) no centro da tela. Ele desenha um círculo centralizado naquele ponto para comparação e, em seguida, calcula os dois pontos de controle da curva de Bézier:

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

Os pontos inicial e final (`point0` e `point3`) são calculados com base nas equações paramétricas normais do círculo. Porque o círculo é centralizado em (0, 0), esses pontos também podem ser tratados como vetores radiais do centro do círculo para a circunferência. Os pontos de controle estão nas linhas que são tangente no círculo, portanto, eles são ângulos retos para esses vetores radiais. Um vetor em um ângulo reto para outro é simplesmente o vetor original com as coordenadas X e Y trocadas e um deles feitas negativo.

Aqui está o programa em execução com ângulos diferentes:

[![captura de tela tripla da página de arco circular de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Examinar de perto a captura de tela de terceiro, e você verá que a curva de Bézier notavelmente se desvia de um semicírculo quando o ângulo de 180 graus, mas a tela do iOS mostra que ele parece se ajustar a um trimestre-círculo bem quando o ângulo é 90 graus.

Calcular as coordenadas dos pontos de controle é muito fácil quando o quarto de círculo é orientado como este:

![Aproximação de um círculo trimestral com uma curva Bézier](beziers-images/bezierarc90.png)

Se o raio do círculo for 100, o *L* será 55, e esse é um número fácil de lembrar.

O **elevar a página de círculo** anima uma figura entre um círculo e um quadrado. O círculo é aproximado por quatro curvas Bézier cujas coordenadas são mostradas na primeira coluna dessa definição de matriz na classe [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) :

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

A segunda coluna contém as coordenadas dos quatro curvas de Bézier que definem um quadrado cuja área é aproximadamente o mesmo que a área do círculo. (Desenhar um quadrado com a área *exata* como um determinado círculo é o problema geométrico não resolvido clássico de [elevar o círculo](https://en.wikipedia.org/wiki/Squaring_the_circle).) Para renderizar um quadrado com curvas Bézier, os dois pontos de controle para cada curva são os mesmos e são colineares com os pontos inicial e final, portanto, a curva de Bézier é renderizada como uma linha reta.

A terceira coluna da matriz é interpolada valores para uma animação. A página define um temporizador para 16 milissegundos e o manipulador de `PaintSurface` é chamado com essa taxa:

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

Os pontos são interpolados com base em um valor de sinusoidally oscillating de `t`. Os pontos interpolados, em seguida, são usados para construir uma série de quatro curvas de Bézier conectadas. Aqui está a animação em execução:

[![captura de tela tripla de elevar a página de círculo](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Uma animação desse tipo seria impossível sem as curvas que estão algoritmicamente é flexíveis o suficiente para ser processado como linhas retas e arcos circulares.

A página **infinito de Bézier** também aproveita a capacidade de uma curva Bézier para aproximar um arco circular. Aqui está o manipulador de `PaintSurface` da classe [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) :

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

Pode ser um bom exercício para plotar essas coordenadas no papel de gráfico para ver como eles estão relacionados. O símbolo de infinito é centralizado em torno do ponto (0, 0), e os dois loops tem centros de (–150, 0) e (150, 0) e o raio de 100. Na série de comandos `CubicTo`, você pode ver as coordenadas X dos pontos de controle assumindo os valores de – 95 e – 205 (esses valores são – 150 mais e menos 55), 205 e 95 (150 Plus e menos 55), bem como 250 e – 250 para os lados direito e esquerdo. A única exceção é quando o símbolo de infinito cruza em si no centro. Nesse caso, os pontos de controle têm coordenadas com uma combinação de 50 e – 50 alinhe a curva próximo ao centro.

Aqui está o símbolo de infinito:

[![captura de tela tripla da página de infinitos de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

É um pouco mais suave em relação ao centro do que o sinal de infinito renderizado pela página de **infinito de arco** das [**três maneiras de desenhar um**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo de arco.

## <a name="the-quadratic-bzier-curve"></a>A curva de Bézier quadrática

A curva de Bézier quadrática tem o ponto de controle de apenas um, e a curva é definida por apenas três pontos: o ponto inicial, o ponto de controle e o ponto de extremidade. Equações paramétricas são muito semelhantes para a curva de Bézier cúbica, exceto que o expoente mais alto é 2, portanto, a curva é uma polinomial quadrática:

x (t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

Para adicionar uma curva Bézier quadrática a um caminho, use o método [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) ou a sobrecarga [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) com coordenadas `x` e `y` separadas:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Os métodos adicionam uma curva da posição atual para `point2` com `point1` como o ponto de controle.

Você pode experimentar as curvas Bézier quadráticas com a página de **curva quadrática** , que é muito semelhante à página de **curva de Bézier** , exceto que ela tem apenas três pontos de toque. Aqui está o manipulador de `PaintSurface` no arquivo code-behind [**QuadraticCurve.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

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

[![captura de tela tripla da página de curva quadrática](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

As linhas pontilhadas são tangente da curva no ponto inicial e o ponto de extremidade e atender a no ponto de controle.

O Bézier quadrática é bom se você precisa de uma curva de uma forma geral, mas você prefere a conveniência de ponto de apenas um controle em vez de dois. O Bézier quadrática renderiza mais eficiente do que qualquer outra curva, que é por isso que ele é usado internamente no Skia para renderizar elípticos.

No entanto, a forma de uma curva Bézier quadrática não é elíptica, motivo pelo qual várias Béziers quadráticas são necessárias para aproximar um arco elíptico. O Bézier quadrática é, em vez disso, um segmento de um Parabola.

## <a name="the-conic-bzier-curve"></a>A curva de Bézier Conic

A curva Bézier de cone &mdash; também conhecida como a curva de Bézier de quadrática racional &mdash; é uma adição relativamente recente à família de curvas Bézier. Como a curva de Bézier quadrática, a curva de Bézier quadrática racional envolve um ponto inicial, um ponto de extremidade e o ponto de um controle. Mas a curva Bézier de quadrática racional também requer um valor de *peso* . Ele é chamado de quadrática *racional* porque as fórmulas paramétricas envolvem proporções.

As equações paramétricas para X e Y são razões que compartilham o mesmo denominador. Aqui está a equação para o denominador para *t* que varia de 0 a 1 e um valor de peso de *w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

Em teoria, uma quadrática racional pode envolver três valores de peso separado, um para cada um dos três termos, mas eles podem ser simplificados para apenas um valor de peso no meio termo.

As equações paramétricas para as coordenadas X e Y são semelhantes às equações paramétricas para de Bézier quadrática, exceto que o meio termo também inclui o valor de peso e a expressão é dividida pelo denominador:

x (t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

As curvas Bézier de quadrática racional também são chamadas de *cônicas* porque podem representar exatamente os segmentos de qualquer seção de cone &mdash; hiperbólicas, parabolas, reticências e círculos.

Para adicionar uma curva de Bézier quadrática racional a um caminho, use o método [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) ou a sobrecarga [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) com coordenadas `x` e `y` separadas:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Observe o parâmetro de `weight` final.

A página **curva de cone** permite que você experimente essas curvas. O `ConicCurvePage` classe deriva de `InteractivePage`. O arquivo [**ConicCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) instancia uma `Slider` para selecionar um valor de peso entre – 2 e 2. O arquivo code-behind [**ConicCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) cria três objetos `TouchPoint`, e o manipulador de `PaintSurface` simplesmente renderiza a curva resultante com as linhas tangentes para os pontos de controle:

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

[![captura de tela tripla da página de curva de cone](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Como você pode ver, o ponto de controle parece puxe a curva em direção ele mais quando o peso é mais alto. Quando o peso é zero, a curva se torna uma linha reta do ponto de início para o ponto de extremidade.

Teoricamente, os pesos negativos são permitidos e fazem com que a curva dobre para *fora* do ponto de controle. No entanto, os pesos de – 1 ou abaixo fazem com que o denominador nas equações paramétricas se torne negativo para valores específicos de *t*. Provavelmente, por esse motivo, os pesos negativos são ignorados nos métodos de `ConicTo`. O programa de **curva de cone** permite definir pesos negativos, mas como você pode ver experimentando, os pesos negativos têm o mesmo efeito que um peso zero e fazem com que uma linha reta seja renderizada.

É muito fácil derivar o ponto de controle e o peso para usar o método `ConicTo` para desenhar um arco circular até (mas não incluindo) um semicírculo. No diagrama a seguir, atender às linhas tangentes do início e pontos de extremidade no ponto de controle.

![Uma renderização de arco de cone de um arco circular](beziers-images/conicarc.png)

Você pode usar trigonometria para determinar a distância do ponto de controle do centro do círculo: é o raio do círculo dividido pelo cosseno de metade do ângulo α. Para desenhar um arco circular entre os pontos inicial e final, defina o peso para esse mesmo cosseno metade do ângulo. Observe que se o ângulo de 180 graus, em seguida, as linhas tangentes nunca se encontram e o peso é zero. Mas para ângulos menor que 180 graus, a matemática funciona bem.

A página de **arco circular de cone** demonstra isso. O arquivo [**ConicCircularArc. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) instancia uma `Slider` para selecionar o ângulo. O manipulador de `PaintSurface` no arquivo code-behind [**ConicCircularArc.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcula o ponto de controle e o peso:

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

Como você pode ver, não há nenhuma diferença visual entre o caminho de `ConicTo` mostrado em vermelho e o círculo subjacente exibido para referência:

[![captura de tela tripla da página de arco circular do cone](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Mas, definir o ângulo de 180 graus e a matemática falhar.

Infelizmente, nesse caso, o `ConicTo` não dá suporte a pesos negativos, porque na teoria (com base nas equações paramétricas), o círculo pode ser concluído com outra chamada para `ConicTo` com os mesmos pontos, mas um valor negativo do peso. Isso permitiria a criação de um círculo inteiro com apenas duas `ConicTo` curvas com base em qualquer ângulo entre (mas não incluindo) zero graus e 180 graus.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
