---
title: Gradiente linear SkiaSharp
description: Descubra como traçar as linhas ou áreas de preenchimento com gradientes composto por uma mesclagem gradual de duas cores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 290e533e54b2ee150b94d9fb6b0f5119324f9cf0
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305405"
---
# <a name="the-skiasharp-linear-gradient"></a>Gradiente linear SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

A classe [`SKPaint`](xref:SkiaSharp.SKPaint) define uma propriedade [`Color`](xref:SkiaSharp.SKPaint.Color) que é usada para traçar linhas ou áreas de preenchimento com uma cor sólida. Você pode, como alternativa, traçar linhas ou preencher áreas com _gradientes_, que são misturas graduais de cores:

![Exemplo de gradiente linear](linear-gradient-images/LinearGradientSample.png "Exemplo de gradiente linear")

O tipo de gradiente mais básico é um gradiente _linear_ . O Blend of Colors ocorre em uma linha (chamada de _linha de gradiente_) de um ponto para outro. Linhas perpendiculares à linha de gradiente têm a mesma cor. Você cria um gradiente linear usando um dos dois métodos de [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) estáticos. A diferença entre as duas sobrecargas é que uma inclui uma transformação de matriz e o outro não. 

Esses métodos retornam um objeto do tipo [`SKShader`](xref:SkiaSharp.SKShader) que você define para a propriedade [`Shader`](xref:SkiaSharp.SKPaint.Shader) de `SKPaint`. Se a propriedade `Shader` for não nula, ela substituirá a propriedade `Color`. Qualquer linha que seja cortada ou qualquer área preenchida usando este `SKPaint` objeto é baseada no gradiente, em vez da cor sólida.

