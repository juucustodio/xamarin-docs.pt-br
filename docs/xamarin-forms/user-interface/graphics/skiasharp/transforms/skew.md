---
title: A transformação de distorção
description: Este artigo explica como a transformação de distorção pode criar objetos gráficos inclinados em SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: c3d7e6e0c0e3230c11e2e96baa9efa57ac988c83
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545654"
---
# <a name="the-skew-transform"></a>A transformação de distorção

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Veja como a transformação de distorção pode criar objetos gráficos inclinados em SkiaSharp_

No SkiaSharp, a transformação de distorção inclina objetos gráficos, como sombra nesta imagem:

![](skew-images/skewexample.png "An example of skewing from the Skew Shadow Text program")

A distorção transforma um retângulo em um paralelogramo, mas uma elipse distorcida ainda é uma elipse.

Embora o xamarin. Forms define propriedades de translação, dimensionamento e as rotações, não há nenhuma propriedade correspondente no xamarin. Forms para distorção.

O [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) método `SKCanvas` aceita dois argumentos para distorção horizontal e vertical distorção:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Um segundo [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) método combina esses argumentos em uma única `SKPoint` valor:

```csharp
public void Skew (SKPoint skew)
```

No entanto, é improvável que você esteja usando qualquer um desses dois métodos em isolamento.

O **distorcer experimentar** página permite que você experimente a distorção de valores que variam entre – 10 e 10. Uma cadeia de caracteres de texto é posicionada no canto superior esquerdo da página, com valores de distorção obtidos dos dois `Slider` elementos. Aqui está o `PaintSurface` manipulador na [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

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

Valores da `xSkew` argumento deslocar a parte inferior do texto à direita para valores positivos ou à esquerda para valores negativos. Valores de `ySkew` shift direita do texto para baixo para valores positivos ou para valores negativos:

[![](skew-images/skewexperiment-small.png "Triple screenshot of the Skew Experiment page")](skew-images/skewexperiment-large.png#lightbox "Triple screenshot of the Skew Experiment page")

Se o valor de `xSkew` for o negativo do valor de `ySkew`, o resultado será rotação, mas também escalado um pouco.

As fórmulas de transformação são da seguinte maneira:

x' = x + xSkew reforçado y

y' = ySkew reforçado x + y

Por exemplo, para um positivo `xSkew` valor transformado `x'` aumenta o valor como `y` aumenta. Esse é o que faz com que a inclinação.

Se um pixels de triângulo 200 largura e 100 pixels de altura é posicionado com seu canto superior esquerdo no ponto (0, 0) e é renderizado com um `xSkew` valor de 1,5, os seguintes resultados paralelogramo:

![](skew-images/skeweffect.png "The effect of the skew transform on a rectangle")

As coordenadas da borda inferior tem `y` valores de 100, portanto, ele é deslocado 150 pixels para a direita.

Para valores diferentes de zero da `xSkew` ou `ySkew`, somente o ponto (0, 0) permanece o mesmo. Desse ponto pode ser considerado o centro da inclinação. Se você precisar o centro da inclinação para ser algo mais (que é geralmente o caso), há nenhuma `Skew` método que fornece que. Você precisará combinar explicitamente `Translate` chamadas com o `Skew` chamar. Para centralizar a distorção na `px` e `py`, faça as seguintes chamadas:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

As fórmulas de transformação composta são:

x' = x + xSkew reforçado (y – py)

y' = ySkew reforçado (x – px) + y

Se `ySkew` for zero, então o `px` valor não é usado. O valor é irrelevante e da mesma forma para `ySkew` e `py`.

Você pode achar mais fácil especificar distorção como um ângulo de inclinação, como o ângulo α neste diagrama:

![](skew-images/skewangleeffect.png "The effect of the skew transform on a rectangle with a skewing angle indicated")

A proporção do turno 150 pixels para a vertical de 100 pixels é a tangente do ângulo de que, neste exemplo 56.3 graus.

O arquivo XAML do **experimento de ângulo de inclinação** página é semelhante ao **ângulo de inclinação** página, exceto que o `Slider` elementos variam de-90 graus a 90 graus. O [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) centraliza o texto na página de código code-behind e usa `Translate` para definir um centro de inclinação para o centro da página. Um breve `SkewDegrees` método na parte inferior do código converte ângulos para distorcer valores:

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

Como se aproxima de um ângulo positivos ou negativos 90 graus, a tangente se aproxima de infinito, mas ângulos até cerca de 80 graus ou isso podem ser usados:

[![](skew-images/skewangleexperiment-small.png "Triple screenshot of the Skew Angle Experiment page")](skew-images/skewangleexperiment-large.png#lightbox "Triple screenshot of the Skew Angle Experiment page")

Uma distorção horizontal de negativa pequeno pode imitar o texto em itálico ou Oblíquo, como o **oblíqua texto** página demonstra. O [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe mostra como isso é feito:

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

O `TextAlign` propriedade de `SKPaint` é definido como `Center`. Sem quaisquer transformações a `DrawText` chamar com coordenadas de (0, 0) seria posicionar o texto com o centro horizontal da linha de base no canto superior esquerdo. O `SkewDegrees` inclina o texto horizontalmente 20 graus em relação à linha de base. O `Translate` chamada move o centro horizontal da linha de base do texto para o centro da tela:

[![](skew-images/obliquetext-small.png "Triple screenshot of the Oblique Text page")](skew-images/obliquetext-large.png#lightbox "Triple screenshot of the Oblique Text page")

O **distorcer texto de sombra** página demonstra como usar uma combinação de uma escala de distorção e vertical de 45 graus para fazer uma sombra de texto que se inclina para longe do texto. Aqui está a parte pertinente do `PaintSurface` manipulador:

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

[![](skew-images/skewshadowtext1-small.png "Triple screenshot of the Skew Shadow Text page")](skew-images/skewshadowtext1-large.png#lightbox "Triple screenshot of the Skew Shadow Text page")

A coordenada vertical é passado para o `DrawText` método indica a posição do texto em relação à linha de base. Essa é a coordenada vertical mesma usada para o Centro de inclinação. Essa técnica não funcionará se a cadeia de caracteres de texto contém descendentes. Por exemplo, substitua a palavra "estranha" para "Sombra" e aqui está o resultado:

[![](skew-images/skewshadowtext2-small.png "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")](skew-images/skewshadowtext2-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")

O sombra e o texto ainda estão alinhadas na linha de base, mas o efeito apenas se parece errado. Para corrigi-lo, você precisará obter os limites de texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

O `Translate` chamadas devem ser ajustados pela altura dos descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Agora, a sombra estende da parte inferior dos descendentes:

[![](skew-images/skewshadowtext3-small.png "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")](skew-images/skewshadowtext3-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")

## <a name="related-links"></a>Links Relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
