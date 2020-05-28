---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 520c4c3b61049bf17c2c964523714db196da6839
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132178"
---
# <a name="the-rotate-transform"></a>A transformação de rotação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore os efeitos e animações possíveis com a transformação SkiaSharp Rotate_

Com a transformação girar, os objetos gráficos SkiaSharp quebram-se livres da restrição de alinhamento com os eixos horizontal e vertical:

![](rotate-images/rotateexample.png "Text rotated around a center")

Para girar um objeto gráfico em volta do ponto (0, 0), SkiaSharp dá suporte a um [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) método e a um [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Um círculo de 360 graus é o mesmo que twoπ radianos, portanto, é fácil converter entre as duas unidades. Use o que for conveniente. Todas as funções trigonométricas na classe .NET [`Math`](xref:System.Math) usam unidades de radianos.

A rotação está no sentido horário para aumentar os ângulos. (Embora a rotação no sistema de coordenadas cartesianas esteja no sentido anti-horário por convenção, a rotação horária é consistente com as coordenadas Y aumentando para baixo como em SkiaSharp.) Ângulos negativos e ângulos maiores que 360 graus são permitidos.

As fórmulas de transformação para rotação são mais complexas do que aquelas para conversão e escala. Para um ângulo de α, as fórmulas de transformação são:

x ' = x • cos (α) – y • sin (α)   

y ' = x • sin (α) + y • cos (α)

A página de **rotação básica** demonstra o `RotateDegrees` método. O arquivo [**BasicRotate.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) exibe algum texto com sua linha de base centralizada na página e gira-o com base em um `Slider` com um intervalo de – 360 a 360. Aqui está a parte relevante do `PaintSurface` manipulador:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Como a rotação é centralizada em todo o canto superior esquerdo da tela, para a maioria dos ângulos definidos neste programa, o texto é girado na tela:

[![](rotate-images/basicrotate-small.png "Triple screenshot of the Basic Rotate page")](rotate-images/basicrotate-large.png#lightbox "Triple screenshot of the Basic Rotate page")

Muitas vezes, você desejará girar algo centralizado em um ponto dinâmico especificado usando essas versões [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) dos [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) métodos e:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

A página de **giro centralizada** é exatamente como a **rotação básica** , exceto que a versão expandida do `RotateDegrees` é usada para definir o centro de rotação para o mesmo ponto usado para posicionar o texto:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Agora, o texto gira em volta do ponto usado para posicionar o texto, que é o centro horizontal da linha de base do texto:

[![](rotate-images/centeredrotate-small.png "Triple screenshot of the Centered Rotate page")](rotate-images/centeredrotate-large.png#lightbox "Triple screenshot of the Centered Rotate page")

Assim como acontece com a versão centralizada do `Scale` método, a versão centralizada da `RotateDegrees` chamada é um atalho. Este é o método:

```csharp
RotateDegrees (degrees, px, py);
```

Essa chamada é equivalente à seguinte:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Você descobrirá que, às vezes, pode combinar `Translate` chamadas com `Rotate` chamadas. Por exemplo, aqui estão as `RotateDegrees` `DrawText` chamadas e na página **girar centralizada** ;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

A `RotateDegrees` chamada é equivalente a duas `Translate` chamadas e uma não centralizada `RotateDegrees` :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

A `DrawText` chamada para exibir texto em um local específico é equivalente a uma `Translate` chamada para esse local seguido por `DrawText` no ponto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

As duas chamadas consecutivas `Translate` cancelam uma à outra:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceitualmente, as duas transformações são aplicadas na ordem oposta ao modo como aparecem no código. A `DrawText` chamada exibe o texto no canto superior esquerdo da tela. A `RotateDegrees` chamada gira esse texto em relação ao canto superior esquerdo. Em seguida, a `Translate` chamada move o texto para o centro da tela.

Geralmente, há várias maneiras de combinar a rotação e a tradução. A página de **texto girada** cria a seguinte exibição:

[![](rotate-images/rotatedtext-small.png "Triple screenshot of the Rotated Text page")](rotate-images/rotatedtext-large.png#lightbox "Triple screenshot of the Rotated Text page")

Este é o `PaintSurface` manipulador da [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe:

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

Os `xCenter` `yCenter` valores e indicam o centro da tela. O `yText` valor é um pouco de deslocamento. Esse valor é a coordenada Y necessária para posicionar o texto para que ele seja realmente centralizado na página. `for`Em seguida, o loop define uma rotação com base no centro da tela. A rotação está em incrementos de 30 graus. O texto é desenhado usando o `yText` valor. O número de espaços em branco antes da palavra "girar" no `text` valor foi determinado empiricamente para fazer com que a conexão entre essas 12 cadeias de caracteres de texto pareça ser um dodecagon.

Uma maneira de simplificar esse código é incrementar o ângulo de rotação em 30 graus a cada vez pelo loop após a `DrawText` chamada. Isso elimina a necessidade de chamadas para `Save` e `Restore` . Observe que a `degrees` variável não é mais usada no corpo do `for` bloco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

Também é possível usar a forma simples de `RotateDegrees` pretendo o loop com uma chamada para `Translate` para mover tudo para o centro da tela:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

O `yText` cálculo modificado não é mais incorpodo `yCenter` . Agora `DrawText` , a chamada centraliza o texto verticalmente na parte superior da tela.

Como as transformações são aplicadas conceitualmente ao que aparecem no código, muitas vezes é possível começar com mais transformações globais, seguidas por mais transformações locais. Geralmente, essa é a maneira mais fácil de combinar a rotação e a tradução.

Por exemplo, suponha que você queira desenhar um objeto gráfico que gira em todo o seu centro como um planeta girando em seu eixo. Mas você também quer que esse objeto gire em todo o centro da tela, como um planeta que gira em todo o sol.

Você pode fazer isso posicionando o objeto no canto superior esquerdo da tela e, em seguida, usando uma animação para girá-lo em todo o canto. Em seguida, traduza o objeto horizontalmente como um raio orbital. Agora, aplique uma segunda rotação animada, também em volta da origem. Isso faz o objeto girar ao lado do canto. Agora, traduza para o centro da tela.

Este é o `PaintSurface` manipulador que contém essas chamadas de transformação na ordem inversa:

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

Os `revolveDegrees` `rotateDegrees` campos e são animados. Este programa usa uma técnica de animação diferente com base na Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) classe. (Essa classe é descrita no [capítulo 22 da *criação de aplicativos móveis Xamarin.Forms com * ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)o) `OnAppearing` . a substituição cria dois `Animation` objetos com métodos de retorno de chamada e, em seguida, `Commit` os chama para uma duração de animação:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

O primeiro `Animation` objeto anima `revolveDegrees` de 0 graus a 360 graus em 10 segundos. A segunda anima `rotateDegrees` de 0 graus a 360 graus a cada 1 segundo e também invalida a superfície para gerar outra chamada para o `PaintSurface` manipulador. A `OnDisappearing` substituição cancela estas duas animações:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

O programa de **relógio analógico ruim** (portanto chamado porque um relógio analógico mais atrativo será descrito em um artigo posterior) usa a rotação para desenhar as marcas de minuto e hora do relógio e para girar as mãos. O programa desenha o relógio usando um sistema de coordenadas arbitrário com base em um círculo que está centralizado no ponto (0, 0) com um raio de 100. Ele usa conversão e dimensionamento para expandir e centralizar esse círculo na página.

As `Translate` `Scale` chamadas e se aplicam globalmente ao relógio, portanto, essas são as primeiras a serem chamadas após a inicialização dos `SKPaint` objetos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

Há 60 marcas de dois tamanhos diferentes que devem ser desenhados em um círculo ao redor do relógio. A `DrawCircle` chamada desenha esse círculo no ponto (0, – 90), que em relação ao centro do relógio corresponde a 12:00. A `RotateDegrees` chamada incrementa o ângulo de rotação em 6 graus depois de cada marca de escala. A `angle` variável é usada unicamente para determinar se um círculo grande ou um pequeno círculo é desenhado:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Por fim, o `PaintSurface` manipulador obtém a hora atual e calcula os graus de rotação para as mãos de hora, minuto e segundo. Cada mão é desenhado na posição 12:00 para que o ângulo de rotação seja relativo a isso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

O relógio é certamente funcional, embora as mãos sejam muito cruas:

[![](rotate-images/uglyanalogclock-small.png "Triple screenshot of the Ugly Analog Clock Text page")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

Para obter um relógio mais atrativo, consulte o artigo [**dados de caminho SVG em SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
