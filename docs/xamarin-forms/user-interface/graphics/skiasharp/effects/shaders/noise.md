---
title: Ruído e composição do SkiaSharp
description: Gere sombreadores de ruído de Perlm e combine com outros sombreadores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a9a8b8dc31369b5774935a2e8fca5cf17faa24b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371242"
---
# <a name="skiasharp-noise-and-composing"></a>Ruído e composição do SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Gráficos vetoriais simples tendem a parecer não naturais. As linhas retas, as curvas suaves e as cores sólidas não se assemelham às imperfeições de objetos do mundo real. Ao trabalhar com gráficos gerados por computador para o filme de 1982 de _Tron_ , a cientista do computador Ken perlr começou a desenvolver algoritmos que usaram processos aleatórios para fornecer a essas imagens mais texturas realistas. Em 1997, Ken Perl ganhou um prêmio da Academia para a realização técnica. Seu trabalho chegou a ser conhecido como o ruído de Perl e tem suporte no SkiaSharp. Aqui está um exemplo:

![Exemplo de ruído de perlm](noise-images/NoiseSample.png "Exemplo de ruído de perlm")

Como você pode ver, cada pixel não é um valor de cor aleatório. A continuidade de pixel para pixel resulta em formas aleatórias.

O suporte do Perl-ruído no skia é baseado em uma especificação W3C para CSS e SVG. A seção 8,20 do [**módulo de efeitos de filtro nível 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) inclui os algoritmos de ruído subjacentes de Perl em código C.

## <a name="exploring-perlin-noise"></a>Explorando o ruído de Perl

A [`SKShader`](xref:SkiaSharp.SKShader) classe define dois métodos estáticos diferentes para gerar o ruído de perlm:  [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) e [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) . Os parâmetros são idênticos:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Os dois métodos também existem em versões sobrecarregadas com um `SKPointI` parâmetro adicional. A seção com [**disposição do som de Perl**](#tiling-perlin-noise) discute essas sobrecargas.

Os dois `baseFrequency` argumentos são valores positivos definidos na documentação do SkiaSharp, variando de 0 a 1, mas também podem ser definidos como valores mais altos. Quanto maior o valor, maior será a alteração na imagem aleatória nas direções horizontal e vertical.

O `numOctaves` valor é um número inteiro de 1 ou superior. Ele está relacionado a um fator de iteração nos algoritmos. Cada Octave adicional contribui com um efeito que é metade da Octave anterior, portanto, o efeito diminui com valores Octave mais altos.

O `seed` parâmetro é o ponto de partida para o gerador de número aleatório. Embora especificado como um valor de ponto flutuante, a fração é truncada antes de ser usada e 0 é igual a 1.

A página de **ruído do Perl** na [ **SkiaSharpFormsDemos** )](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) permite que você experimente vários valores dos `baseFrequency` `numOctaves` argumentos e. Este é o arquivo XAML:

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

Ele usa dois `Slider` modos de exibição para os dois `baseFrequency` argumentos. Para expandir o intervalo dos valores mais baixos, os controles deslizantes são logarítmicas. O arquivo code-behind calcula os argumentos para os `SKShader` métodos de potência dos `Slider` valores. As `Label` exibições exibem os valores calculados:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Um `Slider` valor de 1 corresponde a 0, 1, um `Slider` sistema operacional 2 corresponde a 0, 1, `Slider` os valores 3 correspondem a 0,1 e um `Slider` valor de 4 corresponde a 1.

Este é o arquivo code-behind que inclui esse código:

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

Este é o programa em execução em dispositivos iOS, Android e Plataforma Universal do Windows (UWP). O ruído de fractal é mostrado na metade superior da tela. O ruído de turbulência está na metade inferior:

