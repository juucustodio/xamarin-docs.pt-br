---
title: Os gradientes circulares de SkiaSharp
description: Saiba mais sobre os diferentes tipos de gradientes com base em círculos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 08a79c7685b6e7d210baf11e99edcf928df88c67
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130851"
---
# <a name="the-skiasharp-circular-gradients"></a>Os gradientes circulares de SkiaSharp

O [ `SKShader` ](xref:SkiaSharp.SKShader) classe define os métodos estáticos para criar quatro tipos diferentes de gradientes. O [ **gradiente linear do SkiaSharp** ](linear-gradient.md) artigo discute os [ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) método. Este artigo aborda os outros três tipos de gradientes, que se baseiam em círculos.

O [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*) método cria um gradiente que provém do Centro de um círculo:

![Exemplo de gradiente radial](circular-gradients-images/RadialGradientSample.png)

O [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*) método cria um gradiente que varre em torno do Centro de um círculo:

![Exemplo de gradiente de varredura](circular-gradients-images/SweepGradientSample.png)

O terceiro tipo de gradiente é muito incomum. Ele é chamado de duas pontas cônica gradiente e é definido pela [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) método. Estende o gradiente de um único círculo para outro:

![Exemplo de gradiente cônica](circular-gradients-images/ConicalGradientSample.png)

Se os dois círculos têm tamanhos diferentes, o gradiente assume a forma de um cone.

Este artigo explora esses gradientes em mais detalhes.

## <a name="the-radial-gradient"></a>Gradiente radial

