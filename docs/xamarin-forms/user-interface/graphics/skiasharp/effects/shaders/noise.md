---
title: O ruído de SkiaSharp e compondo
description: Gerar sombreadores de ruído de Perlin e combine com outros sombreadores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: dea7f5e51a864922d56f7b65d19b21a889cbc650
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656166"
---
# <a name="skiasharp-noise-and-composing"></a>O ruído de SkiaSharp e compondo

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Gráficos vetoriais simples costumam parecer artificial. As linhas retas, curvas suaves e cores sólidas não são semelhantes as imperfeições de objetos do mundo real. Ao trabalhar com os gráficos gerados pelo computador para o filme 1982 _Tron_, cientista da computação Ken Perlin começou a desenvolver algoritmos que usados processos aleatórios para fornecer essas imagens de texturas mais realistas. Em 1997, Ken Perlin ganhou um prêmio de Academy para avanços técnicos. Seu trabalho passou a ser conhecido como o ruído de Perlin, e é compatível com SkiaSharp. Veja um exemplo:

![Exemplo de ruído de perlin](noise-images/NoiseSample.png "exemplo de ruído de Perlin")

Como você pode ver, cada pixel não é um valor de cor aleatória. A continuidade do pixel a pixel resulta em formas aleatórias.

O suporte de ruído de Perlin em Skia se baseia em uma especificação do W3C para CSS e SVG. Seção 8.20 dos [ **filtro Efeitos de módulo de nível 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) inclui os algoritmos de ruído de Perlin subjacentes no código C.

## <a name="exploring-perlin-noise"></a>Explorando o ruído de Perlin

O [ `SKShader` ](xref:SkiaSharp.SKShader) classe define dois métodos diferentes de estáticos para a geração de ruído de Perlin: [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) e [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). Os parâmetros são idênticos:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Ambos os métodos também existirem em versões sobrecarregadas com adicional `SKPointI` parâmetro. A seção [ **ruído de Perlin lado a lado** ](#tiling-perlin-noise) aborda essas sobrecargas.

Os dois `baseFrequency` argumentos são valores positivos definidos na documentação do SkiaSharp como variando de 0 a 1, mas eles podem ser definidos para valores mais altos também. Quanto maior o valor, maior a alteração na imagem aleatória nas direções horizontais e verticais.

O `numOctaves` valor é um inteiro de 1 ou superior. Ele se relaciona com um fator de iteração em algoritmos. Cada oitava adicional contribui com um efeito que é a metade da oitava anterior, portanto, o efeito diminui com valores mais altos de octave.

O `seed` parâmetro é o ponto de partida para o gerador de número aleatório. Embora especificado como um valor de ponto flutuante, a fração será truncada antes que ele é usado, e 0 é o mesmo que 1.

O **ruído de Perlin** página na [ **SkiaSharpFormsDemos**)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) amostra permite que você use vários valores da `baseFrequency` e `numOctaves` argumentos. Aqui está o arquivo XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Ele usa dois `Slider` modos de exibição para os dois `baseFrequency` argumentos. Para expandir o intervalo de valores menores, os controles deslizantes são logarítmicos. O arquivo code-behind calcula os argumentos para o `SKShader`métodos de potências do `Slider` valores. O `Label` modos de exibição exibem os valores calculados:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Um `Slider` valor de 1 corresponde à 0,001, uma `Slider` valor os 2 corresponde a 0,01, um `Slider` valores de 3 corresponde a 0,1 e um `Slider` valor 4 corresponde a 1.

Aqui está o arquivo code-behind que inclui esse código:

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Aqui está o programa em execução no iOS, Android e plataforma Universal do Windows (UWP) e dispositivos. O ruído fractal é mostrado na metade superior da tela. O ruído Turbulência é na parte inferior metade:

