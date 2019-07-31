---
title: Integração com Xamarin.Forms
description: Este artigo explica como criar gráficos de SkiaSharp que respondem ao toque e elementos de xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: 586cf0eaa7b0f38af61c9f7d619b30a39023a19e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652966"
---
# <a name="integrating-with-xamarinforms"></a>Integração com Xamarin.Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Criar gráficos de SkiaSharp que respondem ao toque e elementos de xamarin. Forms_

Gráficos de SkiaSharp podem integrar com o restante do xamarin. Forms de várias maneiras. Você pode combinar uma tela de SkiaSharp e xamarin. Forms elementos na mesma página e até mesmo posicionar os elementos xamarin. Forms na parte superior de uma tela de SkiaSharp:

![](integration-images/integrationexample.png "Selecionar uma cor com os controles deslizantes")

Outra abordagem para criar gráficos interativos de SkiaSharp em xamarin. Forms é por meio de toque.
A segunda página do [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa é intitulado **toque em Ativar/desativar preencher**. Desenha um círculo simples de duas maneiras &mdash; sem um preenchimento e com um preenchimento &mdash; alternada por um toque. O [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe mostra como você pode alterar os gráficos de SkiaSharp em resposta à entrada do usuário.

Para esta página, o `SKCanvasView` classe é instanciada na [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) arquivo, que também define um xamarin. Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) no modo de exibição:

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

Observe que o `skia` declaração de namespace XML.

O `Tapped` manipulador para o `TapGestureRecognizer` objeto simplesmente alterna o valor de um campo booliano e chama o [ `InvalidateSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) método de `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

A chamada para `InvalidateSurface` efetivamente gera uma chamada para o `PaintSurface` manipulador, que usa o `showFill` campo para preencher ou não, preencha o círculo:

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

O `StrokeWidth` propriedade foi definida como 50 para destacar a diferença. Você também pode ver a largura da linha inteira desenhando o interior primeiro e, em seguida, a estrutura de tópicos. Por padrão, os gráficos figuras que são desenhadas posteriormente no `PaintSurface` manipulador de eventos obscurecer os desenhados anteriormente no manipulador.

O **cor explorar** página demonstra como você pode também integrar SkiaSharp gráficos com outros elementos de xamarin. Forms e também demonstra a diferença entre os dois métodos alternativos de definição de cores em SkiaSharp. Estático [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) método cria um `SKColor` valor com base no modelo de matiz-saturação-luminosidade:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Estático [ `SKColor.FromHsv` ](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) método cria um `SKColor` valor com base no modelo de valor de matiz-saturação semelhante:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

Em ambos os casos, o `h` varia de argumento de 0 a 360. O `s`, `l`, e `v` argumentos variam de 0 a 100. O `a` (alfa ou opacidade) varia argumento de 0 a 255.

O [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) arquivo cria dois `SKCanvasView` objetos em um `StackLayout` lado a lado com `Slider` e `Label` exibições que permitem que o usuário selecione HSL e HSV valores de cor:

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

Os dois `SKCanvasView` elementos estão em uma única célula `Grid` com um `Label` posicionados na parte superior para exibir o valor de cor RGB resultante.

O [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) arquivo code-behind é relativamente simple. Compartilhado `ValueChanged` manipulador para os três `Slider` elementos invalida apenas os dois `SKCanvasView` elementos. O `PaintSurface` manipuladores de limpar a tela com a cor indicada pela `Slider` elementos e também definir o `Label` sentado na parte superior do `SKCanvasView` elementos:

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

Em modelos de cor HSL tanto o HSV, o valor de matiz varia de 0 a 360 e indica o dominante matiz da cor. Essas são as cores tradicionais do arco-íris: vermelho, laranja, amarelo, verde, azul, indigo, violeta e novamente em um círculo para vermelho.

No modelo de HSL, um valor 0 para luminosidade sempre é preto e um valor de 100 sempre é branco. Quando o valor de saturação é 0, os valores de luminosidade entre 0 e 100 são tons de cinza. Aumentando a saturação adiciona mais cores. Cores puras (que são valores RGB com um componente igual a 255, outro igual a 0 e o terceiro que varia de 0 a 255) ocorrem quando a saturação é 100 e a luminosidade é 50.

No modelo HSV, cores puras resultam quando a saturação e o valor 100. Quando o valor é 0, independentemente de outras configurações, a cor é preta. Tons de cinza ocorrem quando a saturação é 0 e o valor varia de 0 a 100.

Mas a melhor maneira de ter uma noção do que os dois modelos de experimentá-los por conta própria:

[![](integration-images/colorexplore-large.png "Tripla captura de tela da página cor Explore")](integration-images/colorexplore-small.png#lightbox "tripla captura de tela da página explorar de cor")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
