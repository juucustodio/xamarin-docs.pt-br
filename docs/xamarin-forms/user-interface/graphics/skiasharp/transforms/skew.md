---
title: A transformação de distorção
description: Veja como a transformação de distorção pode criar objetos gráficos inclinados em SkiaSharp
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: charlespetzold
ms.author: chape
ms.date: 03/20/2017
ms.openlocfilehash: c8913dcb5dbe9664f1186b1acf46f09cb8da74ed
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="the-skew-transform"></a>A transformação de distorção

_Veja como a transformação de distorção pode criar objetos gráficos inclinados em SkiaSharp_

Em SkiaSharp, a transformação de distorção giram objetos gráficos, como sombra nesta imagem:

![](skew-images/skewexample.png "Um exemplo de inclinação do programa distorcer texto de sombra")

A distorção transforma retângulos em parallelograms, mas uma elipse distorcida ainda é uma elipse.

Embora xamarin. Forms define propriedades de conversão, dimensionar e rotações, não há nenhuma propriedade correspondente no xamarin. Forms para distorção.

O [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/System.Single/System.Single/) método `SKCanvas` aceita dois argumentos de inclinação horizontal e vertical distorcer:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Um segundo [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/SkiaSharp.SKPoint/) método combina esses argumentos em uma única `SKPoint` valor:

```csharp
public void Skew (SKPoint skew)
```

No entanto, é improvável que você estará usando um destes dois métodos em isolamento.

O **distorcer experimentar** página lhe permite fazer experiências com a diferença de valores que variam entre – 10 e 10. Uma cadeia de caracteres de texto é posicionada no canto superior esquerdo da página, com distorção valores obtidos nas duas `Slider` elementos. Aqui está o `PaintSurface` manipulador no [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Valores de `xSkew` argumento de deslocamento para a parte inferior do texto à direita para valores positivos ou à esquerda dos valores negativos. Valores de `ySkew` Deslocar à direita do texto para baixo para valores negativos ou valores positivos:

[![](skew-images/skewexperiment-small.png "Tripla captura de tela da página de teste distorcer")](skew-images/skewexperiment-large.png#lightbox "tripla captura de tela da página de teste distorcer")

Se `xSkew` é negativo do `ySkew`, o resultado é a rotação, mas também dimensionado um pouco como indica a exibição UWP.

As fórmulas de transformação são da seguinte maneira:

x' = x + xSkew · y

y' = ySkew · x + y

Por exemplo, para um positivo `xSkew` valor transformado `x'` aumenta o valor como `y` aumenta. É o que causa a inclinação.

Se um pixels de triângulo 200 largura e 100 pixels de altura é posicionado com seu canto superior esquerdo no ponto (0, 0) e é processado com um `xSkew` valor de 1,5, os seguintes resultados paralelogramo:

![](skew-images/skeweffect.png "O efeito da transformação de distorção em um retângulo")

As coordenadas da borda inferior tem `y` valores de 100, portanto, é deslocado 150 pixels à direita.

Para obter valores diferentes de zero de `xSkew` ou `ySkew`, somente o ponto (0, 0) permanece o mesmo. Esse ponto pode ser considerado o centro da inclinação. Se você precisar centro da inclinação para ser algo mais (que é geralmente o caso), não há nenhum `Skew` método que fornece que. Você precisará combinar explicitamente `Translate` chamadas com o `Skew` chamar. Para centralizar a inclinação na `px` e `py`, fazer as chamadas seguintes:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

As fórmulas de transformação composto são:

x' = x + xSkew · (y – py)

y' = ySkew · (x – px) + y

Se `ySkew` for zero, e você estiver especificando somente um valor diferente de zero de `xSkew`, em seguida, `px` valor não é usado. O valor é irrelevante e da mesma forma para `ySkew` e `py`.

Você pode achar mais fácil especificando distorção como um ângulo de inclinação, como o ângulo α neste diagrama:

![](skew-images/skewangleeffect.png "O efeito da transformação de distorção em um retângulo com um ângulo de inclinação indicado")

A taxa do turno 150 pixels para 100 pixels vertical é a tangente do ângulo de que, neste exemplo 56.3 graus.

O arquivo XAML do **experiência de ângulo de inclinação** página é semelhante do **ângulo de inclinação** página exceto que o `Slider` elementos variam de – 90 a 90 graus. O [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) arquivo code-behind centraliza o texto na página e usa `Translate` para definir um centro de inclinação para o centro da página. Uma breve `SkewDegrees` método na parte inferior do código converte ângulos para inclinar valores:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Como um ângulo abordagens de 90 graus positivos ou negativos, a tangente abordagens infinito, mas ângulos até aproximadamente 80 graus mais ou menos são úteis:

[![](skew-images/skewangleexperiment-small.png "Captura de tela da página de teste de ângulo de inclinação tripla")](skew-images/skewangleexperiment-large.png#lightbox "tripla captura de tela da página de teste de ângulo de inclinação")

Uma pequena inclinação de horizontal negativa pode imitar o texto em itálico ou Oblíquo, como o **texto oblíqua** demonstra da página. O [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe mostra como isso é feito:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

O `TextAlign` propriedade `SKPaint` é definido como `Center`. Sem quaisquer transformações a `DrawText` chamar pelas coordenadas de (0, 0) seria posicionar o texto com o centro da linha de base horizontal no canto superior esquerdo. O `SkewDegrees` inclina o texto horizontalmente 20 graus em relação à linha de base. O `Translate` chamada move o centro da linha de base do texto horizontal para o centro da tela:

[![](skew-images/obliquetext-small.png "Captura de tela da página de texto oblíqua tripla")](skew-images/obliquetext-large.png#lightbox "tripla captura de tela da página de texto oblíqua")

O **distorcer texto de sombra** página demonstra como usar uma combinação de uma escala de distorção e vertical de 45 graus para fazer uma sombra de texto que giram fora do texto. Aqui está a parte pertinente do `PaintSurface` manipulador:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

A sombra será exibido primeiro e, em seguida, o texto:

[![](skew-images/skewshadowtext1-small.png "Tripla captura de tela da página de texto de sombra distorcer")](skew-images/skewshadowtext1-large.png#lightbox "tripla captura de tela da página distorcer texto de sombra")

A coordenada vertical é passado para o `DrawText` método indica a posição do texto em relação à linha de base. É a coordenada vertical mesmo usada para o Centro de inclinação. Essa técnica não funcionará se a cadeia de caracteres de texto contém descendentes. Por exemplo, substitua a palavra "estranha" para "Sombra" e aqui 's o resultado:

[![](skew-images/skewshadowtext2-small.png "Captura de tela de tripla da página distorcer sombra texto com uma palavra alternativa com descendentes")](skew-images/skewshadowtext2-large.png#lightbox "tripla captura de tela da página distorcer sombra texto com uma palavra alternativa com descendentes")

Sombra de texto ainda estão alinhadas na linha de base, mas apenas o efeito parece incorreto. Para corrigi-lo, você precisa obter os limites de texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

O `Translate` chamadas precisam ser ajustadas pela altura do descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Agora, a sombra estende da parte inferior dos descendentes:

[![](skew-images/skewshadowtext3-small.png "Captura de tela de tripla da página de texto de sombra distorcer ajustes para os descendentes")](skew-images/skewshadowtext3-large.png#lightbox "tripla captura de tela da página de texto de sombra distorcer ajustes para os descendentes")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
