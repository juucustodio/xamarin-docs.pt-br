---
title: Pintura a dedo em SkiaSharp
description: Este artigo explica como usar os dedos para pintar a tela de SkiaSharp em um aplicativo xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: b0f28cd3e8a928a6da3169dee96ec089178a64e2
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615815"
---
# <a name="finger-painting-in-skiasharp"></a>Pintura a dedo em SkiaSharp

_Use os dedos para pintar na tela._

Um `SKPath` objeto pode ser continuamente atualizado e exibido. Esse recurso permite que um caminho a ser usado para o desenho interativo, como em um programa para pintura com dedo.

![](finger-paint-images/fingerpaintsample.png "Um exercício de pintura a dedo")

O suporte a toque no xamarin. Forms não permite o acompanhamento de dedos individuais na tela, portanto, um efeito de controle de toque do xamarin. Forms foi desenvolvido para oferecer suporte a toque adicionais. Esse efeito é descrito no artigo [ **invocar eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). O programa de exemplo [ **demonstrações de efeito do controle de toque** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) inclui duas páginas que usam SkiaSharp, incluindo um programa para pintura com dedo.

O [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) solução inclui esse evento de acompanhamento de toque. O projeto de biblioteca .NET Standard inclui o `TouchEffect` classe, o `TouchActionType` enumeração, o `TouchActionEventHandler` delegar e o `TouchActionEventArgs` classe. Cada um dos projetos de plataforma incluem uma `TouchEffect` para a plataforma de classe; o projeto do iOS também contém um `TouchRecognizer` classe.

O **pintura a dedo** página no **SkiaSharpFormsDemos** é uma implementação simplificada de pintura a dedo. Não permitir a seleção de cor ou largura de traço, ele não tem nenhuma maneira para limpar a tela e certamente não é possível salvar sua arte final.

O [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) arquivo coloca a `SKCanvasView` em uma única célula `Grid` e anexa o `TouchEffect` para que `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Anexando a `TouchEffect` diretamente para o `SKCanvasView` não funciona em todas as plataformas.

O [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) arquivo code-behind define duas coleções para armazenar o `SKPath` objetos, bem como um `SKPaint` objeto para esses caminhos de renderização:

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Como o nome sugere, o `inProgressPaths` dicionário armazena os caminhos que estão atualmente sendo desenhados por um ou mais dedos. A chave do dicionário é a ID de toque que acompanha os eventos de toque. O `completedPaths` campo é uma coleção de caminhos que tenham sido executados quando um dedo, o caminho eliminado de desenho na tela.

O `TouchAction` manipulador gerencia essas duas coleções. Quando um dedo toca na tela, primeiro uma nova `SKPath` é adicionado ao `inProgressPaths`. Conforme desse dedo se move, pontos adicionais são adicionados ao caminho. Quando o dedo é liberado, o caminho é transferido para o `completedPaths` coleção. Você pode pintar com vários dedos simultaneamente. Após cada alteração em um dos caminhos ou coleções, o `SKCanvasView` é invalidado:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

Os pontos que acompanha os eventos de rastreamento de toque são coordenadas do xamarin. Forms; eles devem ser convertidos em coordenadas de SkiaSharp, que são pixels. Essa é a finalidade de `ConvertToPixel` método.

O `PaintSurface` manipulador, em seguida, simplesmente renderiza ambas as coleções de caminhos. Os caminhos concluídos anteriormente aparecem sob os caminhos em andamento:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Seu dedo pinturas são limitadas apenas pelos seus talentos:

[![](finger-paint-images/fingerpaint-small.png "Captura de tela da página de pintura a dedo tripla")](finger-paint-images/fingerpaint-large.png#lightbox "tripla captura de tela da página de pintura a dedo")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Demonstrações de efeito do controle de toque (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
