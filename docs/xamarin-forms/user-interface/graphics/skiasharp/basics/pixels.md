---
title: Unidades independentes de dispositivo e pixels
description: Explorar as diferenças entre as coordenadas de SkiaSharp e xamarin. Forms
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: e01d3e228a0684865fb09dd7a4cbb0e2f0b49125
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="pixels-and-device-independent-units"></a>Unidades independentes de dispositivo e pixels

_Explorar as diferenças entre as coordenadas de SkiaSharp e xamarin. Forms_

Este artigo explora as diferenças no sistema de coordenadas usado em SkiaSharp e xamarin. Forms. Você pode obter informações para converter entre os dois sistemas de coordenadas e também desenhar gráficos que preenchem uma área específica:

![](pixels-images/screenfillexample.png "Uma elipse que preenche a tela")

Se você tiver sido programando em xamarin. Forms por algum tempo, você pode ter uma ideia de xamarin. Forms coordenadas e tamanhos. Os círculos desenhados nos dois artigos anteriores podem parecer um pouco pequenos para você.

Esses círculos *são* pequeno em comparação com tamanhos de xamarin. Forms. Por padrão, SkiaSharp desenha em unidades de pixels, enquanto xamarin. Forms bases coordenadas e tamanhos em uma unidade independente de dispositivo estabelecida pela plataforma subjacente. (Para obter mais informações sobre o sistema de coordenadas do xamarin. Forms podem ser encontradas no [capítulo 5. Lidando com tamanhos](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) do catálogo de *criação de aplicativos móveis com o xamarin. Forms*.)

