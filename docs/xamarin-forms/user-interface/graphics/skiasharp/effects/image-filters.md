---
title: Filtros de imagem de SkiaSharp
description: Saiba como usar o filtro de imagem para criar Desfoca e sombras.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 10b61a7279964e8e1fb76a88be4f253e96b59908
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199201"
---
# <a name="skiasharp-image-filters"></a>Filtros de imagem de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de imagem são efeitos que operam em todos os bits de cor de pixels que compõem uma imagem. Eles são mais versáteis que os filtros de máscara, que operam apenas no canal alfa, conforme descrito no artigo [ **filtros de máscara de SkiaSharp**](mask-filters.md). Para usar um filtro de imagem, defina as [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) propriedade do `SKPaint` a um objeto do tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) que você criou, chamando um dos métodos estáticos da classe.

É a melhor maneira de se familiarizar com os filtros de máscara experimentando esses métodos estáticos. Você pode usar um filtro de máscara para desfocar um bitmap inteiro:

![Exemplo de desfoque](image-filters-images/ImageFilterExample.png "exemplo de desfoque")

Este artigo também demonstra o uso de um filtro de imagem para criar uma queda de sombra e para baixo-relevo efeitos e de alto-relevo.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Gráficos vetoriais e bitmaps de desfoque

O efeito de desfoque criado [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) pelo método estático tem uma vantagem significativa sobre os métodos de desfoque [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) na classe: O filtro de imagem pode borrar um bitmap inteiro. O método tem a seguinte sintaxe:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

O método tem dois valores de sigma &mdash; primeiro para a extensão de desfoque na direção horizontal e o segundo para a direção vertical. Você pode colocar em cascata filtros de imagem, especificando outro filtro de imagem como o terceiro argumento opcional. Um retângulo de corte também pode ser especificado.

