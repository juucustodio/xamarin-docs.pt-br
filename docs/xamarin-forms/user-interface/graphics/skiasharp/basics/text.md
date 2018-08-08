---
title: A integração do texto e elementos gráficos
description: Este artigo explica como determinar o tamanho da cadeia de caracteres do texto renderizado para integrar o texto com elementos gráficos de SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: be1524029ada79896f83517c3b439f2ad0e2c6d9
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615155"
---
# <a name="integrating-text-and-graphics"></a>A integração do texto e elementos gráficos

_Veja como determinar o tamanho da cadeia de caracteres de texto renderizado para integrar o texto com elementos gráficos de SkiaSharp_

Este artigo demonstra como medir texto, possivelmente, dimensionar o texto para um tamanho específico e integrar o texto com outros gráficos:

![](text-images/textandgraphicsexample.png "Texto cercado por retângulos")

O SkiaSharp `Canvas` classe também inclui métodos para desenhar um retângulo ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) e um retângulo com cantos arredondados ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Esses métodos exigem o retângulo a ser definido como um `SKRect` valor.

O **enquadrados texto** página centros de uma cadeia de caracteres de texto curto na página e bordadura-lo com um quadro composto por um par de retângulos arredondados. O [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe mostra como isso é feito.

SkiaSharp você usa o `SKPaint` classe para definir atributos de fonte e texto, mas você também pode usá-la para obter o tamanho do texto renderizado. O início dos seguintes `PaintSurface` manipulador de eventos chama duas diferentes `MeasureText` métodos. A primeira [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) chamada tem um simples `string` argumento e retorna a largura de pixel do texto com base nos atributos de fonte atual. O programa, em seguida, calcula uma nova `TextSize` propriedade do `SKPaint` objeto com base no que largura renderizada, atual `TextSize` propriedade e a largura da área de exibição. Isso serve para definir `TextSize` para que o texto da cadeia de caracteres a serem processados em 90% da largura da tela:

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

A segunda [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) chamada tem um `SKRect` argumento, portanto, ele obtém uma largura e a altura do texto renderizado. O `Height` propriedade deste `SKRect` valor depende da presença de letras maiusculas, ascendentes e descendentes na cadeia de texto. Diferentes `Height` valores são relatados para o mom"cadeias de caracteres de texto", "gato" e "cachorro", por exemplo.

O `Left` e `Top` propriedades da `SKRect` estrutura indicam as coordenadas do canto superior esquerdo do texto renderizado, se o texto é exibido por um `DrawText` chamar com posições de X e Y de 0. Por exemplo, quando este programa está em execução em um simulador de iPhone 7 `TextSize` é atribuído o valor 90.6254 como resultado do cálculo após a primeira chamada para `MeasureText`. O `SKRect` valor obtido a segunda chamada para `MeasureText` tem os seguintes valores de propriedade:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Lembre-se de que as coordenadas X e Y você passar para o `DrawText` método especificar à esquerda do texto na linha de base. O `Top` valor indica que o texto estende 68 pixels acima essa linha de base e (subtraindo it 68 de 88) 20 pixels abaixo da linha de base. O `Left` valor 6 indica que o texto começa 6 pixels à direita do valor X no `DrawText` chamar. Isso possibilita o espaçamento entre caracteres normal. Se você quiser exibir o texto confortavelmente no canto superior esquerdo da tela, passe os negativos desses `Left` e `Top` valores como as coordenadas X e Y da `DrawText`, neste exemplo, &ndash;6 e 68.

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

O `PaintSurface` manipulador conclui com duas chamadas para `DrawRoundRect`, sendo que ambos requerem argumentos de `SKRect`. Isso `SKRect` valor é, certamente, semelhante de `SKRect` valor obtido o `MeasureText` método, mas ele não pode ser o mesmo. Primeiro, ele precisa ser um pouco maior para que o retângulo arredondado não desenha sobre bordas do texto e, em segundo lugar, ele precisa ser deslocado no espaço para que o `Left` e `Top` valores correspondem ao canto superior esquerdo, onde o retângulo deve ser posicionado. Esses dois trabalhos são realizados pelos `Offset` e `Inflate` métodos definidos pelo `SKRect`:

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

Depois disso, o restante do método é simples. Ele cria outra `SKPaint` objeto para as bordas e chamadas `DrawRoundRect` duas vezes. A segunda chamada usa um retângulo inflado outro 10 pixels. A primeira chamada especifica um raio de canto de 20 pixels. o segundo tem um raio de canto de 30 pixels, para que eles parecem ser paralela:

 [![](text-images/framedtext-small.png "Captura de tela da página de texto estruturado tripla")](text-images/framedtext-large.png#lightbox "tripla captura de tela da página de texto estruturado")

Você pode transformar seu telefone ou o simulador lateralmente para ver o texto e o aumento no tamanho do quadro.

Se você precisar apenas para o Centro de algum texto na tela, você pode fazer isso aproximadamente sem medindo o texto, definindo o `TextAlign` propriedade de `SKPaint` para `SKTextAlign.Center`. A coordenada X que você especificar o `DrawText` método indica, em seguida, em que o centro horizontal do texto é posicionado. Se você passar o ponto central da tela para o `DrawText` método, o texto será centralizado horizontalmente e *quase* centralizado verticalmente porque a linha de base será centralizada verticalmente.

Texto em si pode ser tratado como uma opção gráfica muito. Uma opção simple é exibir o contorno de caracteres de texto em vez da exibição preenchida normal:

[![](text-images/outlinedtext-small.png "Triplicar a captura de tela da página de texto contornado")](text-images/outlinedtext-large.png#lightbox "triplicar a captura de tela da página de texto contornado")

Isso é feito simplesmente alterando o vetor perpendicular `Style` propriedade do `SKPaint` objeto da sua configuração padrão de `SKPaintStyle.Fill` para `SKPaintStyle.Stroke` e especificando uma largura do traço. O `PaintSurface` manipulador do **texto contornado** página mostra como isso é feito:

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

 Em vários artigos anteriores, você tem agora viu como usar SkiaSharp para desenhar o texto, círculos, elipses e retângulos arredondados. A próxima etapa é [caminhos e linhas de SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) no qual você aprenderá como desenhar linhas conectadas em um caminho de gráficos.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
