---
title: Filtros de imagem SkiaSharp
description: Saiba como usar o filtro de imagem para criar desfoques e soltar sombras.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f10d39ff9fb08897f12cf1991ddcd2d7793b695
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564024"
---
# <a name="skiasharp-image-filters"></a>Filtros de imagem SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Os filtros de imagem são efeitos que operam em todos os bits de cor de pixels que compõem uma imagem. Eles são mais versáteis do que filtros de máscara, que só operam no canal alfa, conforme descrito no artigo [**SkiaSharp Mask Filters**](mask-filters.md). Para usar um filtro de imagem, defina a [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) propriedade de `SKPaint` como um objeto do tipo [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) que você criou chamando um dos métodos estáticos da classe.

A melhor maneira de se familiarizar com filtros de máscara é experimentando esses métodos estáticos. Você pode usar um filtro de máscara para borrar um bitmap inteiro:

![Exemplo de desfoque](image-filters-images/ImageFilterExample.png "Exemplo de desfoque")

Este artigo também demonstra o uso de um filtro de imagem para criar uma sombra e para efeitos de relevo e engraving.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Desfocando gráficos vetoriais e bitmaps

O efeito de desfoque criado pelo [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) método estático tem uma vantagem significativa sobre os métodos de desfoque na [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) classe: o filtro de imagem pode desfocar um bitmap inteiro. O método tem a seguinte sintaxe:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

O método tem dois valores Sigma &mdash; o primeiro para a extensão de desfoque na direção horizontal e o segundo para a direção vertical. Você pode propagar filtros de imagem especificando outro filtro de imagem como o terceiro argumento opcional. Um retângulo de corte também pode ser especificado.

