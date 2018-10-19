---
title: Pontos e traços em SkiaSharp
description: Este artigo explora como dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a28e4bbaae28befd91278ac5c2b9e7c9c0b522b9
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615412"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Pontos e traços em SkiaSharp

_Dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp_

SkiaSharp permite desenhar linhas que não sejam sólidos, mas em vez disso, são compostos de pontos e traços:

![](dots-images/dottedlinesample.png "Linha pontilhada")

Você fazer isso com uma *efeito de caminho*, que é uma instância das [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) classe definida como o [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propriedade do `SKPaint`. Você pode criar um caminho de efeito (ou combinar efeitos de caminho) usando um dos métodos de criação estáticos definidos por `SKPathEffect`. (`SKPathEffect` é um dos efeitos de seis com suporte pelo SkiaSharp; os outros são descritos na seção [ **SkiaSharp efeito**](../effects/index.md).)

Para desenhar linhas tracejadas ou pontilhadas, use o [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método estático. Há dois argumentos: primeiro, isso é uma matriz de `float` valores que indicam os comprimentos dos pontos e traços e o comprimento dos espaços entre elas. Essa matriz deve ter um número par de elementos, e deve haver pelo menos dois elementos. (Pode haver zero elementos na matriz, mas que resulte em uma linha sólida.) Se houver dois elementos, a primeira é o comprimento de um ponto ou traço e o segundo é o comprimento da lacuna antes do próximo ponto ou traço. Se há mais de dois elementos, eles são nesta ordem: traço comprimento, comprimento da lacuna, comprimento do traço, tamanho do intervalo e assim por diante.

Em geral, você vai querer fazer os comprimentos de traço e gap um múltiplo da largura do traço. Se a largura do traço é de 10 pixels, por exemplo, em seguida, a matriz {10, 10} desenhará uma linha pontilhada onde as pontos e os espaços são o mesmo tamanho que a espessura do traço.

No entanto, o `StrokeCap` configuração do `SKPaint` objeto também afeta esses pontos e traços. Como você verá em breve, que tem um impacto sobre os elementos dessa matriz.

Linhas tracejadas são demonstradas no e pontilhadas a **por pontos e traços** página. O [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) arquivo instancia dois `Picker` modos de exibição, um para permitindo que você selecione um limite do traço e o segundo para selecionar uma matriz de traço:

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

 Os primeiros três itens a `dashArrayPicker` supor que a largura do traço é de 10 pixels. O {10, 10} é de matriz para uma linha pontilhada, {30, 10} é para uma linha tracejada e {10, 10, 30, 10} é para uma linha de traço e ponto. (As outras três serão discutidas em breve.)

O [ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) arquivo code-behind contém o `PaintSurface` manipulador de eventos e algumas rotinas auxiliares para acessar o `Picker` modos de exibição:

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

Nas capturas de tela seguir, a tela do iOS na extrema esquerda mostra uma linha pontilhada:

[![](dots-images/dotsanddashes-small.png "Captura de tela da página de pontos e traços tripla")](dots-images/dotsanddashes-large.png#lightbox "tripla captura de tela da página de pontos e traços")

No entanto, a tela do Android também deve para mostrar uma linha pontilhada usando a matriz {10, 10}, mas em vez disso, a linha é sólida. O que aconteceu? O problema é que a tela do Android também tem uma configuração de limites do traço de `Square`. Isso estende a todos os traços pela metade da largura do traço, fazendo com que eles preencher as lacunas.

Para contornar esse problema ao usar um limite do traço de `Square` ou `Round`, você deve diminuir os comprimentos de traço na matriz, o comprimento do traço (às vezes, resultando em um comprimento do traço de 0) e aumentar os comprimentos de lacuna pelo comprimento do traço. Isso é como os três finais traço matrizes no `Picker` foram calculados no arquivo XAML:

- {10, 10} torna-se {0, 20} para uma linha pontilhada
- {30, 10} torna-se {20, 20} para uma linha tracejada
- {10, 10, 30, 10} se torna a {0, 20, 20, 20} para uma linha pontilhada e tracejada

Mostra a tela UWP que tracejado linha para um traço e pontilhadas limite de `Round`. O `Round` limite do traço, geralmente oferece a melhor aparência de pontos e traços em linhas grossa.

Até agora não há menção foi feita do segundo parâmetro para o `SKPathEffect.CreateDash` método. Esse parâmetro é chamado `phase` e ele se refere a um deslocamento dentro do padrão de ponto-e-traço, para o início da linha. Por exemplo, se a matriz de traço é {10, 10} e o `phase` é 10 e, em seguida, a linha começa com uma lacuna em vez de um ponto.

Uma aplicação interessante do `phase` parâmetro está em uma animação. O **espiral animada** página é semelhante à **Archimedean espiral** página, exceto que o [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) animado pela classe o `phase` parâmetro usando o Xamarin. Forms `Device.Timer` método:


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

É claro, você terá de realmente executar o programa para ver a animação:

[![](dots-images/animatedspiral-small.png "Captura de tela da página espiral animada tripla")](dots-images/animatedspiral-large.png#lightbox "tripla captura de tela da página espiral animado")

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
