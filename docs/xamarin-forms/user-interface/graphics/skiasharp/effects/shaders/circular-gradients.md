---
title: Os gradientes circulares do SkiaSharp
description: Saiba mais sobre os diferentes tipos de gradientes com base em círculos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 418d29010a8cce81d2bb8c365608c54b61739622
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135636"
---
# <a name="the-skiasharp-circular-gradients"></a>Os gradientes circulares do SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

A [`SKShader`](xref:SkiaSharp.SKShader) classe define métodos estáticos para criar quatro tipos diferentes de gradientes. O artigo [**gradiente linear SkiaSharp**](linear-gradient.md) discute o [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) método. Este artigo aborda os outros três tipos de gradientes, todos baseados em círculos.

O [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) método cria um gradiente que emana do centro de um círculo:

![Exemplo de gradiente radial](circular-gradients-images/RadialGradientSample.png)

O [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) método cria um gradiente que é varrido em todo o centro de um círculo:

![Exemplo de gradação de varredura](circular-gradients-images/SweepGradientSample.png)

O terceiro tipo de gradiente é bastante incomum. Ele é chamado de gradiente cônica de dois pontos e é definido pelo [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) método. O gradiente se estende de um círculo para outro:

![Amostra de gradiente cônica](circular-gradients-images/ConicalGradientSample.png)

Se os dois círculos forem tamanhos diferentes, o gradiente assume a forma de um cone.

Este artigo explora esses gradientes mais detalhadamente.

## <a name="the-radial-gradient"></a>O gradiente radial

