---
title: A transformação de rotação
description: Este artigo explora os efeitos e animações possíveis com a transformação de rotação SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 514ecd16fedd7d3fda39fe20641cf0ee9ecb119e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244614"
---
# <a name="the-rotate-transform"></a>A transformação de rotação

_Explorar os efeitos e animações possíveis com a transformação de rotação SkiaSharp_

Com a transformação de rotação, objetos de gráficos SkiaSharp escapar da restrição de alinhamento com os eixos horizontais e verticais:

![](rotate-images/rotateexample.png "Texto girado em torno de um centro")

Para girar um objeto gráfico em torno do ponto (0, 0), SkiaSharp oferece suporte a um [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/) método e uma [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Um círculo de 360 graus é o mesmo que 2π radianos, portanto, é fácil converter entre as duas unidades. Use o que for conveniente. Todas as funções trigonométricas em estático [ `Math` ](https://developer.xamarin.com/api/type/System.Math/) classe usar unidades de radianos.

Rotação é no sentido horário para aumentar os ângulos. (Embora a rotação no sistema de coordenadas cartesianas no sentido anti-horário por convenção, Rotação horária é consistente com as coordenadas Y aumento contínuo para baixo). Negativos de ângulos e ângulos maiores que o permitido 360 graus.

As fórmulas de transformação de rotação são mais complexas do que aqueles para traduzir e escala. Para um ângulo de α, as fórmulas de transformação são:

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

O **girar básico** página demonstra o `RotateDegrees` método. O [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) arquivo exibe um texto com sua linha de base centralizada na página e gira com base em um `Slider` com um intervalo de – 360 a 360. Aqui está a parte relevante do `PaintSurface` manipulador:

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

Como rotação gira em torno do canto superior esquerdo da tela, para a maioria dos ângulos definido neste programa, o texto será girado fora da tela:

[![](rotate-images/basicrotate-small.png "Tripla captura de tela da página básica girar")](rotate-images/basicrotate-large.png#lightbox "tripla captura de tela da página girar básico")

Com muita frequência, você desejará girar algo gira em torno de um ponto de corrente especificada usando estas versões dos [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/) e [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/) métodos:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

O **centralizada girar** página é como o **girar básico** exceto que versão expandida do `RotateDegrees` é usado para definir o Centro de rotação para o mesmo ponto usado para posicionar o texto:

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

Agora o texto gira em torno do ponto usado para posicionar o texto, que é o centro da linha de base do texto horizontal:

[![](rotate-images/centeredrotate-small.png "Captura de tela da página centralizada girar tripla")](rotate-images/centeredrotate-large.png#lightbox "tripla captura de tela da página centralizada girar")

Assim como ocorre com a versão centralizada a `Scale` método, a versão centralizada do `RotateDegrees` chamada é um atalho:

```csharp
RotateDegrees (degrees, px, py);
```

Isso é equivalente ao seguinte:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Você descobrirá que, às vezes, você pode combinar `Translate` chamadas com `Rotate` chamadas. Por exemplo, aqui estão as `RotateDegrees` e `DrawText` chamadas no **centralizada girar** página;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

O `RotateDegrees` chamada é equivalente a dois `Translate` chamadas e não centralizado `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

O `DrawText` chamada para exibir texto em uma localização específica é equivalente a uma `Translate` chamar para esse local, seguido por `DrawText` no ponto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Os dois consecutivos `Translate` chamadas cancelam entre si:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceitualmente, as duas transformações são aplicadas na ordem em vez de como eles aparecem no código. O `DrawText` chamada exibe o texto no canto superior esquerdo da tela. O `RotateDegrees` chamada gira esse texto em relação ao canto superior esquerdo. Em seguida, o `Translate` chamada move o texto para o centro da tela.

Geralmente, há várias maneiras para combinar rotação e conversão. O **texto girado** página cria a exibição a seguir:

[![](rotate-images/rotatedtext-small.png "Captura de tela da página de texto girado tripla")](rotate-images/rotatedtext-large.png#lightbox "tripla captura de tela da página de texto girado")

Aqui está o `PaintSurface` manipulador do [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe:

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

O `xCenter` e `yCenter` valores indicam o centro da tela. O `yText` valor é um pouco de deslocamento do. Isso indica que a coordenada Y necessária para posicionar o texto para que ele seja centralizado realmente verticalmente na página. O `for` loop define uma rotação centralizada no centro da tela. A rotação é em incrementos de 30 graus. O texto é desenhado usando o `yText` valor. O número de espaços em branco antes da palavra "GIRA" o `text` valor foi determinado empiricamente para fazer a conexão entre essas cadeias de caracteres de 12 texto parece ser um dodecagon.

Uma maneira para simplificar esse código é usado para incrementar o ângulo de rotação 30 graus cada vez pelo loop após o `DrawText` chamar. Isso elimina a necessidade de chamadas para `Save` e `Restore`. Observe que o `degrees` variável não é mais usada dentro do corpo do `for` bloco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

Também é possível usar a forma simple de `RotateDegrees` precedida o loop com uma chamada para `Translate` mover tudo para o centro da tela:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

A modificação `yText` cálculo não incorpora `yCenter`. Agora o `DrawText` chamada centraliza o texto verticalmente na parte superior da tela.

Como as transformações são aplicadas conceitualmente vez como aparecem no código, geralmente é possível transformações comece com mais globais, seguidas de transformações locais mais. Isso geralmente é a maneira mais fácil para combinar rotação e conversão.

Por exemplo, suponha que você deseja desenhar um objeto gráfico gira em torno de seu centro de forma bastante parecida com um planeta girando em seu eixo. Mas você também deseja esse objeto giram em torno do centro da tela semelhante a um planeta que são exibidas ao redor do sol.

Você pode fazer isso posicionando o objeto no canto superior esquerdo da tela e, em seguida, usando uma animação para gira em torno desse canto. Em seguida, converte o objeto horizontal como um raio de orbital. Agora, aplique uma segundo rotação animada, também em torno de origem. Isso faz com que o objeto giram em torno do canto. Traduza agora para o centro da tela.

Aqui está o `PaintSurface` manipulador que contém essas chamadas de transformação na ordem inversa:

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

O `revolveDegrees` e `rotateDegrees` campos são animados. Este programa usa uma técnica de animação diferentes com base no xamarin. Forms `Animation` classe. (Essa classe é descrita em [capítulo 22 de *criação de aplicativos móveis com o xamarin. Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) o `OnAppearing` substituição cria dois `Animation` objetos com métodos de retorno de chamada e, em seguida, chama `Commit` -los para uma duração da animação:

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

A primeira `Animation` objeto anima `revolveDegrees` de 0 a 360 graus mais de 10 segundos. Segunda anima `rotateDegrees` de 0 a 360 graus cada 1 segundo e também invalida a superfície para gerar outra chamada para o `PaintSurface` manipulador. O `OnDisappearing` substituição cancela essas dois animações:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

O **feio relógio analógico** programa (assim chamado porque um relógio analógico mais atraente será descrito no artigo posterior) usa rotação para desenhar as marcas de minuto e a hora do relógio e girar os ponteiros. O programa desenha o relógio usando um sistema de coordenadas arbitrário com base em um círculo que é centralizado no ponto (0, 0) com um raio de 100. Ele usa a conversão e a escala para expandir e centralizar que círculo na página.

O `Translate` e `Scale` chamadas se aplicam globalmente ao relógio, portanto esses são os primeiro a ser chamado após a inicialização do `SKPaint` objetos:

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

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

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

Por fim, o `PaintSurface` manipulador obtém a hora atual e calcula os graus de rotação para a hora, minuto e segundo mãos. Cada ponteiro é desenhado na posição 12:00, para que o ângulo de rotação é relativo que:

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

O relógio é certamente funcional, embora os ponteiros são bastante crua:

[![](rotate-images/uglyanalogclock-small.png "Captura de tela da página de texto de relógio analógico feio tripla")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
