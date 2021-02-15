---
title: Pontos e traços em SkiaSharp
description: Este artigo explora como dominar as complexidades do desenho de linhas pontilhadas e tracejadas em SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 56a652f6f8ff2c4e9780d72117241d79f71210b5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367381"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Pontos e traços em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Dominar as complexidades do desenho de linhas pontilhadas e tracejadas em SkiaSharp_

O SkiaSharp permite desenhar linhas que não são sólidas, mas em vez disso, são compostas por pontos e traços:

![Linha pontilhada](dots-images/dottedlinesample.png)

Você faz isso com um *efeito de caminho* , que é uma instância da [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe que você define para a [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propriedade de `SKPaint` . Você pode criar um efeito de caminho (ou combinar efeitos de caminho) usando um dos métodos de criação estáticos definidos pelo `SKPathEffect` . ( `SKPathEffect` é um dos seis efeitos com suporte do SkiaSharp; os outros são descritos na seção [**SkiaSharp Effect**](../effects/index.md).)

Para desenhar linhas pontilhadas ou tracejadas, use o [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método estático. Há dois argumentos: o primeiro é uma matriz de `float` valores que indicam os comprimentos dos pontos e traços e o comprimento dos espaços entre eles. Essa matriz deve ter um número par de elementos, e deve haver pelo menos dois elementos. (Pode haver zero elementos na matriz, mas isso resulta em uma linha sólida.) Se houver dois elementos, o primeiro é o comprimento de um ponto ou traço e o segundo é o comprimento da lacuna antes do próximo ponto ou traço. Se houver mais de dois elementos, eles estarão nesta ordem: comprimento do traço, comprimento do intervalo, comprimento do traço, comprimento do intervalo e assim por diante.

Em geral, você desejará fazer com que os comprimentos de traço e de lacuna tenham um múltiplo da largura do traço. Se a largura do traço for de 10 pixels, por exemplo, a matriz {10, 10} desenhará uma linha pontilhada onde os pontos e as lacunas terão o mesmo comprimento da espessura do traço.

No entanto, a `StrokeCap` configuração do `SKPaint` objeto também afeta esses pontos e traços. Como você verá em breve, isso tem um impacto sobre os elementos dessa matriz.

Linhas pontilhadas e tracejadas são demonstradas na página **pontos e traços** . O arquivo [**DotsAndDashesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) instancia duas `Picker` exibições, uma para permitir que você selecione uma extremidade de traço e a segunda para selecionar uma matriz tracejada:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
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

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
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

 Os três primeiros itens no `dashArrayPicker` pressupõem que a largura do traço seja de 10 pixels. A matriz {10, 10} é para uma linha pontilhada, {30, 10} é para uma linha tracejada e {10, 10, 30, 10} é para uma linha de ponto e traço. (Os outros três serão discutidos em breve.)

O [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) arquivo code-behind contém o `PaintSurface` manipulador de eventos e algumas rotinas auxiliares para acessar as `Picker` exibições:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

Nas instantâneos a seguir, a tela do iOS na extrema esquerda mostra uma linha pontilhada:

[![Captura de tela tripla da página pontos e traços](dots-images/dotsanddashes-small.png)](dots-images/dotsanddashes-large.png#lightbox "Captura de tela tripla da página pontos e traços")

No entanto, a tela do Android também deve mostrar uma linha pontilhada usando a matriz {10, 10}, mas em vez disso, a linha é sólida. O que aconteceu? O problema é que a tela do Android também tem uma configuração de Stroke-Caps de `Square` . Isso estende todos os traços pela metade da largura do traço, fazendo com que eles preencham as lacunas.

Para contornar esse problema ao usar uma extremidade de traço de `Square` ou `Round` , você deve diminuir os comprimentos de traço na matriz pelo comprimento do traço (às vezes resultando em um comprimento de um traço de 0) e aumentar os comprimentos de lacunas pelo comprimento do traço. É assim que as três matrizes de traços finais no `Picker` arquivo XAML foram calculadas:

- {10, 10} se torna {0, 20} para uma linha pontilhada
- {30, 10} se torna {20, 20} para uma linha tracejada
- {10, 10, 30, 10} se torna {0, 20, 20, 20} para uma linha pontilhada e tracejada

A tela UWP mostra a linha pontilhada e tracejada para uma extremidade de traço de `Round` . A `Round` extremidade do traço geralmente fornece a melhor aparência de pontos e traços em linhas espessas.

Até aqui, nenhuma menção foi feita do segundo parâmetro para o `SKPathEffect.CreateDash` método. Esse parâmetro é nomeado `phase` e se refere a um deslocamento dentro do padrão de ponto e traço para o início da linha. Por exemplo, se a matriz Dash for {10, 10} e a `phase` for 10, a linha começará com uma lacuna em vez de um ponto.

Um aplicativo interessante do `phase` parâmetro está em uma animação. A página de **espiral animado** é semelhante à página de **espiral Archimedean** , exceto que a [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) classe anima o `phase` parâmetro usando o Xamarin.Forms `Device.Timer` método:

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

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
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

É claro que você precisará realmente executar o programa para ver a animação:

[![Captura de tela tripla da página de espiral animada](dots-images/animatedspiral-small.png)](dots-images/animatedspiral-large.png#lightbox "Captura de tela tripla da página de espiral animada")

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)