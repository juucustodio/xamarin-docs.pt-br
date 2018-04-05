---
title: A transformação de conversão
description: Saiba como usar a transformação de conversão a ser deslocado SkiaSharp gráficos
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 98bf81df3eed951893c6bb717d933cfb61e029d3
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="the-translate-transform"></a>A transformação de conversão

_Saiba como usar a transformação de conversão a ser deslocado SkiaSharp gráficos_

É o tipo mais simples de transformação em SkiaSharp o *traduzir* ou *tradução* de transformação. Essa transformação desloca objetos gráficos nas direções horizontais e verticais. De certa forma, a conversão é a transformação mais desnecessária porque você geralmente pode conseguir o mesmo efeito simplesmente alterando as coordenadas que você está usando na função de desenho. Durante a renderização de um caminho, no entanto, todas as coordenadas são encapsuladas no caminho, portanto, é muito mais fácil aplicar uma transformação de conversão para deslocar o caminho completo.

Tradução também é útil para animação e efeitos de texto simples:

![](translate-images/translateexample.png "Sombra de texto, baixo-relevo e em alto-relevo com conversão")

O [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) método `SKCanvas` tem dois parâmetros que causam objetos gráficos subsequentemente desenhado deslocamento horizontal e verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Esses argumentos podem ser negativos. Um segundo [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/SkiaSharp.SKPoint/) método combina os valores de duas tradução em um único `SKPoint` valor:

```csharp
public void Translate (SKPoint point)
```

O **acumulados traduzir** página do [ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa de exemplo demonstra que várias chamadas do `Translate` método são cumulativas. O [ `AccumulatedTranslate` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe exibe 20 versões do mesmo retângulo, cada um deslocamento do retângulo anterior apenas o suficiente para que eles stretch ao longo da diagonal. Aqui está o `PaintSurface` manipulador de eventos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

Os retângulos sucessivos fluem para baixo na página:

[![](translate-images/accumulatedtranslate-small.png "Tripla captura de tela da página acumulados traduzir")](translate-images/accumulatedtranslate-large.png#lightbox "tripla captura de tela da página acumulados traduzir")

Se os fatores de conversão acumulado são `dx` e `dy`, e é o ponto especificado em uma função de desenho (`x`, `y`), em seguida, o objeto de gráfico é renderizado no ponto (`x'`, `y'`), onde:

x' = x + dx

y' = y + dy

Eles são conhecidos como o *transformação fórmulas* para tradução. Os valores padrão de `dx` e `dy` para um novo `SKCanvas` são 0.

É comum para usar a transformação de conversão para efeitos de sombra e técnicas semelhantes, como o **converter texto efeitos** demonstra da página. Aqui está a parte relevante do `PaintSurface` manipulador no [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe:

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

Em cada um dos três exemplos, `Translate` é chamado para exibir o texto para deslocá-lo do local fornecido pelo `x` e `y` variáveis. Em seguida, o texto será exibido novamente em outra cor sem efeito de conversão:

[![](translate-images/translatetexteffects-small.png "Tripla captura de tela da página Converter texto efeitos")](translate-images/translatetexteffects-large.png#lightbox "tripla captura de tela da página traduzir efeitos de texto")

Cada um dos três exemplos a seguir mostra uma maneira diferente de negar a `Translate` chamar:

O primeiro exemplo simplesmente chama `Translate` novamente, mas com valores negativos. Porque o `Translate` chamadas são cumulativas, esta sequência de chamadas simplesmente restaura a tradução total para os valores padrão de zero.

O segundo exemplo chama [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix()/). Isso faz com que todas as transformações retornar ao estado padrão.

O terceiro exemplo salva o estado do do `SKCanvas` objeto com uma chamada para [ `Save` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Save()/) e, em seguida, restaura o estado com uma chamada para [ `Restore` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Restore/). Essa é a maneira mais versátil para manipular as transformações para uma série de operações de desenho. Essas `Save` e `Restore` função como uma pilha de chamadas: você pode chamar `Save` vários tempo e, em seguida, chame `Restore` em reverte a sequência para retornar ao estado anterior. O `Save` método retorna um inteiro, e você pode passar esse inteiro para [ `RestoreToCount` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RestoreToCount/) efetivamente chamar `Restore` várias vezes. O [ `SaveCount` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.SaveCount/) propriedade retorna o número de estados salvos na pilha.

No entanto, você não precisa se preocupar sobre transformações mantendo de uma chamada do `PaintSurface` manipulador para a próxima. Cada nova chamada `PaintSurface` oferece uma nova `SKCanvas` objeto com transformações padrão.

Outro uso comum de `Translate` transformação é para um objeto visual de renderização que foi originalmente criado usando coordenadas são convenientes para o desenho. Por exemplo, você talvez queira especificar as coordenadas de um relógio analógico com um center no ponto (0, 0). Em seguida, você pode usar transformações para exibi-lo onde você deseja. Isso é demonstrado na [**Hendecagram matriz**] página. O [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) classe começa criando um `SKPath` objeto para uma estrela apontada 11. O `HendecagramPath` objeto é definido como público, estático e somente leitura para que ele possa ser acessado de outros programas de demonstração. Ele é criado em um construtor estático:

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Se o centro da estrela é o ponto (0, 0), todos os pontos da estrela estão em um círculo ao redor desse ponto. Cada ponto é uma combinação de valores de seno e o cosseno de um ângulo que aumenta em 5/11ths de 360 graus. (Também é possível criar uma estrela apontada 11 aumentando o ângulo por 2/11, 11ths/3 ou 4/11 do círculo.) O raio do que círculo é definido como 100.

Se esse caminho será renderizado sem quaisquer transformações, o centro será posicionado no canto superior esquerdo do `SKCanvas`e apenas um trimestre dele ficarão visíveis. O `PaintSurface` manipulador de `HendecagramPage` usa `Translate` para cobrir a tela com várias cópias da estrela, cada um deles aleatoriamente colorido:

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Aqui está o resultado:

[![](translate-images/hendecagramarray-small.png "Captura de tela da página Hendecagram matriz tripla")](translate-images/hendecagramarray-large.png#lightbox "tripla captura de tela da página Hendecagram matriz")

Animações frequentemente envolvem transformações. O **Hendecagram animação** página move a estrela apontada 11 em torno de um círculo. O [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe começa com alguns campos e substituições do `OnAppearing` e `OnDisappearing` métodos para iniciar e parar um temporizador xamarin. Forms:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

O `angle` campo é animado de 0 a 360 graus 5 segundos. O `PaintSurface` manipulador utiliza o `angle` propriedade de duas maneiras: para especificar o matiz da cor no `SKColor.FromHsl` método e como um argumento para o `Math.Sin` e `Math.Cos` métodos para determinar o local da estrela:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

O `PaintSurface` chamadas do manipulador de `Translate` método duas vezes, primeiro para converter para o centro da tela e, em seguida, converter a circunferência de um círculo gira em torno (0, 0). O raio do círculo é definido para ser tão grandes quanto possível, mantendo ainda a estrela dentro dos limites da página:

[![](translate-images/hendecagramanimation-small.png "Captura de tela da página Hendecagram animação tripla")](translate-images/hendecagramanimation-large.png#lightbox "tripla captura de tela da página Hendecagram animação")

Observe que a estrela mantém a mesma orientação à medida que ele gira em torno do centro da página. Ele não gira. Que é um trabalho para uma transformação de giro.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
