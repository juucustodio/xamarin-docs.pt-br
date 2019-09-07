---
title: Animação básica no SkiaSharp
description: Este artigo explica como animar elementos gráficos SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: ceeacaed510005cec7a4017ce45706e492d7e146
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759740"
---
# <a name="basic-animation-in-skiasharp"></a>Animação básica no SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra como animar a seus gráficos de SkiaSharp_

Você pode animar elementos gráficos de SkiaSharp em xamarin. Forms, fazendo com que o `PaintSurface` método a ser chamado periodicamente, cada vez que os gráficos de desenho um pouco diferente. Aqui está uma animação mostrada mais adiante neste artigo com círculos concêntricos que aparentemente expandir no centro da:

![](animation-images/animationexample.png "Vários círculos concêntricos aparentemente expandindo no centro da")

O **elipse Pulsating** página na [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa anima dois eixos de uma elipse para que ele apareça ser pulsating e até mesmo, você pode controlar o taxa deste pulsation. O [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) arquivo cria uma instância de um xamarin. Forms `Slider` e um `Label` para exibir o valor atual do controle deslizante. Essa é uma maneira comum para integrar um `SKCanvasView` com outros modos de exibição do xamarin. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

O arquivo code-behind instancia um `Stopwatch` objeto para servir como um relógio de alta precisão. O `OnAppearing` conjuntos de substituição a `pageIsActive` campo `true` e chama um método chamado `AnimationLoop`. O `OnDisappearing` substituição define qual `pageIsActive` campo `false`:

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

O `AnimationLoop` início do método de `Stopwatch` e, em seguida, loops enquanto `pageIsActive` é `true`. Isso é essencialmente, um "loop infinito", enquanto a página está ativa, mas não fazem com que o programa pare de responder porque o loop é concluído com uma chamada para `Task.Delay` com o `await` operador, que permite que outras partes da função do programa. O argumento para `Task.Delay` faz com que a sua conclusão após 30/1 segundo. Isso define a taxa de quadros da animação.

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

O `while` loop começa obtendo um tempo de ciclo do `Slider`. Isso é um tempo em segundos, por exemplo, 5. A segunda instrução calcula um valor de `t` para *tempo*. Para um `cycleTime` de 5, `t` aumenta de 0 a 1 a cada 5 segundos. O argumento para o `Math.Sin` função nos intervalos segunda instrução de 0 a 2π a cada 5 segundos. O `Math.Sin` função retorna um valor que varia de 0 para trás de 1 para 0 e, em seguida, &ndash;1 e 0 a cada 5 segundos, mas com valores que mudam mais lentamente quando o valor for próximo a 1 ou -1. O valor 1 é adicionado para que os valores são sempre positivos e, em seguida, ele é dividido por 2, portanto, os valores variam de ½ como 1 para ½ como 0 para ½, mas mais lento quando o valor é de cerca de 1 e 0. Isso é armazenado na `scale` campo e o `SKCanvasView` é invalidado.

O `PaintSurface` usa o método isso `scale` valor para calcular os dois eixos da elipse:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

O método calcula um raio máximo com base no tamanho da área de exibição e um raio mínimo com base no máximo radius. O `scale` valor é animada entre 0 e 1 e de volta para 0, portanto, o método usará isso para calcular uma `xRadius` e `yRadius` que varia entre `minRadius` e `maxRadius`. Esses valores são usados para desenhar e preencher uma elipse:

[![](animation-images/pulsatingellipse-small.png "Captura de tela da página elipse pulsando tripla")](animation-images/pulsatingellipse-large.png#lightbox "tripla captura de tela da página pulsando elipse")

Observe que o `SKPaint` objeto é criado em um `using` bloco. Como muitas classes de SkiaSharp `SKPaint` deriva `SKObject`, que é derivada de `SKNativeObject`, que implementa o [ `IDisposable` ](xref:System.IDisposable) interface. `SKPaint` substitui o `Dispose` método para liberar recursos não gerenciados.

 Colocando `SKPaint` em um `using` bloco garante que `Dispose` é chamado no final do bloco para liberar esses recursos não gerenciados. Isso acontece mesmo assim quando a memória usada pelo `SKPaint` objeto é liberado pelo coletor de lixo .NET, mas no código de animação, é melhor ser proativo na liberação de memória de maneira mais ordenada.

 Uma solução melhor nesse caso em particular seria criar duas `SKPaint` objetos de uma vez e salvá-los como campos.

Isso é o que o **círculos expandindo** animação faz. O [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe começa definindo vários campos, incluindo um `SKPaint` objeto:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

Esse programa usa uma abordagem diferente para a animação com base no xamarin. Forms `Device.StartTimer` método. O `t` campo é animado de 0 a 1 cada `cycleTime` milissegundos:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

O `PaintSurface` manipulador desenha círculos concêntricos cinco com raios animados. Se o `baseRadius` variável é calculada como 100, em seguida, como `t` é animado de 0 a 1, os raios do aumento de cinco círculos de 0 a 100 e 200, 100, 200 a 300, 300 a 400 e 400 ou 500. Para a maioria dos círculos a `strokeWidth` é 50, mas para o primeiro círculo, o `strokeWidth` anima de 0 a 50. Para a maioria dos círculos, a cor é azul, mas para o círculo último, a cor é animada de azul para transparente. Observe o quarto argumento para o `SKColor` construtor que especifica a opacidade:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

O resultado é que a imagem é o mesmo ao `t` é igual a 0, assim como quando `t` é igual a 1, e os círculos parecem continue expandindo para sempre:

[![](animation-images/expandingcircles-small.png "Captura de tela da página expandindo círculos tripla")](animation-images/expandingcircles-large.png#lightbox "tripla captura de tela da página expandindo círculos")

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
