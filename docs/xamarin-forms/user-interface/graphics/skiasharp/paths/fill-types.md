---
title: Os tipos de preenchimento do caminho
description: Este artigo examina os diferentes efeitos possíveis com tipos de preenchimento do caminho de SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 98081ed1a9aef1260150671d4fd026dd64c20b62
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292321"
---
# <a name="the-path-fill-types"></a>Os tipos de preenchimento do caminho

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descobrir os diferentes efeitos possíveis com os tipos de preenchimento de caminho SkiaSharp_

Dois dos contornos em um caminho podem se sobrepor e as linhas que compõem uma única delimitação podem se sobrepor. Qualquer área fechada potencialmente pode ser preenchida, mas você não deseja preencher todas as áreas fechadas. Aqui está um exemplo:

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

Você tem controle um pouco sobre isso. O algoritmo de preenchimento é regido pela propriedade [`SKFillType`](xref:SkiaSharp.SKPath.FillType) de `SKPath`, que você define como um membro da enumeração [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) :

- `Winding`, o padrão
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Os algoritmos de contorno e par-ímpares determinam se qualquer área fechada é preenchida ou não preenchida com base em uma linha hipotética extraída nessa área até o infinito. Essa linha cruza uma ou mais linhas de limite que compõem o caminho. Com o modo de Enrolamento, se o número de linhas de limite desenhado no saldo de uma única direção horizontalmente o número das linhas desenhadas na outra direção, em seguida, a área não está preenchido. Caso contrário, a área é preenchida. O algoritmo de par-ímpar preenche uma área, se o número de linhas de limite é ímpar.

Com muitos caminhos de rotina, o algoritmo Enrolamento geralmente preenche todas as áreas contidas de um caminho. O algoritmo de par-ímpar geralmente produz resultados mais interessantes.

O exemplo clássico é uma estrela de cinco pontas, conforme demonstrado na página de **estrela de cinco pontas** . O arquivo [**FivePointedStarPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml) instancia duas exibições de `Picker` para selecionar o tipo de preenchimento de caminho e se o caminho é traçado ou preenchido ou ambos, e em que ordem:

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

O arquivo code-behind usa ambos os valores `Picker` para desenhar uma estrela de cinco pontas:

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

Normalmente, o tipo de preenchimento de caminho deve afetar apenas os preenchimentos e não traços, mas os dois modos de `Inverse` afetam os preenchimentos e os traços. Para preenchimentos, os dois tipos de `Inverse` preencher áreas opostamente para que a área fora da estrela seja preenchida. Para traços, os dois tipos de `Inverse` colorem tudo, exceto o traço. Usando esses tipos de preenchimento inverso pode produzir alguns efeitos ímpares, como demonstra a captura de tela do iOS:

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

A captura de tela do Android mostra os efeitos típicos de retrocesso e ímpar, mas a ordem do traço e do preenchimento também afeta os resultados.

O algoritmo de contorno é dependente da direção que as linhas são desenhadas. Normalmente, quando você estiver criando um caminho, você pode controlar nessa direção conforme você especificar que as linhas são desenhadas de um ponto para outro. No entanto, a classe `SKPath` também define métodos como `AddRect` e `AddCircle` que desempatem contornos inteiros. Para controlar como esses objetos são desenhados, os métodos incluem um parâmetro do tipo [`SKPathDirection`](xref:SkiaSharp.SKPathDirection), que tem dois membros:

- `Clockwise`
- `CounterClockwise`

Os métodos em `SKPath` que incluem um parâmetro `SKPathDirection` dão a ele um valor padrão de `Clockwise`.

A página **círculos sobrepostos** cria um caminho com quatro círculos sobrepostos com um tipo de preenchimento de caminho par-ímpar:

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

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
