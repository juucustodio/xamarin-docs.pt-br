---
title: A transformação de conversão
description: Este artigo examina como usar a transformação de conversão para deslocar elementos gráficos de SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f1efd7610b32e6a3903d34fc2f8b5a6e20c9da8a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291550"
---
# <a name="the-translate-transform"></a>A transformação de conversão

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba como usar a transformação converter para deslocar gráficos SkiaSharp_

O tipo mais simples de Transform em SkiaSharp é a transformação *translate* ou *translation* . Essa transformação passa objetos gráficos nas direções horizontais e verticais. De certa forma, a tradução é a transformação mais desnecessária porque você geralmente pode conseguir o mesmo efeito, simplesmente alterando as coordenadas que você está usando na função de desenho. Durante a renderização de um caminho, no entanto, todas as coordenadas são encapsuladas no caminho, portanto, é muito mais fácil aplicar uma transformação de translação para mudar o todo o caminho.

Tradução também é útil para animação e efeitos de texto simples:

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

O método [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) no `SKCanvas` tem dois parâmetros que fazem com que objetos gráficos desenhados subsequentemente sejam deslocados horizontal e verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Esses argumentos podem ser negativos. Um segundo método [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) combina os dois valores de tradução em um único valor de `SKPoint`:

```csharp
public void Translate (SKPoint point)
```

A página de **tradução acumulada** do programa de exemplo [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) demonstra que várias chamadas do método `Translate` são cumulativas. A classe [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) exibe 20 versões do mesmo retângulo, cada um dos deslocamentos do retângulo anterior apenas o suficiente para que eles se Alonguem ao longo da diagonal. Aqui está o manipulador de eventos `PaintSurface`:

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

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Se os fatores de conversão acumulados forem `dx` e `dy`, e o ponto especificado em uma função de desenho for (`x`, `y`), o objeto gráfico será renderizado no ponto (`x'`, `y'`), em que:

x' = x + dx

y' = y + dy

Elas são conhecidas como *fórmulas de transformação* para tradução. Os valores padrão de `dx` e `dy` para uma nova `SKCanvas` são 0.

É comum usar a transformação converter para efeitos de sombra e técnicas semelhantes, como demonstra a página **converter efeitos de texto** . Aqui está a parte relevante do manipulador de `PaintSurface` na classe [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) :

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

Em cada um dos três exemplos, `Translate` é chamado para exibir o texto para deslocá-lo do local fornecido pelas variáveis `x` e `y`. Em seguida, o texto é exibido novamente em outra cor sem efeito de tradução:

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

Cada um dos três exemplos mostra uma maneira diferente de negar a chamada de `Translate`:

O primeiro exemplo simplesmente chama `Translate` novamente, mas com valores negativos. Como as chamadas de `Translate` são cumulativas, essa sequência de chamadas simplesmente restaura a conversão total para os valores padrão de zero.

O segundo exemplo chama [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix). Isso faz com que todas as transformações retornar ao seu estado padrão.

O terceiro exemplo salva o estado do objeto `SKCanvas` com uma chamada para [`Save`](xref:SkiaSharp.SKCanvas.Save) e, em seguida, restaura o estado com uma chamada para [`Restore`](xref:SkiaSharp.SKCanvas.Restore). Essa é a maneira mais versátil para manipular as transformações para uma série de operações de desenho. Essas chamadas `Save` e `Restore` funcionam como uma pilha: você pode chamar `Save` várias vezes e, em seguida, chamar `Restore` na sequência inversa para retornar aos Estados anteriores. O método `Save` retorna um inteiro e você pode passar esse inteiro para [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) chamar efetivamente `Restore` várias vezes. A propriedade [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) retorna o número de Estados atualmente salvos na pilha.

Você também pode usar a classe [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) para restaurar o estado da tela. O Construtor desta classe deve ser chamado em uma instrução `using`; o estado da tela é restaurado automaticamente no final do bloco de `using`.

No entanto, você não precisa se preocupar com as transformações que são transportadas de uma chamada do manipulador de `PaintSurface` para a próxima. Cada nova chamada para `PaintSurface` fornece um objeto de `SKCanvas` atualizado com transformações padrão.

Outro uso comum da transformação `Translate` é para a renderização de um objeto visual que foi criado originalmente usando coordenadas que são convenientes para desenhar. Por exemplo, você talvez queira especificar as coordenadas de um relógio analógico com uma central no ponto (0, 0). Em seguida, você pode usar transformações para exibir o relógio em que você deseja. Essa técnica é demonstrada na página [**matriz Hendecagram**]. A classe [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) começa criando um objeto `SKPath` para uma estrela de 11 pontas. O objeto `HendecagramPath` é definido como público, estático e somente leitura para que possa ser acessado de outros programas de demonstração. Ele é criado em um construtor estático:

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

Se o centro da estrela é o ponto (0, 0), todos os pontos da estrela estão em um círculo em torno desse ponto. Cada ponto é uma combinação de valores do seno e cosseno de um ângulo que aumenta em 5/11ths de 360 graus. (Também é possível criar uma estrela de 11 pontas aumentando o ângulo de 2/11, 3/11ths ou 4/11 do círculo.) O raio desse círculo é definido como 100.

Se esse caminho for renderizado sem nenhuma transformação, o centro será posicionado no canto superior esquerdo do `SKCanvas`, e apenas um trimestre será visível. O manipulador de `PaintSurface` de `HendecagramPage`, em vez disso, usa `Translate` para dividir a tela com várias cópias da estrela, cada uma colorida aleatoriamente:

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

Eis o resultado:

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

Animações frequentemente envolvem transformações. A página de **animação Hendecagram** move a estrela de 11 pontas em um círculo. A classe [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) começa com alguns campos e substituições dos métodos `OnAppearing` e `OnDisappearing` para iniciar e parar um temporizador Xamarin. Forms:

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

O campo `angle` é animado de 0 graus a 360 graus a cada 5 segundos. O manipulador de `PaintSurface` usa a propriedade `angle` de duas maneiras: para especificar o matiz da cor no método `SKColor.FromHsl` e como um argumento para os métodos `Math.Sin` e `Math.Cos` para controlar o local da estrela:

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

O manipulador de `PaintSurface` chama o método de `Translate` duas vezes, primeiro para traduzir para o centro da tela e, em seguida, para traduzir para a circunferência de um círculo centralizado (0, 0). O raio do círculo é definido para ser tão grandes quanto possível enquanto ainda mantém a estrela dentro dos limites da página:

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Observe que a estrela mantém a mesma orientação à medida que ele gira em torno do centro da página. Ele não gira. Esse é um trabalho para uma transformação de giro.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
