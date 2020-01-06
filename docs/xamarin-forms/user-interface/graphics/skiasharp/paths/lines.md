---
title: Limites de linha e de traço
description: Este artigo explica como usar SkiaSharp para desenhar linhas com extremidades diferentes do traço em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 724a79e618321f97257718bf56dd1fdd18f73563
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545596"
---
# <a name="lines-and-stroke-caps"></a>Limites de linha e de traço

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba como usar SkiaSharp para desenhar linhas com extremidades diferentes de traço_

SkiaSharp, renderização de uma única linha é muito diferente da renderização de uma série de linhas retas conectadas. Até mesmo ao desenhar linhas individuais, no entanto, ele geralmente é necessário dar as linhas de uma largura de traço específica. Como essas linhas se tornam mais ampla, a aparência das extremidades das linhas também se torna importante. A aparência do final da linha é chamada de *limite do traço*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Para desenhar linhas únicas, `SKCanvas` define um simples [ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método cujos argumentos indicam o início e final coordenadas da linha com um `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Por padrão, o [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) propriedade de instâncias recém-criadas `SKPaint` objeto é 0, que tem o mesmo efeito que um valor de 1 na renderização de uma linha de um pixel na espessura. Isso parece muito fino em alta resolução dispositivos como telefones, portanto, você provavelmente vai querer definir o `StrokeWidth` para um valor maior. Mas depois de começar a desenhar linhas de uma espessura dimensionável, que gera outra questão: como deve o começa e termina dessas linhas espessa ser renderizada?

A aparência do começa e termina de linhas é chamada de um *limite de linha* ou, em Skia, um *limite do traço*. A palavra "cap" neste contexto refere-se a um tipo de hat &mdash; algo que se encontra no final da linha. Você definir a [ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap) propriedade do `SKPaint` objeto para um dos seguintes membros dos [ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap) enumeração:

- `Butt` (o padrão)
- `Square`
- `Round`

Eles são mais bem ilustrados com um programa de exemplo. O **caminhos e linhas de SkiaSharp** seção o [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa começa com uma página chamada **traço Caps** com base no [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) classe. Esta página define uma `PaintSurface` manipulador de eventos que percorre os três membros do `SKStrokeCap` enumeração, exibindo o nome do membro de enumeração e desenhando uma linha usando esse limite do traço:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Para cada membro do `SKStrokeCap` enumeração, o manipulador desenha duas linhas, uma com uma espessura de traço de 50 pixels e outra linha posicionado na parte superior com uma espessura de traço de dois pixels. Essa segunda linha é destinada a ilustrar o Geométrico início e fim da linha independente de espessura da linha e um limite do traço:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Como você pode ver, o `Square` e `Round` caps traço efetivamente estendem o comprimento da linha em metade da largura de traço no início da linha e novamente no final. Essa extensão será importante quando é necessário determinar as dimensões de um objeto gráfico renderizado.

O `SKCanvas` classe inclui também outro método é um pouco peculiar para desenhar várias linhas:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

O `points` parâmetro é uma matriz de `SKPoint` valores e `mode` é um membro dos [ `SKPointMode` ](xref:SkiaSharp.SKPointMode) enumeração, que tem três membros:

- `Points` para renderizar os pontos individuais
- `Lines` para se conectar a cada par de pontos
- `Polygon` para se conectar a todos os pontos consecutivos

O **várias linhas** página demonstra esse método. O [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) arquivo instancia dois `Picker` modos de exibição que permitem que você selecione um membro do `SKPointMode` enumeração e um membro do `SKStrokeCap` enumeração:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

Observe que as declarações de namespace de SkiaSharp são um pouco diferente porque o `SkiaSharp` namespace é necessária para fazer referência a membros do `SKPointMode` e `SKStrokeCap` enumerações. O `SelectedIndexChanged` manipulador para ambos `Picker` modos de exibição simplesmente invalida o `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Esse manipulador precisa verificar a existência do `SKCanvasView` objeto porque o manipulador de eventos é a primeiro chamada quando o `SelectedIndex` propriedade da `Picker` é definido como 0 no arquivo XAML, e que ocorre antes do `SKCanvasView` instância foi instanciada.

O `PaintSurface` manipulador obtém os dois valores de enumeração do `Picker` modos de exibição:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

As capturas de tela mostram uma variedade de `Picker` seleções:

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

IPhone no esquerdo mostra como o `SKPointMode.Points` faz com que o membro de enumeração `DrawPoints` para processar cada um dos pontos na `SKPoint` se o limite de linha é de matriz como um quadrado `Butt` ou `Square`. Círculos são renderizados, se o limite de linha é `Round`.

A captura de tela do Android mostra o resultado da `SKPointMode.Lines`. O método `DrawPoints` desenha uma linha entre cada par de valores de `SKPoint`, usando a Cap de linha especificada, nesse caso `Round`.

Quando você usa `SKPointMode.Polygon`, uma linha é desenhada entre os pontos sucessivos na matriz, mas se você olhar muito bem, verá que essas linhas não estão conectadas. Cada uma destas linhas separadas começa e termina com o limite de linha especificado. Se você selecionar o `Round` caps, as linhas podem aparecer para ser conectado, mas não realmente estão conectados.

Se linhas são ou não conectadas é um aspecto essencial de como trabalhar com caminhos de elementos gráficos.

## <a name="related-links"></a>Links Relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
