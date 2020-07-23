---
title: A transformação de distorção
description: Este artigo explica como a transformação de distorção pode criar objetos gráficos inclinados no SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 998584c3deebf5ab722758aeefe7560ba738f426
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939289"
---
# <a name="the-skew-transform"></a>A transformação de distorção

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Veja como a transformação de distorção pode criar objetos gráficos inclinados no SkiaSharp_

No SkiaSharp, a transformação de distorção inclina objetos gráficos, como a sombra nesta imagem:

![Um exemplo de distorção do programa de texto de sombra de distorção](skew-images/skewexample.png)

A distorção transforma um retângulo em um paralelogramo, mas uma elipse inclinada ainda é uma elipse.

Embora Xamarin.Forms defina propriedades para conversão, dimensionamento e rotações, não há nenhuma propriedade correspondente no Xamarin.Forms para distorção.

O [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) método de `SKCanvas` aceita dois argumentos para inclinação horizontal e inclinação vertical:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Um segundo [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) método combina esses argumentos em um único `SKPoint` valor:

```csharp
public void Skew (SKPoint skew)
```

No entanto, é improvável que você use qualquer um desses dois métodos isoladamente.

A página de **teste de distorção** permite que você experimente valores de distorção que variam entre – 10 e 10. Uma cadeia de texto é posicionada no canto superior esquerdo da página, com valores de distorção obtidos de dois `Slider` elementos. Aqui está o `PaintSurface` manipulador na [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

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

Os valores do `xSkew` argumento deslocam a parte inferior do texto para os valores positivos ou para os valores negativos. Valores de `ySkew` deslocamento à direita do texto para valores positivos ou para cima para valores negativos:

[![Captura de tela tripla da página de teste de distorção](skew-images/skewexperiment-small.png)](skew-images/skewexperiment-large.png#lightbox "Captura de tela tripla da página de teste de distorção")

Se o `xSkew` valor é negativo do `ySkew` valor, o resultado é rotação, mas também é um pouco dimensionado.

As fórmulas de transformação são as seguintes:

x ' = x + xSkew · Iar

y ' = ySkew · x + y

Por exemplo, para um `xSkew` valor positivo, o valor transformado `x'` aumenta conforme o `y` aumento. Isso é o que causa a inclinação.

Se um triângulo 200 pixels de largura e 100 pixels de altura estiver posicionado com seu canto superior esquerdo no ponto (0, 0) e for renderizado com um `xSkew` valor de 1,5, os seguintes resultados de paralelogramo:

![O efeito da transformação de distorção em um retângulo](skew-images/skeweffect.png)

As coordenadas da borda inferior têm `y` valores de 100, portanto, é deslocada 150 pixels para a direita.

Para valores diferentes de zero de `xSkew` ou `ySkew` , somente o ponto (0, 0) permanece o mesmo. Esse ponto pode ser considerado o centro de distorção. Se você precisar do centro de distorção para ser algo mais (que geralmente é o caso), não há nenhum `Skew` método que forneça isso. Você precisará combinar explicitamente `Translate` as chamadas com a `Skew` chamada. Para centralizar a distorção em `px` e `py` , faça as seguintes chamadas:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

As fórmulas de transformação composta são:

x ' = x + xSkew · (y – py)

y ' = ySkew · (x – px) + y

Se `ySkew` for zero, o `px` valor não será usado. O valor é irrelevante e, da mesma forma, para `ySkew` e `py` .

Você pode se sentir mais confortável ao especificar distorção como um ângulo de inclinação, como o ângulo α neste diagrama:

![O efeito da transformação de distorção em um retângulo com um ângulo de distorção indicado](skew-images/skewangleeffect.png)

A taxa da mudança de 150 pixels para a vertical de 100 pixels é a tangente desse ângulo, neste exemplo, 56,3 graus.

O arquivo XAML da página de **teste de ângulo de distorção** é semelhante à página de **ângulo de inclinação** , exceto que os `Slider` elementos variam de – 90 graus a 90 graus. O [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) arquivo code-behind centraliza o texto na página e usa `Translate` para definir um centro de distorção para o centro da página. Um `SkewDegrees` método curto na parte inferior do código converte ângulos em valores de distorção:

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

Como um ângulo se aproxima de 90 graus positivos ou negativos, a tangente se aproxima de infinito, mas os ângulos até cerca de 80 graus ou outros são utilizáveis:

[![Captura de tela tripla da página de teste do ângulo de distorção](skew-images/skewangleexperiment-small.png)](skew-images/skewangleexperiment-large.png#lightbox "Captura de tela tripla da página de teste do ângulo de distorção")

Uma pequena inclinação horizontal negativa pode imitar um texto oblíquo ou itálico, como demonstra a página de **texto oblíquo** . A [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe mostra como isso é feito:

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

A `TextAlign` propriedade de `SKPaint` é definida como `Center` . Sem nenhuma transformação, a `DrawText` chamada com coordenadas de (0, 0) posicionaria o texto com o centro horizontal da linha de base no canto superior esquerdo. O `SkewDegrees` inclina o texto horizontalmente em 20 graus em relação à linha de base. A `Translate` chamada move o centro horizontal da linha de base do texto para o centro da tela:

[![Captura de tela tripla da página de texto oblíqua](skew-images/obliquetext-small.png)](skew-images/obliquetext-large.png#lightbox "Captura de tela tripla da página de texto oblíqua")

A página **texto da sombra de distorção** demonstra como usar uma combinação de uma inclinação de 45 graus e uma escala vertical para criar uma sombra de texto que se incline para fora do texto. Aqui está a parte pertinente do `PaintSurface` manipulador:

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

A sombra é exibida primeiro e, em seguida, o texto:

[![Captura de tela tripla da página de texto sombra de distorção](skew-images/skewshadowtext1-small.png)](skew-images/skewshadowtext1-large.png#lightbox "Captura de tela tripla da página de texto sombra de distorção")

A coordenada vertical passada para o `DrawText` método indica a posição do texto em relação à linha de base. Essa é a mesma coordenada vertical usada para o centro de distorção. Essa técnica não funcionará se a cadeia de caracteres de texto contiver descendentes. Por exemplo, substitua a palavra "peculiarity" por "Shadow" e veja o resultado:

[![Captura de tela tripla da página de texto da sombra de distorção com uma palavra alternativa com descendentes](skew-images/skewshadowtext2-small.png)](skew-images/skewshadowtext2-large.png#lightbox "Captura de tela tripla da página de texto da sombra de distorção com uma palavra alternativa com descendentes")

A sombra e o texto ainda estão alinhados na linha de base, mas o efeito simplesmente parece errado. Para corrigi-lo, você precisa obter os limites do texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

As `Translate` chamadas precisam ser ajustadas pela altura dos descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Agora, a sombra se estende da parte inferior dos descendentes:

[![Captura de tela tripla da página de texto de sombra de distorção com ajustes para descendentes](skew-images/skewshadowtext3-small.png)](skew-images/skewshadowtext3-large.png#lightbox "Captura de tela tripla da página de texto de sombra de distorção com ajustes para descendentes")

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
