---
Título: "filtros de máscara de SkiaSharp" Descrição: "saiba como usar o filtro de máscara para criar desfoques e outros efeitos alfa".
MS. Prod: xamarin MS. Technology: xamarin-skiasharp MS. AssetID: 940422A1-8BC0-4039-8AD7-26C61320F858 autor: davidbritch MS. Author: dabritch MS. Date: 08/27/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-mask-filters"></a>Filtros de máscara de SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de máscara são efeitos que manipulam a geometria e o canal alfa de objetos gráficos. Para usar um filtro de máscara, defina a [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) propriedade de `SKPaint` como um objeto do tipo [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) que você criou chamando um dos `SKMaskFilter` métodos estáticos.

A melhor maneira de se familiarizar com filtros de máscara é experimentando esses métodos estáticos. O filtro de máscara mais útil cria um desfoque:

![Exemplo de desfoque](mask-filters-images/MaskFilterExample.png "Exemplo de desfoque")

Esse é o único recurso de filtro de máscara descrito neste artigo. O próximo artigo sobre [**filtros de imagem SkiaSharp**](image-filters.md) também descreve um efeito de desfoque que você pode preferir para este. 

O [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) método estático tem a seguinte sintaxe:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Sobrecargas permitem especificar sinalizadores para o algoritmo usado para criar o desfoque e um retângulo para evitar o desfoque em áreas que serão cobertas por outros objetos gráficos.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle)é uma enumeração com os seguintes membros:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Os efeitos desses estilos são mostrados nos exemplos abaixo. O `sigma` parâmetro especifica a extensão do Desfoque. Em versões mais antigas do skia, a extensão do Desfoque foi indicada com um valor de raio. Se um valor de RADIUS for preferível para seu aplicativo, haverá um [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) método estático que pode converter de um para o outro. O método multiplica o raio por 0,57735 e adiciona 0,5.

A página **experimento de desfoque de máscara** no exemplo de [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) permite que você experimente os estilos de desfoque e os valores Sigma. O arquivo XAML instancia um `Picker` com os quatro `SKBlurStyle` membros de enumeração e um `Slider` para especificar o valor Sigma:

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

O arquivo code-behind usa esses valores para criar um `SKMaskFilter` objeto e defini-lo como a `MaskFilter` propriedade de um `SKPaint` objeto. Esse `SKPaint` objeto é usado para desenhar uma cadeia de caracteres de texto e um bitmap:

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

Aqui está o programa em execução no iOS, no Android e no Plataforma Universal do Windows (UWP) com o `Normal` estilo de desfoque e níveis crescentes `sigma` :

[![Teste de desfoque de máscara-normal](mask-filters-images/MaskBlurExperiment-Normal.png "Teste de desfoque de máscara-normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Você observará que apenas as bordas do bitmap são afetadas pelo desfoque. A `SKMaskFilter` classe não é o efeito correto a ser usado se você quiser borrar uma imagem de bitmap inteira. Para que você queira usar a [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) classe, conforme descrito no próximo artigo em [**SkiaSharp Image Filters**](image-filters.md).

O texto é mais borrado com valores crescentes do `sigma` argumento. Ao experimentar esse programa, você observará que, para um valor específico `sigma` , o desfoque é mais extremo na área de trabalho do Windows 10. Essa diferença ocorre porque a densidade de pixel é inferior em um monitor de área de trabalho do que em dispositivos móveis e, portanto, a altura do texto em pixels é menor. O `sigma` valor é proporcional a uma extensão de desfoque em pixels, portanto, para um `sigma` determinado valor, o efeito é mais extremo em exibições de resolução mais baixa. Em um aplicativo de produção, você provavelmente desejará calcular um `sigma` valor proporcional ao tamanho do gráfico. 

Tente vários valores antes de liquidar um nível de desfoque que tenha a melhor aparência para seu aplicativo. Por exemplo, na página **experimento de desfoque de máscara** , tente configurar da `sigma` seguinte maneira:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Agora `Slider` , o não tem efeito, mas o grau de desfoque é consistente entre as plataformas:

[![Mascarar o experimento de desfoque consistente](mask-filters-images/MaskBlurExperiment-Consistent.png "Mascarar o experimento de desfoque consistente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Todas as capturas de tela até o momento mostraram o desfoque criado com o `SKBlurStyle.Normal` membro de enumeração. As capturas de tela a seguir mostram os efeitos dos `Solid` `Outer` estilos de `Inner` Desfoque, e:

[![Teste de desfoque de máscara](mask-filters-images/MaskBlurExperiment.png "Teste de desfoque de máscara")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

A captura de tela do iOS mostra o `Solid` estilo: os caracteres de texto ainda estão presentes como traços pretos sólidos e o desfoque é adicionado ao fora desses caracteres de texto. 

A captura de tela do Android no meio mostra o `Outer` estilo: os próprios traços de caractere são eliminados (como é o bitmap) e o desfoque circunda o espaço vazio onde os caracteres de texto são exibidos. 

A captura de tela UWP à direita mostra o `Inner` estilo. O desfoque é restrito à área normalmente ocupada pelos caracteres de texto.

O artigo de [**gradiente linear SkiaSharp**](shaders/linear-gradient.md#transparency-and-gradients) descreveu um programa de **gradação de reflexo** que usava um gradiente linear e uma transformação para imitar uma reflexão de uma cadeia de texto:

[![Gradiente de reflexo](shaders/linear-gradient-images/ReflectionGradient.png "Gradiente de reflexo")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

A página de **reflexo borrada** adiciona uma única instrução a esse código:

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

A nova instrução adiciona um filtro de desfoque para o texto refletido com base no tamanho do texto:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Esse filtro de desfoque faz com que a reflexão pareça muito mais realista:

[![Reflexo borrada](mask-filters-images/BlurryReflection.png "Reflexo borrada")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
