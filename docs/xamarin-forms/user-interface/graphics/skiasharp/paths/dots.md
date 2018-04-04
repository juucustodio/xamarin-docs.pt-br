---
title: Pontos e traços
description: A complexidade do desenho linhas pontilhadas e tracejadas no SkiaSharp do mestre
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 274c8e9a79fa3fadff14f1174d86aad04d902b05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="dots-and-dashes"></a>Pontos e traços

_A complexidade do desenho linhas pontilhadas e tracejadas no SkiaSharp do mestre_

SkiaSharp permite que você desenhar linhas que não são sólidos, mas em vez disso, são compostos de pontos e traços:

![](dots-images/dottedlinesample.png "Linha pontilhada")

Você pode fazer isso uma *efeito*, que é uma instância do [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe definida como o [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) propriedade de `SKPaint`. Você pode criar um caminho de efeito (ou combinar caminho efeitos) usando o estático `Create` métodos definidos pelo `SKPathEffect`.

Para desenhar linhas tracejadas ou pontilhadas, você deve usar o [ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) método estático. Há dois argumentos: primeiro, isso é uma matriz de `float` valores que indicam os comprimentos de pontos e hífens e o comprimento dos espaços entre eles. Essa matriz deve ter um número par de elementos, e deve haver pelo menos dois elementos. (Pode haver nenhum elemento na matriz, mas que resulta em uma linha sólida.) Se houver dois elementos, a primeira é o comprimento de um ponto ou traço e o segundo é o comprimento do espaço antes do próximo ponto ou traço. Se há mais de dois elementos, eles são nesta ordem: tracejado comprimento, comprimento do intervalo, o comprimento de traço, comprimento do intervalo e assim por diante.

Em geral, você desejará certificar os comprimentos de traço e espaço múltiplo da largura do traço. Se a largura do traço é de 10 pixels, por exemplo, em seguida, a matriz {10, 10} desenhará uma linha pontilhada onde as pontos e espaços são do mesmo comprimento que a espessura do traço.

No entanto, o `StrokeCap` configuração o `SKPaint` objeto também afeta a esses pontos e hífens. Como você verá em breve, que tem um impacto sobre os elementos dessa matriz.

Pontilhado e tracejadas são demonstradas no **pontos e hífens** página. O [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) arquivo cria dois `Picker` exibições, uma para permitindo que você selecione um limite de traço e a segunda para selecionar uma matriz de traço:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.DotsAndDashesPage"
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
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>10, 10</x:String>
                <x:String>30, 10</x:String>
                <x:String>10, 10, 30, 10</x:String>
                <x:String>0, 20</x:String>
                <x:String>20, 20</x:String>
                <x:String>0, 20, 20, 20</x:String>
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

Os três primeiros itens de `dashArrayPicker` supor que a largura do traço é de 10 pixels. O {10, 10} matriz é para uma linha pontilhada, {30, 10} é para uma linha tracejada e {10, 10, 30, 10} é para uma linha de ponto-e-traço. (As outras três serão discutidas em breve.)

O [ `DotsAndDashesPage` arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contém o `PaintSurface` manipulador de eventos e alguns rotinas auxiliares para acessar o `Picker` modos de exibição:

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
        StrokeCap = (SKStrokeCap)Enum.Parse(typeof(SKStrokeCap),
                        strokeCapPicker.Items[strokeCapPicker.SelectedIndex]),

        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }

    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string[] strs = picker.Items[picker.SelectedIndex].Split(new char[] { ' ', ',' },
                                                             StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

Nas capturas de tela seguir, a tela de iOS na extremidade esquerda mostra uma linha pontilhada:

[![](dots-images/dotsanddashes-small.png "Captura de tela da página de pontos e hífens tripla")](dots-images/dotsanddashes-large.png#lightbox "tripla captura de tela da página de pontos e traços")

No entanto, a tela Android também deve para mostrar uma linha pontilhada usando a matriz {10, 10}, mas em vez disso, a linha é sólida. O que aconteceu? O problema é que a tela Android também tem uma configuração de limites de traço de `Square`. Isso estende a todos os traços pela metade da largura do traço, fazendo com que eles preencher as lacunas.

Para contornar esse problema ao usar um limite de traço de `Square` ou `Round`, você deve diminuir os comprimentos de traço na matriz, o comprimento de traço (às vezes, resultando em um comprimento de traço de 0) e aumentar os comprimentos de intervalo, o comprimento do traço. Isso é como as três finais tracejado matrizes no `Picker` foram calculados no arquivo XAML:

- {10, 10} se torna {0, 20} para uma linha pontilhada
- {30, 10} se torna {20, 20} para uma linha tracejada
- {10, 10, 30, 10} ficar {0, 20, 20, 20} por uma linha pontilhada e tracejada

Limitar a mostra a tela Windows pontilhado e tracejado da linha para um traço do `Round`. O `Round` cap traço geralmente fornece melhor a aparência de pontos e traços nas linhas espessa.

Até o momento nenhuma referência foi feita do segundo parâmetro para o `SKPathEffect.CreateDash` método. Esse parâmetro é chamado `phase` e ela se refere a um deslocamento dentro do padrão de traço e ponto de início da linha. Por exemplo, se a matriz de traço é {10, 10} e o `phase` for 10, a linha começa com um intervalo, em vez de um ponto.

Uma aplicação interessante do `phase` parâmetro é de uma animação. O **animado em** página é semelhante de **Archimedean espiral** página, exceto que o [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs) classe anima a `phase` parâmetro. A página também demonstra outra abordagem para animação. O exemplo anterior do [ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs) usado o `Task.Delay` método para controlar a animação. Em vez disso, este exemplo usa o xamarin. Forms `Device.Timer` método:


```csharp
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
```

Naturalmente, você terá que, na verdade, execute o programa para ver a animação:

[![](dots-images/animatedspiral-small.png "Captura de tela da página de animação em tripla")](dots-images/animatedspiral-large.png#lightbox "tripla captura de tela da página em animado")

Você viu como desenhar linhas e definir curvas usando equações paramétricas. Uma seção a ser publicado posteriormente aborda os vários tipos de curvas que `SKPath` oferece suporte.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
