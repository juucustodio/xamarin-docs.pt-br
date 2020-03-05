---
title: Pontos e traços em SkiaSharp
description: Este artigo explora como dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 229f60cbb96058454a1c634e53a7bb00ec725bcf
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292933"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Pontos e traços em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Dominar as complexidades do desenho de linhas pontilhadas e tracejadas em SkiaSharp_

SkiaSharp permite desenhar linhas que não sejam sólidos, mas em vez disso, são compostos de pontos e traços:

![](dots-images/dottedlinesample.png "Dotted line")

Você faz isso com um *efeito de caminho*, que é uma instância da classe [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) que você define para a propriedade [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) de `SKPaint`. Você pode criar um efeito de caminho (ou combinar efeitos de caminho) usando um dos métodos de criação estáticos definidos por `SKPathEffect`. (`SKPathEffect` é um dos seis efeitos com suporte do SkiaSharp; os outros são descritos na seção [**SkiaSharp Effect**](../effects/index.md).)

Para desenhar linhas pontilhadas ou tracejadas, use o método estático [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) . Há dois argumentos: o primeiro é uma matriz de `float` valores que indicam os comprimentos dos pontos e traços e o comprimento dos espaços entre eles. Essa matriz deve ter um número par de elementos, e deve haver pelo menos dois elementos. (Pode haver zero elementos na matriz, mas isso resulta em uma linha sólida.) Se houver dois elementos, o primeiro é o comprimento de um ponto ou traço e o segundo é o comprimento da lacuna antes do próximo ponto ou traço. Se há mais de dois elementos, eles são nesta ordem: traço comprimento, comprimento da lacuna, comprimento do traço, tamanho do intervalo e assim por diante.

Em geral, você vai querer fazer os comprimentos de traço e gap um múltiplo da largura do traço. Se a largura do traço é de 10 pixels, por exemplo, em seguida, a matriz {10, 10} desenhará uma linha pontilhada onde as pontos e os espaços são o mesmo tamanho que a espessura do traço.

No entanto, a configuração `StrokeCap` do objeto `SKPaint` também afeta esses pontos e traços. Como você verá em breve, que tem um impacto sobre os elementos dessa matriz.

Linhas pontilhadas e tracejadas são demonstradas na página **pontos e traços** . O arquivo [**DotsAndDashesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) instancia duas exibições de `Picker`, uma para permitir que você selecione uma extremidade de traço e a segunda para selecionar uma matriz tracejada:

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

 Os três primeiros itens no `dashArrayPicker` pressupõem que a largura do traço seja de 10 pixels. O {10, 10} é de matriz para uma linha pontilhada, {30, 10} é para uma linha tracejada e {10, 10, 30, 10} é para uma linha de traço e ponto. (As outras três serão discutidas em breve.)

O arquivo code-behind [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) contém o manipulador de eventos `PaintSurface` e algumas rotinas auxiliares para acessar as exibições de `Picker`:

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

[![](dots-images/dotsanddashes-small.png "Triple screenshot of the Dots and Dashes page")](dots-images/dotsanddashes-large.png#lightbox "Triple screenshot of the Dots and Dashes page")

No entanto, a tela do Android também deve para mostrar uma linha pontilhada usando a matriz {10, 10}, mas em vez disso, a linha é sólida. O que aconteceu? O problema é que a tela do Android também tem uma configuração do Stroke-Caps de `Square`. Isso estende a todos os traços pela metade da largura do traço, fazendo com que eles preencher as lacunas.

Para contornar esse problema ao usar uma extremidade de traço de `Square` ou `Round`, você deve diminuir os comprimentos de traço na matriz pelo comprimento do traço (às vezes resultando em um comprimento de um traço de 0) e aumentar os comprimentos de lacunas pelo comprimento do traço. É assim que as três matrizes de traços finais no `Picker` no arquivo XAML foram calculadas:

- {10, 10} torna-se {0, 20} para uma linha pontilhada
- {30, 10} torna-se {20, 20} para uma linha tracejada
- {10, 10, 30, 10} se torna a {0, 20, 20, 20} para uma linha pontilhada e tracejada

A tela UWP mostra essa linha pontilhada e tracejada para uma extremidade de traço de `Round`. A `Round` limite de traço geralmente oferece a melhor aparência de pontos e traços em linhas espessas.

Até aqui, nenhuma menção foi feita do segundo parâmetro para o método `SKPathEffect.CreateDash`. Esse parâmetro é denominado `phase` e se refere a um deslocamento dentro do padrão de ponto e traço para o início da linha. Por exemplo, se a matriz Dash for {10, 10} e a `phase` for 10, a linha começará com uma lacuna em vez de um ponto.

Um aplicativo interessante do parâmetro `phase` está em uma animação. A página de **espiral animado** é semelhante à página de **espiral Archimedean** , exceto que a classe [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) anima o parâmetro `phase` usando o método `Device.Timer` Xamarin. Forms:

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

[![](dots-images/animatedspiral-small.png "Triple screenshot of the Animated Spiral page")](dots-images/animatedspiral-large.png#lightbox "Triple screenshot of the Animated Spiral page")

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
