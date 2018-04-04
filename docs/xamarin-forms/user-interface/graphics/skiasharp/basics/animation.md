---
title: Animação básica
description: Descubra como animar SkiaSharp elementos gráficos
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 7435807e77a9a79d7fc3821675c1d959a16caa8f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="basic-animation"></a>Animação básica

_Descubra como animar SkiaSharp elementos gráficos_

É possível animar SkiaSharp gráficos em xamarin. Forms fazendo com que o `PaintSurface` método a ser chamado com muita frequência, cada vez que os gráficos de desenho um pouco diferente. Aqui está uma animação mostrada mais adiante neste artigo com círculos concêntricos que aparentemente a expansão do centro da:

![](animation-images/animationexample.png "Vários círculos concêntricos aparentemente expandindo no centro da")

O **Pulsating elipse** página o [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa anima dois eixos de uma elipse para que ele parece ser pulsating, e você ainda pode controlar o taxa desse pulsation:


O [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) arquivo instancia um xamarin. Forms `Slider` e um `Label` para exibir o valor atual do controle deslizante. Essa é uma maneira comum para integrar um `SKCanvasView` com outros modos de exibição do xamarin. Forms:

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

O arquivo code-behind instancia um `Stopwatch` objeto para servir como um relógio de alta precisão. O `OnAppearing` substituir conjuntos de `pageIsActive` campo `true` e chama um método chamado `AnimationLoop`. O `OnDisappearing` conjuntos de substituição que `pageIsActive` campo `false`:

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

O `AnimationLoop` método inicia o `Stopwatch` e, em seguida, loops ao `pageIsActive` é `true`. Isso é essencialmente, um "loop infinito", enquanto a página está ativa, mas ele não faz o programa travar porque o loop é concluído com uma chamada para `Task.Delay` com o `await` operador, que permite que outras partes da função do programa. O argumento `Task.Delay` faz com que ela seja concluída após 30/1 segundo. Isso define a taxa de quadros da animação.

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

O `while` loop começa obtendo o ciclo de tempo a partir de `Slider`. Este é um tempo em segundos, por exemplo, 5. A segunda instrução calcula um valor de `t` para *tempo*. Para uma `cycleTime` de 5, `t` aumenta de 0 para 1 a cada 5 segundos. O argumento para o `Math.Sin` função nos intervalos segunda instrução de 0 a 2π a cada 5 segundos. O `Math.Sin` função retorna um valor entre 0 e 1 volta para 0 e, em seguida, &ndash;1 e 0 a 5 segundos, mas com valores que são alterados com mais lentidão quando o valor for próximo a 1 ou -1. O valor 1 é adicionado para que os valores são sempre positivos e, em seguida, ele está dividido por 2, para que os valores varia de ½ como 1 para ½ como 0 para ½, mas mais lenta quando o valor é em torno de 1 e 0. Isso é armazenado na `scale` campo e o `SKCanvasView` é invalidado.

O `PaintSurface` método utiliza `scale` para calcular os dois eixos da elipse:

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

O método calcula um raio máximo com base no tamanho da área de exibição e um raio mínimo com base no raio máximo. O `scale` valor é animado entre 0 e 1 e de volta para 0, para o método usa para calcular uma `xRadius` e `yRadius` que varia entre `minRadius` e `maxRadius`. Esses valores são usados para desenhar e preencher uma elipse:

[![](animation-images/pulsatingellipse-small.png "Captura de tela da página elipse pulsando tripla")](animation-images/pulsatingellipse-large.png#lightbox "tripla captura de tela da página pulsando elipse")

Observe que o `SKPaint` objeto é criado em um `using` bloco. Como muitas classes SkiaSharp `SKPaint` deriva `SKObject`, que é derivado de `SKNativeObject`, que implementa o [ `IDisposable` ](https://developer.xamarin.com/api/type/System.IDisposable/) interface. `SKPaint` substitui o `Dispose` método para liberar recursos não gerenciados.

 Colocando `SKPaint` em uma `using` bloco garante que `Dispose` é chamado no final do bloco para liberar esses recursos não gerenciados. Isso acontece mesmo assim quando a memória usada pelo `SKPaint` objeto é liberado pelo coletor de lixo .NET, mas no código de animação, é melhor ser um pouco proativo na liberação de memória de maneira mais ordenada.

 A melhor solução nesse caso específico seria criar duas `SKPaint` objetos de uma vez e salvá-los como campos.

É o que o **expandindo círculos** does de animação. O [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe começa com a definição de vários campos, incluindo um `SKPaint` objeto:

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

Este programa usa uma abordagem diferente para a animação com base no xamarin. Forms `Device.StartTimer`. O `t` campo é animado de 0 a 1 cada `cycleTime` milissegundos:

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

O `PaintSurface` manipulador desenha círculos concêntricos 5 com raios animados. Se o `baseRadius` variável é calculada como 100, em seguida, como `t` é animado de 0 a 1, os raios do aumento cinco círculos de 0 a 100, 100 a 200, 200 para 300, 300 a 400 e 400 e 500. Para a maioria dos círculos o `strokeWidth` é círculo 50, mas para o primeiro, o `strokeWidth` anima de 0 a 50. Para a maioria dos círculos, a cor é azul, mas para o último círculo, a cor é animada de azul para transparente:

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

O resultado é que a imagem está com o mesmo ao `t` é igual a 0, como quando `t` é igual a 1, e os círculos parecem continue expandindo para sempre:

[![](animation-images/expandingcircles-small.png "Captura de tela da página expandindo círculos tripla")](animation-images/expandingcircles-large.png#lightbox "tripla captura de tela da página expandindo círculos")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
