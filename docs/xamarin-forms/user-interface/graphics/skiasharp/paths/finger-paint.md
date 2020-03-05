---
title: Pintura a dedo em SkiaSharp
description: Este artigo explica como usar os dedos para pintar a tela de SkiaSharp em um aplicativo xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: d5cf0927c64732d6d0a44204db9509fae77f0d1d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292529"
---
# <a name="finger-painting-in-skiasharp"></a>Pintura a dedo em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use seus dedos para pintar na tela._

Um objeto de `SKPath` pode ser atualizado e exibido continuamente. Esse recurso permite que um caminho a ser usado para o desenho interativo, como em um programa para pintura com dedo.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

O suporte a toque no xamarin. Forms não permite o acompanhamento de dedos individuais na tela, portanto, um efeito de controle de toque do xamarin. Forms foi desenvolvido para oferecer suporte a toque adicionais. Esse efeito é descrito no artigo [**invocando eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). As [**demonstrações de efeito de controle de toque**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) do programa de exemplo incluem duas páginas que usam SkiaSharp, incluindo um programa de pintura a dedo.

A solução [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclui esse evento de acompanhamento de toque. O projeto de biblioteca .NET Standard inclui a classe `TouchEffect`, a enumeração `TouchActionType`, o delegado `TouchActionEventHandler` e a classe `TouchActionEventArgs`. Cada um dos projetos de plataforma inclui uma classe de `TouchEffect` para essa plataforma; o projeto do iOS também contém uma classe `TouchRecognizer`.

A página de **pintura digital** no **SkiaSharpFormsDemos** é uma implementação simplificada da pintura de dedos. Não permitir a seleção de cor ou largura de traço, ele não tem nenhuma maneira para limpar a tela e certamente não é possível salvar sua arte final.

O arquivo [**FingerPaintPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) coloca o `SKCanvasView` em um `Grid` de célula única e anexa o `TouchEffect` a esse `Grid`:

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

Anexar o `TouchEffect` diretamente ao `SKCanvasView` não funciona em todas as plataformas.

O arquivo code-behind [**FingerPaintPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) define duas coleções para armazenar os objetos `SKPath`, bem como um objeto `SKPaint` para processar esses caminhos:

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

Como o nome sugere, o dicionário de `inProgressPaths` armazena os caminhos que estão sendo desenhados atualmente por um ou mais dedos. A chave do dicionário é a ID de toque que acompanha os eventos de toque. O campo `completedPaths` é uma coleção de caminhos que foram concluídos quando um dedo que estava desenhando o caminho foi levantado da tela.

O manipulador de `TouchAction` gerencia essas duas coleções. Quando um dedo toca a tela pela primeira vez, um novo `SKPath` é adicionado ao `inProgressPaths`. Conforme desse dedo se move, pontos adicionais são adicionados ao caminho. Quando o dedo é liberado, o caminho é transferido para a coleção de `completedPaths`. Você pode pintar com vários dedos simultaneamente. Após cada alteração em um dos caminhos ou coleções, a `SKCanvasView` é invalidada:

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

Os pontos que acompanha os eventos de rastreamento de toque são coordenadas do xamarin. Forms; eles devem ser convertidos em coordenadas de SkiaSharp, que são pixels. Essa é a finalidade do método `ConvertToPixel`.

O manipulador de `PaintSurface`, em seguida, simplesmente renderiza as duas coleções de caminhos. Os caminhos concluídos anteriormente aparecem sob os caminhos em andamento:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
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

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Agora, você já viu como desenhar linhas e definir curvas usando equações paramétricas. Uma seção posterior em [**curvas e caminhos de SkiaSharp**](../curves/index.md) abrange os vários tipos de curvas aos quais `SKPath` dá suporte. Mas um pré-requisito útil é uma exploração de [**transformações de SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demonstrações de efeito de controle de toque (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Invocando eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