O **imagem desfoque experimento** página na [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclui dois `Slider` modos de exibição que permitem que você fazer experiências com vários níveis de Desfoque de configuração:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind usa os dois `Slider` valores de chamar `SKImageFilter.CreateBlur` para o `SKPaint` objeto usado para exibir texto e um bitmap:


```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

As três capturas de tela mostram várias configurações para o `sigmaX` e `sigmaY` configurações:

[![Experimento de desfoque da imagem](image-filters-images/ImageBlurExperiment.png "experimento de desfoque da imagem")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Para manter o desfoque consistente entre as resoluções e tamanhos de exibição diferentes, defina `sigmaX` e `sigmaY` para valores que são proporcionais ao tamanho da imagem de Desfoque aplicado ao pixel renderizado.

## <a name="drop-shadow"></a>Sombra

O [ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) método estático cria um `SKImageFilter` objeto para uma sombra:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Definir este objeto o `ImageFilter` propriedade de um `SKPaint` objeto e qualquer coisa que você desenha com esse objeto terá uma sombra atrás dele.

O `dx` e `dy` parâmetros indicam os deslocamentos horizontais e verticais da sombra em pixels, do objeto de gráfico. A convenção em gráficos 2D é assumir uma fonte de luz provenientes do canto superior esquerdo, o que implica que ambos esses argumentos devem ser positivos para posicionar a sombra abaixo e à direita do objeto gráfico.

O `sigmaX` e `sigmaY` parâmetros são desfoque fatores para a sombra projetada.

O `color` parâmetro é a cor da sombra. Isso `SKColor` valor pode incluir a transparência. Uma possibilidade é o valor de cor `SKColors.Black.WithAlpha(0x80)` para escurecer qualquer cor de fundo.

Os dois parâmetros finais são opcionais.

O **Drop Shadow experimento** programa permite que você use valores de `dx`, `dy`, `sigmaX`, e `sigmaY` para exibir uma cadeia de caracteres de texto com uma sombra. O arquivo XAML instancia quatro `Slider` modos de exibição para definir estes valores:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind usa esses valores para criar uma sombra vermelho em uma cadeia de caracteres de texto azul:

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear();

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Aqui está o programa em execução:

[![Descartar um experimento de sombra](image-filters-images/DropShadowExperiment.png "descartar o experimento de sombra")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Os valores de deslocamento negativos na captura de tela da plataforma Universal do Windows na extremidade direita causam a sombra sejam exibidos acima e à esquerda do texto. Isso sugere uma fonte de luz no canto direito inferior, que não é a convenção para gráficos de computador. Mas talvez ele não parece errado de qualquer forma, porque a sombra também se torna muito desfocada e parece mais ornamental que a maioria das sombras.

## <a name="lighting-effects"></a>Efeitos de iluminação

O `SKImageFilter` classe define seis métodos que têm nomes e parâmetros, listados aqui em ordem crescente complexidade semelhantes:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Esses métodos criam os filtros de imagem que imitam o efeito de diferentes tipos de luz em superfícies tridimensionais. O filtro de imagem resultante ilumina objetos bidimensionais, como se eles existiam no espaço 3D, que pode fazer com que esses objetos sejam exibidos com privilégios elevados ou baixo-relevo, ou com o realce especular.

O `Distant` luz métodos supõem que a luz é proveniente de uma distância distante. Para fins de objetos pois resolvem, é considerada a luz do ponto em uma direção consistente no espaço 3D, assim como o sol em uma pequena área da Terra. O `Point` métodos luz imitam uma lâmpada posicionada no espaço 3D que emite luz em todas as direções. O `Spot` light tem uma posição e uma direção, muito parecido com uma lanterna.

Locais e instruções no espaço 3D são especificadas com valores da [ `SKPoint3` ](xref:SkiaSharp.SKPoint3) estrutura, que é semelhante ao `SKPoint` , mas com três propriedades nomeadas `X`, `Y`, e `Z`.

O número e a complexidade dos parâmetros para esses métodos dificultam experimentação com eles. Para começar, o **experiências de luz distante** página permite a você fazer experiências com parâmetros para o `CreateDistantLightDiffuse` método:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

A página não usa os dois últimos parâmetros opcionais.

Três `Slider` modos de exibição em que o XAML de arquivos permitem que você selecione o `Z` coordenada da `SKPoint3` valor, o `surfaceScale` parâmetro e o `kd` parâmetro, que é definido na documentação da API, como o "constante de iluminação difusa":

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind obtém esses três valores e os utiliza para criar um filtro de imagem para exibir uma cadeia de caracteres de texto:

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        canvas.Clear();

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

O primeiro argumento de `SKImageFilter.CreateDistantLitDiffuse` é a direção da luz. Positivo X e Y coordenadas indicam que a luz é apontada para a direita e para baixo. Ponto de coordenadas Z positivo para a tela. O arquivo XAML permite que você selecione valores Z negativos, mas isso é apenas para que você possa ver o que acontece: Conceitualmente, as coordenadas Z negativas fazem com que a luz aponte para fora da tela. Para qualquer coisa que outros valores negativos, em seguida, pequenos, o efeito de iluminação deixará de funcionar.

O `surfaceScale` argumento pode variar de -1 para 1. (Os valores maiores ou menores não tem nenhum efeito adicional.) Esses são valores relativos no eixo Z que indicam o deslocamento do objeto gráfico (nesse caso, a cadeia de caracteres de texto) da superfície da tela. Use valores negativos para gerar a cadeia de caracteres de texto acima da superfície da tela e valores positivos seja pressionado-lo na tela.

O `lightConstant` valor deve ser positivo. (O programa permite valores negativos para que você possa ver que eles fazem com que o efeito de parar de funcionar.) Valores mais altos fazem com que a luz mais intensa.

Esses fatores podem ser balanceados para obter um alto-relevo efeito quando `surfaceScale` é negativo (assim como acontece com o iOS e Android capturas de tela) e um baixo-relevo efeito quando `surfaceScale` for positivo, como com a captura de tela UWP à direita:

[![Experimento de luz distante](image-filters-images/DistantLightExperiment.png "experimento de luz distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

A captura de tela Android tem um valor de Z de 0, o que significa que a luz só está apontando para baixo e para a direita. O plano de fundo não está aceso e a superfície da cadeia de caracteres de texto não está aceso ambos. A luz afeta somente a borda do texto para um efeito muito sutil.

Uma abordagem alternativa para texto em relevo e baixo-relevo foi demonstrada no artigo [a transformação converter](../transforms/translate.md): A cadeia de texto é exibida duas vezes com cores diferentes que são ligeiramente deslocadas umas das outras.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
