---
title: Os gradientes circulares de SkiaSharp
description: Saiba mais sobre os diferentes tipos de gradientes com base em círculos.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304173"
---
# <a name="the-skiasharp-circular-gradients"></a>Os gradientes circulares de SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

A classe [`SKShader`](xref:SkiaSharp.SKShader) define métodos estáticos para criar quatro tipos diferentes de gradientes. O artigo de [**gradiente linear SkiaSharp**](linear-gradient.md) discute o método [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) . Este artigo aborda os outros três tipos de gradientes, que se baseiam em círculos.

O método [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) cria um gradiente que emana do centro de um círculo:

![Exemplo de gradiente radial](circular-gradients-images/RadialGradientSample.png)

O método [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) cria um gradiente que varre em todo o centro de um círculo:

![Exemplo de gradiente de varredura](circular-gradients-images/SweepGradientSample.png)

O terceiro tipo de gradiente é muito incomum. Ele é chamado de gradiente cônica de dois pontos e é definido pelo método [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) . Estende o gradiente de um único círculo para outro:

![Exemplo de gradiente cônica](circular-gradients-images/ConicalGradientSample.png)

Se os dois círculos têm tamanhos diferentes, o gradiente assume a forma de um cone.

Este artigo explora esses gradientes em mais detalhes.

## <a name="the-radial-gradient"></a>Gradiente radial

O método [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) tem a seguinte sintaxe:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Uma sobrecarga de [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) também inclui um parâmetro de matriz de transformação.

Os dois primeiros especificam o Centro de um círculo e um raio. O gradiente começa nesse centro e se estende para fora para `radius` pixels. O que acontece além `radius` depende do argumento [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) . O parâmetro `colors` é uma matriz de duas ou mais cores (assim como nos métodos de gradiente linear) e `colorPos` é uma matriz de inteiros no intervalo de 0 a 1. Esses inteiros indicam as posições relativas das cores ao longo dessa `radius` linha. Você pode definir esse argumento como `null` para espaçar igualmente as cores.

Se você usar `CreateRadialGradient` para preencher um círculo, poderá definir o centro do gradiente para o centro do círculo e o raio do gradiente para o raio do círculo. Nesse caso, o argumento `SKShaderTileMode` não tem efeito sobre a renderização do gradiente. Mas se a área preenchida pelo gradiente for maior do que o círculo definido pelo gradiente, o argumento `SKShaderTileMode` terá um efeito profundo sobre o que acontece fora do círculo.

O efeito da `SKShaderMode` é demonstrado na página **gradiente radial** no exemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . O arquivo XAML para esta página cria uma instância de um `Picker` que permite selecionar um dos três membros da enumeração `SKShaderTileMode`:

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

Esse código cria um gradiente com preta no centro, esmaecida gradualmente para branco 100 pixels a partir do centro. O que acontece além desse raio depende do argumento `SKShaderTileMode`:

