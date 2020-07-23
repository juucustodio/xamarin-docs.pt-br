---
title: Pintura de dedos em SkiaSharp
description: Este artigo explica como usar seus dedos para pintar na tela do SkiaSharp em um Xamarin.Forms aplicativo e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 074b782bfc5458cf89e593913b6f31de633ffd73
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938145"
---
# <a name="finger-painting-in-skiasharp"></a>Pintura de dedos em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use seus dedos para pintar na tela._

Um `SKPath` objeto pode ser atualizado e exibido continuamente. Esse recurso permite que um caminho seja usado para desenho interativo, como em um programa de pintura a dedos.

![Um exercício na pintura de dedos](finger-paint-images/fingerpaintsample.png)

O suporte ao touch no não Xamarin.Forms permite o acompanhamento de dedos individuais na tela, portanto, um Xamarin.Forms efeito de controle de toque foi desenvolvido para fornecer suporte adicional ao toque. Esse efeito é descrito no artigo [**invocando eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). As [**demonstrações de efeito de controle de toque**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) do programa de exemplo incluem duas páginas que usam SkiaSharp, incluindo um programa de pintura a dedo.

A solução [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclui esse evento de acompanhamento de toque. O projeto de biblioteca .NET Standard inclui a `TouchEffect` classe, a `TouchActionType` enumeração, o `TouchActionEventHandler` delegado e a `TouchActionEventArgs` classe. Cada um dos projetos de plataforma inclui uma `TouchEffect` classe para essa plataforma; o projeto do IOS também contém uma `TouchRecognizer` classe.

A página de **pintura digital** no **SkiaSharpFormsDemos** é uma implementação simplificada da pintura de dedos. Ele não permite selecionar a largura da cor ou do traço, não tem como limpar a tela e, claro, você não pode salvar seu trabalho artístico.

O arquivo [**FingerPaintPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) coloca o `SKCanvasView` em uma única célula `Grid` e anexa o `TouchEffect` a ele `Grid` :

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

O arquivo code-behind [**FingerPaintPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) define duas coleções para armazenar os `SKPath` objetos, bem como um `SKPaint` objeto para renderizar esses caminhos:

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

Como o nome sugere, o `inProgressPaths` dicionário armazena os caminhos que estão sendo desenhados atualmente por um ou mais dedos. A chave de dicionário é a ID de toque que acompanha os eventos de toque. O `completedPaths` campo é uma coleção de caminhos que foram concluídos quando um dedo que estava desenhando o caminho foi levantado da tela.

O `TouchAction` manipulador gerencia essas duas coleções. Quando um dedo toca a tela pela primeira vez, um novo `SKPath` é adicionado ao `inProgressPaths` . Conforme o dedo se move, pontos adicionais são adicionados ao caminho. Quando o dedo é liberado, o caminho é transferido para a `completedPaths` coleção. Você pode pintar com vários dedos simultaneamente. Após cada alteração em um dos caminhos ou coleções, o `SKCanvasView` é invalidado:

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

Os pontos que acompanham os eventos de acompanhamento de toque são Xamarin.Forms coordenadas; eles devem ser convertidos em coordenadas SkiaSharp, que são pixels. Essa é a finalidade do `ConvertToPixel` método.

O `PaintSurface` manipulador simplesmente renderiza as duas coleções de caminhos. Os caminhos concluídos anteriormente aparecem abaixo dos caminhos em andamento:

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

Seus dedos são limitados apenas pelo seu talento:

[![Captura de tela tripla da página Pintura a Dedo](finger-paint-images/fingerpaint-small.png)](finger-paint-images/fingerpaint-large.png#lightbox "Captura de tela tripla da página Pintura a Dedo")

Agora você viu como desenhar linhas e definir curvas usando equações paramétricas. Uma seção posterior em [**curvas e caminhos de SkiaSharp**](../curves/index.md) abrange os vários tipos de curvas `SKPath` com suporte no. Mas um pré-requisito útil é uma exploração de [**transformações de SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demonstrações de efeito de controle de toque (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Invocando eventos por meio de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
