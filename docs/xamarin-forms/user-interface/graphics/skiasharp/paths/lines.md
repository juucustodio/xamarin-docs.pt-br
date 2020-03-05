---
title: Limites de linha e de traço
description: Este artigo explica como usar SkiaSharp para desenhar linhas com extremidades diferentes do traço em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9aaecb8c63ff28111097dce81954f523b4c7731b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291805"
---
# <a name="lines-and-stroke-caps"></a>Limites de linha e de traço

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba como usar o SkiaSharp para desenhar linhas com limites de traço diferentes_

SkiaSharp, renderização de uma única linha é muito diferente da renderização de uma série de linhas retas conectadas. Até mesmo ao desenhar linhas individuais, no entanto, ele geralmente é necessário dar as linhas de uma largura de traço específica. Como essas linhas se tornam mais ampla, a aparência das extremidades das linhas também se torna importante. A aparência do final da linha é chamada de ponta do *traço*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Para desenhar linhas únicas, `SKCanvas` define um método [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) simples cujos argumentos indicam as coordenadas inicial e final da linha com um objeto `SKPaint`:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Por padrão, a propriedade [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) de um objeto de `SKPaint` instanciado recentemente é 0, que tem o mesmo efeito que um valor de 1 na renderização de uma linha de um pixel em espessura. Isso aparece muito fino em dispositivos de alta resolução, como telefones, portanto, você provavelmente desejará definir o `StrokeWidth` para um valor maior. Mas depois de começar a desenhar linhas de uma espessura dimensionável, que gera outra questão: como deve o começa e termina dessas linhas espessa ser renderizada?

A aparência de começa e termina em linhas é chamada de uma *extremidade de linha* ou, em skia, uma *extremidade de traço*. A palavra "Cap" neste contexto refere-se a um tipo de chapéu &mdash; algo que fica no final da linha. Você define a propriedade [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) do objeto `SKPaint` como um dos seguintes membros da enumeração [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) :

- `Butt` (o padrão)
- `Square`
- `Round`

Eles são mais bem ilustrados com um programa de exemplo. A seção **linhas e caminhos SkiaSharp** do programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) começa com uma página chamada **Stroke Caps** com base na classe [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) . Esta página define um manipulador de eventos `PaintSurface` que percorre os três membros da enumeração `SKStrokeCap`, exibindo o nome do membro de enumeração e desenhando uma linha usando essa extremidade de traço:

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

Para cada membro da enumeração `SKStrokeCap`, o manipulador desenha duas linhas, uma com uma espessura de traço de 50 pixels e outra linha posicionada na parte superior com uma espessura de traço de dois pixels. Essa segunda linha é destinada a ilustrar o Geométrico início e fim da linha independente de espessura da linha e um limite do traço:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Como você pode ver, o `Square` e `Round` Stroke-Caps efetivamente estendem o comprimento da linha por metade da largura do traço no início da linha e novamente no final. Essa extensão será importante quando é necessário determinar as dimensões de um objeto gráfico renderizado.

A classe `SKCanvas` também inclui outro método para desenhar várias linhas que é um pouco peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

O parâmetro `points` é uma matriz de valores de `SKPoint` e `mode` é um membro da enumeração [`SKPointMode`](xref:SkiaSharp.SKPointMode) , que tem três membros:

- `Points` renderizar os pontos individuais
- `Lines` para conectar cada par de pontos
- `Polygon` conectar todos os pontos consecutivos

A página **várias linhas** demonstra esse método. O arquivo [**MultipleLinesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) instancia duas exibições de `Picker` que permitem selecionar um membro da enumeração `SKPointMode` e um membro da enumeração `SKStrokeCap`:

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

Observe que as declarações de namespace SkiaSharp são um pouco diferentes porque o namespace de `SkiaSharp` é necessário para fazer referência aos membros das enumerações de `SKPointMode` e `SKStrokeCap`. O manipulador de `SelectedIndexChanged` para as duas exibições de `Picker` simplesmente invalida o objeto `SKCanvasView`:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Esse manipulador precisa verificar a existência do objeto `SKCanvasView` porque o manipulador de eventos é chamado pela primeira vez quando a propriedade `SelectedIndex` da `Picker` é definida como 0 no arquivo XAML e isso ocorre antes que o `SKCanvasView` tenha sido instanciado.

O manipulador de `PaintSurface` Obtém os dois valores de enumeração das exibições de `Picker`:

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

O iPhone à esquerda mostra como o membro de enumeração de `SKPointMode.Points` faz `DrawPoints` processar cada um dos pontos na matriz de `SKPoint` como um quadrado se a extremidade da linha for `Butt` ou `Square`. Os círculos serão renderizados se a extremidade da linha for `Round`.

A captura de tela do Android mostra o resultado da `SKPointMode.Lines`. O método `DrawPoints` desenha uma linha entre cada par de valores de `SKPoint`, usando a Cap de linha especificada, nesse caso `Round`.

Quando você usa `SKPointMode.Polygon`, uma linha é desenhada entre os pontos sucessivos na matriz, mas se você olhar muito bem, verá que essas linhas não estão conectadas. Cada uma destas linhas separadas começa e termina com o limite de linha especificado. Se você selecionar a `Round` Caps, as linhas podem parecer estar conectadas, mas elas não estão realmente conectadas.

Se linhas são ou não conectadas é um aspecto essencial de como trabalhar com caminhos de elementos gráficos.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
