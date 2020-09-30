---
title: Recorte com caminhos de regiões
description: Este artigo explica como usar caminhos SkiaSharp para recortar elementos gráficos para áreas específicas e para criar regiões e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 604d2aa8368c85b852530f999601fffca8e104e6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562919"
---
# <a name="clipping-with-paths-and-regions"></a>Recorte com caminhos de regiões

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar caminhos para recortar elementos gráficos para áreas específicas e para criar regiões_

Às vezes, é necessário restringir a renderização de gráficos em uma área específica. Isso é conhecido como *recorte*. Você pode usar o recorte para efeitos especiais, como esta imagem de um macaco visto por meio de um Keyhole:

![Macaco por meio de um Keyhole](clipping-images/clippingsample.png)

A *área de recorte* é a área da tela na qual os gráficos são renderizados. Tudo o que é exibido fora da área de recorte não é renderizado. A área de recorte geralmente é definida por um retângulo ou um [`SKPath`](xref:SkiaSharp.SKPath) objeto, mas você pode definir como alternativa uma área de recorte usando um [`SKRegion`](xref:SkiaSharp.SKRegion) objeto. Esses dois tipos de objetos à primeira parecem relacionados, pois você pode criar uma região a partir de um caminho. No entanto, você não pode criar um caminho de uma região e eles são muito diferentes internamente: um caminho consiste em uma série de linhas e curvas, enquanto uma região é definida por uma série de linhas de varredura horizontais.

A imagem acima foi criada pelo **macaco por meio** da página Keyhole. A  [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe define um caminho usando dados SVG e usa o construtor para carregar um bitmap de recursos do programa:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Embora o `keyholePath` objeto Descreva o contorno de um Keyhole, as coordenadas são completamente arbitrárias e refletem o que era conveniente quando os dados de caminho foram desestruturados. Por esse motivo, o `PaintSurface` manipulador obtém os limites desse caminho e chama `Translate` e `Scale` para mover o caminho para o centro da tela e torná-lo tão alto quanto a tela:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Mas o caminho não é renderizado. Em vez disso, seguindo as transformações, o caminho é usado para definir uma área de recorte com esta instrução:

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`Em seguida, o manipulador redefine as transformações com uma chamada para `ResetMatrix` e desenha o bitmap para estender para a altura total da tela. Esse código pressupõe que o bitmap é quadrado, que é esse bitmap em particular. O bitmap é renderizado apenas dentro da área definida pelo caminho de recorte:

[![Captura de tela tripla do macaco por meio da página Keyhole](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

O caminho de recorte está sujeito às transformações em vigor quando o `ClipPath` método é chamado, e não às transformações em vigor quando um objeto gráfico (como um bitmap) é exibido. O caminho de recorte faz parte do estado da tela que é salvo com o `Save` método e restaurado com o `Restore` método.

## <a name="combining-clipping-paths"></a>Combinando caminhos de recorte

Estritamente falando, a área de recorte não é "definida" pelo `ClipPath` método. Em vez disso, ele é combinado com o caminho de recorte existente, que começa como um retângulo igual em tamanho à tela. Você pode obter os limites retangulares da área de recorte usando a [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) propriedade ou a [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) propriedade. A `ClipBounds` propriedade retorna um `SKRect` valor que reflete as transformações que podem estar em vigor. A `ClipDeviceBounds` propriedade retorna um `RectI` valor. Este é um retângulo com dimensões de número inteiro e descreve a área de recorte em dimensões de pixel reais.

Qualquer chamada para `ClipPath` reduz a área de recorte combinando a área de recorte com uma nova área. A sintaxe completa do [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) método é:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Também há um [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) método que combina a área de recorte com um retângulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Por padrão, a área de recorte resultante é uma interseção da área de recorte existente e o `SKPath` ou `SKRect` que é especificado no `ClipPath` método ou `ClipRect` . Isso é demonstrado na página de **clipe com interseção de quatro círculos** . O `PaintSurface` manipulador na  [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe reutiliza o mesmo `SKPath` objeto para criar quatro círculos sobrepostos, cada um deles reduz a área de recorte por meio de chamadas sucessivas para `ClipPath` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

O que resta é a interseção desses quatro círculos:

[![Captura de tela tripla da página de clipe com interseção de quatro círculos](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

A [`SKClipOperation`](xref:SkiaSharp.SKClipOperation) enumeração tem apenas dois membros:

- `Difference` Remove o caminho ou retângulo especificado da área de recorte existente

- `Intersect` cruza o caminho ou retângulo especificado com a área de recorte existente

Se você substituir os quatro `SKClipOperation.Intersect` argumentos na `FourCircleIntersectClipPage` classe por `SKClipOperation.Difference` , você verá o seguinte:

[![Captura de tela tripla da página de clipe com interseção de quatro círculos com a operação de diferença](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

Quatro círculos sobrepostos foram removidos da área de recorte.

A página de **operações do clipe** ilustra a diferença entre essas duas operações com apenas um par de círculos. O primeiro círculo à esquerda é adicionado à área de recorte com a operação de clipe padrão de `Intersect` , enquanto o segundo círculo à direita é adicionado à área de recorte com a operação de clipe indicada pelo rótulo de texto:

[![Captura de tela tripla da página de operações do clipe](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

A [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe define dois `SKPaint` objetos como campos e divide a tela em duas áreas retangulares. Essas áreas são diferentes dependendo se o telefone está no modo retrato ou paisagem. `DisplayClipOp`Em seguida, a classe exibe o texto e chama `ClipPath` os dois caminhos de círculo para ilustrar cada operação de clipe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

Chamar `DrawPaint` normalmente faz com que toda a tela seja preenchida com esse `SKPaint` objeto, mas nesse caso, o método simplesmente pinta dentro da área de recorte.

## <a name="exploring-regions"></a>Explorando regiões

Você também pode definir uma área de recorte em termos de um [`SKRegion`](xref:SkiaSharp.SKRegion) objeto.

Um objeto recém-criado `SKRegion` descreve uma área vazia. Normalmente, a primeira chamada no objeto é [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) para que a região Descreva uma área retangular. O parâmetro para `SetRect` é um `SKRectI` valor &mdash; de um retângulo com coordenadas de inteiros porque especifica o retângulo em termos de pixels. Em seguida, você pode chamar [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) com um `SKPath` objeto. Isso cria uma região que é igual ao interior do caminho, mas recortada na região retangular inicial.

A região também pode ser modificada chamando uma das [`Op`](xref:SkiaSharp.SKRegion.Op*) sobrecargas de método, como esta:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

A [`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation) enumeração é semelhante a `SKClipOperation` , mas tem mais membros:

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

