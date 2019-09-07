---
title: Desenhar um círculo simples em SkiaSharp
description: Este artigo explica as Noções básicas de desenho do SkiaSharp, inclusive canvases e objetos de pintura, em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 110b2646fb7e1bda00c628749489c14a540e2b54
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759539"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Desenhar um círculo simples em SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda as Noções básicas de desenho do SkiaSharp, inclusive canvases e pintar objetos_

Este artigo apresenta os conceitos de desenho de gráficos no xamarin. Forms usando SkiaSharp, incluindo a criação de um `SKCanvasView` objeto para hospedar os gráficos, manipulação de `PaintSurface` event e usando um `SKPaint` objeto para especificar a cor e outro desenho atributos.

O [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa contém todo o código de exemplo para esta série de artigos de SkiaSharp. A primeira página é intitulada **círculo simples** e chama a classe page [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Este código mostra como desenhar um círculo no centro da página com um raio de 100 pixels. O contorno do círculo é vermelho e o interior do círculo é azul.

![](circle-images/circleexample.png "Um círculo azul descrito em vermelho")

O [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) deriva de classe page `ContentPage` e contém duas `using` diretivas para os namespaces de SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Cria o seguinte construtor da classe um [ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView) objeto, anexa um manipulador para o [ `PaintSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) eventos e define o `SKCanvasView` objeto como o conteúdo da página:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

O `SKCanvasView` ocupa a área de conteúdo inteira da página. Como alternativa, você pode combinar uma `SKCanvasView` com outros xamarin. Forms `View` derivados, como você verá em outros exemplos.

O `PaintSurface` manipulador de eventos é onde você pode fazer todos os seus desenhos. Esse método pode ser chamado várias vezes enquanto seu programa está em execução, para que ele deve manter todas as informações necessárias para recriar os gráficos de exibição:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

O [ `SKPaintSurfaceEventArgs` ](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) objeto que acompanha o evento tem duas propriedades:

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) do tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) do tipo [`SKSurface`](xref:SkiaSharp.SKSurface)

O `SKImageInfo` estrutura contém informações sobre a superfície de desenho, mais importante, sua largura e altura em pixels. O `SKSurface` objeto representa a superfície de desenho. Neste programa, a superfície de desenho é um monitor de vídeo, mas em outros programas um `SKSurface` objeto também pode representar um bitmap que você usar SkiaSharp para desenhar em.

A propriedade mais importante da `SKSurface` está [ `Canvas` ](xref:SkiaSharp.SKSurface.Canvas) do tipo [ `SKCanvas` ](xref:SkiaSharp.SKCanvas). Essa classe é uma contexto que você usa para executar o desenho real de desenho de gráficos. O `SKCanvas` objeto encapsula um estado de elementos gráficos, que inclui transformações gráficas e recorte.

Eis aqui um início típico de um `PaintSurface` manipulador de eventos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

O [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) método limpa a tela com uma cor transparente. Uma sobrecarga permite que você especificar uma cor de plano de fundo para a tela.

A meta aqui é desenhar um círculo vermelho preenchido com azul. Como essa imagem em gráfico particular contém duas cores diferentes, o trabalho precisa ser feito em duas etapas. A primeira etapa é desenhar o contorno do círculo. Para especificar a cor e outra característica da linha, você pode criar e inicializar uma [ `SKPaint` ](xref:SkiaSharp.SKPaint) objeto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

O [ `Style` ](xref:SkiaSharp.SKPaint.Style) propriedade indica que você deseja *traço* uma linha (no caso o contorno do círculo) em vez de *preenchimento* o interior. Os três membros do [ `SKPaintStyle` ](xref:SkiaSharp.SKPaintStyle) enumeração são da seguinte maneira:

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

O padrão é `Fill`. Use a terceira opção para traçar a linha e preencher o interior com a mesma cor.

Defina as [ `Color` ](xref:SkiaSharp.SKPaint.Color) propriedade para um valor do tipo [ `SKColor` ](xref:SkiaSharp.SKColor). É uma maneira de obter um `SKColor` valor está convertendo um xamarin. Forms `Color` valor para um `SKColor` valor usando o método de extensão [ `ToSKColor` ](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*). O [ `Extensions` ](xref:SkiaSharp.Views.Forms.Extensions) classe o `SkiaSharp.Views.Forms` namespace inclui outros métodos que convertem entre valores de xamarin. Forms e SkiaSharp.

O [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) propriedade indica a espessura da linha. Aqui, ele é definido como 25 pixels.

Você usá-lo `SKPaint` objeto para desenhar o círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

As coordenadas são especificadas em relação ao canto superior esquerdo da superfície de exibição. As coordenadas X aumentam para a direita e aumento de coordenadas Y ficar inativo. Na discussão sobre gráficos, geralmente a notação matemática (x, y) é usada para denotar um ponto. O ponto (0, 0) é o canto superior esquerdo da superfície de exibição e geralmente é chamado de *origem*.

Os dois primeiros argumentos de `DrawCircle` indicam as coordenadas X e Y do centro do círculo. Eles são atribuídos a metade da largura e altura da superfície de exibição para colocar o centro do círculo no centro da superfície de exibição. O terceiro argumento especifica o raio do círculo, e o último argumento é o `SKPaint` objeto.

Para preencher o interior do círculo, você pode alterar duas propriedades de `SKPaint` objeto e chame `DrawCircle` novamente. Esse código também mostra uma maneira alternativa de obter um `SKColor` valor de um dos muitos campos do [ `SKColors` ](xref:SkiaSharp.SKColors) estrutura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

Neste momento, o `DrawCircle` chamada preenche o círculo usando as novas propriedades do `SKPaint` objeto.

Aqui está o programa em execução no iOS, Android e plataforma Universal do Windows:

[![](circle-images/simplecircle-small.png "Captura de tela da página círculo simples tripla")](circle-images/simplecircle-large.png#lightbox "tripla captura de tela da página círculo simples")

Ao executar o programa, você pode ativar o telefone ou o simulador lateralmente para ver como o gráfico é redesenhado. Cada vez que o elemento gráfico precisa ser redesenhado, o `PaintSurface` manipulador de eventos é chamado novamente.

Também é possível colorir objetos gráficos com gradientes ou blocos de bitmap. Essas opções são discutidas na seção sobre [ **sombreadores de SkiaSharp**](../effects/shaders/index.md).

Um `SKPaint` objeto é pouco mais do que uma coleção de propriedades de desenho de gráficos. Esses objetos são leves. Você pode reutilizar `SKPaint` objetos como esse programa, ou você pode criar várias `SKPaint` objetos para várias combinações de propriedades de desenho. Você pode criar e inicializar esses objetos fora do `PaintSurface` manipulador de eventos e você pode salvá-los como campos na sua classe de página.

> [!NOTE]
> O `SKPaint` classe define um [ `IsAntialias` ](xref:SkiaSharp.SKPaint.IsAntialias) para habilitar a suavização no processamento de seus elementos gráficos. A suavização geralmente resulta em bordas visualmente mais suaves, portanto, você provavelmente desejará definir essa propriedade como `true` na maioria dos seus `SKPaint` objetos. Para fins de simplicidade, essa propriedade é _não_ definido na maioria das páginas de exemplo.

Embora a largura do contorno do círculo seja especificada como 25 pixels &mdash; ou um quarto do raio do círculo &mdash; , parece ser mais fino, e há um bom motivo para isso: Metade da largura da linha é obscurecida pelo círculo azul. Os argumentos para o `DrawCircle` método definem as coordenadas geométricas abstratas de um círculo. O interior azul é dimensionado para essa dimensão até o pixel mais próximo, mas a estrutura de tópicos de 25 pixels de largura permeiam círculo Geométrico &mdash; pela metade dentro e fora da metade.

O exemplo a seguir na [a integração com o xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) artigo demonstra isso visualmente.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
