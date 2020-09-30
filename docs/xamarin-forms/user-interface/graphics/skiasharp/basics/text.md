---
title: Integração de texto e elementos gráficos
description: Este artigo explica como determinar o tamanho da cadeia de caracteres de texto renderizado para integrar texto com gráficos do SkiaSharp em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 032a01a1e4e0f2b3e3d394aec6a30bd215fd84f8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562412"
---
# <a name="integrating-text-and-graphics"></a>Integração de texto e elementos gráficos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Consulte como determinar o tamanho da cadeia de caracteres de texto renderizado para integrar texto com gráficos SkiaSharp_

Este artigo demonstra como medir texto, dimensionar o texto para um tamanho específico e integrar texto com outros elementos gráficos:

![Texto rodeado por retângulos](text-images/textandgraphicsexample.png)

Essa imagem também inclui um retângulo arredondado. A `Canvas` classe SkiaSharp inclui [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) métodos para desenhar um retângulo e [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) métodos para desenhar um retângulo com cantos arredondados. Esses métodos permitem que o retângulo seja definido como um `SKRect` valor ou de outras maneiras.

A página de **texto com quadros** centraliza uma pequena cadeia de caracteres de texto na página e a circunda com um quadro composto por um par de retângulos arredondados. A [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe mostra como isso é feito.

No SkiaSharp, você usa a `SKPaint` classe para definir os atributos Text e Font, mas também pode usá-lo para obter o tamanho renderizado do texto. O início do manipulador de `PaintSurface` eventos a seguir chama dois `MeasureText` métodos diferentes. A primeira [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) chamada tem um `string` argumento simples e retorna a largura de pixel do texto com base nos atributos de fonte atuais. Em seguida, o programa calcula uma nova `TextSize` Propriedade do `SKPaint` objeto com base na largura renderizada, na `TextSize` propriedade atual e na largura da área de exibição. Esse cálculo deve ser definido para `TextSize` que a cadeia de texto seja renderizada em 90% da largura da tela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

A segunda [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) chamada tem um `SKRect` argumento, portanto, ela obtém a largura e a altura do texto renderizado. A `Height` propriedade desse `SKRect` valor depende da presença de letras maiúsculas, ascendentes e descendentes na cadeia de texto. `Height`Valores diferentes são relatados para as cadeias de caracteres de texto "Mom", "gato" e "cachorro", por exemplo.

As `Left` `Top` Propriedades e da `SKRect` estrutura indicam as coordenadas do canto superior esquerdo do texto renderizado se o texto for exibido por uma `DrawText` chamada com as posições X e Y de 0. Por exemplo, quando esse programa estiver em execução em um simulador do iPhone 7, `TextSize` o valor 90,6254 será atribuído como resultado do cálculo após a primeira chamada para `MeasureText` . O `SKRect` valor obtido da segunda chamada para `MeasureText` tem os seguintes valores de propriedade:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664,8214
- `Height` = 88;

Tenha em mente que as coordenadas X e Y que você passa para o `DrawText` método especificam o lado esquerdo do texto na linha de base. O `Top` valor indica que o texto estende 68 pixels acima da linha de base e (subtraindo 68 de 88) 20 pixels abaixo da linha de base. O `Left` valor de 6 indica que o texto começa com seis pixels à direita do valor X na `DrawText` chamada. Isso permite um espaçamento entre caracteres normal. Se você quiser exibir o texto de forma confortavel no canto superior esquerdo da tela, passe os negativos desses `Left` `Top` valores e como as coordenadas X e Y de `DrawText` , neste exemplo, &ndash; 6 e 68.

A `SKRect` estrutura define várias propriedades e métodos práticos, algumas das quais são usadas no restante do `PaintSurface` manipulador. Os `MidX` `MidY` valores e indicam as coordenadas do centro do retângulo. (No exemplo do iPhone 7, esses valores são 338,4107 e &ndash; 24.) O código a seguir usa esses valores para o cálculo mais fácil de coordenadas para centralizar o texto na exibição:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

A `SKImageInfo` estrutura de informações também define uma [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) Propriedade do tipo `SKRect` , de modo que você também pode calcular `xText` e `yText` assim:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

O `PaintSurface` manipulador termina com duas chamadas para `DrawRoundRect` , que exigem argumentos de `SKRect` . Esse `SKRect` valor se baseia no `SKRect` valor obtido do `MeasureText` método, mas não pode ser o mesmo. Primeiro, ele precisa ser um pouco maior para que o retângulo arredondado não desenhe as bordas do texto. Em segundo lugar, ele precisa ser deslocado no espaço para que `Left` os `Top` valores e correspondam ao canto superior esquerdo onde o retângulo deve ser posicionado. Esses dois trabalhos são realizados pelos [`Offset`](xref:SkiaSharp.SKRect.Offset*) métodos e [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) definidos por `SKRect` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

Depois disso, o restante do método é direto. Ele cria outro `SKPaint` objeto para as bordas e chamadas `DrawRoundRect` duas vezes. A segunda chamada usa um retângulo com outros 10 pixels. A primeira chamada especifica um raio de canto de 20 pixels. O segundo tem um raio de canto de 30 pixels, portanto, eles parecem ser paralelos:

 [![Captura de tela tripla da página de texto com quadros](text-images/framedtext-small.png)](text-images/framedtext-large.png#lightbox "Captura de tela tripla da página de texto com quadros")

Você pode transformar seu telefone ou simulador para ver o tamanho do texto e do quadro.

Se você só precisa centralizar algum texto na tela, é possível fazê-lo aproximadamente sem medir o texto. Em vez disso, defina a [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) propriedade de `SKPaint` como o membro de enumeração [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) . A coordenada X que você especificar no `DrawText` método indicará onde o centro horizontal do texto será posicionado. Se você passar o ponto médio da tela para o `DrawText` método, o texto será horizontalmente centralizado e estará *quase* verticalmente centralizado porque a linha de base será verticalmente centralizada.

O texto pode ser tratado de forma semelhante a qualquer outro objeto gráfico. Uma opção simples é exibir o contorno dos caracteres de texto:

[![Captura de tela tripla da página de texto contornada](text-images/outlinedtext-small.png)](text-images/outlinedtext-large.png#lightbox "Captura de tela tripla da página de texto contornada")

Isso é feito simplesmente alterando a `Style` Propriedade normal do `SKPaint` objeto de sua configuração padrão de `SKPaintStyle.Fill` para `SKPaintStyle.Stroke` e especificando uma largura de traço. O `PaintSurface` manipulador da página de **texto contornada** mostra como isso é feito:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Outro objeto gráfico comum é o bitmap. Esse é um tópico grande abordado em detalhes na seção [**SkiaSharp bitmaps**](../bitmaps/index.md), mas o próximo artigo, [**noções básicas de bitmap no SkiaSharp**](bitmaps.md), fornece uma introdução mais rápida.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)