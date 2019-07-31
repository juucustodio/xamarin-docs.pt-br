---
title: Os tipos de preenchimento do caminho
description: Este artigo examina os diferentes efeitos possíveis com tipos de preenchimento do caminho de SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 46442e2377400cb3488f1a9e2fc8d47f27bbacfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655800"
---
# <a name="the-path-fill-types"></a>Os tipos de preenchimento do caminho

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra os diferentes efeitos possíveis com tipos de preenchimento do caminho de SkiaSharp_

Dois dos contornos em um caminho podem se sobrepor e as linhas que compõem uma única delimitação podem se sobrepor. Qualquer área fechada potencialmente pode ser preenchida, mas você não deseja preencher todas as áreas fechadas. Veja um exemplo:

![](fill-types-images/filltypeexample.png "Cinco pontas parcialmente estrelas filles")

Você tem controle um pouco sobre isso. O algoritmo de preenchimento é regido pela [ `SKFillType` ](xref:SkiaSharp.SKPath.FillType) propriedade do `SKPath`, que você definir um membro do [ `SKPathFillType` ](xref:SkiaSharp.SKPathFillType) enumeração:

- `Winding`, o padrão
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Os algoritmos de contorno e par-ímpares determinam se qualquer área fechada é preenchida ou não preenchida com base em uma linha hipotética extraída nessa área até o infinito. Essa linha cruza uma ou mais linhas de limite que compõem o caminho. Com o modo de Enrolamento, se o número de linhas de limite desenhado no saldo de uma única direção horizontalmente o número das linhas desenhadas na outra direção, em seguida, a área não está preenchido. Caso contrário, a área é preenchida. O algoritmo de par-ímpar preenche uma área, se o número de linhas de limite é ímpar.

Com muitos caminhos de rotina, o algoritmo Enrolamento geralmente preenche todas as áreas contidas de um caminho. O algoritmo de par-ímpar geralmente produz resultados mais interessantes.

O exemplo clássico é uma estrela de cinco pontas, conforme demonstrado a **Five-Pointed estrela** página. O [ **FivePointedStarPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) arquivo instancia dois `Picker` modos de exibição para selecionar o caminho de preencher o tipo e se o caminho é traçado ou preenchido ou ambos e em qual ordem:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

O arquivo code-behind usa ambos `Picker` valores para desenhar uma estrela de cinco pontos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle), 
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Normalmente, o tipo de preenchimento do caminho deve afetar apenas preenchimentos e não traços, mas os dois `Inverse` modos afetam preenchimentos e traços. Para preenchimentos, os dois `Inverse` tipos preencher as áreas oppositely, de modo que a área de fora a estrela é preenchida. Para traços, dois `Inverse` tipos de cor tudo, exceto o traço. Usando esses tipos de preenchimento inverso pode produzir alguns efeitos ímpares, como demonstra a captura de tela do iOS:

[![](fill-types-images/fivepointedstar-small.png "Captura de tela da página Five-Pointed estrela tripla")](fill-types-images/fivepointedstar-large.png#lightbox "tripla captura de tela da página Five-Pointed estrela")

As capturas de tela do Android e UWP mostram os efeitos de par-ímpares e Enrolamento típicos, mas a ordem do traço e preenchimento também afeta os resultados.

O algoritmo de contorno é dependente da direção que as linhas são desenhadas. Normalmente, quando você estiver criando um caminho, você pode controlar nessa direção conforme você especificar que as linhas são desenhadas de um ponto para outro. No entanto, o `SKPath` classe também define os métodos, como `AddRect` e `AddCircle` que desenhar delimitações inteiras. Para controlar como esses objetos são desenhados, os métodos incluem um parâmetro do tipo [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection), que tem dois membros:

- `Clockwise`
- `CounterClockwise`

Os métodos `SKPath` que incluem uma `SKPathDirection` parâmetro dê a ele um valor padrão de `Clockwise`.

O **círculos sobrepostos** página cria um caminho com quatro círculos sobrepostos com um tipo de preenchimento do caminho par-ímpar:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

É uma imagem interessante criada com um mínimo de código:

[![](fill-types-images/overlappingcircles-small.png "Captura de tela da página círculos sobrepostos tripla")](fill-types-images/overlappingcircles-large.png#lightbox "tripla captura de tela da página círculos sobrepostos")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
