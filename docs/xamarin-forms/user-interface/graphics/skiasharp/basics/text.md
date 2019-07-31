---
title: Integração de texto e elementos gráficos
description: Este artigo explica como determinar o tamanho da cadeia de caracteres do texto renderizado para integrar o texto com elementos gráficos de SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d23b4dbb97f4f98ff0361bb056e394bb7cbd941a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645639"
---
# <a name="integrating-text-and-graphics"></a>Integração de texto e elementos gráficos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Veja como determinar o tamanho da cadeia de caracteres de texto renderizado para integrar o texto com elementos gráficos de SkiaSharp_

Este artigo demonstra como medir texto, dimensionar o texto para um tamanho específico e integrar o texto com outros gráficos:

![](text-images/textandgraphicsexample.png "Texto cercado por retângulos")

Essa imagem também inclui um retângulo arredondado. O SkiaSharp `Canvas` classe inclui [ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*) métodos para desenhar um retângulo e [ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*) cantos arredondados de métodos para desenhar um retângulo com. Esses métodos permitem que o retângulo a ser definido como um `SKRect` valor ou de outras maneiras.

O **enquadrados texto** página centros de uma cadeia de caracteres de texto curto na página e bordadura-lo com um quadro composto por um par de retângulos arredondados. O [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe mostra como isso é feito.

SkiaSharp, você usa o `SKPaint` classe para definir atributos de fonte e texto, mas você também pode usá-la para obter o tamanho do texto renderizado. O início dos seguintes `PaintSurface` manipulador de eventos chama duas diferentes `MeasureText` métodos. A primeira [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) chamada tem um simples `string` argumento e retorna a largura de pixel do texto com base nos atributos de fonte atual. O programa, em seguida, calcula uma nova `TextSize` propriedade do `SKPaint` objeto com base no que largura renderizada, atual `TextSize` propriedade e a largura da área de exibição. Esse cálculo destina-se definir `TextSize` para que o texto da cadeia de caracteres a serem processados em 90% da largura da tela:

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

A segunda [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) chamada tem um `SKRect` argumento, portanto, ele obtém uma largura e a altura do texto renderizado. O `Height` propriedade deste `SKRect` valor depende da presença de letras maiusculas, ascendentes e descendentes na cadeia de texto. Diferentes `Height` valores são relatados para o mom"cadeias de caracteres de texto", "gato" e "cachorro", por exemplo.

O `Left` e `Top` propriedades da `SKRect` estrutura indicam as coordenadas do canto superior esquerdo do texto renderizado, se o texto é exibido por um `DrawText` chamar com posições de X e Y de 0. Por exemplo, quando este programa está em execução em um simulador de iPhone 7 `TextSize` é atribuído o valor 90.6254 como resultado do cálculo após a primeira chamada para `MeasureText`. O `SKRect` valor obtido a segunda chamada para `MeasureText` tem os seguintes valores de propriedade:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Lembre-se de que as coordenadas X e Y você passar para o `DrawText` método especificar à esquerda do texto na linha de base. O `Top` valor indica que o texto estende 68 pixels acima essa linha de base e (subtraindo it 68 de 88) 20 pixels abaixo da linha de base. O `Left` valor 6 indica que o texto começa seis pixels à direita do valor X no `DrawText` chamar. Isso possibilita o espaçamento entre caracteres normal. Se você quiser exibir o texto confortavelmente no canto superior esquerdo da tela, passe os negativos desses `Left` e `Top` valores como as coordenadas X e Y da `DrawText`, neste exemplo, &ndash;6 e 68.

O `SKRect` estrutura define várias propriedades úteis e métodos, alguns dos quais são usados no restante do `PaintSurface` manipulador. O `MidX` e `MidY` valores indicam as coordenadas do centro do retângulo. (No exemplo iPhone 7, esses valores são 338.4107 e &ndash;24.) O código a seguir usa esses valores para o cálculo mais fácil de coordenadas para centralizar o texto na tela:

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

O `SKImageInfo` também define uma estrutura de informações de um [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) propriedade do tipo `SKRect`, portanto, você também pode calcular `xText` e `yText` como este:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

O `PaintSurface` manipulador conclui com duas chamadas para `DrawRoundRect`, sendo que ambos requerem argumentos de `SKRect`. Isso `SKRect` valor se baseia o `SKRect` valor obtido o `MeasureText` método, mas ele não pode ser o mesmo. Primeiro, ele precisa ser um pouco maior para que o retângulo arredondado não desenha sobre bordas do texto. Em segundo lugar, ele precisa ser deslocado no espaço para que o `Left` e `Top` valores correspondem ao canto superior esquerdo, em que o retângulo é posicionado. Esses dois trabalhos são realizados pela [ `Offset` ](xref:SkiaSharp.SKRect.Offset*) e [ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*) métodos definidos pelo `SKRect`:

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

Depois disso, o restante do método é simples. Ele cria outra `SKPaint` objeto para as bordas e chamadas `DrawRoundRect` duas vezes. A segunda chamada usa um retângulo inflado outro 10 pixels. A primeira chamada especifica um raio de canto de 20 pixels. O segundo tem um raio de canto de 30 pixels, para que eles parecem ser paralela:

 [![](text-images/framedtext-small.png "Captura de tela da página de texto estruturado tripla")](text-images/framedtext-large.png#lightbox "tripla captura de tela da página de texto estruturado")

Você pode transformar seu telefone ou o simulador lateralmente para ver o texto e o aumento no tamanho do quadro.

Se você precisar apenas para o Centro de algum texto na tela, você pode fazer isso aproximadamente sem medindo o texto. Em vez disso, defina as [ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign) propriedade do `SKPaint` para o membro de enumeração [ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign). A coordenada X que você especificar o `DrawText` método indica, em seguida, em que o centro horizontal do texto é posicionado. Se você passar o ponto central da tela para o `DrawText` método, o texto será centralizado horizontalmente e *quase* centralizado verticalmente porque a linha de base será centralizada verticalmente.

Texto pode ser tratado como qualquer outro objeto de gráfico muito. Uma opção simple é exibir a estrutura de tópicos dos caracteres de texto:

[![](text-images/outlinedtext-small.png "Triplicar a captura de tela da página de texto contornado")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Isso é feito simplesmente alterando o vetor perpendicular `Style` propriedade do `SKPaint` objeto da sua configuração padrão de `SKPaintStyle.Fill` para `SKPaintStyle.Stroke`e especificando uma largura do traço. O `PaintSurface` manipulador do **texto contornado** página mostra como isso é feito:

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

Outro objeto gráfico comum é o bitmap. Esse é um grande assunto abordado em detalhes na seção [ **SkiaSharp Bitmaps**](../bitmaps/index.md), mas o próximo artigo, [ **Noções básicas de Bitmap no SkiaSharp**](bitmaps.md), Fornece uma introdução mais resumida.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
