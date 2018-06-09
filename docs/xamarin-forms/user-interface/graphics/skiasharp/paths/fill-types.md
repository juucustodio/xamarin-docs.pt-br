---
title: Os tipos de preenchimento do caminho
description: Este artigo examina os efeitos de diferentes possíveis com tipos de preenchimento do caminho SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: d54ebd157fcc76b0fcc15bf89c72edbcd88b42f2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243701"
---
# <a name="the-path-fill-types"></a>Os tipos de preenchimento do caminho

_Descobrir os efeitos de diferentes possíveis com tipos de preenchimento do caminho SkiaSharp_

Dois contornos em um caminho podem se sobrepor, e as linhas que compõem uma único delimitação podem se sobrepor. Qualquer área fechada potencialmente pode ser preenchida, mas você não deseja preencher todas as áreas fechadas. Veja um exemplo:

![](fill-types-images/filltypeexample.png "Pontos de cinco estrelas parcialmente filles")

Você tem controle um pouco sobre isso. O algoritmo de preenchimento é controlado pelo [ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/) propriedade `SKPath`, que você definir um membro do [ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/) enumeração:

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/), o padrão
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

Os algoritmos de contorno e par-ímpares determinam se qualquer área fechada é preenchida ou não preenchida com base em uma linha hipotética extraída da área para o infinito. Essa linha cruza uma ou mais linhas de limites que compõem o caminho. Com o modo de contorno, se o número de linhas de limite desenhado no equilíbrio de uma única direção o número de linhas desenhadas em outra direção, em seguida, a área não está preenchido. Caso contrário, a área é preenchida. O algoritmo par-ímpar preenche uma área, se o número de linhas de limite é ímpar.

Com vários caminhos de rotina, o algoritmo contorno geralmente preenche todas as áreas embutidas de um caminho. O algoritmo par-ímpar geralmente produz resultados mais interessantes.

O exemplo clássico é uma estrela de cinco pontas, conforme demonstrado no **Five-Pointed estrela** página. O [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) arquivo cria dois `Picker` modos de exibição para selecionar o caminho de preencher o tipo e se o caminho é traçado preenchido ou ambos e em qual ordem:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.FivePointedStarPage"
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
            <Picker.Items>
                <x:String>Winding</x:String>
                <x:String>EvenOdd</x:String>
                <x:String>InverseWinding</x:String>
                <x:String>InverseEvenOdd</x:String>
            </Picker.Items>
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
            <Picker.Items>
                <x:String>Fill only</x:String>
                <x:String>Stroke only</x:String>
                <x:String>Stroke then Fill</x:String>
                <x:String>Fill then Stroke</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

O arquivo code-behind usa `Picker` valores para desenhar uma estrela de cinco pontas:

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
        FillType = (SKPathFillType)Enum.Parse(typeof(SKPathFillType),
                        fillTypePicker.Items[fillTypePicker.SelectedIndex])
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

    switch (drawingModePicker.SelectedIndex)
    {
        case 0:
            canvas.DrawPath(path, fillPaint);
            break;

        case 1:
            canvas.DrawPath(path, strokePaint);
            break;

        case 2:
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case 3:
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Normalmente, o tipo de preenchimento do caminho deve afetar apenas preenchimentos e não traços, mas os dois `Inverse` modos afetam preenchimentos e traçados. Para preencher, os dois `Inverse` tipos preencher as áreas oppositely para que a área fora estrela é preenchida. Para traços, os dois `Inverse` tipos de cores tudo, exceto o traçado. Usar esses tipos de preenchimento inverso pode produzir efeitos ímpares, como mostra a captura de tela do iOS:

[![](fill-types-images/fivepointedstar-small.png "Captura de tela da página Five-Pointed estrela tripla")](fill-types-images/fivepointedstar-large.png#lightbox "tripla captura de tela da página Five-Pointed estrela")

As capturas de tela do Android e UWP mostram os efeitos de par-ímpares e contorno típicos, mas a ordem de traço e preenchimento também afeta os resultados.

O algoritmo de contorno depende a direção que é desenhada. Normalmente quando você estiver criando um caminho, você pode controlar direção como especificar o que é desenhada de um ponto para outro. No entanto, o `SKPath` classe também define os métodos, como `AddRect` e `AddCircle` que desenhar contornos inteiros. Para controlar como esses objetos são desenhados, os métodos incluem um parâmetro de tipo [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/), que tem dois membros:

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

Os métodos em `SKPath` que incluem um `SKPathDirection` parâmetro dê a ele um valor padrão de `Clockwise`.

O **círculos sobrepostos** página cria um caminho com quatro círculos sobrepostos com um tipo de preenchimento par-ímpar caminho:

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

[![](fill-types-images/overlappingcircles-small.png "Tripla captura de tela da página círculos sobrepostos")](fill-types-images/overlappingcircles-large.png#lightbox "tripla captura de tela da página círculos sobrepostos")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
