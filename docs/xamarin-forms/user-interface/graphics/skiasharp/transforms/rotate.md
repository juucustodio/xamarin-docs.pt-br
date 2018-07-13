---
title: A transformação de rotação
description: Este artigo explora os efeitos e animações possíveis com a transformação de rotação de SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: cbb34fb4887fc3fa086fa9912d25addebd9b13f2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994994"
---
# <a name="the-rotate-transform"></a>A transformação de rotação

_Explore os efeitos e animações possíveis com a transformação de rotação de SkiaSharp_

Com a transformação de rotação, objetos de gráficos de SkiaSharp fique livres da restrição de alinhamento com os eixos horizontais e verticais:

![](rotate-images/rotateexample.png "Texto girado em torno de um centro")

Para girar um objeto gráfico em torno do ponto (0, 0), SkiaSharp dá suporte a um [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/) método e uma [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/) método:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Um círculo de 360 graus é o mesmo que 2π radianos, portanto, é fácil converter entre as duas unidades. Use o que for conveniente. Todas as funções trigonométricas em estático [ `Math` ](xref:System.Math) classe usar unidades de radianos.

Rotação é no sentido horário para aumentar os ângulos. (Embora a rotação sobre o sistema de coordenadas cartesianas seja no sentido anti-horário por convenção, rotação no sentido horário é consistente com coordenadas Y aumentando vai para baixo). Negativo de ângulos e ângulos maiores que o permitido 360 graus.

As fórmulas de transformação de rotação são mais complexas do que aqueles para traduzir e escala. Para um ângulo de α, as fórmulas de transformação são:

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

O **girar básica** página demonstra o `RotateDegrees` método. O [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) arquivo exibe um texto com sua linha de base centralizada na página e o gira com base em um `Slider` com um intervalo de – 360 a 360. Aqui está a parte relevante do `PaintSurface` manipulador:

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

Porque a rotação é centralizada em torno do canto superior esquerdo da tela, para a maioria dos ângulos definido neste programa, o texto é girado fora da tela:

[![](rotate-images/basicrotate-small.png "Captura de tela da página básica girar tripla")](rotate-images/basicrotate-large.png#lightbox "tripla captura de tela da página girar básico")

Com muita frequência você desejará girar algo centralizadas em torno de um ponto de dinâmico especificado usando estas versões dos [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/) e [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/) métodos:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

O **centralizado girar** página é como o **girar básica** exceto que a versão expandida do `RotateDegrees` é usado para definir o Centro de rotação para o mesmo ponto usado para posicionar o texto:

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

Agora o texto gira em torno do ponto usado para posicionar o texto, que é o centro horizontal da linha de base do texto:

[![](rotate-images/centeredrotate-small.png "Captura de tela da página centralizada girar tripla")](rotate-images/centeredrotate-large.png#lightbox "tripla captura de tela da página centralizada girar")

Assim como ocorre com a versão centralizada da `Scale` método, a versão centralizada do `RotateDegrees` chamada é um atalho:

```csharp
RotateDegrees (degrees, px, py);
```

Isso é equivalente ao seguinte:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Você descobrirá que, às vezes, você pode combinar `Translate` chamadas com `Rotate` chamadas. Por exemplo, aqui estão os `RotateDegrees` e `DrawText` chamadas na **centralizado girar** página;

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

O `DrawText` chamadas para exibir o texto em um local específico é equivalente a um `Translate` chamar para esse local, seguido por `DrawText` no ponto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Os dois consecutivos `Translate` chamadas cancelam um ao outro:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceitualmente, as duas transformações são aplicadas na ordem oposta à como eles aparecem no código. O `DrawText` chamada exibe o texto no canto superior esquerdo da tela. O `RotateDegrees` chamada gira esse texto em relação ao canto superior esquerdo. Em seguida, a `Translate` chamada move o texto para o centro da tela.

Normalmente, há várias maneiras para combinar a rotação e translação. O **texto girado** página cria a exibição a seguir:

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

O `xCenter` e `yCenter` valores indicam o centro da tela. O `yText` valor é deslocada um pouco do que. Isso indica a coordenada Y necessária para posicionar o texto para que ele realmente verticalmente está centralizado na página. O `for` loop, em seguida, define uma rotação centrada no centro da tela. A rotação é em incrementos de 30 graus. O texto é desenhado usando o `yText` valor. O número de espaços em branco antes da palavra "GIRAR" no `text` valor foi determinado empiricamente para fazer a conexão entre essas cadeias de caracteres de 12 texto parece ser um dodecagon.

Uma maneira de simplificar esse código é incrementar o ângulo de rotação em 30 graus a cada vez pelo loop após o `DrawText` chamar. Isso elimina a necessidade de chamadas para `Save` e `Restore`. Observe que o `degrees` variável não é mais usada dentro do corpo do `for` bloco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

Também é possível usar a forma simple de `RotateDegrees` colocando o loop com uma chamada para `Translate` deseja mover tudo para o centro da tela:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Modificado `yText` cálculo não incorpora `yCenter`. Agora o `DrawText` chamada centraliza o texto verticalmente na parte superior da tela.

Porque, conceitualmente, as transformações são aplicadas ao como aparecem no código, geralmente é possíveis transformações para começar é mais globais, seguidas por mais de transformações de locais. Isso geralmente é a maneira mais fácil para combinar a rotação e translação.

Por exemplo, suponha que você deseja desenhar um objeto gráfico que gira em torno de seu centro muito parecido com um planeta girando em seu eixo. Mas você também quiser esse objeto giram em torno do centro da tela muito parecido com um planeta revolução ao redor do sol.

Você pode fazer isso, posicionando o objeto no canto superior esquerdo da tela e, em seguida, usando uma animação para girá-lo em torno desse canto. Em seguida, converter o objeto horizontalmente como um raio orbital. Agora, aplique uma segunda rotação animada, também em torno da origem. Isso torna o objeto giram em torno do canto. Converter agora para o centro da tela.

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

O `revolveDegrees` e `rotateDegrees` campos são animados. Esse programa usa uma técnica de animação diferentes com base no xamarin. Forms `Animation` classe. (Essa classe é descrita em [capítulo 22 do *criação de aplicativos móveis com xamarin. Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) a `OnAppearing` substituição cria dois `Animation` objetos com métodos de retorno de chamada e, em seguida, chama `Commit` neles para uma duração da animação:

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

A primeira `Animation` objeto é animado `revolveDegrees` de 0 a 360 graus de mais de 10 segundos. O segundo é anima `rotateDegrees` de 0 a 360 graus cada 1 segundo e também invalida a superfície para gerar outra chamada para o `PaintSurface` manipulador. O `OnDisappearing` substituição cancela essas duas animações:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

O **feio relógio analógico** programa (portanto, é chamado porque um relógio analógico mais atrativo será descrito em um artigo posterior) usa a rotação para desenhar as marcas de minuto e a hora do relógio e girar mãos. O programa desenha o relógio usando um sistema de coordenadas arbitrário com base em um círculo que é centralizado no ponto (0, 0) com um raio de 100. Ele usa a translação e dimensionamento para expandir e centralizar desse círculo na página.

O `Translate` e `Scale` chamadas se aplicam globalmente a hora, portanto, esses são os primeiros a ser chamado após a inicialização do `SKPaint` objetos:

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

Por fim, o `PaintSurface` manipulador obtém a hora atual e calcula os graus de rotação para a hora, minuto e segundo mãos. Cada mão é desenhado na posição 12:00, para que o ângulo de rotação é relativo ao que:

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
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
