---
title: Pontos e traços em SkiaSharp
description: Este artigo explora como dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 7c336e6b5224f61ff84eb39652788b23f52b806e
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615412"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Pontos e traços em SkiaSharp

_Dominar as complicações do desenho linhas pontilhadas e tracejadas no SkiaSharp_

SkiaSharp permite desenhar linhas que não sejam sólidos, mas em vez disso, são compostos de pontos e traços:

![](dots-images/dottedlinesample.png "Linha pontilhada")

Você fazer isso com uma *efeito de caminho*, que é uma instância das [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe definida como o [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) propriedade do `SKPaint`. Você pode criar um caminho de efeito (ou combinar efeitos de caminho) usando o estático `Create` métodos definidos pelo `SKPathEffect`.

Para desenhar linhas tracejadas ou pontilhadas, use o [ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) método estático. Há dois argumentos: primeiro, isso é uma matriz de `float` valores que indicam os comprimentos dos pontos e traços e o comprimento dos espaços entre elas. Essa matriz deve ter um número par de elementos, e deve haver pelo menos dois elementos. (Pode haver zero elementos na matriz, mas que resulte em uma linha sólida.) Se houver dois elementos, a primeira é o comprimento de um ponto ou traço e o segundo é o comprimento da lacuna antes do próximo ponto ou traço. Se há mais de dois elementos, eles são nesta ordem: traço comprimento, comprimento da lacuna, comprimento do traço, tamanho do intervalo e assim por diante.

Em geral, você vai querer fazer os comprimentos de traço e gap um múltiplo da largura do traço. Se a largura do traço é de 10 pixels, por exemplo, em seguida, a matriz {10, 10} desenhará uma linha pontilhada onde as pontos e os espaços são o mesmo tamanho que a espessura do traço.

No entanto, o `StrokeCap` configuração do `SKPaint` objeto também afeta esses pontos e traços. Como você verá em breve, que tem um impacto sobre os elementos dessa matriz.

Linhas tracejadas são demonstradas no e pontilhadas a **por pontos e traços** página. O [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) arquivo instancia dois `Picker` modos de exibição, um para permitindo que você selecione um limite do traço e o segundo para selecionar uma matriz de traço:

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

Os primeiros três itens a `dashArrayPicker` supor que a largura do traço é de 10 pixels. O {10, 10} é de matriz para uma linha pontilhada, {30, 10} é para uma linha tracejada e {10, 10, 30, 10} é para uma linha de traço e ponto. (As outras três serão discutidas em breve.)

O [ `DotsAndDashesPage` arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contém o `PaintSurface` manipulador de eventos e algumas rotinas auxiliares para acessar o `Picker` modos de exibição:

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

Nas capturas de tela seguir, a tela do iOS na extrema esquerda mostra uma linha pontilhada:

[![](dots-images/dotsanddashes-small.png "Captura de tela da página de pontos e traços tripla")](dots-images/dotsanddashes-large.png#lightbox "tripla captura de tela da página de pontos e traços")

No entanto, a tela do Android também deve para mostrar uma linha pontilhada usando a matriz {10, 10}, mas em vez disso, a linha é sólida. O que aconteceu? O problema é que a tela do Android também tem uma configuração de limites do traço de `Square`. Isso estende a todos os traços pela metade da largura do traço, fazendo com que eles preencher as lacunas.

Para contornar esse problema ao usar um limite do traço de `Square` ou `Round`, você deve diminuir os comprimentos de traço na matriz, o comprimento do traço (às vezes, resultando em um comprimento do traço de 0) e aumentar os comprimentos de lacuna pelo comprimento do traço. Isso é como os três finais traço matrizes no `Picker` foram calculados no arquivo XAML:

- {10, 10} torna-se {0, 20} para uma linha pontilhada
- {30, 10} torna-se {20, 20} para uma linha tracejada
- {10, 10, 30, 10} se torna a {0, 20, 20, 20} para uma linha pontilhada e tracejada

Mostra a tela UWP que tracejado linha para um traço e pontilhadas limite de `Round`. O `Round` limite do traço, geralmente oferece a melhor aparência de pontos e traços em linhas grossa.

Até agora não há menção foi feita do segundo parâmetro para o `SKPathEffect.CreateDash` método. Esse parâmetro é chamado `phase` e ele se refere a um deslocamento dentro do padrão de ponto-e-traço, para o início da linha. Por exemplo, se a matriz de traço é {10, 10} e o `phase` é 10 e, em seguida, a linha começa com uma lacuna em vez de um ponto.

Uma aplicação interessante do `phase` parâmetro está em uma animação. O **animada espiral** página é semelhante ao **Archimedean espiral** página, exceto que o [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs) animado pela classe o `phase` parâmetro. A página também demonstra outra abordagem para animação. O exemplo anterior do [ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs) usado o `Task.Delay` método para controlar a animação. Em vez disso, este exemplo usa o xamarin. Forms `Device.Timer` método:


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

É claro, você terá de realmente executar o programa para ver a animação:

[![](dots-images/animatedspiral-small.png "Captura de tela da página espiral animada tripla")](dots-images/animatedspiral-large.png#lightbox "tripla captura de tela da página espiral animado")

Agora, você já viu como desenhar linhas e definir curvas usando equações paramétricas. Uma seção a ser publicado posteriormente abordará os vários tipos de curvas que `SKPath` dá suporte.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
