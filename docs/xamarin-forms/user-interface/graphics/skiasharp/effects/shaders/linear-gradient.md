---
title: Gradiente linear SkiaSharp
description: Descubra como traçar as linhas ou áreas de preenchimento com gradientes composto por uma mesclagem gradual de duas cores.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: de563c4780a57e439abab61378919501ce88b11b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130789"
---
# <a name="the-skiasharp-linear-gradient"></a>Gradiente linear SkiaSharp

O [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe define um [ `Color` ](xref:SkiaSharp.SKPaint.Color) propriedade que é usada para traçar as linhas ou preencher as áreas com uma cor sólida. Como alternativa, você pode traçar as linhas ou preencher as áreas com _gradientes_, que são graduais misturas de cores:

![Exemplo de gradiente linear](linear-gradient-images/LinearGradientSample.png "amostra de Gradiente Linear")

O tipo mais básico do gradiente é um _linear_ gradiente. A mistura de cores ocorre em uma linha (chamado de _linha de gradação_) de um ponto para outro. Linhas perpendiculares à linha de gradiente têm a mesma cor. Você cria um gradiente linear usando uma das duas estático [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) métodos. A diferença entre as duas sobrecargas é que uma inclui uma transformação de matriz e o outro não. 

Esses métodos retornam um objeto do tipo [ `SKShader` ](xref:SkiaSharp.SKShader) que você definir para o [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) propriedade `SKPaint`. Se o `Shader` propriedade não for nulo, ele substituirá o `Color` propriedade. Qualquer linha que é traçada ou qualquer área preenchida usando este `SKPaint` objeto baseia-se no gradiente em vez da cor sólida.

> [!NOTE]
> O `Shader` propriedade será ignorada quando você inclui um `SKPaint` do objeto em um `DrawBitmap` chamar. Você pode usar o `Color` propriedade de `SKPaint` para definir um nível de transparência para exibir um bitmap (conforme descrito no artigo [SkiaSharp exibindo bitmaps](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), mas não é possível usar o `Shader` propriedade para exibir um bitmap com um gradiente transparência. Outras técnicas estão disponíveis para exibição de bitmaps com gradiente transparências: elas são descritas nos artigos [gradientes circulares de SkiaSharp](circular-gradients.md#radial-gradients-for-masking) e [modos de composição e o blend SkiaSharp](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Canto ao outro gradientes

Muitas vezes um gradiente linear se estende de um dos cantos de um retângulo para outro. Se o ponto de partida é o canto superior esquerdo do retângulo, pode estender o gradiente:

- verticalmente para o canto inferior esquerdo
- horizontal para o canto superior direito
- diagonalmente para o canto inferior direito

Gradiente linear diagonal é demonstrado na primeira página a **SkiaSharp sombreadores e outros efeitos** seção o [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo. O **canto ao outro gradiente** página cria um `SKCanvasView` em seu construtor. O `PaintSurface` manipulador cria um `SKPaint` do objeto em um `using` instrução e, em seguida, define um retângulo de 300 pixels quadrado centralizado na tela:

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

O `Shader` propriedade de `SKPaint` é atribuído a `SKShader` retornar o valor de estático `SKShader.CreateLinearGradient` método. Os cinco argumentos são da seguinte maneira:

- O ponto inicial do gradiente, definidos aqui para o canto superior esquerdo do retângulo
- O ponto de extremidade do gradiente, definidos aqui para o canto inferior direito do retângulo
- Uma matriz de duas ou mais cores que contribuem para o gradiente
- Uma matriz de `float` valores que indicam a posição relativa das cores dentro da linha gradiente
- Um membro do [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeração que indica como o gradiente se comporta além do final da linha de gradiente

Depois que o objeto de gradiente é criado, o `DrawRect` método desenha o retângulo de 300 pixels quadrado usando o `SKPaint` objeto que inclui o sombreador. Aqui ele está em execução no iOS, Android e Universal Windows Platform (UWP):

[![Gradiente do canto ao outro](linear-gradient-images/CornerToCornerGradient.png "canto ao outro gradiente")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

A linha gradiente é definida pelos dois pontos especificados como os primeiros dois argumentos. Observe que esses pontos são relativos a _tela_ e _não_ para o objeto de gráfico exibido com o gradiente. Ao longo da linha de gradiente, a cor gradualmente faz a transição de vermelho no canto superior esquerdo para azul no canto inferior direito. Qualquer linha perpendicular à linha de gradiente tem uma cor constante.

A matriz de `float` valores especificados como o quarto argumento têm uma correspondência direta com a matriz de cores. Os valores indicam a posição relativa ao longo da linha gradiente onde ocorrem essas cores. Aqui, 0 significa que `Red` ocorre no início da linha de gradiente, e 1 significa que `Blue` ocorre no final da linha. Os números devem estar em ordem crescente e devem estar no intervalo de 0 a 1. Se não estiverem nesse intervalo, eles serão ajustados para ser nesse intervalo.

Os dois valores na matriz podem ser definidos como algo diferente de 0 e 1. Experimente isto:

```csharp
new float[] { 0.25f, 0.75f }
```

Agora totalmente primeiro trimestre de linha de gradiente é vermelho puro, e o último trimestre é azul puro. A mistura de vermelho e azul é restrita a central na metade da linha de gradiente.

Em geral, você desejará espaço esses valores de posição igualmente de 0 a 1. Se esse for o caso, você pode simplesmente fornecer `null` como o quarto argumento `CreateLinearGradient`.

Embora esse gradiente é definido entre dois cantos do retângulo 300 pixels quadrado, não é restrito para preencher o retângulo. O **canto ao outro gradiente** página inclui um código extra que responde aos toques ou cliques do mouse na página. O `drawBackground` campo é alternado entre `true` e `false` com cada toque. Se o valor for `true`, em seguida, a `PaintSurface` manipulador usa as mesmas `SKPaint` de objeto para preencher a tela inteira e, em seguida, desenha um retângulo preto, que indica o retângulo menor: 

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

[![Total de gradiente do canto ao outro](linear-gradient-images/CornerToCornerGradientFull.png "completo de gradiente do canto ao outro")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Observe que o gradiente repete-se no mesmo padrão além de pontos que define a linha com gradiente. Essa repetição ocorre porque o último argumento para `CreateLinearGradient` é `SKShaderTileMode.Repeat`. (Você verá as outras opções em breve.)

Além disso, observe que os pontos que você usa para especificar a linha de gradação não são exclusivos. Linhas perpendiculares à linha de gradiente têm a mesma cor, portanto, há um número infinito de gradiente linhas que você pode especificar para o mesmo efeito. Por exemplo, ao preencher um retângulo com um gradiente horizontal, você pode especificar os cantos superior esquerdo e no canto superior direito, ou os cantos inferior esquerdo e inferior direita ou dois pontos que estão, mesmo com e paralela para essas linhas.

## <a name="interactively-experiment"></a>Testar interativamente

Interativamente, você pode fazer experiências com gradientes lineares com o **Gradiente Linear interativo** página. Essa página usa o `InteractivePage` classe introduzido no artigo [ **três maneiras de desenhar um arco**](../../curves/arcs.md). `InteractivePage` alças [ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) eventos para manter uma coleção de `TouchPoint` objetos que podem ser movidos com o mouse ou de seus dedos.

Anexa o arquivo XAML a `TouchEffect` a um pai do `SKCanvasView` e também inclui um `Picker` que permite que você selecione um dos três membros dos [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeração:

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

O construtor no arquivo code-behind cria dois `TouchPoint` objetos para os pontos inicial e final do gradiente linear. O `PaintSurface` manipulador define uma matriz de três cores (para um gradiente de vermelho a verde para azul) e obtém a atual `SKShaderTileMode` do `Picker`:

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

O `PaintSurface` manipulador cria o `SKShader` objeto a partir de todas essas informações e usa-o para a tela inteira de cores. A matriz de `float` valores é definido como `null`. Caso contrário, para espaçar igualmente três cores, definir esse parâmetro para uma matriz com os valores 0, 0,5 e 1.

A maior parte do `PaintSurface` manipulador é dedicado a exibir vários objetos: os pontos de toque como círculos da estrutura de tópicos, da linha do gradiente e as linhas perpendiculares às linhas de gradiente nos pontos de toque:

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

As linhas perpendiculares coincidirem com o início e no final do gradiente. O que acontece Além dessas linhas depende da configuração do `SKShaderTileMode` enumeração:

[![Gradiente Linear interativo](linear-gradient-images/InteractiveLinearGradient.png "interativo Gradiente Linear")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

As três capturas de tela mostram os resultados dos três valores diferentes de [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode). A captura de tela do iOS mostra `SKShaderTileMode.Clamp`, que simplesmente estende as cores na borda do gradiente. O `SKShaderTileMode.Repeat` opção na captura de tela Android mostra como o padrão de gradiente é repetido. O `SKShaderTileMode.Mirror` opção na captura de tela UWP também repete o padrão, mas o padrão é invertido cada vez, resultando no descontinuidades nenhuma cor.

## <a name="gradients-on-gradients"></a>Gradientes em gradientes

O `SKShader` classe não define nenhuma propriedades públicas nem os métodos, exceto para `Dispose`. O `SKShader` objetos que criados por seus métodos estáticos, portanto, são imutáveis. Mesmo se você usar o mesmo gradiente para dois objetos diferentes, é provável que você vai querer variar um pouco o gradiente. Para fazer isso, você precisará criar um novo `SKShader` objeto.

O **gradiente texto** página exibe o texto e um plano de fundo são coloridos com gradientes semelhantes:

[![Texto de gradiente](linear-gradient-images/GradientText.png "texto gradiente")](linear-gradient-images/GradientText-Large.png#lightbox)

As únicas diferenças nos gradientes são os pontos inicial e final. O gradiente usado para exibir texto baseia-se em dois pontos nos cantos do retângulo delimitador do texto. Para o plano de fundo, os dois pontos são baseados na tela inteira. Aqui está o código:

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

O `Shader` propriedade do `SKPaint` objeto é definido pela primeira vez para exibir um gradiente para cobrir o plano de fundo. Os pontos de gradiente são definidos para os cantos superior esquerdo e inferior direito da tela.

O código define a `TextSize` propriedade do `SKPaint` do objeto para que o texto é exibido em 90% da largura da tela. Os limites de texto são usados para calcular `xText` e `yText` valores a serem passados para o `DrawText` método centralizar o texto.

No entanto, o gradiente aponta para o segundo `CreateLinearGradient` chamada deve se referir ao canto superior esquerdo e inferior direito do texto em relação à tela quando ele for exibido. Isso é feito com a mudança a `textBounds` retângulo pelo mesmo `xText` e `yText` valores:

```csharp
textBounds.Offset(xText, yText);
```

Agora os cantos superior esquerdo e direito inferior do retângulo podem ser usados para definir os pontos inicial e final do gradiente.

## <a name="animating-a-gradient"></a>Animando um gradiente

Há várias maneiras para animar um gradiente. Uma abordagem é animar os pontos inicial e final. O **animação gradiente** página move os dois pontos em um círculo que é centralizado na tela. O raio deste círculo é meia largura ou altura da tela, o que for menor. Os pontos inicial e final são opostas neste círculo, e o gradiente vai de branco para preto com uma `Mirror` modo lado a lado:

[![Animação de gradiente](linear-gradient-images/GradientAnimation.png "animação gradiente")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

O construtor cria o `SKCanvasView`. O `OnAppearing` e `OnDisappearing` métodos lidam com a lógica da animação:

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

O `OnTimerTick` método calcula um `angle` valor que é animado de 0 a 2π cada 3 segundos. 

Aqui está uma maneira de calcular os dois pontos de gradiente. Uma `SKPoint` valor denominado `vector` é calculada para estender do centro da tela para um ponto em que o raio do círculo. A direção desse vetor baseia-se nos valores de seno e cosseno do ângulo. Os dois pontos de gradiente opostos, em seguida, são calculados: um ponto é calculado subtraindo esse vetor a partir do ponto central, e outro ponto é calculado somando-se do vetor ao ponto central:

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

Uma abordagem um pouco diferente requer menos código. Essa abordagem utiliza o [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarregar o método com uma transformação de matriz como o último argumento. Essa abordagem é a versão na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo:

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

Se a largura da tela é menor que a altura, em seguida, os dois pontos de gradiente são definidos como (0, 0) e (`info.Width`, 0). A transformação de rotação é passado como o último argumento para `CreateLinearGradient` efetivamente gira esses dois pontos em torno do centro da tela.

Observe que, se o ângulo será 0, não há nenhuma rotação e os dois pontos de gradiente são os cantos superior esquerdo e no canto superior direito da tela. Esses pontos não são os mesmos pontos de gradiente calculados conforme mostrado anteriormente na `CreateLinearGradient` chamar. Mas esses pontos são _paralela_ para a linha de gradiente horizontal que bisects o centro da tela, e eles resultam em um gradiente idêntico.

**Arco-íris gradiente**

O **Rainbow gradiente** página desenha um arco-íris do canto superior esquerdo da tela para o canto inferior direito. Mas esse gradiente rainbow não é como um arco-íris real. Ele é diretamente em vez de curva, mas ele se baseia em oito cores HSL (Matiz-saturação-luminosidade) que são determinadas pela circulando através de valores de matiz de 0 a 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Código faz parte do `PaintSurface` manipulador mostrado abaixo. O manipulador começa com a criação de um caminho que define um polígono seis lados que se estende do canto superior esquerdo da tela para o canto inferior direito:

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

Os dois pontos de gradiente no `CreateLinearGradient` método baseiam-se em dois pontos que definem esse caminho: os dois pontos forem perto do canto superior esquerdo. A primeira fica na borda superior da tela e o segundo é na borda esquerda da tela. Aqui está o resultado:

[![Gradiente de Rainbow defeituoso](linear-gradient-images/RainbowGradientFaulty.png "Rainbow gradiente com defeito")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Esta é uma imagem interessante, mas não é bastante a intenção. O problema é que, ao criar um gradiente linear, as linhas de cor constante são perpendiculares à linha de gradiente. A linha com gradiente baseia-se nos pontos de onde a Figura toca os lados superior e esquerdos, e essa linha geralmente não é perpendicular às bordas da figura que se estendem para o canto inferior direito. Essa abordagem funcionaria apenas se a tela quadrada.

Para criar um gradiente rainbow adequado, a linha com gradiente deve ser perpendicular à borda do arco-íris. Que é um cálculo mais envolvido. Um vetor deve ser definido é paralela no lado longo da figura. O vetor é girada em 90 graus, para que ele seja perpendicular àquele lado. Em seguida, é mais longo para ser a largura da figura multiplicando por `rainbowWidth`. Os dois pontos de gradiente são calculados com base em um ponto na lateral da figura, e que aponte além do vetor. Aqui está o código que aparece na **Rainbow gradiente** página na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo:

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

[![Gradiente Rainbow](linear-gradient-images/RainbowGradient.png "Rainbow gradiente")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Cores de infinito**

Um gradiente rainbow também é usado na **infinito cores** página. Esta página desenha um sinal de infinito usando um objeto de caminho descrito no artigo [ **tipos de três das curvas de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). A imagem é colorida, em seguida, com um gradiente de rainbow animado continuamente varre em toda a imagem.

O construtor cria o `SKPath` objeto que descreve o símbolo de infinito. Depois que o caminho é criado, o construtor também pode obter os limites retangulares do caminho. Em seguida, calcula um valor chamado `gradientCycleLength`. Se um gradiente é baseado nos cantos superior esquerdo e inferior direito dos `pathBounds` retângulo, isso `gradientCycleLength` valor é a largura horizontal total do gradiente padrão:

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

O construtor também cria o `colors` matriz para o arco-íris e o `SKCanvasView` objeto.

Substituições do `OnAppearing` e `OnDisappearing` métodos executam a sobrecarga para a animação. O `OnTimerTick` método anima a `offset` campo de 0 a `gradientCycleLength` cada dois segundos:

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

Por fim, o `PaintSurface` manipulador processa o sinal de infinito. Porque o caminho contém coordenadas positivas e negativas em torno de um ponto central (0, 0), um `Translate` transformação na tela é usada para deslocá-lo para o centro. A transformação de conversão é seguida por um `Scale` transformação que aplica-se um fator de dimensionamento que faz com que o símbolo de infinito tão grandes quanto possível enquanto ainda fica dentro de 95% da largura e altura da tela. 

Observe que o `STROKE_WIDTH` constante é adicionada à largura e altura do caminho do retângulo delimitador. O caminho será ser traçado com uma linha dessa largura, portanto, o tamanho do tamanho infinito renderizado é aumentado pela metade essa largura em todos os quatro lados:

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

Examinar os pontos passados como os dois primeiros argumentos de `SKShader.CreateLinearGradient`. Esses pontos baseiam-se o retângulo delimitador de caminho original. O primeiro ponto é (&ndash;250, &ndash;100) e a segunda é (250, 100). Interno para SkiaSharp, esses pontos estão sujeitos a transformação atual da tela para que eles se alinham corretamente com o símbolo de infinito exibido.

Sem o último argumento para `CreateLinearGradient`, você verá um gradiente rainbow que se estende do superior esquerdo do sinal de infinito para o canto inferior direito. (Na verdade, o gradiente se estende do canto superior esquerdo para o canto inferior direito do retângulo delimitador. O símbolo de infinito renderizado é maior que o retângulo delimitador pela metade o `STROKE_WIDTH` valor em todos os lados. Porque o gradiente é vermelho no início e término, e o gradiente é criado com `SKShaderTileMode.Repeat`, a diferença não é perceptível.)

Com esse último argumento para `CreateLinearGradient`, o padrão de gradiente varre continuamente em toda a imagem:

[![Cores de infinito](linear-gradient-images/InfinityColors.png "cores de infinito")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>Transparência e gradientes

As cores que contribuem para um gradiente podem incorporar a transparência. Em vez de um gradiente que passa de uma cor para outra, o gradiente poderá esmaecer de uma cor como transparente. 

Você pode usar essa técnica para alguns efeitos interessantes. Um dos exemplos clássicos mostra um objeto gráfico com sua reflexão:

[![Gradiente de reflexão](linear-gradient-images/ReflectionGradient.png "gradiente de reflexão")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

O texto que está de cabeça para baixo é colorido com um gradiente que é 50% transparente na parte superior para totalmente transparente na parte inferior. Esses níveis de transparência são associados com os valores alfa de 0x80 e 0.

O `PaintSurface` manipulador na **reflexão gradiente** página dimensiona o tamanho do texto em 90% da largura da tela. Em seguida, ele calcula `xText` e `yText` valores para posicionar o texto horizontalmente centralizado, mas localizado em uma linha de base correspondente ao centro vertical da página:

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

Aqueles `xText` e `yText` valores são os mesmos valores usados para exibir o texto refletido na `DrawText` chamar na parte inferior do `PaintSurface` manipulador. Logo antes que o código, no entanto, você verá uma chamada para o `Scale` método de `SKCanvas`. Isso `Scale` método pode ser dimensionado horizontalmente em 1 (que não faz nada), mas verticalmente por &ndash;1, o que efetivamente inverte tudo cabeça para baixo. O Centro de rotação é definido como o ponto (0, `yText`), onde `yText` é o centro vertical da tela, originalmente calculada como `info.Height` dividido por 2.

Tenha em mente que Skia usa o gradiente para colorir objetos gráficos antes das transformações de tela. Depois que o texto unreflected é desenhado, o `textBounds` retângulo é deslocado para que ele corresponde ao texto exibido:

```csharp
textBounds.Offset(xText, yText);
```

O `CreateLinearGradient` chamada define um gradiente da parte superior do retângulo na parte inferior. O gradiente é de um azul completamente transparente (`paint.Color.WithAlpha(0)`) para uma azul transparente de 50% (`paint.Color.WithAlpha(0x80)`). A transformação de canvas inverte o texto de cabeça para baixo, para que o azul transparente de 50% começa na linha de base e se tornará transparente na parte superior do texto.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
