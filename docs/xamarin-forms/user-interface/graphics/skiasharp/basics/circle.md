---
title: Desenhando um círculo simples em SkiaSharp
description: Este artigo explica os conceitos básicos do desenho do SkiaSharp, incluindo telas e objetos de pintura, em Xamarin.Forms aplicativos, e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 39e9effa1c43a4456a1e75e391e14804e2aa0011
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375090"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Desenhando um círculo simples em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprenda as noções básicas do desenho SkiaSharp, incluindo telas e objetos de pintura_

Este artigo apresenta os conceitos de desenho de elementos gráficos no Xamarin.Forms usando o SkiaSharp, incluindo a criação de um `SKCanvasView` objeto para hospedar os gráficos, o tratamento do `PaintSurface` evento e o uso de um `SKPaint` objeto para especificar a cor e outros atributos de desenho.

O programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contém todo o código de exemplo para esta série de artigos do SkiaSharp. A primeira página é denominada **círculo simples** e invoca a classe de página [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) . Este código mostra como desenhar um círculo no centro da página com um raio de 100 pixels. O contorno do círculo é vermelho e o interior do círculo é azul.

![Um círculo azul descrito em vermelho](circle-images/circleexample.png)

A [`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) classe de página deriva de `ContentPage` e contém duas `using` diretivas para os namespaces SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

O Construtor a seguir da classe cria um [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) objeto, anexa um manipulador para o [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) evento e define o `SKCanvasView` objeto como o conteúdo da página:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

O `SKCanvasView` ocupa toda a área de conteúdo da página. Como alternativa, você pode combinar um `SKCanvasView` com outros Xamarin.Forms `View` derivativos, como verá em outros exemplos.

O `PaintSurface` manipulador de eventos é onde você faz todo o desenho. Esse método pode ser chamado várias vezes enquanto o programa está em execução, portanto, ele deve manter todas as informações necessárias para recriar a exibição dos gráficos:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

O [`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) objeto que acompanha o evento tem duas propriedades:

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) do tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) do tipo [`SKSurface`](xref:SkiaSharp.SKSurface)

A `SKImageInfo` estrutura contém informações sobre a superfície de desenho, o mais importante, sua largura e altura em pixels. O `SKSurface` objeto representa a superfície de desenho em si. Neste programa, a superfície de desenho é uma exibição de vídeo, mas em outros programas um `SKSurface` objeto também pode representar um bitmap que você usa SkiaSharp para desenhar.

A propriedade mais importante de `SKSurface` é [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) do tipo [`SKCanvas`](xref:SkiaSharp.SKCanvas) . Essa classe é um contexto de desenho gráfico que você usa para executar o desenho real. O `SKCanvas` objeto encapsula um estado gráfico, que inclui transformações e recortes gráficos.

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

O [`Clear`](xref:SkiaSharp.SKCanvas.Clear) método limpa a tela com uma cor transparente. Uma sobrecarga permite especificar uma cor de plano de fundo para a tela.

A meta aqui é desenhar um círculo vermelho preenchido com azul. Como essa imagem gráfica específica contém duas cores diferentes, o trabalho precisa ser feito em duas etapas. A primeira etapa é desenhar o contorno do círculo. Para especificar a cor e outras características da linha, você cria e inicializa um [`SKPaint`](xref:SkiaSharp.SKPaint) objeto:

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

A [`Style`](xref:SkiaSharp.SKPaint.Style) propriedade indica que você deseja *traçar* uma linha (neste caso, o contorno do círculo) em vez de *preencher* o interior. Os três membros da [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) enumeração são os seguintes:

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

O padrão é `Fill`. Use a terceira opção para traçar a linha e preencher o interior com a mesma cor.

Defina a [`Color`](xref:SkiaSharp.SKPaint.Color) propriedade como um valor do tipo [`SKColor`](xref:SkiaSharp.SKColor) . Uma maneira de obter um `SKColor` valor é convertendo um Xamarin.Forms `Color` valor em um `SKColor` valor usando o método de extensão [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) . A [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions) classe no `SkiaSharp.Views.Forms` namespace inclui outros métodos que convertem entre Xamarin.Forms valores e valores SkiaSharp.

A [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propriedade indica a espessura da linha. Aqui está definido como 25 pixels.

Você usa esse `SKPaint` objeto para desenhar o círculo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

As coordenadas são especificadas em relação ao canto superior esquerdo da superfície de exibição. As coordenadas X aumentam para a direita e as coordenadas Y aumentam a saída. Em discussão sobre gráficos, geralmente a notação matemática (x, y) é usada para denotar um ponto. O ponto (0, 0) é o canto superior esquerdo da superfície de exibição e geralmente é chamado de *origem*.

Os dois primeiros argumentos de `DrawCircle` indicam as coordenadas X e Y do centro do círculo. Elas são atribuídas à metade da largura e altura da superfície de exibição para colocar o centro do círculo no centro da superfície de exibição. O terceiro argumento especifica o raio do círculo e o último argumento é o `SKPaint` objeto.

Para preencher o interior do círculo, você pode alterar duas propriedades do `SKPaint` objeto e chamar `DrawCircle` novamente. Esse código também mostra uma maneira alternativa de obter um `SKColor` valor de um dos muitos campos da [`SKColors`](xref:SkiaSharp.SKColors) estrutura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

Desta vez, a `DrawCircle` chamada preenche o círculo usando as novas propriedades do `SKPaint` objeto.

Aqui está o programa em execução no iOS e no Android:

[![Captura de tela tripla da página de círculo simples](circle-images/simplecircle-small.png)](circle-images/simplecircle-large.png#lightbox "Captura de tela tripla da página de círculo simples")

Ao executar o programa por conta própria, você pode transformar o telefone ou o simulador no lado para ver como o gráfico é redesenhado. Cada vez que o gráfico precisa ser redesenhado, o `PaintSurface` manipulador de eventos é chamado novamente.

Também é possível colorir objetos gráficos com gradientes ou blocos de bitmap. Essas opções são discutidas na seção sobre [**sombreadores SkiaSharp**](../effects/shaders/index.md).

Um `SKPaint` objeto é pouco mais do que uma coleção de propriedades de desenho de gráficos. Esses objetos são leves. Você pode reutilizar `SKPaint` objetos conforme esse programa faz, ou pode criar vários `SKPaint` objetos para várias combinações de propriedades de desenho. Você pode criar e inicializar esses objetos fora do `PaintSurface` manipulador de eventos e pode salvá-los como campos em sua classe de página.

> [!NOTE]
> A `SKPaint` classe define um [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) para habilitar a suavização de serrilhado na renderização de seus elementos gráficos. A suavização de serrilhado geralmente resulta em bordas visualmente mais suaves, portanto, você provavelmente desejará definir essa propriedade como `true` na maioria de seus `SKPaint` objetos. Para fins de simplicidade, essa propriedade _não_ é definida na maioria das páginas de exemplo.

Embora a largura da estrutura de tópicos do círculo seja especificada como 25 pixels &mdash; ou um quarto do raio do círculo &mdash; , ela parece ser mais fina, e há um bom motivo para isso: metade da largura da linha é obscurecida pelo círculo azul. Os argumentos para o `DrawCircle` método definem as coordenadas geométricas abstratas de um círculo. O interior azul é dimensionado para a dimensão para o pixel mais próximo, mas a estrutura de tópicos de 25 pixels amplia a metade do círculo geométrico &mdash; na parte interna e na metade externa.

A próxima amostra no artigo [integração com Xamarin.Forms ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) demonstra isso visualmente.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)