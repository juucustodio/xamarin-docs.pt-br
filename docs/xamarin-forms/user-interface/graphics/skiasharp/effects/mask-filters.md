---
title: Filtros de máscara de SkiaSharp
description: Saiba como usar o filtro de máscara para criar Desfoca e outros efeitos alfabéticos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 36a8b5c32261d4f508c82feea1e6127574db6a20
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198250"
---
# <a name="skiasharp-mask-filters"></a>Filtros de máscara de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Os filtros de máscara são efeitos que manipulam a geometria e o canal alfa de objetos gráficos. Para usar um filtro de máscara, defina as [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) propriedade do `SKPaint` a um objeto do tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) que você criou, chamando um do `SKMaskFilter` métodos estáticos.

É a melhor maneira de se familiarizar com os filtros de máscara experimentando esses métodos estáticos. O filtro mais úteis de máscara cria um desfoque:

![Exemplo de desfoque](mask-filters-images/MaskFilterExample.png "exemplo de desfoque")

Que é o único recurso de filtro de máscara descrito neste artigo. O próximo artigo sobre [ **filtros de imagem de SkiaSharp** ](image-filters.md) também descreve um efeito de desfoque que talvez você prefira a esta. 

Estático [ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) método tem a seguinte sintaxe:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Sobrecargas permitem especificar sinalizadores para o algoritmo usado para criar o desfoque e um retângulo a Evite manchar nas áreas que serão abordadas com outros objetos gráficos.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) é uma enumeração com os seguintes membros:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Os efeitos desses estilos são mostrados nos exemplos a seguir. O `sigma` parâmetro especifica a extensão do desfoque. Nas versões mais antigas do Skia, a extensão do desfoque foi indicada com um valor de raio. Se um valor de raio for melhor para seu aplicativo, há um estático [ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) método que pode ser convertido de um para o outro. O método multiplica o radius por 0.57735 e adiciona 0,5.

O **máscara desfoque experimento** página na [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemplo permite que você pode experimentar com os estilos de desfoque e os valores de sigma. O arquivo XAML instancia uma `Picker` com o quatro `SKBlurStyle` membros de enumeração e um `Slider` para especificar o valor do sigma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind usa esses valores para criar uma `SKMaskFilter` do objeto e defina-a como o `MaskFilter` propriedade de um `SKPaint` objeto. Isso `SKPaint` objeto é usado para desenhar uma cadeia de caracteres de texto e um bitmap:

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Aqui está o programa em execução no iOS, Android e a Universal Windows Platform (UWP) com o `Normal` estilo e o aumento de desfoque `sigma` níveis:

[![Experimento de desfoque - Normal de máscara](mask-filters-images/MaskBlurExperiment-Normal.png "máscara desfoque experimento - Normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Você observará que apenas as bordas do bitmap são afetadas pelo desfoque. O `SKMaskFilter` classe não é o efeito correto a ser usado se você deseja uma imagem de bitmap inteiro de desfoque. Para que você desejará usar o [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) classe conforme descrito no próximo artigo sobre [ **filtros de imagem de SkiaSharp**](image-filters.md).

O texto é indistinta mais com valores crescentes do `sigma` argumento. Experimentar a esse programa, você perceberá que para um determinado `sigma` valor, o desfoque é mais extremo na área de trabalho do Windows 10. Essa diferença ocorre porque a densidade de pixels é inferior em um monitor da área de trabalho que em dispositivos móveis, e, portanto, a altura em pixels de texto inferior. O `sigma` valor é proporcional em desfoque medida em pixels, então, para um determinado `sigma` valor, o efeito é mais extremo em vídeos de resolução mais baixos. Em um aplicativo de produção, você provavelmente vai querer calcular um `sigma` valor que é proporcional ao tamanho do gráfico. 

Tente vários valores antes de definir um nível de desfoque que se parece a melhor para seu aplicativo. Por exemplo, nos **máscara desfoque experimento** página, tente definir `sigma` como este:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Agora o `Slider` não tem nenhum efeito, mas o grau de desfoque é consistente entre as plataformas:

[![Mascarar o experimento de desfoque - consistente](mask-filters-images/MaskBlurExperiment-Consistent.png "mascarar o experimento de desfoque - consistente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

As capturas de tela até o momento mostraram desfoque criado com o `SKBlurStyle.Normal` membro de enumeração. As capturas de tela a seguir mostram os efeitos do `Solid`, `Outer`, e `Inner` desfoque estilos:

[![Mascarar o experimento de desfoque](mask-filters-images/MaskBlurExperiment.png "mascarar o experimento de desfoque")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

A captura de tela do `Solid` Ios mostra o estilo: Os caracteres de texto ainda estão presentes como traços pretos sólidos e o desfoque é adicionado ao fora desses caracteres de texto. 

A captura de tela do Android no meio `Outer` mostra o estilo: Os próprios traços de caractere são eliminados (como o bitmap) e o desfoque circunda o espaço vazio onde os caracteres de texto apareceram depois. 

A captura de tela UWP no direito mostra o `Inner` estilo. O desfoque é restrito para a área ocupada normalmente por caracteres de texto.

O [ **gradiente linear do SkiaSharp** ](shaders/linear-gradient.md#transparency-and-gradients) artigo descrito um **reflexão gradiente** programa que usou um gradiente linear e uma transformação para imitar um reflexo de uma cadeia de caracteres de texto:

[![Gradiente de reflexão](shaders/linear-gradient-images/ReflectionGradient.png "gradiente de reflexão")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

O **reflexão desfocado** página adiciona uma única instrução para que o código:

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

A nova instrução adiciona um filtro de Desfoque para o texto refletido com base no tamanho do texto:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Esse filtro de desfoque faz com que a reflexão parecer muito mais realistas:

[![Reflexão desfocado](mask-filters-images/BlurryReflection.png "desfocado reflexão")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
