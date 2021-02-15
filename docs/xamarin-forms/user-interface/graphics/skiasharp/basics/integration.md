---
title: Integração com o Xamarin.Forms
description: Este artigo explica como criar gráficos SkiaSharp que respondem ao toque e aos Xamarin.Forms elementos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8288ad3238babe1ce6abb6d9517cdae71373d27c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366601"
---
# <a name="integrating-with-no-locxamarinforms"></a>Integração com o Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Criar elementos gráficos do SkiaSharp que respondem ao toque e aos Xamarin.Forms elementos_

Os gráficos do SkiaSharp podem se integrar com o restante de Xamarin.Forms várias maneiras. Você pode combinar uma tela de SkiaSharp e Xamarin.Forms elementos na mesma página e até mesmo posicionar Xamarin.Forms elementos sobre uma tela SkiaSharp:

![Selecionando uma cor com controles deslizantes](integration-images/integrationexample.png)

Outra abordagem para criar gráficos interativos do SkiaSharp no Xamarin.Forms é por toque.
A segunda página do programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) tem o direito de **tocar em Alternar Preenchimento**. Ele desenha um círculo simples de duas maneiras &mdash; sem um preenchimento e com um preenchimento &mdash; alternado por um toque. A [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe mostra como você pode alterar os elementos gráficos do SkiaSharp em resposta à entrada do usuário.

Para essa página, a `SKCanvasView` classe é instanciada no arquivo [TapToggleFill. XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) , que também define um Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) na exibição:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Observe a `skia` declaração de namespace XML.

O `Tapped` manipulador para o `TapGestureRecognizer` objeto simplesmente alterna o valor de um campo booliano e chama o [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) método de `SKCanvasView` :

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

A chamada para `InvalidateSurface` gera efetivamente uma chamada para o `PaintSurface` manipulador, que usa o `showFill` campo para preencher ou não preencher o círculo:

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
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

A `StrokeWidth` propriedade foi definida como 50 para acentuar a diferença. Você também pode ver a largura da linha inteira desenhando o interior primeiro e, em seguida, o contorno. Por padrão, as figuras gráficas desenhadas posteriormente no `PaintSurface` manipulador de eventos obscurecem aquelas desenhadas anteriormente no manipulador.

A página de **exploração de cores** demonstra como você também pode integrar gráficos SkiaSharp com outros Xamarin.Forms elementos e também demonstra a diferença entre dois métodos alternativos para definir cores em SkiaSharp. O [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) método estático cria um `SKColor` valor baseado no modelo matiz-saturação-luminosidade:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

O [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) método estático cria um `SKColor` valor baseado no modelo de valor matiz-saturação-Value semelhante:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

Em ambos os casos, o `h` argumento varia de 0 a 360. Os `s` `l` argumentos, e `v` variam de 0 a 100. O `a` argumento (alfa ou Opacity) varia de 0 a 255.

O arquivo [**ColorExplorePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) cria dois `SKCanvasView` objetos lado a `StackLayout` lado com `Slider` e `Label` exibições que permitem ao usuário selecionar os valores de cor HSL e HSV:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Os dois `SKCanvasView` elementos estão em uma única célula `Grid` com um `Label` na parte superior para exibir o valor de cor RGB resultante.

O arquivo code-behind [**ColorExplorePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) é relativamente simples. O `ValueChanged` manipulador compartilhado para os três `Slider` elementos simplesmente invalida ambos os `SKCanvasView` elementos. Os `PaintSurface` manipuladores desmarcam a tela com a cor indicada pelos `Slider` elementos e também definem a `Label` parte superior dos `SKCanvasView` elementos:

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

Nos modelos de cor HSL e HSV, o valor de matiz varia de 0 a 360 e indica o matiz dominante da cor. Essas são as cores tradicionais do arco-íris: vermelho, laranja, amarelo, verde, azul, Indigo, violeta e de volta em um círculo para vermelho.

No modelo HSL, um valor 0 para luminosidade é sempre preto e um valor 100 é sempre branco. Quando o valor de saturação é 0, os valores de luminosidade entre 0 e 100 são tons de cinza. Aumentar a saturação aumenta a cor. As cores puras (que são valores RGB com um componente igual a 255, outra igual a 0 e a terceira varia de 0 a 255) ocorrem quando a saturação é 100 e a claridade é 50.

No modelo HSV, as cores puras resultam quando a saturação e o valor são 100. Quando o valor for 0, independentemente de qualquer outra configuração, a cor será preta. As tonalidades cinzas ocorrem quando a saturação é 0 e o valor varia de 0 a 100.

Mas a melhor maneira de ter uma ideia para os dois modelos é experimentá-los por conta própria:

[![Captura de tela tripla da página de exploração de cores](integration-images/colorexplore-large.png)](integration-images/colorexplore-small.png#lightbox "Captura de tela tripla da página de exploração de cores")

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)