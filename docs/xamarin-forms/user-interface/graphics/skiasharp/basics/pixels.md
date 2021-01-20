---
title: Unidades independentes de dispositivo e pixels
description: Este artigo explora as diferenças entre coordenadas geoSkiaSharps e Xamarin.Forms coordenadas e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04f0a056b83c3ebb298e284fefc93d83dfc57b52
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98608995"
---
# <a name="pixels-and-device-independent-units"></a>Unidades independentes de dispositivo e pixels

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore as diferenças entre coordenadas geoSkiaSharps e Xamarin.Forms coordenadas_

Este artigo explora as diferenças no sistema de coordenadas usado no SkiaSharp e no Xamarin.Forms . Você pode obter informações para converter entre os dois sistemas de coordenadas e também desenhar gráficos que preenchem uma área específica:

![Uma elipse que preenche a tela](pixels-images/screenfillexample.png)

Se você já está se programando em Xamarin.Forms algum tempo, pode ter uma ideia de Xamarin.Forms coordenadas e tamanhos. Os círculos desenhados nos dois artigos anteriores podem parecer um pouco pequenos para você.

Esses círculos *são* pequenos em comparação com Xamarin.Forms tamanhos. Por padrão, o SkiaSharp desenha em unidades de pixels enquanto Xamarin.Forms baseia as coordenadas e os tamanhos em uma unidade independente de dispositivo estabelecida pela plataforma subjacente. (Mais informações sobre o Xamarin.Forms sistema de coordenadas podem ser encontradas no [capítulo 5. Lidando com tamanhos](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) do livro *criando aplicativos móveis com Xamarin.Forms* o.

A página no programa [**SkewSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) intitulado **tamanho da superfície** usa saída de texto SkiaSharp para mostrar o tamanho da superfície de exibição de três fontes diferentes:

- As Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) Propriedades normal e [`Height`](xref:Xamarin.Forms.VisualElement.Height) do `SKCanvasView` objeto.
- A [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) Propriedade do `SKCanvasView` objeto.
- A [`Size`](xref:SkiaSharp.SKImageInfo.Size) Propriedade do `SKImageInfo` valor, que é consistente com as `Width` Propriedades e `Height` usadas nas duas páginas anteriores.

