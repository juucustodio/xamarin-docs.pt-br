---
title: Animação básica no SkiaSharp
description: Este artigo explica como animar elementos gráficos SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 80de16a0cf9b601ac3795085b638b9d62812f4d9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291735"
---
# <a name="basic-animation-in-skiasharp"></a>Animação básica no SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descubra como animar seus elementos gráficos do SkiaSharp_

Você pode animar os gráficos do SkiaSharp no Xamarin. Forms fazendo com que o método `PaintSurface` seja chamado periodicamente, cada vez que desenhar os elementos gráficos um pouco diferente. Aqui está uma animação mostrada mais adiante neste artigo com círculos concêntricos que aparentemente expandir no centro da:

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

A página de **elipse Pulsating** no programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anima os dois eixos de uma elipse para que pareça ser Pulsating e você pode até mesmo controlar a taxa dessa pulsation. O arquivo [**PulsatingEllipsePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) instancia um Xamarin. Forms `Slider` e um `Label` para exibir o valor atual do controle deslizante. Essa é uma maneira comum de integrar um `SKCanvasView` com outras exibições do Xamarin. Forms:

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

O arquivo code-behind instancia um objeto `Stopwatch` para servir como um relógio de alta precisão. A substituição de `OnAppearing` define o campo `pageIsActive` como `true` e chama um método chamado `AnimationLoop`. Os conjuntos de substituição de `OnDisappearing` que `pageIsActive` campo a `false`:

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

O método `AnimationLoop` inicia o `Stopwatch` e, em seguida, executa um loop enquanto `pageIsActive` é `true`. Isso é essencialmente um "loop infinito" enquanto a página está ativa, mas não faz com que o programa trave porque o loop termina com uma chamada para `Task.Delay` com o operador `await`, que permite que outras partes do programa funcionem. O argumento para `Task.Delay` faz com que ele seja concluído após 1/30 segundos. Isso define a taxa de quadros da animação.

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

O loop de `while` começa obtendo um tempo de ciclo do `Slider`. Isso é um tempo em segundos, por exemplo, 5. A segunda instrução calcula um valor de `t` para *tempo*. Para um `cycleTime` de 5, `t` aumenta de 0 para 1 a cada 5 segundos. O argumento para a função `Math.Sin` na segunda instrução varia de 0 a 2π a cada 5 segundos. A função `Math.Sin` retorna um valor que varia de 0 a 1 a 0 e, em seguida, para &ndash;1 e 0 a cada 5 segundos, mas com valores que são alterados mais lentamente quando o valor é próximo a 1 ou – 1. O valor 1 é adicionado para que os valores são sempre positivos e, em seguida, ele é dividido por 2, portanto, os valores variam de ½ como 1 para ½ como 0 para ½, mas mais lento quando o valor é de cerca de 1 e 0. Isso é armazenado no campo `scale` e o `SKCanvasView` é invalidado.

O método `PaintSurface` usa esse valor `scale` para calcular os dois eixos da elipse:

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

O método calcula um raio máximo com base no tamanho da área de exibição e um raio mínimo com base no máximo radius. O valor de `scale` é animado entre 0 e 1 e de volta para 0, portanto, o método usa isso para calcular um `xRadius` e `yRadius` que varia entre `minRadius` e `maxRadius`. Esses valores são usados para desenhar e preencher uma elipse:

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

Observe que o objeto `SKPaint` é criado em um bloco de `using`. Como muitas classes SkiaSharp `SKPaint` deriva de `SKObject`, que deriva de `SKNativeObject`, que implementa a interface [`IDisposable`](xref:System.IDisposable) . `SKPaint` substitui o método `Dispose` para liberar recursos não gerenciados.

 Colocar `SKPaint` em um bloco de `using` garante que `Dispose` seja chamado no final do bloco para liberar esses recursos não gerenciados. Isso acontece mesmo quando a memória usada pelo objeto de `SKPaint` é liberada pelo coletor de lixo do .NET, mas no código de animação, é melhor ser proativa na liberação de memória de forma mais ordenada.

 Uma solução melhor nesse caso específico seria criar dois objetos `SKPaint` uma vez e salvá-los como campos.

É isso que a animação de **círculos em expansão** faz. A classe [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) começa definindo vários campos, incluindo um objeto `SKPaint`:

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

Este programa usa uma abordagem diferente para a animação com base no método `Device.StartTimer` Xamarin. Forms. O campo `t` é animado de 0 a 1 a cada `cycleTime` milissegundos:

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

O manipulador de `PaintSurface` desenha cinco círculos concêntricos com raios animados. Se a variável `baseRadius` for calculada como 100, quando `t` for animada de 0 a 1, o raios dos cinco círculos aumentará de 0 para 100, 100 para 200, 200 para 300, 300 para 400 e 400 para 500. Para a maioria dos círculos, a `strokeWidth` é 50, mas para o primeiro círculo, o `strokeWidth` anima de 0 a 50. Para a maioria dos círculos, a cor é azul, mas para o círculo último, a cor é animada de azul para transparente. Observe o quarto argumento para o construtor de `SKColor` que especifica a opacidade:

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

O resultado é que a imagem tem a mesma aparência quando `t` é igual a 0 como quando `t` é igual a 1, e os círculos parecem continuar expandindo para sempre:

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