[![Gradiente radial](circular-gradients-images/RadialGradient.png "Gradiente radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Em todos os três casos, o gradiente preenche a tela. Na tela do iOS à esquerda, o gradiente além do raio continua com a última cor é branca. Esse é o resultado de `SKShaderTileMode.Clamp`. A tela do Android mostra o efeito de `SKShaderTileMode.Repeat`: a 100 pixels do centro, o gradiente começa novamente com a primeira cor, que é preta. O gradiente se repete a cada 100 pixels do radius. 

A tela de Plataforma Universal do Windows à direita mostra como `SKShaderTileMode.Mirror` faz com que os gradientes sejam alternados para direções. O primeiro gradiente é de preto no centro em branco em um raio de 100 pixels. A próxima é branca do raio de 100 pixels para preto em um raio de 200 pixels e Avançar gradiente é invertido novamente.

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

Suponha que o mínimo da largura e da altura da tela seja 1000, o que significa que o valor de `rainbowWidth` é 250. Os valores `outerRadius` e `innerRadius` são definidos como 1000 e 750, respectivamente. Esses valores são usados para calcular a matriz de `positions`; os oito valores variam de 0,75 f a 1. O valor de `radius` é usado para traçar o círculo. O valor de 875 significa que se estende a largura do traço de 250 pixels entre o raio de 750 pixels e o raio de 1000 pixels:

[![Gradiente de arco-íris](circular-gradients-images/RainbowArcGradient.png "Gradiente de arco-íris")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se você preencher a tela inteira com esse gradiente, veria que ela está vermelha dentro do raio interno. Isso ocorre porque a matriz de `positions` não começa com 0. A primeira cor é usada para deslocamentos de 0 até o primeiro valor de matriz. O gradiente é também vermelho além do raio externo. Esse é o resultado do modo de bloco `Clamp`. Como o gradiente é usado para traçar uma linha espessa, essas áreas vermelhas não são visíveis.

## <a name="radial-gradients-for-masking"></a>Gradientes radiais para o mascaramento

Como gradientes lineares, gradientes radiais podem incorporar as cores transparentes ou parcialmente transparentes. Esse recurso é útil para um processo chamado _mascaramento_, que oculta parte de uma imagem para acentuar outra parte da imagem.

A página **máscara de gradiente radial** mostra um exemplo. O programa carrega um dos bitmaps de recurso. Os campos `CENTER` e `RADIUS` foram determinados de um exame do bitmap e fazem referência a uma área que deve ser realçada. O manipulador de `PaintSurface` começa calculando um retângulo para exibir o bitmap e, em seguida, o exibe nesse retângulo:

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

Depois de desenhar o bitmap, um código simples converte `CENTER` e `RADIUS` para `center` e `radius`, que se referem à área realçada no bitmap que foi dimensionado e deslocado para exibição. Esses valores são usados para criar um gradiente radial com center e o radius. As duas cores começam em transparente no Centro de e para o primeiro 60% do raio. Em seguida, o gradiente fica branca:

[![Máscara de gradiente radial](circular-gradients-images/RadialGradientMask.png "Máscara de gradiente radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Essa abordagem não é a melhor maneira para mascarar um bitmap. O problema é que a máscara principalmente tem uma cor branca, que foi escolhida para coincidir com o plano de fundo da tela. Se o plano de fundo for outro &mdash; de cor ou talvez o próprio gradiente &mdash; ele não corresponderá. Uma abordagem melhor para mascaramento é mostrada no artigo [SkiaSharp carregador-Duff Blend Modes](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Gradientes radiais realces especulares

Quando uma luz atinge uma superfície arredondada, ele reflete a luz em muitas direções, mas algumas da luz bounces diretamente para os olhos do visualizador. Isso geralmente cria a aparência de uma área branca difusa na superfície chamada de _realce especular_.

Elementos gráficos tridimensionais, realces especulares geralmente resultam de algoritmos usados para determinar os caminhos de luz e sombreamento. Em gráficos bidimensionais, realces especulares, às vezes, são adicionados para sugerir a aparência de uma superfície 3D. Um realce especular pode transformar um círculo vermelho simples em uma bola vermelha redonda.

A página de **realce de especula radial** usa um gradiente radial para fazer exatamente isso. O manipulador de `PaintSurface` se aplica ao cálculo de um raio para o círculo e dois valores de `SKPoint` &mdash; um `center` e um `offCenter` que é metade entre o centro e a borda superior esquerda do círculo:

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

A chamada `CreateRadialGradient` cria um gradiente que começa nesse ponto de `offCenter` com branco e termina com vermelho a uma distância da metade do raio. Veja como é sua aparência:

[![Realce de especula radial](circular-gradients-images/RadialSpecularHighlight.png "Realce de especula radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se você examinar de perto desse gradiente, você pode decidir que tem falhas. O gradiente é centralizado em torno de um ponto específico, e talvez você queira que ele fosse um pouco menos simétricos para refletir a superfície de cantos arredondada. Nesse caso, você pode preferir o realce especular mostrado abaixo na seção [**gradientes cônicas para realces especulares**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>O gradiente de varredura

O método [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) tem a sintaxe mais simples de todos os métodos de criação de gradiente:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Ele é apenas um centro, uma matriz de cores e as posições de cor. O gradiente começa à direita do ponto central e varre 360 graus no sentido horário em torno do centro. Observe que não há `SKShaderTileMode` parâmetro.

Uma sobrecarga de [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) com um parâmetro de transformação de matriz também está disponível. Você pode aplicar uma transformação de rotação para o gradiente para alterar o ponto de partida. Você também pode aplicar uma transformação de escala para alterar a direção de no sentido horário para no sentido anti-horário.

A página **gradação de limpeza** usa um gradiente de limpeza para colorir um círculo com uma largura de traço de 50 pixels:

[![Gradação de limpeza](circular-gradients-images/SweepGradient.png "Gradação de limpeza")](circular-gradients-images/SweepGradient-Large.png#lightbox)

A classe `SweepGradientPage` define uma matriz de oito cores com valores de matiz diferentes. Observe que a matriz começa e termina com vermelho (um valor de matiz 0 ou 360), que é exibido na extremidade direita nas capturas de tela:

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

O programa também implementa um `TapGestureRecognizer` que habilita algum código no final do manipulador de `PaintSurface`. Esse código usa o mesmo gradiente para preencher a tela:

[![Gradiente de limpeza completo](circular-gradients-images/SweepGradientFull.png "Gradiente de limpeza completo")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Essas capturas de tela demonstram que os preenchimentos com gradiente qualquer área é colorida por ele. Se o gradiente não começar e terminar com a mesma cor, haverá uma descontinuidade à direita do ponto central.

## <a name="the-two-point-conical-gradient"></a>O gradiente de cone de duas pontas

O método [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) tem a seguinte sintaxe:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Os parâmetros começam com pontos centrais e raios para dois círculos, chamados de círculo de _início_ e _fim_ . Os três parâmetros restantes são os mesmos para `CreateLinearGradient` e `CreateRadialGradient`. Uma sobrecarga de [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) inclui uma transformação de matriz.

O gradiente começa no início círculo e termina em círculo final. O parâmetro `SKShaderTileMode` rege o que acontece além dos dois círculos. O gradiente de cone de duas pontas é apenas gradação inteiramente não preenche uma área. Se os dois círculos tem o mesmo raio, o gradiente é restrito a um retângulo com uma largura que é o mesmo que o diâmetro dos círculos. Se os dois círculos tem raios diferentes, o gradiente constitui um cone.

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

O arquivo code-behind define os dois objetos `TouchPoint` com raios fixos de 50 e 100:

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

A matriz de `colors` é vermelha, verde e azul. O código em direção à parte inferior do manipulador de `PaintSurface` desenha os dois pontos de toque como círculos pretos para que eles não obstruam o gradiente.

Observe que `DrawRect` chamada usa o gradiente para colorir a tela inteira. Em geral, no entanto, grande parte da tela permanece sem cor, o gradiente. Aqui está o programa mostrando três configurações possíveis:

[![Gradiente cônica](circular-gradients-images/ConicalGradient.png "Gradiente cônica")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

A tela do iOS à esquerda mostra o efeito da configuração de `SKShaderTileMode` de `Clamp`. O gradiente começa com vermelho dentro da extremidade do círculo menor do que é o oposto do lado mais próximo ao segundo círculo. O valor de `Clamp` também faz com que o vermelho continue até o ponto do cone. O gradiente termina com azul na borda externa do círculo maior do que está mais próximo ao primeiro círculo, mas continua com azul dentro desse círculo e muito mais.

A tela do Android é semelhante, mas com uma `SKShaderTileMode` de `Repeat`. Agora é mais clara do que o gradiente começa dentro do círculo primeiro e termina fora do círculo de segundo. A configuração `Repeat` faz com que o gradiente repita novamente com vermelho dentro do círculo maior.

A tela UWP mostra o que acontece quando é movido para o círculo menor inteiramente dentro do círculo maior. O gradiente deixa de ser um cone e, em vez disso, preenche toda a área. O efeito é semelhante ao gradiente radial, mas é assimétrica, se o círculo menor não exatamente é centralizado dentro do círculo maior.

Quando um círculo é aninhado em outro, mas ele é ideal para um realce especular, você talvez dúvidas a utilidade prática do gradiente.

## <a name="conical-gradients-for-specular-highlights"></a>Cônica gradientes de realces especulares

Neste artigo, você viu como usar um gradiente radial para criar um realce especular. Você também pode usar o gradiente de cone de duas pontas para essa finalidade, e talvez você prefira a aparência dele:

[![Realce de especular cônica](circular-gradients-images/ConicalSpecularHighlight.png "Realce de especular cônica")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

A aparência assimétrica melhor sugere a superfície arredondada do objeto. 

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

Os dois círculos têm centros de `offCenter` e `center`. O círculo centralizado em `center` está associado a um raio que abrange toda a bola, mas o círculo centralizado em `offCenter` tem um raio de apenas um pixel. O gradiente efetivamente começa nesse ponto e termina na borda da bola.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
