---
title: A transformação de conversão
description: Este artigo examina como usar a transformação de conversão para deslocar elementos gráficos de SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 2171c8f0b2926a645fb98df52bae2391449bf89c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120925"
---
# <a name="the-translate-transform"></a>A transformação de conversão

_Saiba como usar a transformação de conversão para deslocar elementos gráficos de SkiaSharp_

O tipo mais simples de transformação no SkiaSharp é o *traduzir* ou *tradução* transformar. Essa transformação passa objetos gráficos nas direções horizontais e verticais. De certa forma, a tradução é a transformação mais desnecessária porque você geralmente pode conseguir o mesmo efeito, simplesmente alterando as coordenadas que você está usando na função de desenho. Durante a renderização de um caminho, no entanto, todas as coordenadas são encapsuladas no caminho, portanto, é muito mais fácil aplicar uma transformação de translação para mudar o todo o caminho.

Tradução também é útil para animação e efeitos de texto simples:

![](translate-images/translateexample.png "Sombra de texto, baixo-relevo e alto-relevo com a conversão")

O [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método `SKCanvas` tem dois parâmetros que fazem os objetos gráficos subsequentemente desenhado deslocamento horizontal e verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Esses argumentos podem ser negativos. Um segundo [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) método combina os valores de dois tradução em um único `SKPoint` valor:

```csharp
public void Translate (SKPoint point)
```

O **acumulados traduzir** página do [ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa de exemplo demonstra que várias chamadas do `Translate` método são cumulativas. O [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe exibe 20 versões do mesmo retângulo, cada um deslocamento do retângulo anterior apenas o suficiente para que eles Alongar ao longo da diagonal. Aqui está o `PaintSurface` manipulador de eventos:

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

Os retângulos sucessivos repassadas a página:

[![](translate-images/accumulatedtranslate-small.png "Captura de tela da página da acumulados traduzir tripla")](translate-images/accumulatedtranslate-large.png#lightbox "tripla captura de tela da página da acumulados traduzir")

Se são os fatores de conversão acumulado `dx` e `dy`, e é o ponto especificado em uma função de desenho (`x`, `y`), em seguida, o objeto de gráfico é renderizado no ponto (`x'`, `y'`), onde:

x' = x + dx

y' = y + dy

Eles são conhecidos como o *transformar fórmulas* para tradução. Os valores padrão das `dx` e `dy` para um novo `SKCanvas` são 0.

É comum para usar a transformação de conversão para efeitos de sombra e técnicas semelhantes, como o **converter efeitos de texto** página demonstra. Aqui está a parte relevante do `PaintSurface` manipulador na [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe:

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

Em cada um dos três exemplos, `Translate` é chamado para exibir o texto a ser deslocado-lo do local fornecido pela `x` e `y` variáveis. Em seguida, o texto é exibido novamente em outra cor sem efeito de tradução:

[![](translate-images/translatetexteffects-small.png "Captura de tela da página Converter efeitos de texto tripla")](translate-images/translatetexteffects-large.png#lightbox "tripla captura de tela da página Converter efeitos de texto")

Cada um dos três exemplos a seguir mostra uma maneira diferente de negar a `Translate` chamar:

O primeiro exemplo simplesmente chama `Translate` novamente, mas com valores negativos. Porque o `Translate` chamadas são cumulativas, esta sequência de chamadas simplesmente restaura a tradução total para os valores padrão de zero.

O segundo exemplo chama [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix). Isso faz com que todas as transformações retornar ao seu estado padrão.

O terceiro exemplo salva o estado do `SKCanvas` objeto com uma chamada para [ `Save` ](xref:SkiaSharp.SKCanvas.Save) e, em seguida, restaura o estado com uma chamada para [ `Restore` ](xref:SkiaSharp.SKCanvas.Restore). Essa é a maneira mais versátil para manipular as transformações para uma série de operações de desenho. Esses `Save` e `Restore` função como uma pilha de chamadas: você pode chamar `Save` várias vezes e, em seguida, chame `Restore` em inversa sequência para retornar ao estado anterior. O `Save` método retorna um inteiro, e você pode passar esse inteiro para [ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*) efetivamente chamar `Restore` várias vezes. O [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount) propriedade retorna o número de estados salvos no momento na pilha.

Você também pode usar o [ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore) classe para restaurar o estado da tela. O construtor dessa classe se destina a ser chamado em um `using` instrução; a tela de estado é restaurado automaticamente no final do `using` bloco. 

No entanto, você não precisa se preocupar sobre transformações Carregando mais de uma chamada da `PaintSurface` manipulador para o próximo. Cada nova chamada para `PaintSurface` oferece uma nova `SKCanvas` objeto com transformações padrão.

Outro uso comum do `Translate` transformação é para renderizar um objeto visual que foi originalmente criado usando coordenadas que são convenientes para o desenho. Por exemplo, você talvez queira especificar as coordenadas de um relógio analógico com uma central no ponto (0, 0). Em seguida, você pode usar transformações para exibir o relógio em que você deseja. Essa técnica é demonstrada na [**Hendecagram matriz**] página. O [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) classe começa criando um `SKPath` objeto para uma estrela 11-lo. O `HendecagramPath` objeto é definido como público, estático e somente leitura para que ele possa ser acessado de outros programas de demonstração. Ele é criado em um construtor estático:

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

Se o centro da estrela é o ponto (0, 0), todos os pontos da estrela estão em um círculo em torno desse ponto. Cada ponto é uma combinação de valores do seno e cosseno de um ângulo que aumenta em 5/11ths de 360 graus. (Também é possível criar uma estrela 11-lo, aumentando o ângulo por 2/11, 11ths/3 ou 4/11º do círculo.) O raio do que o círculo é definido como 100.

Se esse caminho é renderizado sem quaisquer transformações, o centro será posicionado no canto superior esquerdo do `SKCanvas`e apenas um trimestre dele ficarão visíveis. O `PaintSurface` manipulador de `HendecagramPage` em vez disso, usa `Translate` lado a lado de canvas com várias cópias da estrela, cada uma delas aleatoriamente colorido:

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

Animações frequentemente envolvem transformações. O **Hendecagram animação** página move a estrela 11-lo em um círculo. O [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe começa com alguns campos e substituições dos `OnAppearing` e `OnDisappearing` métodos para iniciar e parar um temporizador de xamarin. Forms:

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

O `angle` campo é animado de graus de 0 a 360 graus a cada 5 segundos. O `PaintSurface` manipulador usa o `angle` propriedade de duas maneiras: para especificar o matiz da cor na `SKColor.FromHsl` método e como um argumento para o `Math.Sin` e `Math.Cos` métodos para controlar o local da estrela:

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

O `PaintSurface` chamadas do manipulador a `Translate` método duas vezes, primeiro para converter para o centro da tela e, em seguida, converter a circunferência de um círculo centralizada em torno de (0, 0). O raio do círculo é definido para ser tão grandes quanto possível enquanto ainda mantém a estrela dentro dos limites da página:

[![](translate-images/hendecagramanimation-small.png "Captura de tela da página Hendecagram animação tripla")](translate-images/hendecagramanimation-large.png#lightbox "tripla captura de tela da página Hendecagram animação")

Observe que a estrela mantém a mesma orientação à medida que ele gira em torno do centro da página. Ele não gira. Esse é um trabalho para uma transformação de giro.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
