---
title: Desenho de um círculo simples
description: Conheça os fundamentos de desenho SkiaSharp, incluindo telas e pintura
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: a718d051c2bf04e75c87b0e4a18aced4824802ba
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="drawing-a-simple-circle"></a>Desenho de um círculo simples

_Conheça os fundamentos de desenho SkiaSharp, incluindo telas e pintura_

Este artigo apresenta os conceitos de desenho de elementos gráficos no xamarin. Forms usando SkiaSharp, incluindo a criação de um `SKCanvasView` objeto para hospedar os gráficos, manipulação de `PaintSurface` eventos e usando um `SKPaint` objeto para especificar a cor e outro desenho atributos.

O [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa contém todo o código de exemplo para esta série de artigos SkiaSharp. A primeira página é intitulada **círculo simples** e invoca a classe da página [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Este código mostra como desenhar um círculo no centro da página com um raio de 100 pixels. A estrutura de tópicos do círculo é vermelha, e o interior do círculo é azul.

![](circle-images/circleexample.png "Um círculo azul realçado em vermelho")

O [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) página classe é derivada de `ContentPage` e contém duas `using` diretivas para os namespaces SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Cria o seguinte construtor da classe um [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) de objeto, anexa um manipulador para o [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) eventos e define o `SKCanvasView` objeto como o conteúdo da página:

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

O `PaintSurface` manipulador de eventos é onde você pode fazer todo o desenho. Este método é geralmente chamado várias vezes enquanto o programa estiver em execução, para que ele deve manter todas as informações necessárias para recriar o vídeo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

O [ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) objeto que acompanha o evento tem duas propriedades:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) do tipo [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) do tipo [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

O `SKImageInfo` estrutura contém informações sobre a superfície de desenho, mais importante, é largura e altura em pixels. O `SKSurface` objeto representa a superfície de desenho. Esse programa, a superfície de desenho é um monitor de vídeo, mas em outros programas um `SKSurface` objeto também pode representar um bitmap que usam SkiaSharp no qual desenhar.

A propriedade mais importante de `SKSurface` é [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) do tipo [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Essa classe é um contexto que você usa para executar o desenho real de desenho de elementos gráficos. O `SKCanvas` objeto encapsula um estado de gráficos, que inclui transformações gráficas e recortes.

Aqui está um início típico de um `PaintSurface` manipulador de eventos:

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

O [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) método limpa a tela com uma cor transparente. Uma sobrecarga permite que você especifique uma cor de plano de fundo para a tela.

A meta aqui é desenhar um círculo vermelho preenchido com azul. Como esta imagem de gráfico específica contém duas cores diferentes, o trabalho deve ser feito em duas etapas. A primeira etapa é desenhar o contorno do círculo. Para especificar a cor e outras características da linha, você pode criar e inicializar uma [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) objeto:

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

O [ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/) propriedade indica que você deseja *traço* uma linha (no caso da estrutura de tópicos do círculo) em vez de *preenchimento* o interior. Os três membros do [ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/) enumeração são os seguintes:

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

O padrão é `Fill`. Use a terceira opção para traçar a linha e preencher o interior com a mesma cor.

Definir o [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) propriedade para um valor do tipo [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). Uma maneira de obter um `SKColor` valor estiver convertendo um xamarin. Forms `Color` valor para um `SKColor` valor usando o método de extensão [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). O [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) classe no `SkiaSharp.Views.Forms` namespace inclui outros métodos que converter entre valores xamarin. Forms e SkiaSharp.

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

As coordenadas são especificadas em relação ao canto superior esquerdo da superfície de exibição. Coordenadas X aumente à direita e o aumento de coordenadas Y ficar inativo. A discussão sobre gráficos, geralmente a notação de matemática (x, y) é usada para denotar um ponto. O ponto (0, 0) é o canto superior esquerdo da superfície de exibição e é geralmente chamado de *origem*.

Os dois primeiros argumentos de `DrawCircle` indicar as coordenadas X e Y do centro do círculo. Eles são atribuídos a metade a largura e altura da superfície de exibição para colocar o centro do círculo no centro da superfície de exibição. O terceiro argumento especifica o raio do círculo, e o último argumento é o `SKPaint` objeto.

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

Aqui está o programa em execução no iOS, Android e a plataforma Universal do Windows:

[![](circle-images/simplecircle-small.png "Captura de tela da página círculo simples tripla")](circle-images/simplecircle-large.png#lightbox "tripla captura da página do círculo simples")

Ao executar o programa, você pode ativar o telefone ou o simulador lado para ver como o gráfico é redesenhado. Cada vez que o gráfico deve ser redesenhado, o `PaintSurface` manipulador de eventos é chamado novamente.

Um `SKPaint` objeto é um pouco mais de uma coleção de propriedades de desenho de elementos gráficos. Esses objetos são muito simples. Você pode reutilizar `SKPaint` objetos como esse programa não ou você pode criar vários `SKPaint` objetos para várias combinações de propriedades de desenho. Você pode criar e inicializar esses objetos fora do `PaintSurface` manipulador de eventos e você poderá salvá-los como campos na sua classe de página.

Embora a largura do contorno do círculo é especificada como 25 pixels &mdash; ou um quarto de raio do círculo &mdash; parece ser mais estreito, e há um bom motivo para isso: metade da largura da linha é obscurecida pelo círculo azul. Os argumentos para o `DrawCircle` método definem as coordenadas geométricas abstratas de um círculo. O interior azul é dimensionado para essa dimensão para o pixel mais próximo, mas a estrutura de tópicos de 25 pixels de largura amplia o círculo Geométrico &mdash; metade em interna e metade fora.

O exemplo seguinte o [integrando xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) artigo demonstra isso visualmente.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
