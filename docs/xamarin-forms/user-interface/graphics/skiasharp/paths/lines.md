---
title: "Linhas e limites de traço"
description: "Saiba como usar SkiaSharp para desenhar linhas com extremidades diferentes de traço"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9a3090873569db2466db9ab25cc105ea59401df3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="lines-and-stroke-caps"></a>Linhas e limites de traço

_Saiba como usar SkiaSharp para desenhar linhas com extremidades diferentes de traço_

SkiaSharp, renderização de uma única linha é muito diferente da renderização de uma série de linhas retas conectadas. Mesmo ao desenhar linhas simples, no entanto, geralmente é necessário dar as linhas de uma largura específica traço e quanto maior a linha, mais importante se torna a aparência do final de linhas, chamados de *cap traço*:

![](lines-images/strokecapsexample.png "As opções de caps três traço")

Para desenhar linhas únicas, `SKCanvas` define um simples [ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) método cujos argumentos indicam o início e término de coordenadas de linha com um `SKPaint` objeto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Por padrão, o `StrokeWidth` propriedade de instâncias recém-criadas `SKPaint` objeto é 0, que tem o mesmo efeito que um valor de 1 na renderização de uma linha de um pixel em espessura. Isso parece muito fino em dispositivos de alta resolução, como telefones, portanto você provavelmente desejará definir o `StrokeWidth` com um valor maior. Mas quando você começa a desenhar linhas de uma espessura dimensionável, que gera a outro problema: como deve o inicia e termina dessas linhas espesso renderizada?

A aparência do inicia e termina de linhas é chamada um *terminação de linha* ou, em Skia, uma *cap traço*. A palavra "cap" neste contexto se refere a um tipo de hat & #x 2014; algo que se encontra no final da linha. Definir o [ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/) propriedade do `SKPaint` objeto para um dos seguintes membros o [ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/) enumeração:

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) (o padrão)
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

Esses são ilustrados melhor com um programa de exemplo. A segunda seção da página inicial do [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa começa com uma página chamada **traço Caps** com base no [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) classe. Essa página define uma `PaintSurface` manipulador de eventos que percorre três membros do `SKStrokeCap` enumeração, exibindo o nome do membro de enumeração e desenhar uma linha usando esse limite de traço:

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

Para cada membro do `SKStrokeCap` enumeração, o manipulador desenha duas linhas, uma com uma espessura do traço de 50 pixels e outra linha posicionado na parte superior com uma espessura do traço de 2 pixels. Essa segunda linha destina-se a ilustrar o Geométrico início e fim da linha independente da espessura da linha e um limite de traço:

[![](lines-images/strokecaps-small.png "Tripla captura de tela da página traço Caps")](lines-images/strokecaps-large.png "tripla captura de tela da página tampas de traço")

Como você pode ver, o `Square` e `Round` caps traço efetivamente estendem o comprimento da linha, metade da largura do traço no início da linha e novamente no final. Essa extensão será importante quando é necessário determinar as dimensões de um objeto gráfico renderizado.

O `SKCanvas` classe também inclui outro método para desenhar várias linhas que é um pouco peculiar:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

O `points` parâmetro é uma matriz de `SKPoint` valores e `mode` é um membro do [ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/) enumeração, que tem três membros:

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) para processar os pontos individuais
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) para se conectar a cada par de pontos
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) para se conectar a todos os pontos consecutivos

O **várias linhas** página demonstra esse método. O [ `MultipleLinesPage` arquivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) cria dois `Picker` exibições que permitem que você selecione um membro do `SKPointMode` enumeração e um membro do `SKStrokeCap` enumeração:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
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
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

O `SelectedIndexChanged` manipulador para ambos `Picker` exibições simplesmente invalida o `SKCanvasView` objeto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Este manipulador precisa verificar a existência da `SKCanvasView` objeto porque o manipulador de eventos é primeiro chamado quando o `SelectedIndex` propriedade do `Picker` é definido como 0 no arquivo XAML, e que ocorre antes do `SKCanvasView` instanciado.

O `PaintSurface` manipulador acessa um método genérico para obtenção de dois itens selecionados do `Picker` modos de exibição e convertê-los para valores de enumeração:

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
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

A captura de tela mostra uma variedade de `Picker` seleções nas três plataformas:

[![](lines-images/multiplelines-small.png "Captura de tela da página de várias linhas tripla")](lines-images/multiplelines-large.png "tripla captura de tela da página de várias linhas")

O iPhone na esquerda mostra como o `SKPointMode.Points` faz com que o membro de enumeração `DrawPoints` para processar cada um dos pontos do `SKPoint` matriz como um quadrado, se o limite de linha é `Butt` ou `Square`. Círculos são renderizados, se o limite de linha é `Round`.

Quando você usar `SKPointMode.Lines`, conforme mostrado na tela Android no centro, o `DrawPoints` método desenha uma linha entre cada par de `SKPoint` valores, usando a terminação de linha especificada, nesse caso `Round`.

O dispositivo móvel Windows mostra o resultado da `SKPointMode.Polygon` valor. Uma linha é desenhada entre os pontos sucessivos na matriz, mas se você observar em conjunto, você verá que essas linhas não estão conectadas. Cada uma dessas linhas separadas inicia e termina com o limite de linha especificado. Se você selecionar o `Round` caps, as linhas podem parecer estar conectado, mas não realmente estão conectados.

Se linhas são conectadas ou não é um aspecto fundamental de trabalhar com caminhos de gráficos.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