A [`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe mostra como exibir esses valores. O Construtor salva o `SKCanvasView` objeto como um campo, para que possa ser acessado no `PaintSurface` manipulador de eventos:

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

`SKCanvas` inclui seis `DrawText` métodos diferentes, mas esse [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) método é o mais simples:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Você especifica a cadeia de texto, as coordenadas X e Y em que o texto deve começar e um `SKPaint` objeto. A coordenada X especifica onde o lado esquerdo do texto está posicionado, mas observe: a coordenada Y especifica a posição da *linha de base* do texto. Se você já escreveu manualmente em papel, a linha de base é a linha na qual os caracteres estão disponíveis e abaixo quais os descendentes (como aqueles nas letras g, p, q e y) descendem.

O `SKPaint` objeto permite que você especifique a cor do texto, a família de fontes e o tamanho do texto. Por padrão, a [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) propriedade tem um valor de 12, o que resulta em um pequeno texto em dispositivos de alta resolução, como telefones. Em qualquer coisa, exceto nos aplicativos mais simples, você também precisará de algumas informações sobre o tamanho do texto que você está exibindo. A `SKPaint` classe define uma [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) propriedade e vários [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) métodos, mas para necessidades menos sofisticadas, a [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) propriedade fornece um valor recomendado para espaçamento de linhas sucessivas de texto.

O manipulador a seguir `PaintSurface` cria um `SKPaint` objeto de um `TextSize` de 40 pixels, que é a altura vertical desejada do texto da parte superior dos ascendentes até a parte inferior dos descendentes. O `FontSpacing` valor que o `SKPaint` objeto retorna é um pouco maior que esse, cerca de 47 pixels.

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

O método começa a primeira linha de texto com uma coordenada X de 20 (para uma pequena margem à esquerda) e uma coordenada Y de `fontSpacing` , que é um pouco mais do que o necessário para exibir a altura total da primeira linha de texto na parte superior da superfície de exibição. Após cada chamada para `DrawText` , a coordenada Y é aumentada em um ou dois incrementos de `fontSpacing` .

Este é o programa em execução:

[![Capturas de tela mostram o aplicativo de tamanho de superfície em execução em dois dispositivos móveis.](pixels-images/surfacesize-small.png)](pixels-images/surfacesize-large.png#lightbox "Captura de tela tripla da página tamanho da superfície")

Como você pode ver, a `CanvasSize` propriedade de `SKCanvasView` e a `Size` Propriedade do `SKImageInfo` valor são consistentes ao relatar as dimensões de pixel. As `Height` `Width` Propriedades e de `SKCanvasView` são propriedades e Xamarin.Forms relatam o tamanho da exibição nas unidades independentes do dispositivo definidas pela plataforma.

O simulador de sete do iOS à esquerda tem dois pixels por unidade independente de dispositivo e o Android Nexus 5 no centro tem três pixels por unidade. É por isso que o círculo simples mostrado anteriormente tem tamanhos diferentes em plataformas diferentes.

Se você preferir trabalhar inteiramente em unidades independentes de dispositivo, poderá fazer isso definindo a `IgnorePixelScaling` Propriedade do `SKCanvasView` para `true` . No entanto, talvez você não goste dos resultados. O SkiaSharp renderiza os elementos gráficos em uma superfície de dispositivo menor, com um tamanho de pixel igual ao tamanho da exibição em unidades independentes de dispositivo. (Por exemplo, SkiaSharp usaria uma superfície de exibição de 360 x 512 pixels no Nexus 5.) Em seguida, ele escala verticalmente essa imagem, resultando em um bitmap perceptível jaggies.

Para manter a mesma resolução de imagem, uma solução melhor é escrever suas próprias funções simples para converter entre os dois sistemas de coordenadas.

Além do `DrawCircle` método, `SKCanvas` também define dois `DrawOval` métodos que desenham uma elipse. Uma elipse é definida por dois raios, em vez de um único raio. Eles são conhecidos como o *raio principal* e o *raio secundário*. O `DrawOval` método desenha uma elipse com dois raios paralelos aos eixos X e Y. (Se você precisar desenhar uma elipse com eixos que não são paralelos aos eixos X e Y, poderá usar uma transformação de rotação conforme discutido no artigo [**a transformação girar**](../transforms/rotate.md) ou um caminho gráfico, conforme discutido no artigo [**três maneiras de desenhar um arco**](../curves/arcs.md)). Essa sobrecarga do [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) método nomeia os dois parâmetros de raios `rx` e `ry` para indicar que eles são paralelos aos eixos X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

É possível desenhar uma elipse que preenche a superfície de exibição? A página **preenchimento da elipse** demonstra como. O `PaintSurface` manipulador de eventos na classe [**EllipseFillPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) subtrai metade da largura do traço dos `xRadius` valores e `yRadius` para se ajustar à elipse inteira e sua estrutura de tópicos dentro da superfície de exibição:

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

Aqui está executando:

[![Capturas de tela mostram o aplicativo preenchimento de elipse em execução em dois dispositivos móveis.](pixels-images/ellipsefill-small.png)](pixels-images/ellipsefill-large.png#lightbox "Captura de tela tripla da página tamanho da superfície")

O outro [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) método tem um [`SKRect`](xref:SkiaSharp.SKRect) argumento, que é um retângulo definido em termos das coordenadas X e Y de seu canto superior esquerdo e canto inferior direito. A oval preenche esse retângulo, o que sugere que pode ser possível usá-lo na página de **preenchimento da elipse** como esta:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

No entanto, isso trunca todas as bordas do contorno da elipse nos quatro lados. Você precisa ajustar todos os `SKRect` argumentos do construtor com base no `strokeWidth` para fazer esse trabalho certo:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)