[![Ruído de Perl](noise-images/PerlinNoise.png "Ruído de Perl")](noise-images/PerlinNoise-Large.png#lightbox)

Os mesmos argumentos sempre produzem o mesmo padrão que começa no canto superior esquerdo. Essa consistência é óbvia quando você ajusta a largura e a altura da janela UWP. À medida que o Windows 10 redesenha a tela, o padrão na metade superior da tela permanece o mesmo.

O padrão de ruído incorpora vários graus de transparência. A transparência se torna óbvia se você definir uma cor na `canvas.Clear()` chamada. Essa cor torna-se proeminente no padrão. Você também verá esse efeito na seção [**combinando vários sombreadores**](#combining-multiple-shaders).

Esses padrões de ruído Perl são raramente usados por si mesmos. Geralmente, eles estão sujeitos a modos de mesclagem e filtros de cores discutidos em artigos posteriores.

## <a name="tiling-perlin-noise"></a>Ruído de Perl em blocos gráficos

Os dois métodos estáticos `SKShader` para criar o ruído de perlm também existem em versões de sobrecarga. As [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) sobrecargas e têm um `SKPointI` parâmetro adicional:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

A [`SKPointI`](xref:SkiaSharp.SKPointI) estrutura é a versão inteira da estrutura familiar [`SKPoint`](xref:SkiaSharp.SKPoint) . `SKPointI` define `X` e `Y` Propriedades do tipo `int` em vez de `float` .

Esses métodos criam um padrão de repetição do tamanho especificado. Em cada bloco, a borda direita é igual à borda esquerda e a borda superior é igual à borda inferior. Essa característica é demonstrada na página de **ruído do Perl** . O arquivo XAML é semelhante ao exemplo anterior, mas tem apenas uma `Stepper` exibição para alterar o `seed` argumento:

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

O arquivo code-behind define uma constante para o tamanho do bloco. O `PaintSurface` manipulador cria um bitmap desse tamanho e um `SKCanvas` para desenhar nesse bitmap. O `SKShader.CreatePerlinNoiseTurbulence` método cria um sombreador com esse tamanho de bloco. Este sombreador é desenhado no bitmap:

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

Depois que o bitmap tiver sido criado, outro `SKPaint` objeto será usado para criar um padrão de bitmap com o lado do ladrilho chamando `SKShader.CreateBitmap` . Observe os dois argumentos de `SKShaderTileMode.Repeat` :

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Esse sombreador é usado para cobrir a tela. Por fim, outro `SKPaint` objeto é usado para traçar um retângulo mostrando o tamanho do bitmap original.

Somente o `seed` parâmetro é selecionável na interface do usuário. Se o mesmo `seed` padrão for usado em cada plataforma, ele mostrará o mesmo padrão. `seed`Valores diferentes resultam em padrões diferentes:

[![Ruído de Perl de xadrez](noise-images/TiledPerlinNoise.png "Ruído de Perl de xadrez")](noise-images/TiledPerlinNoise-Large.png#lightbox)

O padrão quadrado de 200 pixels no canto superior esquerdo flui diretamente para os outros blocos.

## <a name="combining-multiple-shaders"></a>Combinando vários sombreadores

A `SKShader` classe inclui um [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) método que cria um sombreador com uma cor sólida especificada. Esse sombreador não é muito útil por si só porque você pode simplesmente definir essa cor como a `Color` Propriedade do `SKPaint` objeto e definir a `Shader` propriedade como NULL.

Esse `CreateColor` método se torna útil em outro método que `SKShader` define. Esse método é [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) , que combina dois sombreadores. Esta é a sintaxe:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

O `srcShader` (sombreador de origem) é efetivamente desenhado na parte superior do `dstShader` (sombreador de destino). Se o sombreador de origem for uma cor sólida ou um gradiente sem transparência, o sombreador de destino será completamente obscurecido.

Um sombreador de ruído de Perl contém transparência. Se esse sombreador for a origem, o sombreador de destino será exibido por meio das áreas transparentes.

A página de **ruído do perlm** é um arquivo XAML que é praticamente idêntico à primeira página de **ruído do perlm** . O arquivo code-behind também é semelhante. Mas a página de ruído original do **Perl** define a `Shader` propriedade de `SKPaint` para o sombreador retornado dos métodos estáticos `CreatePerlinNoiseFractalNoise` e `CreatePerlinNoiseTurbulence` . Essa página de **ruído do Perl** `CreateCompose` . O destino é um sombreador azul sólido criado usando `CreateColor` . A origem é um sombreador de ruído em perl:

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

O sombreador de ruído de fractal está na parte superior; o sombreador turbulência está na parte inferior:

[![Ruído de Perli composto](noise-images/ComposedPerlinNoise.png "Ruído de Perli composto")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Observe a quantidade mais azul desses sombreadores que os exibidos pela página de **ruído do perlm** . A diferença ilustra a quantidade de transparência nos sombreadores de ruído.

Também há uma sobrecarga do [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) método:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

O parâmetro final é um membro da `SKBlendMode` enumeração, uma enumeração com 29 membros que é abordada na próxima série de artigos nos [**modos de composição e mesclagem SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)