O [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tem a seguinte sintaxe:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Uma [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga também inclui um parâmetro de matriz de transformação.

Os dois primeiros argumentos especificam o centro de um círculo e um raio. O gradiente começa nesse centro e se estende para fora para `radius` pixels. O que acontece além `radius` depende do [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) argumento. O `colors` parâmetro é uma matriz de duas ou mais cores (assim como nos métodos de gradiente linear) e `colorPos` é uma matriz de inteiros no intervalo de 0 a 1. Esses inteiros indicam as posições relativas das cores ao longo dessa `radius` linha. Você pode definir esse argumento como `null` para espaçar igualmente as cores.

Se você usar `CreateRadialGradient` o para preencher um círculo, poderá definir o centro do gradiente para o centro do círculo e o raio do gradiente para o raio do círculo. Nesse caso, o `SKShaderTileMode` argumento não tem nenhum efeito sobre a renderização do gradiente. Mas se a área preenchida pelo gradiente for maior do que o círculo definido pelo gradiente, o `SKShaderTileMode` argumento terá um efeito profundo sobre o que acontece fora do círculo.

O efeito de `SKShaderMode` é demonstrado na página **gradiente radial** no exemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . O arquivo XAML para esta página cria uma instância de um `Picker` que permite que você selecione um dos três membros da `SKShaderTileMode` enumeração:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

O arquivo code-behind colore a tela inteira com um gradiente radial. O centro do gradiente é definido como o centro da tela e o raio é definido como 100 pixels. O gradiente consiste em apenas duas cores, preto e branco:

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Esse código cria um gradiente com preto no centro, esmaecido gradualmente até o White 100 pixels do centro. O que acontece além desse raio depende do `SKShaderTileMode` argumento:

[![Gradiente radial](circular-gradients-images/RadialGradient.png "Gradiente radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Em todos os três casos, o gradiente preenche a tela. Na tela do iOS à esquerda, o gradiente além do raio continua com a última cor, que é branco. Esse é o resultado de `SKShaderTileMode.Clamp` . A tela do Android mostra o efeito de `SKShaderTileMode.Repeat` : a 100 pixels do centro, o gradiente começa novamente com a primeira cor, que é preta. O gradiente se repete a cada 100 pixels de raio. 

A tela de Plataforma Universal do Windows à direita mostra como `SKShaderTileMode.Mirror` faz com que os gradientes sejam alternados para direções. O primeiro gradiente é de preto no centro para branco em um raio de 100 pixels. O próximo é branco do raio de 100-pixel para preto em um raio de 200 pixels e o próximo gradiente é revertido novamente.

Você pode usar mais de duas cores em um gradiente radial. O exemplo de **gradiente de arco arco-íris** cria uma matriz de oito cores correspondentes às cores do arco-íris e terminando com vermelho e também uma matriz de oito valores de posição:

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Suponha que o mínimo da largura e da altura da tela seja 1000, o que significa que o `rainbowWidth` valor é 250. Os `outerRadius` `innerRadius` valores e são definidos como 1000 e 750, respectivamente. Esses valores são usados para calcular a `positions` matriz; os oito valores variam de 0,75 f a 1. O `radius` valor é usado para traçar o círculo. O valor de 875 significa que a largura do traço de 250 pixels se estende entre o raio de 750 pixels e o raio de 1000 pixels:

[![Gradiente de arco-íris](circular-gradients-images/RainbowArcGradient.png "Gradiente de arco-íris")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se você preencher toda a tela com esse gradiente, verá que ela é vermelha no raio interno. Isso ocorre porque a `positions` matriz não começa com 0. A primeira cor é usada para deslocamentos de 0 por meio do primeiro valor de matriz. O gradiente também está vermelho além do raio externo. Esse é o resultado do `Clamp` modo de bloco. Como o gradiente é usado para traçar uma linha espessa, essas áreas vermelhas não são visíveis.

## <a name="radial-gradients-for-masking"></a>Gradientes radiais para mascaramento

Como gradientes lineares, gradientes radiais podem incorporar cores transparentes ou parcialmente transparentes. Esse recurso é útil para um processo chamado _mascaramento_, que oculta parte de uma imagem para acentuar outra parte da imagem.

A página **máscara de gradiente radial** mostra um exemplo. O programa carrega um dos bitmaps de recurso. Os `CENTER` `RADIUS` campos e foram determinados de um exame do bitmap e fazem referência a uma área que deve ser realçada. O `PaintSurface` manipulador começa calculando um retângulo para exibir o bitmap e, em seguida, o exibe nesse retângulo:

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Depois de desenhar o bitmap, alguns códigos simples são convertidos `CENTER` e `RADIUS` em `center` e `radius` , que se referem à área realçada no bitmap que foi dimensionado e deslocado para exibição. Esses valores são usados para criar um gradiente radial com esse centro e o raio. As duas cores começam em transparente no centro e para o primeiro 60% do raio. Em seguida, o gradiente desaparece para o branco:

[![Máscara de gradiente radial](circular-gradients-images/RadialGradientMask.png "Máscara de gradiente radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Essa abordagem não é a melhor maneira de mascarar um bitmap. O problema é que a máscara tem, na maioria, uma cor de branco, que foi escolhida para corresponder ao plano de fundo da tela. Se o plano de fundo for outra cor &mdash; ou talvez um gradiente &mdash; , ele não corresponderá. Uma abordagem melhor para mascaramento é mostrada no artigo [SkiaSharp carregador-Duff Blend Modes](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Gradientes radiais para realces especulares

Quando uma luz dura uma superfície arredondada, ela reflete a luz em muitas direções, mas algumas das luzes se resaltam diretamente para o olho do visualizador. Isso geralmente cria a aparência de uma área branca difusa na superfície chamada de _realce especular_.

Em gráficos tridimensionais, os realces especulares geralmente resultam dos algoritmos usados para determinar os caminhos leves e o sombreamento. Em gráficos bidimensionais, às vezes, os realces especulares são adicionados para sugerir a aparência de uma superfície 3D. Um realce especular pode transformar um círculo vermelho simples em uma bola vermelha redonda.

A página de **realce de especula radial** usa um gradiente radial para fazer exatamente isso. O `PaintSurface` manipulador se aplica ao cálculo de um raio para o círculo e `SKPoint` a dois valores &mdash; a `center` e `offCenter` a metade entre o centro e a borda superior esquerda do círculo:

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

A `CreateRadialGradient` chamada cria um gradiente que começa nesse `offCenter` ponto com branco e termina com vermelho a uma distância da metade do raio. Veja como ela se parece:

[![Realce de especula radial](circular-gradients-images/RadialSpecularHighlight.png "Realce de especula radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se você olhar mais de acordo com esse gradiente, poderá decidir se ele tem falhas. O gradiente é centralizado em um ponto específico e talvez você queira que ele fosse um pouco menos simétrico para refletir a superfície arredondada. Nesse caso, você pode preferir o realce especular mostrado abaixo na seção [**gradientes cônicas para realces especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>O gradiente de limpeza

O [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) método tem a sintaxe mais simples de todos os métodos de criação de gradiente:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

É apenas um centro, uma matriz de cores e as posições de cor. O gradiente começa à direita do ponto central e varre 360 graus no sentido horário em todo o centro. Observe que não há nenhum `SKShaderTileMode` parâmetro.

Uma [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) sobrecarga com um parâmetro de transformação de matriz também está disponível. Você pode aplicar uma transformação de rotação ao gradiente para alterar o ponto de partida. Você também pode aplicar uma transformação de escala para alterar a direção do sentido horário para o sentido anti-horário.

A página **gradação de limpeza** usa um gradiente de limpeza para colorir um círculo com uma largura de traço de 50 pixels:

[![Gradação de limpeza](circular-gradients-images/SweepGradient.png "Gradação de limpeza")](circular-gradients-images/SweepGradient-Large.png#lightbox)

A `SweepGradientPage` classe define uma matriz de oito cores com valores de matiz diferentes. Observe que a matriz começa e termina com vermelho (um valor de matiz 0 ou 360), que aparece na extrema direita das capturas de tela:

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

O programa também implementa um `TapGestureRecognizer` que habilita algum código no final do `PaintSurface` manipulador. Esse código usa o mesmo gradiente para preencher a tela:

[![Gradiente de limpeza completo](circular-gradients-images/SweepGradientFull.png "Gradiente de limpeza completo")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Essas capturas de tela demonstram que o gradiente preenche qualquer área colorida. Se o gradiente não começar e terminar com a mesma cor, haverá uma descontinuidade à direita do ponto central.

## <a name="the-two-point-conical-gradient"></a>O gradiente cônica de dois pontos

O [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tem a seguinte sintaxe:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Os parâmetros começam com pontos centrais e raios para dois círculos, chamados de círculo de _início_ e _fim_ . Os três parâmetros restantes são os mesmos para `CreateLinearGradient` e `CreateRadialGradient` . Uma [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga inclui uma transformação de matriz.

O gradiente começa no círculo inicial e termina no círculo final. O `SKShaderTileMode` parâmetro controla o que acontece além dos dois círculos. O gradiente cônica de dois pontos é o único gradiente que não preenche totalmente uma área. Se os dois círculos tiverem o mesmo raio, o gradiente será restrito a um retângulo com uma largura igual ao diâmetro dos círculos. Se os dois círculos tiverem raios diferentes, o gradiente forma um cone.

É provável que você queira experimentar o gradiente cônica de dois pontos, de modo que a página **gradiente cônica** deriva de `InteractivePage` para permitir que dois pontos de toque sejam movidos para o raios de dois círculos:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

O arquivo code-behind define os dois `TouchPoint` objetos com raios fixos de 50 e 100:

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

A `colors` matriz é vermelha, verde e azul. O código em direção à parte inferior do `PaintSurface` manipulador desenha os dois pontos de toque como círculos pretos para que eles não obstruam o gradiente.

Observe que a `DrawRect` chamada usa o gradiente para colorir a tela inteira. No entanto, no caso geral, grande parte da tela permanece descolorido pelo gradiente. Aqui está o programa mostrando três configurações possíveis:

[![Gradiente cônica](circular-gradients-images/ConicalGradient.png "Gradiente cônica")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

A tela do iOS à esquerda mostra o efeito da `SKShaderTileMode` configuração de `Clamp` . O gradiente começa com vermelho dentro da borda do círculo menor que é oposto ao lado mais próximo do segundo círculo. O `Clamp` valor também faz com que o vermelho continue até o ponto do cone. O gradiente termina com azul na borda externa do círculo maior que é mais próximo do primeiro círculo, mas continua com azul dentro desse círculo e além disso.

A tela do Android é semelhante, mas com um `SKShaderTileMode` de `Repeat` . Agora é mais claro que o gradiente começa dentro do primeiro círculo e termina fora do segundo círculo. A `Repeat` configuração faz com que o gradiente repita novamente com vermelho dentro do círculo maior.

A tela UWP mostra o que acontece quando o círculo menor é movido inteiramente dentro do círculo maior. O gradiente pára de ser um cone e, em vez disso, preenche toda a área. O efeito é semelhante ao gradiente radial, mas é assimétrico se o círculo menor não estiver exatamente centralizado no círculo maior.

Você pode ter dúvidas quanto à utilidade prática do gradiente quando um círculo está aninhado em outro, mas é ideal para um realce especular.

## <a name="conical-gradients-for-specular-highlights"></a>Gradientes cônicas para realces especulares

Anteriormente neste artigo, você viu como usar um gradiente radial para criar um realce especular. Você também pode usar o gradiente cônica de dois pontos para essa finalidade, e você pode preferir como ele parece:

[![Realce de especular cônica](circular-gradients-images/ConicalSpecularHighlight.png "Realce de especular cônica")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

A aparência assimétrica sugere melhor a superfície arredondada do objeto. 

O código de desenho na página de **realce especular cônicas** é o mesmo que a página de **realce especular radial** , exceto pelo sombreador:

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Os dois círculos têm centros de `offCenter` e `center` . O círculo centralizado em `center` está associado a um raio que abrange toda a bola, mas o círculo centralizado em `offCenter` tem um raio de apenas um pixel. O gradiente começa com eficiência nesse ponto e termina na borda da bola.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
