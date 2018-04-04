---
title: Integração com o xamarin. Forms
description: Criar gráficos SkiaSharp que respondem a toque e elementos de xamarin. Forms
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 67c4330d8e446a407dec7792fe5f40cdd9d23c22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="integrating-with-xamarinforms"></a>Integração com o xamarin. Forms

_Criar gráficos SkiaSharp que respondem a toque e elementos de xamarin. Forms_

Gráficos de SkiaSharp podem integrar com o restante do xamarin. Forms de várias maneiras. Você pode combinar uma tela de SkiaSharp e xamarin. Forms elementos na mesma página e até mesmo posição xamarin. Forms elementos na parte superior de uma tela de SkiaSharp:

![](integration-images/integrationexample.png "Selecionar uma cor com controles deslizantes")

Outra abordagem para criar gráficos interativos de SkiaSharp no xamarin. Forms é por meio de toque.
A segunda página do [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa tem direito **preenchimento de alternar toque**. Desenha um círculo simples duas maneiras &mdash; sem preenchimento e com um preenchimento &mdash; alternada por um toque. O [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe mostra como você pode alterar SkiaSharp gráficos em resposta à entrada do usuário.

Para essa página, o `SKCanvasView` classe é instanciada no [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) arquivo, que também define uma xamarin. Forms [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) no modo de exibição:

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

Observe o `skia` declaração de namespace XML.

O `Tapped` manipulador para o `TapGestureRecognizer` objeto simplesmente alterna o valor de um campo booliano e chama o [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/) método de `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

A chamada para `InvalidateSurface` efetivamente gera uma chamada para o `PaintSurface` manipulador, que usa o `showFill` campo preencher ou não, preencha o círculo:

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

O `StrokeWidth` propriedade foi definida como 50 para destacar a diferença. Você também pode ver a largura da linha inteira desenhando o interior primeiro e, em seguida, a estrutura de tópicos. Por padrão, gráficos figuras desenhadas posteriormente o `PaintSurface` manipulador de eventos podem ocultar os desenhados anteriormente no manipulador.

O **cor explorar** página demonstra como você também pode integrar SkiaSharp gráficos com outros elementos de xamarin. Forms e também demonstra a diferença entre os dois métodos alternativos para definir cores em SkiaSharp. Estático [ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/) método cria um `SKColor` valor com base no modelo de matiz-saturação-luminosidade:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Estático [ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/) método cria um `SKColor` valor com base no modelo de valor de saturação de cor semelhante:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

Em ambos os casos, o `h` varia de argumento de 0 a 360. O `s`, `l`, e `v` argumentos variam de 0 a 100. O `a` (alpha ou opacidade) intervalos argumento de 0 a 255.

O [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) arquivo cria dois `SKCanvasView` objetos em um `StackLayout` lado a lado com `Slider` e `Label` exibições que permitem que o usuário selecione HSL e Valores de cor HSV:

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

O [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) arquivo code-behind é relativamente simple. Compartilhado `ValueChanged` manipulador para as três `Slider` elementos simplesmente invalida ambos `SKCanvasView` elementos. O `PaintSurface` manipuladores de limpar a tela com a cor indicada pelo `Slider` elementos e também definir o `Label` colocada na parte superior do `SKCanvasView` elementos:

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

Em modelos de cor HSL tanto o HSV, o valor de matiz varia de 0 a 360 e indica o dominante matiz da cor. Estas são as cores tradicionais do arco-íris: vermelho, laranja, amarelo, verde, azul, indigo, violeta e novamente em um círculo para vermelho.

No modelo HSL, é sempre um valor 0 para luminosidade preto e um valor de 100 é sempre branca. Quando o valor de saturação for 0, os valores de luminosidade entre 0 e 100 são tons de cinza. Aumentar a saturação adiciona mais cores. Cores puras (que são valores RGB com um componente igual a 255, outro igual a 0 e o terceiro que varia de 0 a 255) ocorrem quando a saturação é 100 e a luminosidade é 50.

No modelo de HSV cores puras resultam quando a saturação e o valor 100. Quando o valor é 0, independentemente de outras configurações, a cor é preta. Tons de cinza ocorrem quando a saturação é 0 e intervalos de valores de 0 a 100.

Mas a melhor maneira de conhecer os dois modelos experimentá-los:

[![](integration-images/colorexplore-large.png "Tripla captura de tela da página de cor explorar")](integration-images/colorexplore-small.png#lightbox "tripla captura de tela da página explorar de cor")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