[![O ruído de perlin](noise-images/PerlinNoise.png "ruído de Perlin")](noise-images/PerlinNoise-Large.png#lightbox)

Os mesmos argumentos sempre produzirá o mesmo padrão que começa no canto superior esquerdo. Essa consistência é óbvia quando você ajustar a largura e altura da janela do UWP. Como o Windows 10 redesenha a tela, o padrão na metade superior da tela permanece o mesmo.

O padrão de ruído incorpora vários níveis de transparência. A transparência se tornará óbvia se você definir uma cor no `canvas.Clear()` chamar. Essa cor se tornará proeminente no padrão. Você também verá esse efeito na seção [ **combinando vários sombreadores**](#combining-multiple-shaders).

Esses padrões de ruído de Perlin raramente são usados por si só. Geralmente, eles estão sujeitos para misturar modos e os filtros de cor discutidos nos artigos posteriores.

## <a name="tiling-perlin-noise"></a>Ruído de Perlin lado a lado

Dois estático `SKShader` também existem métodos para a criação de ruído de Perlin nas versões de sobrecarga. O [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) e [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) sobrecargas possuem adicional `SKPointI` parâmetro:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

O [ `SKPointI` ](xref:SkiaSharp.SKPointI) estrutura é a versão de inteiro do familiar [ `SKPoint` ](xref:SkiaSharp.SKPoint) estrutura. `SKPointI` define `X` e `Y` propriedades do tipo `int` em vez de `float`.

Esses métodos criam um padrão de repetição do tamanho especificado. Em cada bloco, a borda direita é o mesmo que a borda esquerda e a borda superior é o mesmo que a borda inferior. Essa característica é demonstrada na **lado a lado ruído de Perlin** página. O arquivo XAML é semelhante ao exemplo anterior, mas ele tem apenas um `Stepper` modo de exibição para alterar o `seed` argumento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

O arquivo code-behind define uma constante para o tamanho de bloco. O `PaintSurface` manipulador cria um bitmap desse tamanho e um `SKCanvas` para o desenho para esse bitmap. O `SKShader.CreatePerlinNoiseTurbulence` método cria um sombreador com esse tamanho de bloco. Esse sombreador é desenhado no bitmap:

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

Depois que o bitmap tiver sido criado, outra `SKPaint` objeto é usado para criar um padrão lado a lado bitmap chamando `SKShader.CreateBitmap`. Observe os dois argumentos de `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Esse sombreador é usada para cobrir a tela. Por fim, outro `SKPaint` objeto é usado para traçar um retângulo que mostra o tamanho do bitmap original.

Somente o `seed` parâmetro é selecionável na interface do usuário. Se o mesmo `seed` padrão é usado em cada plataforma, eles seriam mostram o mesmo padrão. Diferentes `seed` valores resultam em padrões diferentes:

[![Lado a lado o ruído de Perlin](noise-images/TiledPerlinNoise.png "lado a lado o ruído de Perlin")](noise-images/TiledPerlinNoise-Large.png#lightbox)

O padrão de 200 pixels quadrado no canto superior esquerdo fluem diretamente para os outros blocos.

## <a name="combining-multiple-shaders"></a>Combinando vários sombreadores

O `SKShader` classe inclui um [ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*) método que cria um sombreador com uma cor sólida especificada. Esse sombreador não é muito útil, por si só porque você pode simplesmente definir cor o `Color` propriedade do `SKPaint` do objeto e defina o `Shader` propriedade como nulo.

Isso `CreateColor` método torna-se útil em outro método que `SKShader` define. Esse método é [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), que combina dois sombreadores. Aqui está a sintaxe:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

O `srcShader` (sombreador de origem) com eficiência é desenhado na parte superior do `dstShader` (sombreador de destino). Se o sombreador de origem for uma cor sólida ou um gradiente sem transparência, o sombreador de destino será completamente obscurecido.

Um sombreador de ruído de Perlin contém transparência. Se esse sombreador é a origem, o sombreador de destino será exibido através das áreas transparentes.

O **composto ruído de Perlin** página tem um arquivo XAML que é praticamente idêntico ao primeiro **ruído de Perlin** página. O arquivo code-behind também é semelhante. Mas o original **ruído de Perlin** página conjuntos a `Shader` propriedade do `SKPaint` para o sombreador retornado de estático `CreatePerlinNoiseFractalNoise` e `CreatePerlinNoiseTurbulence` métodos. Isso **composto ruído de Perlin** página chamadas `CreateCompose` um sombreador de combinação. O destino é um sombreador de azul sólido criado usando `CreateColor`. A fonte é um sombreador de ruído de Perlin:

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

O sombreador de ruído fractal é na parte superior; o sombreador Turbulência está na parte inferior:

[![Composto de ruído de Perlin](noise-images/ComposedPerlinNoise.png "composto ruído de Perlin")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Observe que quanto mais azuis esses sombreadores são daqueles exibidos pela **ruído de Perlin** página. A diferença ilustra a quantidade de transparência nos sombreadores de ruído.

Também há uma sobrecarga da [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) método:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

O parâmetro final é um membro do `SKBlendMode` enumeração, uma enumeração com 29 membros que é discutido na próxima série de artigos sobre [ **modos de composição e o blend SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
