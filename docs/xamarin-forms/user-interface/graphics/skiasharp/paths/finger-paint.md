---
title: Pintura a dedo
description: Use seus dedos para pintar na tela.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: 9f7e3f64122f3b95291973d032aaf507dfbc8fab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="finger-painting"></a>Pintura a dedo

_Use seus dedos para pintar na tela._

Um `SKPath` objeto pode ser atualizado e exibido continuamente. Esse recurso permite um caminho a ser usado para desenhar interativo, como em um programa finger-painting.

![](finger-paint-images/fingerpaintsample.png "Um exercício de pintura a dedo")

O suporte de toque no xamarin. Forms não permite controle individuais dedos na tela, para que um efeito de controle de toque xamarin. Forms foi desenvolvido para fornecer suporte adicional de toque. Esse efeito é descrito no artigo [ **invocar eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). O programa de exemplo [ **demonstrações de efeito do controle de toque** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) inclui duas páginas que usam SkiaSharp, incluindo um programa finger-painting.

O [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) solução inclui esse evento de rastreamento de toque. O projeto de biblioteca de classes portátil inclui o `TouchEffect` classe, o `TouchActionType` enumeração, o `TouchActionEventHandler` delegado e o `TouchActionEventArgs` classe. Cada um dos projetos plataforma incluem um `TouchEffect` de classe para a plataforma; o projeto iOS também contém um `TouchRecognizer` classe.

O **dedo pintura** página **SkiaSharpFormsDemos** é uma implementação simplificada de pintura a dedo. Não permitir a seleção de cor ou largura de caracteres, ele não tem como limpar a tela e obviamente você não pode salvar sua arte final.

O [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) arquivo coloca o `SKCanvasView` em uma única célula `Grid` e anexa o `TouchEffect` ao `Grid`:

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

O [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) arquivo code-behind define duas coleções para armazenar o `SKPath` objetos, bem como um `SKPaint` objeto para processar esses caminhos:

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

Como o nome sugere, o `inProgressPaths` dicionário armazena os caminhos que estão atualmente sendo desenhados por dedos de uma ou mais. A chave de dicionário é a ID de toque que acompanha os eventos de toque. O `completedPaths` campo é uma coleção de caminhos que foram concluídas quando um dedo na tela de desenho o caminho foi eliminado.

O `TouchAction` manipulador gerencia essas duas coleções. Quando um dedo toca primeiro a tela, um novo `SKPath` é adicionado ao `inProgressPaths`. À medida que dedo movido, pontos adicionais são adicionados ao caminho. Quando o dedo for lançado, o caminho é transferido para o `completedPaths` coleção. Você pode pintar com vários dedos simultaneamente. Após cada alteração em um dos caminhos ou coleções, o `SKCanvasView` é invalidado:

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

Os pontos que acompanha os eventos de rastreamento de toque são xamarin. Forms coordenadas; Esses devem ser convertidos em coordenadas de SkiaSharp, que são pixels. Essa é a finalidade de `ConvertToPixel` método.

O `PaintSurface` manipulador simplesmente renderiza ambas as coleções de caminhos. Os caminhos concluídos anteriormente aparecem sob os caminhos em andamento:

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

Pinturas seu dedo são limitadas apenas pelo seu talent:

[![](finger-paint-images/fingerpaint-small.png "Tripla captura de tela da página de pintura dedo")](finger-paint-images/fingerpaint-large.png#lightbox "tripla captura de tela da página de pintura dedo")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Controle de toque efeito demonstrações (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