A página de [ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa intitulado **tamanho da superfície** usa SkiaSharp saída de texto para mostrar o tamanho da superfície de exibição de três fontes diferentes:

- O xamarin. Forms normal [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propriedades do `SKCanvasView` objeto.
- O [ `CanvasSize` ](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKCanvasView.CanvasSize/) propriedade o `SKCanvasView` objeto.
- O [ `Size` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Size/) propriedade o `SKImageInfo` valor, que é consistente com o `Width` e `Height` propriedades usadas em duas páginas anteriores.

O [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe mostra como exibir esses valores. O construtor salva o `SKCanvasView` objeto como um campo, para que ele possa ser acessado no `PaintSurface` manipulador de eventos:

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

`SKCanvas` inclui seis diferentes `DrawText` métodos, mas isso [ `DrawText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawText/p/System.String/System.Single/System.Single/SkiaSharp.SKPaint/) método é mais simples:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Especifique a cadeia de caracteres de texto, as coordenadas X e Y onde o texto é começar e um `SKPaint` objeto. A coordenada X Especifica onde posicionada à esquerda do texto, mas tome cuidado com: A coordenada Y Especifica a posição do *linha de base* do texto. Se você já tenha escrito manualmente em papel com linhas, a linha de base é a linha na qual sit caracteres e abaixo decrescem quais descendentes (como aqueles no letras g, p, q e y).

O `SKPaint` objeto permite que você especifique a cor do texto, a família de fontes e o tamanho do texto. Por padrão, o [ `TextSize` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.TextSize/) propriedade tem um valor de 12, que resulta em texto muito pequeno em dispositivos de alta resolução, como celulares. Em qualquer coisa menos os aplicativos mais simples, você também precisará algumas informações sobre o tamanho do texto que você está exibindo. O `SKPaint` classe define um [ `FontMetrics` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontMetrics/) propriedade e vários [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) métodos, mas menos necessidades especial, o [ `FontSpacing` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontSpacing/) propriedade fornece um valor recomendado para linhas sucessivas do espaçamento do texto.

O seguinte `PaintSurface` manipulador cria um `SKPaint` de objeto para um `TextSize` de 40 pixels, que é a altura desejada vertical do texto da parte superior da ascendentes à parte inferior de descendentes. O `FontSpacing` valor que o `SKPaint` objeto retorna um pouco maior que sobre 47 pixels.

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

O método começa a primeira linha de texto com uma coordenada X de 20 (para uma pequena margem à esquerda) e uma coordenada Y de `fontSpacing`, que é um pouco mais do que o que é necessário para exibir a altura total da primeira linha do texto na parte superior da superfície de exibição. Após cada chamada para `DrawText`, a coordenada Y aumenta em incrementos de um ou dois de `fontSpacing`.

Aqui está o programa em execução em todas as três plataformas:

[![](pixels-images/surfacesize-small.png "Captura de tela da página de tamanho da superfície tripla")](pixels-images/surfacesize-large.png#lightbox "tripla captura de tela da página de tamanho da superfície")

Como você pode ver, o `CanvasSize` propriedade do `SKCanvasView` e o `Size` propriedade o `SKImageInfo` valor estão consistentes em reporting as dimensões de pixel. O `Height` e `Width` propriedades do `SKCanvasView` xamarin. Forms propriedades e o tamanho da exibição nas unidades independentes de dispositivo definidas pela plataforma de relatório.

O simulador de iOS 7 à esquerda tem 2 pixels por unidade independente de dispositivo, o Android 5 Nexus no centro tem 3 pixels por unidade e o 925 de Lumia Nokia à direita tem 2,25 pixels por unidade. Que por que simples círculo mostrado parece anterior sobre o mesmo tamanho no iPhone e Windows phone, mas é menor no telefone Android.

Se você preferir trabalhar inteiramente em unidades independentes de dispositivo, você pode fazer isso definindo o `IgnorePixelScaling` propriedade o `SKCanvasView` para `true`. No entanto, você pode não gostar dos resultados. SkiaSharp renderiza os gráficos em uma menor superfície de dispositivo, com um tamanho de pixel igual ao tamanho da exibição em unidades independentes de dispositivo. (Por exemplo, SkiaSharp usaria uma superfície de exibição de 360 x 512 pixels em 5 a Nexus.) Ele, em seguida, sobe essa imagem em tamanho, resultando em jaggies bitmap notável.

Para manter a mesma resolução de imagem, uma solução melhor é escrever suas próprias funções simples para converter entre os dois sistemas de coordenadas.

Além de `DrawCircle` método `SKCanvas` também define duas `DrawOval` métodos que desenhar uma elipse. Uma elipse é definida por dois raios, em vez de um único radius. Eles são conhecidos como o *radius principal* e *radius secundária*. O `DrawOval` método desenha uma elipse com os dois raios paralelas aos eixos X e Y. Essa restrição pode ser superada com transformações ou o uso de um caminho de gráficos (para ser abordada posteriormente), mas [isso `DrawOval` método](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) nomeia o argumento de dois raios `rx` e `ry` para indicar que eles são paralelos os eixos X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

É possível desenhar uma elipse que preenche a superfície de exibição? O **elipse preencher** página demonstra como. O `PaintSurface` manipulador de eventos a [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) classe subtrai metade da largura do traço do `xRadius` e `yRadius` valores para se ajustar a elipse inteira e seu estrutura de tópicos na superfície de exibição:

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

Aqui, ele é executado em três plataformas:

[![](pixels-images/ellipsefill-small.png "Captura de tela da página de tamanho da superfície tripla")](pixels-images/ellipsefill-large.png#lightbox "tripla captura de tela da página de tamanho da superfície")

O [outros `DrawOval` método](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/) tem um [ `SGRect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRect/) argumento, que é um retângulo definido em termos de coordenadas X e Y de seu canto superior esquerdo e o canto inferior direito. A elipse preenche esse retângulo, o que sugere que ele poderá usá-lo no **elipse preencher** página assim:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

No entanto, que trunca todas as bordas da estrutura de tópicos da elipse quatro lados. Você precisa ajustar todos os `SKRect` argumentos de construtor com base no `strokeWidth` para fazer isso funcionar direito:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
