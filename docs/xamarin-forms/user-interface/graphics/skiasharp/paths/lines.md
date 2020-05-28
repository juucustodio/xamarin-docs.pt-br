---
title: ''
description: Este artigo explica como usar o SkiaSharp para desenhar linhas com limites de traço diferentes em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87b97ad913e08c42d16bbf055f168c07b9bd60e8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137196"
---
# <a name="lines-and-stroke-caps"></a>Limites de linha e de traço

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba como usar o SkiaSharp para desenhar linhas com limites de traço diferentes_

No SkiaSharp, a renderização de uma única linha é muito diferente da renderização de uma série de linhas retas conectadas. No entanto, mesmo ao desenhar linhas únicas, muitas vezes é necessário dar uma largura de traço específica a linhas. Como essas linhas se tornam mais largas, a aparência das extremidades das linhas também se torna importante. A aparência do final da linha é chamada de ponta do *traço*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Para desenhar linhas únicas, `SKCanvas` define um [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método simples cujos argumentos indicam as coordenadas inicial e final da linha com um `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Por padrão, a [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propriedade de um objeto instanciado recentemente `SKPaint` é 0, que tem o mesmo efeito que um valor de 1 na renderização de uma linha de um pixel em espessura. Isso aparece muito fino em dispositivos de alta resolução, como telefones, portanto, você provavelmente desejará definir o `StrokeWidth` para um valor maior. Mas depois de começar a desenhar linhas de uma espessura ajustável, isso gera outro problema: como deve ser renderizado o início e término dessas linhas espessas?

A aparência de começa e termina em linhas é chamada de uma *extremidade de linha* ou, em skia, uma *extremidade de traço*. A palavra "Cap" neste contexto refere-se a um tipo de chapéu &mdash; algo que fica no final da linha. Você define a [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) Propriedade do `SKPaint` objeto como um dos membros da enumeração a seguir [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) :

- `Butt`(o padrão)
- `Square`
- `Round`

Elas são mais bem ilustradas com um programa de exemplo. A seção **linhas e caminhos SkiaSharp** do programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) começa com uma página chamada **Stroke Caps** com base na [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) classe. Esta página define um `PaintSurface` manipulador de eventos que percorre os três membros da `SKStrokeCap` enumeração, exibindo o nome do membro de enumeração e desenhando uma linha usando essa extremidade de traço:

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

Para cada membro da `SKStrokeCap` enumeração, o manipulador desenha duas linhas, uma com uma espessura de traço de 50 pixels e outra linha posicionada na parte superior com uma espessura de traço de dois pixels. Essa segunda linha destina-se a ilustrar o início e o término geométricos da linha, independentemente da espessura da linha e da ponta do traço:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Como você pode ver, as `Square` `Round` arremates de e de traço efetivamente estendem o comprimento da linha por metade da largura do traço no início da linha e novamente no final. Essa extensão se torna importante quando é necessário determinar as dimensões de um objeto gráfico renderizado.

A `SKCanvas` classe também inclui outro método para desenhar várias linhas que é um pouco peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

O `points` parâmetro é uma matriz de `SKPoint` valores e `mode` é um membro da [`SKPointMode`](xref:SkiaSharp.SKPointMode) enumeração, que tem três membros:

- `Points`para renderizar os pontos individuais
- `Lines`para conectar cada par de pontos
- `Polygon`para conectar todos os pontos consecutivos

A página **várias linhas** demonstra esse método. O arquivo [**MultipleLinesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) instancia duas `Picker` exibições que permitem selecionar um membro da `SKPointMode` enumeração e um membro da `SKStrokeCap` enumeração:

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

Observe que as declarações de namespace SkiaSharp são um pouco diferentes porque o `SkiaSharp` namespace é necessário para fazer referência aos membros `SKPointMode` das `SKStrokeCap` enumerações e. O `SelectedIndexChanged` manipulador para ambas as `Picker` exibições simplesmente invalida o `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Esse manipulador precisa verificar a existência do `SKCanvasView` objeto porque o manipulador de eventos é chamado primeiro quando a `SelectedIndex` propriedade de `Picker` está definida como 0 no arquivo XAML e isso ocorre antes que o `SKCanvasView` tenha sido instanciado.

O `PaintSurface` manipulador obtém os dois valores de enumeração dos `Picker` modos de exibição:

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

O iPhone à esquerda mostra como o `SKPointMode.Points` membro de enumeração faz com que o `DrawPoints` processe cada um dos pontos na `SKPoint` matriz como um quadrado se a extremidade da linha for `Butt` ou `Square` . Os círculos serão renderizados se a extremidade da linha for `Round` .

A captura de tela do Android mostra o resultado do `SKPointMode.Lines` . O `DrawPoints` método desenha uma linha entre cada par de `SKPoint` valores, usando a Cap de linha especificada, nesse caso `Round` .

Quando você usa `SKPointMode.Polygon` , uma linha é desenhada entre os pontos sucessivos na matriz, mas se você olhar muito bem, verá que essas linhas não estão conectadas. Cada uma dessas linhas separadas começa e termina com a extremidade de linha especificada. Se você selecionar as `Round` letras em maiúsculas, as linhas podem parecer estar conectadas, mas elas não estão realmente conectadas.

Se as linhas estão conectadas ou não, o aspecto crucial do trabalho com caminhos gráficos.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
