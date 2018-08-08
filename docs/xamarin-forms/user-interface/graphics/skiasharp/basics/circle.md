---
title: Desenhar um círculo simples em SkiaSharp
description: Este artigo explica as Noções básicas de desenho do SkiaSharp, inclusive canvases e paint, em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e06a1310fad01da11c8d8b115df504cc19426344
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615217"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Desenhar um círculo simples em SkiaSharp

_Aprenda as Noções básicas de desenho do SkiaSharp, inclusive canvases e pintura_

Este artigo apresenta os conceitos de desenho de gráficos no xamarin. Forms usando SkiaSharp, incluindo a criação de um `SKCanvasView` objeto para hospedar os gráficos, manipulação de `PaintSurface` event e usando um `SKPaint` objeto para especificar a cor e outro desenho atributos.

O [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa contém todo o código de exemplo para esta série de artigos de SkiaSharp. A primeira página é intitulada **círculo simples** e chama a classe page [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Este código mostra como desenhar um círculo no centro da página com um raio de 100 pixels. O contorno do círculo é vermelho e o interior do círculo é azul.

![](circle-images/circleexample.png "Um círculo azul descrito em vermelho")

O [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) deriva de classe page `ContentPage` e contém duas `using` diretivas para os namespaces de SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Cria o seguinte construtor da classe um [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) objeto, anexa um manipulador para o [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) eventos e define o `SKCanvasView` objeto como o conteúdo da página:

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

O `PaintSurface` manipulador de eventos é onde você pode fazer todos os seus desenhos. Esse método geralmente é chamado várias vezes enquanto seu programa está em execução, para que ele deve manter todas as informações necessárias para recriar os gráficos de exibição:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

O [ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) objeto que acompanha o evento tem duas propriedades:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) do tipo [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) do tipo [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

O `SKImageInfo` estrutura contém informações sobre a superfície de desenho, mais importante, é largura e altura em pixels. O `SKSurface` objeto representa a superfície de desenho. Neste programa, a superfície de desenho é um monitor de vídeo, mas em outros programas um `SKSurface` objeto também pode representar um bitmap que você usar SkiaSharp para desenhar em.

A propriedade mais importante da `SKSurface` está [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) do tipo [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Essa classe é uma contexto que você usa para executar o desenho real de desenho de gráficos. O `SKCanvas` objeto encapsula um estado de elementos gráficos, que inclui transformações gráficas e recorte.

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

O [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) método limpa a tela com uma cor transparente. Uma sobrecarga permite que você especificar uma cor de plano de fundo para a tela.

A meta aqui é desenhar um círculo vermelho preenchido com azul. Como essa imagem em gráfico particular contém duas cores diferentes, o trabalho precisa ser feito em duas etapas. A primeira etapa é desenhar o contorno do círculo. Para especificar a cor e outra característica da linha, você pode criar e inicializar uma [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) objeto:

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

O [ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/) propriedade indica que você deseja *traço* uma linha (no caso o contorno do círculo) em vez de *preenchimento* o interior. Os três membros do [ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/) enumeração são da seguinte maneira:

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

O padrão é `Fill`. Use a terceira opção para traçar a linha e preencher o interior com a mesma cor.

Defina as [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) propriedade para um valor do tipo [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). É uma maneira de obter um `SKColor` valor está convertendo um xamarin. Forms `Color` valor para um `SKColor` valor usando o método de extensão [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). O [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) classe o `SkiaSharp.Views.Forms` namespace inclui outros métodos que convertem entre valores de xamarin. Forms e SkiaSharp.

O [ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/) propriedade indica a espessura da linha. Aqui, ele é definido como 25 pixels.

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

Para preencher o interior do círculo, você pode alterar duas propriedades de `SKPaint` objeto e chame `DrawCircle` novamente. Esse código também mostra uma maneira alternativa de obter um `SKColor` valor de um dos muitos campos do [ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/) estrutura:

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

Um `SKPaint` objeto é pouco mais do que uma coleção de propriedades de desenho de gráficos. Esses objetos são muito simples. Você pode reutilizar `SKPaint` objetos como esse programa, ou você pode criar várias `SKPaint` objetos para várias combinações de propriedades de desenho. Você pode criar e inicializar esses objetos fora do `PaintSurface` manipulador de eventos e você pode salvá-los como campos na sua classe de página.

Embora a largura do contorno do círculo é especificada como 25 pixels &mdash; ou um quarto do raio do círculo &mdash; ele parece ser mais finos e há um bom motivo para isso: metade da largura da linha é obscurecida pelo círculo azul. Os argumentos para o `DrawCircle` método definem as coordenadas geométricas abstratas de um círculo. O interior azul é dimensionado para essa dimensão até o pixel mais próximo, mas a estrutura de tópicos de 25 pixels de largura permeiam círculo Geométrico &mdash; pela metade dentro e fora da metade.

O exemplo a seguir na [a integração com o xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) artigo demonstra isso visualmente.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
