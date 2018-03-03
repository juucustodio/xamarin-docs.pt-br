---
title: "Integração de texto e elementos gráficos"
description: "Consulte como determinar o tamanho da cadeia de caracteres de texto renderizado integrar texto SkiaSharp gráficos"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9a8cfac8911b1a8a13a4b6f149d02cd2f2c78453
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="integrating-text-and-graphics"></a>Integração de texto e elementos gráficos

_Consulte como determinar o tamanho da cadeia de caracteres de texto renderizado integrar texto SkiaSharp gráficos_

Este artigo demonstra como medir o texto, possivelmente dimensionar o texto para um determinado tamanho e integrar o texto com outros gráficos:

![](text-images/textandgraphicsexample.png "Texto entre os retângulos")

O SkiaSharp `Canvas` classe também inclui métodos para desenhar um retângulo ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) e um retângulo com cantos arredondados ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Esses métodos exigem o retângulo a ser definido como um `SKRect` valor.

O **texto estruturado** página centros de uma cadeia de caracteres de texto curto na página e envolve com um quadro composto de um par de retângulos arredondados. O [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe mostra como isso é feito.

SkiaSharp você usa o `SKPaint` classe para definir atributos de fonte e de texto, mas você também pode usá-la para obter o tamanho do texto renderizado. O início das seguintes `PaintSurface` manipulador de eventos chama duas diferentes `MeasureText` métodos. A primeira [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) chamada tem um simples `string` argumento e retorna a largura em pixels do texto com base nos atributos de fonte atual. O programa, em seguida, calcula uma nova `TextSize` propriedade o `SKPaint` objeto com base no que largura processada, atual `TextSize` propriedade e a largura da área de exibição. Isso serve para definir `TextSize` para que o texto da cadeia de caracteres para ser renderizado em 90% da largura da tela:

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
    SKRect textBounds;
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

A segunda [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) chamada tem um `SKRect` argumento, para que ele obtém de largura e altura do texto renderizado. O `Height` propriedade deste `SKRect` valor depende da presença de letras maiusculas, ascendentes e descendentes na cadeia de texto. Diferentes `Height` valores são relatados para o mom"cadeias de caracteres de texto", "cat" e "dog", por exemplo.

O `Left` e `Top` propriedades do `SKRect` estrutura indicar as coordenadas do canto superior esquerdo do texto renderizado se o texto é exibido por um `DrawText` chamar com posições de X e Y de 0. Por exemplo, quando o programa é executado em um simulador de iPhone 7, `TextSize` é atribuído o valor 90.6254 como resultado do cálculo após a primeira chamada para `MeasureText`. O `SKRect` valor obtido a segunda chamada para `MeasureText` tem os seguintes valores de propriedade:

- `Left` = 6
- `Top` = &#x2013;68
- `Width` = 664.8214
- `Height` = 88;

Tenha em mente que as coordenadas X e Y você passar para o `DrawText` método especificar à esquerda do texto na linha de base. O `Top` valor indica que o texto estende 68 pixels acima dessa linha de base e (subtrair it 68 de 88) 20 pixels abaixo da linha de base. O `Left` valor 6 indica que o texto começa 6 pixels à direita do valor X do `DrawText` chamar. Isso possibilita o espaçamento entre caracteres normal. Se você deseja exibir o texto perfeitamente no canto superior esquerdo da tela, passar negativos desses `Left` e `Top` valores como as coordenadas X e Y de `DrawText`, neste exemplo, & #x 2013; 6 e 68.

O `SKRect` estrutura define várias propriedades úteis e métodos, alguns dos quais são usados no restante do `PaintSurface` manipulador. O `MidX` e `MidY` valores indicam as coordenadas do centro do retângulo. (No exemplo iPhone 7, esses valores são 338.4107 e & #x 2013; 24.) O código a seguir usa esses valores para o cálculo mais fácil de coordenadas para centralizar o texto na tela:

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

O `PaintSurface` manipulador é concluído com duas chamadas para `DrawRoundRect`, que exigem argumentos de `SKRect`. Isso `SKRect` valor é certamente semelhante para o `SKRect` valor obtido o `MeasureText` método, mas ele não pode ser o mesmo. Primeiro, ele precisa ser um pouco maior para que o retângulo arredondado não desenhe sobre bordas do texto e, em segundo lugar, ele precisa ser alterado no espaço para que o `Left` e `Top` valores correspondem ao canto superior esquerdo onde o retângulo é para ser posicionado. Esses dois trabalhos são realizados por `Offset` e `Inflate` métodos definidos pelo `SKRect`:

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

Depois disso, o restante do método é direta. Ele cria outro `SKPaint` objeto de bordas e chamadas `DrawRoundRect` duas vezes. A segunda chamada usa um retângulo inflado outro 10 pixels. A primeira chamada especifica um raio de canto de 20 pixels. o segundo tem um raio de canto de 30 pixels, para que eles parecem ser paralela:

 [![](text-images/framedtext-small.png "Captura de tela da página de texto estruturado tripla")](text-images/framedtext-large.png "tripla captura de tela da página de texto estruturado")

Você pode ativar o seu telefone ou o simulador lado para ver o texto e o aumento do tamanho do quadro.

Se você precisar somente centralizar um texto na tela, você pode fazer isso aproximadamente sem medindo o texto, definindo o `TextAlign` propriedade `SKPaint` para `SKTextAlign.Center`. A coordenada X que você especificar o `DrawText` método indica, em seguida, em que o centro horizontal do texto é posicionado. Se você passar o ponto central da tela para o `DrawText` método, o texto será centralizado horizontalmente e *quase* verticalmente centralizados porque a linha de base será centralizada verticalmente.

Texto em si pode ser tratado como uma opção gráfica muito. É uma opção simple exibir a estrutura de tópicos de caracteres de texto em vez da exibição preenchida normal:

[![](text-images/outlinedtext-small.png "Tripla captura de tela da página de texto apresentadas")](text-images/outlinedtext-large.png "tripla captura de tela da página de texto apresentadas")

Isso é feito alterando o normal `Style` propriedade o `SKPaint` objeto da sua configuração padrão de `SKPaintStyle.Fill` para `SKPaintStyle.Stroke` e especificando uma largura do traço. O `PaintSurface` manipulador do **texto descritas** página mostra como isso é feito:

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
    SKRect textBounds;
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 Nos últimos vários artigos, você tem agora visto como usar SkiaSharp para desenhar o texto, círculos, elipses e retângulos arredondados. A próxima etapa é [SkiaSharp linhas e caminhos](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) no qual você aprenderá como desenhar linhas conectadas em um caminho de gráficos.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