> [!NOTE]
> A propriedade `Shader` é ignorada quando você inclui um objeto `SKPaint` em uma chamada `DrawBitmap`. Você pode usar a propriedade `Color` de `SKPaint` para definir um nível de transparência para exibir um bitmap (conforme descrito no artigo [exibindo bitmaps SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), mas não pode usar a propriedade `Shader` para exibir um bitmap com uma transparência de gradiente. Outras técnicas estão disponíveis para exibir bitmaps com transparências de gradiente: elas são descritas nos modos de exibição [SkiaSharp gradientes circulares](circular-gradients.md#radial-gradients-for-masking) e [SkiaSharp e mesclagem](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Canto ao outro gradientes

Muitas vezes um gradiente linear se estende de um dos cantos de um retângulo para outro. Se o ponto de partida é o canto superior esquerdo do retângulo, pode estender o gradiente:

- verticalmente para o canto inferior esquerdo
- horizontal para o canto superior direito
- diagonalmente para o canto inferior direito

O gradiente linear diagonal é demonstrado na primeira página na seção **sombreadores SkiaSharp e outros efeitos** do exemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . A página **gradiente de canto a canto** cria um `SKCanvasView` em seu construtor. O manipulador de `PaintSurface` cria um objeto `SKPaint` em uma instrução `using` e, em seguida, define um retângulo quadrado de 300 pixels centralizado na tela:

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

A propriedade `Shader` de `SKPaint` é atribuída ao valor de retorno de `SKShader` do método de `SKShader.CreateLinearGradient` estático. Os cinco argumentos são da seguinte maneira:

- O ponto inicial do gradiente, definidos aqui para o canto superior esquerdo do retângulo
- O ponto de extremidade do gradiente, definidos aqui para o canto inferior direito do retângulo
- Uma matriz de duas ou mais cores que contribuem para o gradiente
- Uma matriz de valores `float` indicando a posição relativa das cores dentro da linha de gradiente
- Um membro da enumeração de [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) indicando como o gradiente se comporta além das extremidades da linha de gradiente

Depois que o objeto de gradiente é criado, o método `DrawRect` desenha o retângulo quadrado de 300 pixels usando o objeto `SKPaint` que inclui o sombreador. Aqui ele está em execução no iOS, Android e Universal Windows Platform (UWP):

[![Gradiente de canto a canto](linear-gradient-images/CornerToCornerGradient.png "Gradiente de canto a canto")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

A linha gradiente é definida pelos dois pontos especificados como os primeiros dois argumentos. Observe que esses pontos são relativos à _tela_ e _não_ ao objeto gráfico exibido com o gradiente. Ao longo da linha de gradiente, a cor gradualmente faz a transição de vermelho no canto superior esquerdo para azul no canto inferior direito. Qualquer linha perpendicular à linha de gradiente tem uma cor constante.

A matriz de valores `float` especificada como o quarto argumento tem uma correspondência um-para-um com a matriz de cores. Os valores indicam a posição relativa ao longo da linha gradiente onde ocorrem essas cores. Aqui, o 0 significa que `Red` ocorre no início da linha de gradiente e 1 significa que `Blue` ocorre no final da linha. Os números devem estar em ordem crescente e devem estar no intervalo de 0 a 1. Se não estiverem nesse intervalo, eles serão ajustados para ser nesse intervalo.

Os dois valores na matriz podem ser definidos como algo diferente de 0 e 1. Experimente isto:

```csharp
new float[] { 0.25f, 0.75f }
```

Agora totalmente primeiro trimestre de linha de gradiente é vermelho puro, e o último trimestre é azul puro. A mistura de vermelho e azul é restrita a central na metade da linha de gradiente.

Em geral, você desejará espaço esses valores de posição igualmente de 0 a 1. Se esse for o caso, você pode simplesmente fornecer `null` como o quarto argumento para `CreateLinearGradient`.

Embora esse gradiente é definido entre dois cantos do retângulo 300 pixels quadrado, não é restrito para preencher o retângulo. A página **gradiente de canto a canto** inclui um código extra que responde a toques ou cliques do mouse na página. O campo `drawBackground` é alternado entre `true` e `false` com cada toque. Se o valor for `true`, o manipulador de `PaintSurface` usará o mesmo objeto `SKPaint` para preencher a tela inteira e, em seguida, desenhará um retângulo preto indicando o retângulo menor: 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
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
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

Aqui está o que você verá depois de tocar a tela:

[![Gradiente de canto a canto cheio](linear-gradient-images/CornerToCornerGradientFull.png "Gradiente de canto a canto cheio")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Observe que o gradiente repete-se no mesmo padrão além de pontos que define a linha com gradiente. Essa repetição ocorre porque o último argumento para `CreateLinearGradient` é `SKShaderTileMode.Repeat`. (Você verá as outras opções em breve.)

Além disso, observe que os pontos que você usa para especificar a linha de gradação não são exclusivos. Linhas perpendiculares à linha de gradiente têm a mesma cor, portanto, há um número infinito de gradiente linhas que você pode especificar para o mesmo efeito. Por exemplo, ao preencher um retângulo com um gradiente horizontal, você pode especificar os cantos superior esquerdo e no canto superior direito, ou os cantos inferior esquerdo e inferior direita ou dois pontos que estão, mesmo com e paralela para essas linhas.

## <a name="interactively-experiment"></a>Testar interativamente

Você pode experimentar interativamente com gradientes lineares com a página **gradiente linear interativo** . Esta página usa a classe `InteractivePage` introduzida no artigo [**três maneiras de desenhar um arco**](../../curves/arcs.md). `InteractivePage` manipula [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) eventos para manter uma coleção de objetos `TouchPoint` que você pode mover com os dedos ou com o mouse.

O arquivo XAML anexa o `TouchEffect` a um pai do `SKCanvasView` e também inclui uma `Picker` que permite que você selecione um dos três membros da enumeração [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
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

O Construtor no arquivo code-behind cria dois objetos `TouchPoint` para os pontos inicial e final do gradiente linear. O manipulador de `PaintSurface` define uma matriz de três cores (para um gradiente de vermelho a verde a azul) e obtém a `SKShaderTileMode` atual da `Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

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
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

O manipulador de `PaintSurface` cria o objeto de `SKShader` de todas essas informações e a usa para colorir a tela inteira. A matriz de valores de `float` é definida como `null`. Caso contrário, para espaçar igualmente três cores, definir esse parâmetro para uma matriz com os valores 0, 0,5 e 1.

A maior parte do manipulador de `PaintSurface` é dedicada à exibição de vários objetos: os pontos de toque como círculos de contorno, a linha de gradiente e as linhas perpendiculares às linhas de gradiente nos pontos de toque:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
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

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

A linha com gradiente conectando os dois pontos de contato é fácil desenhar, mas as linhas perpendiculares requerem algum trabalho mais. Da linha do gradiente é convertida em um vetor, normalizada para ter um comprimento de uma unidade e, em seguida, girada em 90 graus. Esse vetor, em seguida, recebe um comprimento de 200 pixels. Ele é usado para desenhar quatro linhas que se estendem dos pontos de toque para ser perpendicular à linha de gradiente.

As linhas perpendiculares coincidirem com o início e no final do gradiente. O que acontece além dessas linhas depende da configuração da enumeração de `SKShaderTileMode`:

[![Gradiente linear interativo](linear-gradient-images/InteractiveLinearGradient.png "Gradiente linear interativo")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

As três capturas de tela mostram os resultados dos três valores diferentes de [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode). A captura de tela do iOS mostra `SKShaderTileMode.Clamp`, que apenas estende as cores na borda do gradiente. A opção `SKShaderTileMode.Repeat` na captura de tela do Android mostra como o padrão de gradiente é repetido. A opção `SKShaderTileMode.Mirror` na captura de tela UWP também repete o padrão, mas o padrão é revertido a cada vez, resultando em nenhuma descontinuidades de cor.

## <a name="gradients-on-gradients"></a>Gradientes em gradientes

A classe `SKShader` não define propriedades ou métodos públicos, exceto para `Dispose`. Portanto, os objetos de `SKShader` criados por seus métodos estáticos são imutáveis. Mesmo se você usar o mesmo gradiente para dois objetos diferentes, é provável que você vai querer variar um pouco o gradiente. Para fazer isso, você precisará criar um novo objeto `SKShader`.

A página de **texto gradiente** exibe texto e um brackground que são coloridos com gradientes semelhantes:

[![Texto de gradiente](linear-gradient-images/GradientText.png "Texto de gradiente")](linear-gradient-images/GradientText-Large.png#lightbox)

As únicas diferenças nos gradientes são os pontos inicial e final. O gradiente usado para exibir texto baseia-se em dois pontos nos cantos do retângulo delimitador do texto. Para o plano de fundo, os dois pontos são baseados na tela inteira. O código é o seguinte:

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

A propriedade `Shader` do objeto `SKPaint` é definida primeiro para exibir um gradiente para cobrir o plano de fundo. Os pontos de gradiente são definidos para os cantos superior esquerdo e inferior direito da tela.

O código define a propriedade `TextSize` do objeto `SKPaint` para que o texto seja exibido em 90% da largura da tela. Os limites de texto são usados para calcular `xText` e `yText` valores a serem passados para o método `DrawText` para centralizar o texto.

No entanto, os pontos graduais para a segunda chamada de `CreateLinearGradient` devem se referir ao canto superior esquerdo e inferior direito do texto em relação à tela quando ele é exibido. Isso é feito alternando o `textBounds` retângulo com os mesmos `xText` e `yText` valores:

```csharp
textBounds.Offset(xText, yText);
```

Agora os cantos superior esquerdo e direito inferior do retângulo podem ser usados para definir os pontos inicial e final do gradiente.

## <a name="animating-a-gradient"></a>Animando um gradiente

Há várias maneiras para animar um gradiente. Uma abordagem é animar os pontos inicial e final. A página **animação de gradiente** move os dois pontos em um círculo centralizado na tela. O raio deste círculo é meia largura ou altura da tela, o que for menor. Os pontos inicial e final são opostos uns aos outros neste círculo, e o gradiente de branco para preto com um `Mirror` modo de bloco:

[![Animação de gradiente](linear-gradient-images/GradientAnimation.png "Animação de gradiente")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

O construtor cria o `SKCanvasView`. Os métodos `OnAppearing` e `OnDisappearing` lidam com a lógica da animação:

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

O método `OnTimerTick` calcula um valor de `angle` que é animado de 0 a 2π a cada 3 segundos. 

Aqui está uma maneira de calcular os dois pontos de gradiente. Um valor de `SKPoint` chamado `vector` é calculado para se estender do centro da tela até um ponto no raio do círculo. A direção desse vetor baseia-se nos valores de seno e cosseno do ângulo. Os dois pontos de gradiente opostos, em seguida, são calculados: um ponto é calculado subtraindo esse vetor a partir do ponto central, e outro ponto é calculado somando-se do vetor ao ponto central:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Uma abordagem um pouco diferente requer menos código. Essa abordagem utiliza o método de sobrecarga [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) com uma transformação de matriz como o último argumento. Essa abordagem é a versão no exemplo de [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Se a largura da tela for menor que a altura, os dois pontos de gradiente serão definidos como (0, 0) e (`info.Width`, 0). A transformação de rotação passada como o último argumento para `CreateLinearGradient` gira efetivamente esses dois pontos em volta do centro da tela.

Observe que, se o ângulo será 0, não há nenhuma rotação e os dois pontos de gradiente são os cantos superior esquerdo e no canto superior direito da tela. Esses pontos não são os mesmos pontos de gradação calculados, conforme mostrado na chamada de `CreateLinearGradient` anterior. Mas esses pontos são _paralelos_ à linha gradiente horizontal que bilista o centro da tela e resulta em um gradiente idêntico.

**Gradiente-íris**

A página **gradiente arco-íris** desenha um arco-íris do canto superior esquerdo da tela até o canto inferior direito. Mas esse gradiente rainbow não é como um arco-íris real. Ele é diretamente em vez de curva, mas ele se baseia em oito cores HSL (Matiz-saturação-luminosidade) que são determinadas pela circulando através de valores de matiz de 0 a 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Esse código faz parte do manipulador de `PaintSurface` mostrado abaixo. O manipulador começa com a criação de um caminho que define um polígono seis lados que se estende do canto superior esquerdo da tela para o canto inferior direito:

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Os dois pontos graduais no método `CreateLinearGradient` baseiam-se em dois pontos que definem esse caminho: ambos os pontos estão próximos ao canto superior esquerdo. A primeira fica na borda superior da tela e o segundo é na borda esquerda da tela. Este é o resultado:

[![Falha de gradiente de arco-íris](linear-gradient-images/RainbowGradientFaulty.png "Falha de gradiente de arco-íris")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Esta é uma imagem interessante, mas não é bastante a intenção. O problema é que, ao criar um gradiente linear, as linhas de cor constante são perpendiculares à linha de gradiente. A linha com gradiente baseia-se nos pontos de onde a Figura toca os lados superior e esquerdos, e essa linha geralmente não é perpendicular às bordas da figura que se estendem para o canto inferior direito. Essa abordagem funcionaria apenas se a tela quadrada.

Para criar um gradiente rainbow adequado, a linha com gradiente deve ser perpendicular à borda do arco-íris. Que é um cálculo mais envolvido. Um vetor deve ser definido é paralela no lado longo da figura. O vetor é girada em 90 graus, para que ele seja perpendicular àquele lado. Em seguida, ele é ampliado para ser a largura da figura multiplicando por `rainbowWidth`. Os dois pontos de gradiente são calculados com base em um ponto na lateral da figura, e que aponte além do vetor. Aqui está o código que aparece na página de **gradiente arco-íris** no exemplo de [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Agora, as cores de rainbow estejam alinhadas com a figura a:

[![Gradiente-íris](linear-gradient-images/RainbowGradient.png "Gradiente-íris")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Cores de infinito**

Um gradiente de arco-íris também é usado na página **cores de infinito** . Esta página desenha um sinal de infinito usando um objeto de caminho descrito no artigo [**três tipos de curvas Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). A imagem é colorida, em seguida, com um gradiente de rainbow animado continuamente varre em toda a imagem.

O construtor cria o `SKPath` objeto que descreve o sinal de infinito. Depois que o caminho é criado, o construtor também pode obter os limites retangulares do caminho. Em seguida, ele calcula um valor chamado `gradientCycleLength`. Se um gradiente for baseado nos cantos superior esquerdo e inferior direito do retângulo `pathBounds`, esse valor `gradientCycleLength` será a largura horizontal total do padrão de gradiente:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

O Construtor também cria a matriz de `colors` para o arco-íris e o objeto `SKCanvasView`.

As substituições dos métodos `OnAppearing` e `OnDisappearing` executam a sobrecarga da animação. O método `OnTimerTick` anima o campo `offset` de 0 a `gradientCycleLength` a cada dois segundos:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Por fim, o manipulador de `PaintSurface` renderiza o sinal de infinito. Como o caminho contém coordenadas negativas e positivas ao redor de um ponto central de (0, 0), uma transformação `Translate` na tela é usada para deslocar para o centro. A transformação translate é seguida por uma transformação `Scale` que aplica um fator de dimensionamento que torna o sinal de infinito o mais grande possível e, ao mesmo tempo, permanece dentro de 95% da largura e altura da tela. 

Observe que a constante de `STROKE_WIDTH` é adicionada à largura e à altura do retângulo delimitador de caminho. O caminho será ser traçado com uma linha dessa largura, portanto, o tamanho do tamanho infinito renderizado é aumentado pela metade essa largura em todos os quatro lados:

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

Observe os pontos passados como os dois primeiros argumentos de `SKShader.CreateLinearGradient`. Esses pontos baseiam-se o retângulo delimitador de caminho original. O primeiro ponto é (&ndash;250, &ndash;100) e o segundo é (250, 100). Interno para SkiaSharp, esses pontos estão sujeitos a transformação atual da tela para que eles se alinham corretamente com o símbolo de infinito exibido.

Sem o último argumento para `CreateLinearGradient`, você verá um gradiente de arco-íris que se estende do canto superior esquerdo do sinal de infinito para o canto inferior direito. (Na verdade, o gradiente se estende do canto superior esquerdo para o canto inferior direito do retângulo delimitador. O sinal de infinito renderizado é maior que o retângulo delimitador por meio do valor de `STROKE_WIDTH` em todos os lados. Como o gradiente é vermelho no início e no final, e o gradiente é criado com `SKShaderTileMode.Repeat`, a diferença não é perceptível.)

Com esse último argumento para `CreateLinearGradient`, o padrão de gradiente varre continuamente na imagem:

[![Cores de infinito](linear-gradient-images/InfinityColors.png "Cores de infinito")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Transparência e gradientes

As cores que contribuem para um gradiente podem incorporar a transparência. Em vez de um gradiente que passa de uma cor para outra, o gradiente poderá esmaecer de uma cor como transparente. 

Você pode usar essa técnica para alguns efeitos interessantes. Um dos exemplos clássicos mostra um objeto gráfico com sua reflexão:

[![Gradiente de reflexo](linear-gradient-images/ReflectionGradient.png "Gradiente de reflexo")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

O texto que está de cabeça para baixo é colorido com um gradiente que é 50% transparente na parte superior para totalmente transparente na parte inferior. Esses níveis de transparência são associados com os valores alfa de 0x80 e 0.

O manipulador de `PaintSurface` na página de **gradiente de reflexão** dimensiona o tamanho do texto para 90% da largura da tela. Em seguida, ele calcula `xText` e `yText` valores para posicionar o texto a ser centralizado horizontalmente, mas está em uma linha de base correspondente ao centro vertical da página:

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Esses valores `xText` e `yText` são os mesmos valores usados para exibir o texto refletido na chamada `DrawText` na parte inferior do manipulador de `PaintSurface`. Logo antes desse código, no entanto, você verá uma chamada para o método de `Scale` de `SKCanvas`. Esse método `Scale` escala horizontalmente por 1 (que não faz nada), mas verticalmente por &ndash;1, o que efetivamente inverte tudo de cabeça para baixo. O centro de rotação é definido como o ponto (0, `yText`), em que `yText` é o centro vertical da tela, originalmente calculado como `info.Height` dividido por 2.

Tenha em mente que Skia usa o gradiente para colorir objetos gráficos antes das transformações de tela. Depois que o texto não refletido é desenhado, o retângulo `textBounds` é deslocado para que corresponda ao texto exibido:

```csharp
textBounds.Offset(xText, yText);
```

A chamada `CreateLinearGradient` define um gradiente da parte superior do retângulo para a parte inferior. O gradiente é de um azul (`paint.Color.WithAlpha(0)`) completamente transparente para um azul transparente de 50% (`paint.Color.WithAlpha(0x80)`). A transformação de canvas inverte o texto de cabeça para baixo, para que o azul transparente de 50% começa na linha de base e se tornará transparente na parte superior do texto.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