O [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tem a seguinte sintaxe:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Um [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga também inclui um parâmetro de matriz de transformação.

Os dois primeiros especificam o Centro de um círculo e um raio. O gradiente começa em que a Central e se estende para fora para `radius` pixels. O que acontece além `radius` depende do [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) argumento. O `colors` parâmetro é uma matriz de duas ou mais cores (assim como em lineares gradiente métodos), e `colorPos` é uma matriz de inteiros no intervalo de 0 a 1. Esses inteiros indicarem as posições relativas das cores ao longo do que `radius` linha. Você pode definir esse argumento como `null` espaçar igualmente as cores.

Se você usar `CreateRadialGradient` para preencher um círculo, você pode definir o centro do gradiente para o centro do círculo e o raio do gradiente para o raio do círculo. Nesse caso, o `SKShaderTileMode` argumento não tem efeito sobre a renderização do gradiente. Porém, se a área preenchida pelo gradiente é maior do que o círculo definido pelo gradiente, em seguida, a `SKShaderTileMode` argumento tem um efeito profundo sobre o que acontece fora do círculo.

O efeito de `SKShaderMode` é demonstrado na **gradiente Radial** página a [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo. O arquivo XAML para essa página instancia uma `Picker` que permite que você selecione um dos três membros do `SKShaderTileMode` enumeração:

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

Esse código cria um gradiente com preta no centro, esmaecida gradualmente para branco 100 pixels a partir do centro. O que acontece além desse radius depende o `SKShaderTileMode` argumento:

[![Gradiente radial](circular-gradients-images/RadialGradient.png "gradiente Radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Em todos os três casos, o gradiente preenche a tela. Na tela do iOS à esquerda, o gradiente além do raio continua com a última cor é branca. Que é o resultado de `SKShaderTileMode.Clamp`. A tela Android mostra o efeito de `SKShaderTileMode.Repeat`: em 100 pixels do centro do gradiente iniciado novamente com a primeira cor é preta. O gradiente se repete a cada 100 pixels do radius. 

Tela da plataforma Universal do Windows o direito mostra como `SKShaderTileMode.Mirror` faz com que os gradientes para direções alternativos. O primeiro gradiente é de preto no centro em branco em um raio de 100 pixels. A próxima é branca do raio de 100 pixels para preto em um raio de 200 pixels e Avançar gradiente é invertido novamente.

Você pode usar mais de duas cores em um gradiente radial. O **Rainbow arco gradiente** exemplo cria uma matriz de oito cores correspondente para as cores das rainbow e terminando com vermelho e uma matriz de oito valores de posição:

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

Suponha que o mínimo da largura e altura da tela é 1000, o que significa que o `rainbowWidth` valor é 250. O `outerRadius` e `innerRadius` valores são definidos como 1000 e 750, respectivamente. Esses valores são usados para calcular o `positions` matriz; o intervalo de oito valores de 0.75f a 1. O `radius` valor é usado para traçar um círculo. O valor de 875 significa que se estende a largura do traço de 250 pixels entre o raio de 750 pixels e o raio de 1000 pixels:

[![Gradiente de arco Rainbow](circular-gradients-images/RainbowArcGradient.png "Rainbow arco gradiente")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se você preencher a tela inteira com esse gradiente, veria que ela está vermelha dentro do raio interno. Isso ocorre porque o `positions` matriz não começa com 0. A primeira cor é usada para deslocamentos de 0 até o primeiro valor de matriz. O gradiente é também vermelho além do raio externo. Que é o resultado do `Clamp` modo lado a lado. Como o gradiente é usado para traçar uma linha espessa, essas áreas vermelhas não são visíveis.

## <a name="radial-gradients-for-masking"></a>Gradientes radiais para o mascaramento

Como gradientes lineares, gradientes radiais podem incorporar as cores transparentes ou parcialmente transparentes. Esse recurso é útil para um processo chamado _mascaramento_, que oculta a parte de uma imagem para destacar a outra parte da imagem.

O **máscara de gradiente Radial** página mostra um exemplo. O programa carrega um dos bitmaps de recurso. O `CENTER` e `RADIUS` campos foram determinados a partir de um exame do bitmap e fazer referência a uma área que deve ser realçada. O `PaintSurface` manipulador começa, calculando um retângulo para exibir o bitmap e o exibe no retângulo:

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

Depois de desenhar o bitmap, alguns códigos simples converte `CENTER` e `RADIUS` à `center` e `radius`, que se refere a na área realçada no bitmap que foi dimensionado e deslocado para exibição. Esses valores são usados para criar um gradiente radial com center e o radius. As duas cores começam em transparente no Centro de e para o primeiro 60% do raio. Em seguida, o gradiente fica branca:

[![Máscara de gradiente radial](circular-gradients-images/RadialGradientMask.png "máscara de gradiente Radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Essa abordagem não é a melhor maneira para mascarar um bitmap. O problema é que a máscara principalmente tem uma cor branca, que foi escolhida para coincidir com o plano de fundo da tela. Se o plano de fundo for alguma outra cor &mdash; ou talvez um gradiente em si &mdash; não corresponderá a ele. Uma abordagem melhor para o mascaramento é mostrada no artigo [SkiaSharp Porter-Duff modos do blend](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Gradientes radiais realces especulares

Quando uma luz atinge uma superfície arredondada, ele reflete a luz em muitas direções, mas algumas da luz bounces diretamente para os olhos do visualizador. Isso geralmente cria a aparência de uma área branca difusa na superfície de chamado de um _realce especular_.

Elementos gráficos tridimensionais, realces especulares geralmente resultam de algoritmos usados para determinar os caminhos de luz e sombreamento. Em gráficos bidimensionais, realces especulares, às vezes, são adicionados para sugerir a aparência de uma superfície 3D. Um realce especular pode transformar um círculo vermelho simples em uma bola vermelha redonda.

O **realce especular Radial** página usa um gradiente radial para fazer exatamente isso. O `PaintSurface` seres manipulador, calculando um raio para o círculo e duas `SKPoint` valores &mdash; um `center` e um `offCenter` que está a meio caminho entre o centro e a borda superior esquerda do círculo:

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

O `CreateRadialGradient` chamada cria um gradiente que começa em que `offCenter` ponto com branco e termina com vermelho em uma distância de metade do radius. Veja como ela se parece:

[![Realce especular radial](circular-gradients-images/RadialSpecularHighlight.png "realce especular Radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se você examinar de perto desse gradiente, você pode decidir que tem falhas. O gradiente é centralizado em torno de um ponto específico, e talvez você queira que ele fosse um pouco menos simétricos para refletir a superfície de cantos arredondada. Nesse caso, você pode preferir o realce especular mostrado abaixo na seção [ **cônica gradientes de realces especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>O gradiente de varredura

O [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) método tem a sintaxe mais simples de todos os métodos de criação de gradiente:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Ele é apenas um centro, uma matriz de cores e as posições de cor. O gradiente começa à direita do ponto central e varre 360 graus no sentido horário em torno do centro. Observe que não há nenhum `SKShaderTileMode` parâmetro.

Um [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) sobrecarga com um parâmetro de matriz de transformação também está disponível. Você pode aplicar uma transformação de rotação para o gradiente para alterar o ponto de partida. Você também pode aplicar uma transformação de escala para alterar a direção de no sentido horário para no sentido anti-horário.

O **varredura gradiente** página usa um gradiente de limpeza para um círculo com uma largura de traço de 50 pixels de cores:

[![Gradiente de varredura](circular-gradients-images/SweepGradient.png "gradiente de varredura")](circular-gradients-images/SweepGradient-Large.png#lightbox)

O `SweepGradientPage` classe define uma matriz de oito cores com valores de matiz diferentes. Observe que a matriz começa e termina com vermelho (um valor de matiz 0 ou 360), que é exibido na extremidade direita nas capturas de tela:

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

O programa também implementa um `TapGestureRecognizer` que permite algum código no final do `PaintSurface` manipulador. Esse código usa o mesmo gradiente para preencher a tela:

[![Varredura completa de gradiente](circular-gradients-images/SweepGradientFull.png "gradiente completo de varredura")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Essas capturas de tela demonstram que os preenchimentos com gradiente qualquer área é colorida por ele. Se o gradiente não começar e terminar com a mesma cor, haverá uma descontinuidade à direita do ponto central.

## <a name="the-two-point-conical-gradient"></a>O gradiente de cone de duas pontas

O [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) método tem a seguinte sintaxe:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Os parâmetros começam com pontos centrais e raios para dois círculos, conhecidos como o _inicie_ círculo e _final_ círculo. Os três parâmetros restantes são os mesmos para `CreateLinearGradient` e `CreateRadialGradient`. Um [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga inclui uma transformação de matriz.

O gradiente começa no início círculo e termina em círculo final. O `SKShaderTileMode` parâmetro controla o que acontece além dos dois círculos. O gradiente de cone de duas pontas é apenas gradação inteiramente não preenche uma área. Se os dois círculos tem o mesmo raio, o gradiente é restrito a um retângulo com uma largura que é o mesmo que o diâmetro dos círculos. Se os dois círculos tem raios diferentes, o gradiente constitui um cone.

É provável que você vai querer fazer experiências com o gradiente de cone de duas pontas, portanto, o **cônica gradiente** deriva de página `InteractivePage` para permitir que os dois pontos de toque de ser movidos para os dois raios de círculo:

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

O arquivo code-behind define dois `TouchPoint` objetos com raios fixos de 50 e 100:

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

O `colors` matriz é vermelho, verde e azul. O código na parte inferior do `PaintSurface` manipulador desenha os pontos de duas toque como preto círculos, de modo que elas não obstruir o gradiente.

Observe que `DrawRect` chamada usa o gradiente para colorir a tela inteira. Em geral, no entanto, grande parte da tela permanece sem cor, o gradiente. Aqui está o programa mostrando três configurações possíveis:

[![Gradiente cônica](circular-gradients-images/ConicalGradient.png "cônica gradiente")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

A tela do iOS à esquerda mostra o efeito do `SKShaderTileMode` configuração de `Clamp`. O gradiente começa com vermelho dentro da extremidade do círculo menor do que é o oposto do lado mais próximo ao segundo círculo. O `Clamp` também faz com que o valor de vermelho para continuar para o ponto do cone. O gradiente termina com azul na borda externa do círculo maior do que está mais próximo ao primeiro círculo, mas continua com azul dentro desse círculo e muito mais.

A tela do Android é semelhante, mas com um `SKShaderTileMode` de `Repeat`. Agora é mais clara do que o gradiente começa dentro do círculo primeiro e termina fora do círculo de segundo. O `Repeat` configuração faz com que o gradiente Repita novamente com vermelho dentro do círculo maior.

A tela UWP mostra o que acontece quando é movido para o círculo menor inteiramente dentro do círculo maior. O gradiente deixa de ser um cone e, em vez disso, preenche toda a área. O efeito é semelhante ao gradiente radial, mas é assimétrica, se o círculo menor não exatamente é centralizado dentro do círculo maior.

Quando um círculo é aninhado em outro, mas ele é ideal para um realce especular, você talvez dúvidas a utilidade prática do gradiente.

## <a name="conical-gradients-for-specular-highlights"></a>Cônica gradientes de realces especulares

Neste artigo, você viu como usar um gradiente radial para criar um realce especular. Você também pode usar o gradiente de cone de duas pontas para essa finalidade, e talvez você prefira a aparência dele:

[![Realce especular cônica](circular-gradients-images/ConicalSpecularHighlight.png "cônica realce especular")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

A aparência assimétrica melhor sugere a superfície arredondada do objeto. 

O código do desenho a **realce especular cônica** página é o mesmo que o **realce especular Radial** página exceto o sombreador:

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

Os dois círculos tem centros das `offCenter` e `center`. O círculo centralizado no `center` está associado com um raio que abrange a bola inteira, mas o círculo centralizado no `offCenter` com um raio de apenas um pixel. O gradiente efetivamente começa nesse ponto e termina na borda da bola.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