A região em que você está fazendo a `Op` chamada é combinada com a região especificada como um parâmetro com base no `SKRegionOperation` membro. Quando você finalmente Obtém uma região adequada para recorte, pode defini-la como a área de recorte da tela usando o [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) método de `SKCanvas` :

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

A captura de tela a seguir mostra as áreas de recorte com base nas seis operações de região. O círculo esquerdo é a região em que o `Op` método é chamado e o círculo direito é a região passada para o `Op` método:

[![Captura de tela tripla da página de operações de região](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

Essas são as possibilidades de combinar esses dois círculos? Considere a imagem resultante como uma combinação de três componentes, que por si só são vistos nas `Difference` operações, `Intersect` e `ReverseDifference` . O número total de combinações é de dois para a terceira potência, ou oito. Os dois que estão faltando são a região original (que resulta de não chamar `Op` ) e uma região totalmente vazia.

É mais difícil usar regiões para recorte porque você precisa primeiro criar um caminho, depois uma região desse caminho e, em seguida, combinar várias regiões. A estrutura geral da página de **operações de região** é muito semelhante às operações de **clipe** , mas a [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divide a tela em seis áreas e mostra o trabalho extra necessário para usar regiões para este trabalho:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Aqui está uma grande diferença entre o `ClipPath` método e o `ClipRegion` método:

> [!IMPORTANT]
> Ao contrário do `ClipPath` método, o `ClipRegion` método não é afetado pelas transformações.

Para entender a lógica dessa diferença, é útil entender o que é uma região. Se você pensou sobre como as operações do clipe ou as operações de região podem ser implementadas internamente, provavelmente parece muito complicada. Vários caminhos potencialmente muito complexos estão sendo combinados, e a estrutura de tópicos do caminho resultante é provavelmente um pesadelo de algoritmo.

Esse trabalho será consideravelmente simplificado se cada caminho for reduzido a uma série de linhas de varredura horizontais, como aquelas em TVs com tubo de vácuo antigo. Cada linha de digitalização é simplesmente uma linha horizontal com um ponto inicial e um ponto de extremidade. Por exemplo, um círculo com um raio de 10 pixels pode ser decomposto em 20 linhas de varredura horizontais, cada uma delas começa na parte esquerda do círculo e termina na parte direita. A combinação de dois círculos com qualquer operação de região se torna muito simples, pois é simplesmente uma questão de examinar as coordenadas de início e término de cada par de linhas de varredura correspondentes.

Isso é o que é uma região: uma série de linhas de varredura horizontal que definem uma área.

No entanto, quando uma área é reduzida a uma série de linhas de digitalização, essas linhas de verificação se baseiam em uma dimensão de pixel específica. Estritamente falando, a região não é um objeto gráfico vetorial. Ele está mais próximo de natureza a um bitmap monocromático compactado do que a um caminho. Consequentemente, as regiões não podem ser dimensionadas ou giradas sem perder a fidelidade e, por esse motivo, elas não são transformadas quando usadas para áreas de recorte.

No entanto, você pode aplicar transformações a regiões para fins de pintura. O programa de **pintura de região** demonstra de claro a natureza interna das regiões. A [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe cria um `SKRegion` objeto com base em um `SKPath` círculo de raio de 10 unidades. Em seguida, uma transformação expande esse círculo para preencher a página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

A `DrawRegion` chamada preenche a região em laranja, enquanto a `DrawPath` chamada traça o caminho original em azul para comparação:

[![Captura de tela tripla da página de pintura da região](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

A região é claramente uma série de coordenadas discretas.

Se você não precisar usar transformações em conjunto com as áreas de recorte, poderá usar regiões para recorte, como demonstra a página de **trevo folhas de quatro folhas** . A [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe constrói uma região composta de quatro regiões circulares, define essa região composta como a área de recorte e, em seguida, desenha uma série de 360 linhas retas que emanam do centro da página:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

Na verdade, ele não parece um trevo folhas de quatro folhas, mas é uma imagem que, de outra forma, poderia ser difícil de renderizar sem recorte:

[![Captura de tela tripla da página de trevo folhas de quatro folhas](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)