A página **experimento de desfoque de imagem** no [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclui dois modos de `Slider` exibição que permitem experimentar a configuração de vários níveis de desfoque:

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

O arquivo code-behind usa os dois `Slider` valores para chamar `SKImageFilter.CreateBlur` o `SKPaint` objeto usado para exibir o texto e um bitmap:

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

As três capturas de tela mostram várias configurações para as `sigmaX` `sigmaY` configurações e:

[![Teste de desfoque de imagem](image-filters-images/ImageBlurExperiment.png "Teste de desfoque de imagem")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Para manter o desfoque consistente entre diferentes tamanhos de exibição e resoluções, defina `sigmaX` e `sigmaY` com valores que são proporcionais ao tamanho de pixel renderizado da imagem à qual o desfoque é aplicado.

## <a name="drop-shadow"></a>Sombra

O [`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) método estático cria um `SKImageFilter` objeto para uma sombra projetada:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Defina esse objeto como a `ImageFilter` propriedade de um `SKPaint` objeto e tudo o que você desenhar com esse objeto terá uma sombra subjacente.

Os `dx` `dy` parâmetros e indicam os deslocamentos horizontal e vertical da sombra em pixels do objeto gráfico. A Convenção em gráficos 2D é assumir uma fonte de luz proveniente do canto superior esquerdo, o que implica que ambos os argumentos devem ser positivos para posicionar a sombra abaixo e à direita do objeto gráfico.

Os `sigmaX` `sigmaY` parâmetros e são fatores de desfoque para a sombra.

O `color` parâmetro é a cor da sombra de projeção. Esse `SKColor` valor pode incluir transparência. Uma possibilidade é o valor de cor `SKColors.Black.WithAlpha(0x80)` para escurecer qualquer plano de fundo de cor.

Os dois últimos parâmetros são opcionais.

O programa de **teste do tipo "soltar sombra** " permite que você teste os valores de `dx` , `dy` , `sigmaX` e `sigmaY` para exibir uma cadeia de caracteres de texto com uma sombra. O arquivo XAML instancia quatro `Slider` modos de exibição para definir esses valores:

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

O arquivo code-behind usa esses valores para criar uma sombra vermelha em uma cadeia de texto azul:

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

Este é o programa em execução:

[![Teste de sombra drop](image-filters-images/DropShadowExperiment.png "Teste de sombra drop")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Os valores de deslocamento negativo no Plataforma Universal do Windows captura de tela na extrema direita fazem com que a sombra apareça acima e à esquerda do texto. Isso sugere uma fonte de luz no canto inferior direito, que não é a Convenção para gráficos de computador. Mas não parece errado de nenhuma forma, talvez porque a sombra também se tornou muito borrada e parece mais ornamental do que a maioria das sombras.

## <a name="lighting-effects"></a>Efeitos de iluminação

A `SKImageFilter` classe define seis métodos que têm nomes e parâmetros semelhantes, listados aqui em ordem de complexidade crescente:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Esses métodos criam filtros de imagem que imitam o efeito de diferentes tipos de luz em superfícies tridimensionais. O filtro de imagem resultante ilumina objetos bidimensionais como se eles existissem no espaço 3D, o que pode fazer com que esses objetos apareçam com privilégios elevados ou embutidos ou com realce especulativo.

Os `Distant` métodos leves pressupõem que a luz venha de uma distância distante. Com a finalidade de iluminar objetos, pressupõe-se que a luz aponte para uma direção consistente no espaço 3D, assim como o sol em uma pequena área da terra. Os `Point` métodos leves imitam uma lâmpada posicionada no espaço 3D que emite luz em todas as direções. A `Spot` luz tem uma posição e uma direção, bem como uma lanterna.

Os locais e as direções no espaço 3D são especificados com valores da [`SKPoint3`](xref:SkiaSharp.SKPoint3) estrutura, que é semelhante a, `SKPoint` mas com três propriedades chamadas `X` , `Y` e `Z` .

O número e a complexidade dos parâmetros para esses métodos tornam a experimentação mais difícil. Para começar, a página **experimento de luz distante** permite que você experimente parâmetros para o `CreateDistantLightDiffuse` método:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

A página não usa os dois últimos parâmetros opcionais.

Três `Slider` exibições no arquivo XAML permitem que você selecione a `Z` coordenada do `SKPoint3` valor, o `surfaceScale` parâmetro e o `kd` parâmetro, que é definido na documentação da API como a "constante de iluminação difusa":

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

O arquivo code-behind Obtém esses três valores e os usa para criar um filtro de imagem para exibir uma cadeia de texto:

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

O primeiro argumento de `SKImageFilter.CreateDistantLitDiffuse` é a direção da luz. As coordenadas X e Y positivas indicam que a luz é apontada para a direita e para baixo. As coordenadas Z positivas apontam para a tela. O arquivo XAML permite que você selecione valores Z negativos, mas isso é apenas para que você possa ver o que acontece: conceitualmente, as coordenadas Z negativas fazem com que a luz aponte para fora da tela. Para qualquer outro valor negativo pequeno, o efeito de iluminação para de funcionar.

O `surfaceScale` argumento pode variar de – 1 a 1. (Valores superiores ou mais baixos não têm efeito adicional.) Esses são valores relativos no eixo Z que indicam a substituição do objeto gráfico (nesse caso, a cadeia de texto) da superfície da tela. Use valores negativos para gerar a cadeia de caracteres de texto acima da superfície da tela e os valores positivos para ensolte-la na tela.

O `lightConstant` valor deve ser positivo. (O programa permite valores negativos para que você possa ver que eles fazem com que o efeito pare de funcionar.) Valores mais altos causam uma luz mais intensa.

Esses fatores podem ser balanceados para obter um efeito em relevo quando `surfaceScale` é negativo (como nas capturas de tela do IOS e Android) e um efeito em baixo-relevo quando `surfaceScale` é positivo, como com a captura de tela UWP à direita:

[![Experimento de luz distante](image-filters-images/DistantLightExperiment.png "Experimento de luz distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

A captura de tela do Android tem um valor Z de 0, o que significa que a luz está apontando para baixo e para a direita. O plano de fundo não está aceso e a superfície da cadeia de texto não está iluminada. A luz afeta apenas a borda do texto para um efeito muito sutil.

Uma abordagem alternativa para texto em relevo e de baixo relevo foi demonstrada no artigo [a transformação de conversão](../transforms/translate.md): a cadeia de texto é exibida duas vezes com cores diferentes que são um pouco deslocadas umas das outras.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)