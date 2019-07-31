---
title: Unidades independentes de dispositivo e pixels
description: Este artigo explora as diferenças entre coordenadas de SkiaSharp e xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: e2bf493a5d8a4197fbc59044edf126761b41cf8d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649721"
---
# <a name="pixels-and-device-independent-units"></a>Unidades independentes de dispositivo e pixels

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore as diferenças entre coordenadas de SkiaSharp e xamarin. Forms_

Este artigo explora as diferenças no sistema de coordenadas usado em SkiaSharp e xamarin. Forms. Você pode obter as informações necessárias para converter entre os dois sistemas de coordenadas e também desenhar gráficos que preenchem uma área específica:

![](pixels-images/screenfillexample.png "Uma elipse que ocupa a tela")

Se você tiver Programando no xamarin. Forms por algum tempo, você pode ter uma ideia de tamanhos e as coordenadas do xamarin. Forms. Os círculos desenhados nos dois artigos anteriores podem parecer um pouco pequenos para você.

Os círculos *são* pequeno em comparação com tamanhos de xamarin. Forms. Por padrão, SkiaSharp desenha em unidades de pixels, enquanto o xamarin. Forms baseia as coordenadas e tamanhos em uma unidade independente de dispositivo estabelecida pela plataforma subjacente. (Para obter mais informações sobre o sistema de coordenadas do xamarin. Forms podem ser encontradas no [capítulo 5. Lidando com tamanhos](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) do livro *criação de aplicativos móveis com xamarin. Forms*.)

A página na [ **SkewSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa intitulado **tamanho da superfície** usa saída de texto de SkiaSharp para mostrar o tamanho da superfície de exibição de três fontes diferentes:

- O xamarin. Forms normal [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) e [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propriedades do `SKCanvasView` objeto.
- O [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) propriedade o `SKCanvasView` objeto.
- O [ `Size` ](xref:SkiaSharp.SKImageInfo.Size) propriedade o `SKImageInfo` valor, que é consistente com o `Width` e `Height` propriedades usadas em duas páginas anteriores.

O [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe mostra como exibir esses valores. O construtor salva a `SKCanvasView` objeto como um campo, portanto, ela pode ser acessada no `PaintSurface` manipulador de eventos:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` inclui seis diferentes `DrawText` métodos, mas isso [ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) método é mais simples:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Você especifica a cadeia de caracteres de texto, as coordenadas X e Y em que o texto deve iniciar e um `SKPaint` objeto. A coordenada X especifica onde o lado esquerdo do texto está posicionado, mas observe: A coordenada Y especifica a posição da *linha de base* do texto. Se você já escreveu manualmente no papel pautado, a linha de base é a linha na qual caracteres sit e abaixo descendem quais descendentes (como aqueles no letras g, p, q e y).

O `SKPaint` objeto permite que você especifique a cor do texto, a família de fontes e o tamanho do texto. Por padrão, o [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize) propriedade tem um valor de 12, que resulta em texto pequeno em alta resolução dispositivos como telefones. Em nada mais simples aplicativos, você também precisará algumas informações sobre o tamanho do texto que você está exibindo. O `SKPaint` classe define um [ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics) propriedade e vários [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) métodos, mas para necessidades menos sofisticadas, o [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing) propriedade fornece um valor recomendado para linhas sucessivas de espaçamento de texto.

O seguinte `PaintSurface` manipulador cria um `SKPaint` do objeto para um `TextSize` de 40 pixels, que é a altura desejada vertical do texto na parte superior dos ascendentes na parte inferior do descendentes. O `FontSpacing` valor que o `SKPaint` objeto retorna é um pouco maior do que isso, sobre 47 pixels.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

O método começa a primeira linha de texto com uma coordenada X de 20 (para uma pequena margem à esquerda) e uma coordenada Y de `fontSpacing`, que é um pouco mais do que o que é necessário para exibir a altura total da primeira linha do texto na parte superior da superfície de exibição. Após cada chamada para `DrawText`, a coordenada Y é aumentada por um ou dois incrementos de `fontSpacing`.

Aqui está o programa em execução:

[![](pixels-images/surfacesize-small.png "Captura de tela da página de tamanho da superfície tripla")](pixels-images/surfacesize-large.png#lightbox "tripla captura de tela da página de tamanho da superfície")

Como você pode ver, o `CanvasSize` propriedade do `SKCanvasView` e o `Size` propriedade do `SKImageInfo` valor estão consistentes em relatar as dimensões de pixel. O `Height` e `Width` propriedades do `SKCanvasView` são propriedades do xamarin. Forms e o tamanho da exibição em unidades independentes de dispositivo definidas pela plataforma de relatório.

O simulador de iOS sete à esquerda tem dois pixels por unidade independente de dispositivo e o Android Nexus 5 no centro tem três pixels por unidade. É por isso que o círculo simples mostrado anteriormente tem tamanhos diferentes em diferentes plataformas.

Se você preferir trabalhar inteiramente em unidades independentes de dispositivo, você pode fazer isso definindo a `IgnorePixelScaling` propriedade do `SKCanvasView` para `true`. No entanto, você não pode gostar dos resultados. SkiaSharp renderiza os elementos gráficos em uma superfície menor do dispositivo, com um tamanho de pixel igual ao tamanho da exibição em unidades independentes de dispositivo. (Por exemplo, SkiaSharp usaria uma superfície de exibição de 360 x 512 pixels no Nexus 5.) Em seguida, dimensionado essa imagem em tamanho, resultando em jaggies bitmap perceptível.

Para manter a mesma resolução de imagem, uma solução melhor é escrever suas próprias funções simples para converter entre os dois sistemas de coordenadas.

Além de `DrawCircle` método, `SKCanvas` também define dois `DrawOval` métodos que desenhar uma elipse. Uma elipse é definida por dois raios em vez de um único raio. Eles são conhecidos como o *radius principais* e o *radius secundárias*. O `DrawOval` método desenha uma elipse com os dois raios paralelas aos eixos X e Y. (Se você precisar desenhar uma elipse com eixos que não são paralelos aos eixos X e Y, você pode usar uma transformação de rotação, conforme discutido no artigo [ **girar a transformar** ](../transforms/rotate.md) ou um caminho de gráficos, conforme discutido no artigo [ **três formas para desenhar um arco**](../curves/arcs.md)). Essa sobrecarga da [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) os parâmetros de dois raios nomes dos métodos `rx` e `ry` para indicar que eles são paralelos aos eixos X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

É possível desenhar uma elipse que preenche a superfície de exibição? O **preenchimento da elipse** página demonstra como. O `PaintSurface` manipulador de eventos na [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) classe subtrai metade da largura do traço do `xRadius` e `yRadius` valores de acordo com a elipse inteira e seu estrutura de tópicos na superfície de exibição:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

Aqui ele está em execução:

[![](pixels-images/ellipsefill-small.png "Captura de tela da página de tamanho da superfície tripla")](pixels-images/ellipsefill-large.png#lightbox "tripla captura de tela da página de tamanho da superfície")

A outra [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) método tem um [ `SKRect` ](xref:SkiaSharp.SKRect) argumento, que é um retângulo definido em termos de coordenadas X e Y de seu canto superior esquerdo e o canto inferior direito. A elipse preenche esse retângulo, que sugere que talvez seja possível usá-lo na **preenchimento da elipse** página como esta:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

No entanto, que trunca todas as bordas do contorno da elipse nos quatro lados. Você precisa ajustar todos os `SKRect` argumentos de construtor com base no `strokeWidth` para fazer isso funcionar direito:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
