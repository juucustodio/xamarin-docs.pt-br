---
title: Com regiões e caminhos de recorte
description: Use caminhos para gráficos de clipe para áreas específicas e criar regiões
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 051ceec148a569d00048a661e6ba8dc3ce96fc81
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="clipping-with-paths-and-regions"></a>Com regiões e caminhos de recorte

_Use caminhos para gráficos de clipe para áreas específicas e criar regiões_

Às vezes, é necessário restringir o processamento de imagens para uma determinada área. Isso é conhecido como *recorte*. Você pode usar o recorte de efeitos especiais, como essa imagem de um monkey visto por meio de um buraco de fechadura:

![](clipping-images/clippingsample.png "Macaco por meio de um buraco de fechadura")

O *área de recorte* é a área da tela em que os gráficos são renderizados. Tudo o que é exibido fora da área de recorte não será renderizado. A área de recorte normalmente é definida por um [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto, mas você também pode definir uma área de recorte usando um [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto. Esses dois tipos de objetos no primeiro parece estar relacionados porque você pode criar uma região de um caminho. No entanto, não é possível criar um caminho de uma região, e eles são muito diferentes internamente: um caminho consiste em uma série de linhas e curvas, enquanto uma região é definida por uma série de linhas de varredura horizontal.

A imagem acima foi criada pelo **Monkey por meio de buraco de fechadura** página. O [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe define um caminho usando dados SVG e usa o construtor para carregar um bitmap de recursos do programa:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

Embora o `keyholePath` objeto descreve a estrutura de um buraco de fechadura, as coordenadas são completamente arbitrárias e refletem o que foi conveniente quando os dados do caminho foi desenvolvidos. Por esse motivo, o `PaintSurface` manipulador obtém os limites deste caminho e chamadas `Translate` e `Scale` para mover o caminho para o centro da tela e torná-la quase tão alto como a tela:


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

Mas o caminho não é processado. Em vez disso, as transformações a seguir o caminho é usado para definir uma área de recorte com esta instrução:

```csharp
canvas.ClipPath(keyholePath);
```

O `PaintSurface` manipulador, em seguida, redefine as transformações com uma chamada para `ResetMatrix` e desenha o bitmap para estender até a altura total da tela. Esse código supõe que o bitmap é quadrado, que é determinado bitmap. O bitmap é processado somente na área definida pelo caminho de recorte:

[![](clipping-images/monkeythroughkeyhole-small.png "Captura de tela tripla de Monkey por meio da página de buraco de fechadura")](clipping-images/monkeythroughkeyhole-large.png#lightbox "tripla captura de tela de Monkey por meio da página de buraco de fechadura")

O caminho de recorte está sujeito às transformações quando na verdade o `ClipPath` método é chamado, e não para as transformações em vigor quando um objeto de gráfico (como um bitmap) é exibido. O caminho de recorte é parte do estado da tela é salva com o `Save` método e restaurado com o `Restore` método.

## <a name="combining-clipping-paths"></a>Combinação de caminhos de recorte

Estritamente falando, a área de recorte não é "definida" pelo `ClipPath` método. Em vez disso, ele é combinado com o caminho de recorte existente, que começa com um retângulo de tamanho igual para a tela. Você pode obter retangular dos limites da área de recorte usando o [ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/) propriedade ou o [ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/) propriedade. O `ClipBounds` propriedade retorna um `SKRect` valor reflete quaisquer transformações que pode estar em vigor. O `ClipDeviceBounds` propriedade retorna um `RectI` valor. Isso é um retângulo com dimensões de inteiro e descreve a área de recorte em dimensões de pixel real.

Qualquer chamada para `ClipPath` reduz a área de recorte combinando a área de recorte com uma nova área. A sintaxe completa do [ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/) método é:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Também há um [ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/) método que combina a área de recorte com um retângulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Por padrão, a área de recorte resultante é uma interseção da área de recorte existente e o `SKPath` ou `SKRect` especificada no `ClipPath` ou `ClipRect` método. Isso é demonstrado no **clipe de interseção de círculos quatro** página. O `PaintSurface` manipulador no [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe reutiliza o mesmo `SKPath` objeto para criar quatro círculos sobrepostos, cada um deles reduz a área de recorte por meio de chamadas sucessivas `ClipPath`:

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

O restante é a interseção dessas quatro círculos:

[![](clipping-images//fourcircleintersectclip-small.png "Tripla captura de tela da página clipe de interseção de círculo quatro")](clipping-images/fourcircleintersectclip-large.png#lightbox "tripla captura de tela da página quatro clipe de círculo Intersect")

O [ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/) enumeração tem apenas dois membros:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) Remove o caminho especificado ou retângulo da área de recorte existente

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) faz interseção com o caminho especificado ou o retângulo com a área de recorte existente

Se você substituir os quatro `SKClipOperation.Intersect` argumentos a `FourCircleIntersectClipPage` classe com `SKClipOperation.Difference`, você verá o seguinte:

[![](clipping-images//fourcircledifferenceclip-small.png "Captura de tela de tripla da página quatro clipe de círculo interseção com a operação de diferença")](clipping-images/fourcircledifferenceclip-large.png#lightbox "tripla captura de tela da página quatro clipe de círculo interseção com a operação de diferença")

Quatro círculos sobrepostos foram removidos da área de recorte.

O **operações de recortar** página ilustra a diferença entre essas duas operações com apenas um par de círculos. O primeiro círculo à esquerda é adicionado à área de recorte com a operação de recorte de padrão de `Intersect`, enquanto o segundo círculo à direita é adicionado à área de recorte com a operação recortar indicada pelo rótulo de texto:

[![](clipping-images//clipoperations-small.png "Captura de tela da página de operações de recortar tripla")](clipping-images/clipoperations-large.png#lightbox "tripla captura de tela da página de operações de recortar")

O [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe define dois `SKPaint` objetos como campos e, em seguida, divide a tela para cima em duas áreas retangulares. Essas áreas são diferentes dependendo se o telefone estiver no modo retrato ou paisagem. O `DisplayClipOp` classe exibe o texto e chamadas `ClipPath` com os caminhos de dois círculo para ilustrar cada operação recortar:

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

Chamando `DrawPaint` normalmente faz com que a tela inteira a ser preenchida com que `SKPaint` objeto, mas nesse caso, o método apenas pinta dentro da área de recorte.

## <a name="exploring-regions"></a>Explorando regiões

Se ter explorado a documentação da API para `SKCanvas`, você deve ter notado sobrecargas do `ClipPath` e `ClipRect` métodos que são semelhantes dos métodos descritos acima, mas em vez disso, tem um parâmetro chamado [ `SKRegionOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/) em vez de `SKClipOperation`. `SKRegionOperation` tem seis membros, fornecendo um pouco mais flexibilidade na combinação de caminhos para áreas de recorte do formulário:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

No entanto, as sobrecargas de `ClipPath` e `ClipRect` com `SKRegionOperation` parâmetros estão obsoletos e não poderão ser usadas.

Você ainda pode usar o `SKRegionOperation` enumeração mas exige que você defina uma área de recorte em termos de um [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto.

Criada uma nova `SKRegion` objeto descreve uma área vazia. Geralmente é a primeira chamada no objeto [ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/) para que a região descrever uma área retangular. O parâmetro `SetRect` é um uma `SKRectI` valor &mdash; o valor de retângulo com propriedades de inteiro. Em seguida, você pode chamar [ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/) com um `SKPath` objeto. Isso cria uma região que é o mesmo que o interior do caminho, mas recortado para a região retangular inicial.

O `SKRegionOperation` enumeração só entra em jogo quando você chama um do [ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/) sobrecargas do método, como este:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

A região que você está fazendo a `Op` chamada é combinada com a região especificada como um parâmetro com base no `SKRegionOperation` membro. Quando você finalmente obter adequado para recorte de uma região, você pode definir que, como a área de recorte da tela usando o [ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/) método `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Captura de tela a seguir mostra as áreas de recorte com base em operações de seis região. O círculo esquerdo é a região que o `Op` método é chamado em e o círculo à direita é a região passada para o `Op` método:

[![](clipping-images//regionoperations-small.png "Captura de tela da página de operações de região tripla")](clipping-images/regionoperations-large.png#lightbox "tripla captura de tela da página de operações de região")

São esses todas as possibilidades de combinar esses dois círculos? Considere a imagem resultante como uma combinação de três componentes, que por si só é vista no `Difference`, `Intersect`, e `ReverseDifference` operações. O número total de combinações é dois à terceira potência ou oito. Os dois ausentes são região original (que resulta da chamada não `Op` em todos os) e uma região completamente vazia.

É mais difícil para usar regiões de recorte porque você precisa primeiro criar um caminho e, em seguida, uma região de caminho e, em seguida, combinar várias regiões. A estrutura geral do **operações de região** página é muito semelhante ao **operações de recortar** , mas o [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divide a tela para cima em seis áreas e mostra o trabalho extra necessário para usar regiões para este trabalho:

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
> Ao contrário de `ClipPath` método, o `ClipRegion` método não é afetado pelas transformações.

Para entender a razão para essa diferença, é útil entender é que uma região. Se você já pensou sobre como as operações de recortar ou operações de região podem ser implementadas internamente, provavelmente parece muito complicado. Vários caminhos potencialmente muito complexos estão sendo combinados e a estrutura de tópicos do caminho resultante provavelmente é um algoritmo pesadelo.

Mas esse trabalho é simplificado consideravelmente se cada caminho é reduzido em uma série de linhas de varredura horizontal, como aqueles em antigo vácuo tubo TVs. Cada linha de verificação é simplesmente uma linha horizontal com um ponto inicial e um ponto de extremidade. Por exemplo, um círculo com raio de 10 pode ser decomposto em 20 linhas de varredura horizontal, cada um deles inicia na parte esquerda do círculo e termina na parte direita. Combinar dois círculos com qualquer operação de região se torna muito simple, porque ele é simplesmente uma questão de examinar as coordenadas de início e término de cada par de linhas de verificação correspondente.

Este é o que é uma região: uma série de linhas de varredura horizontal que define uma área.

No entanto, quando uma área é reduzida em uma série de verificação de linhas, essas linhas se baseiam em uma dimensão de pixel específico de verificação. Estritamente falando, a região não é um objeto de gráfico de vetor. É mais próximo na natureza um bitmap monocromático compactado que a um caminho. Consequentemente, regiões não podem ser dimensionadas ou giradas sem perder a fidelidade e por esse motivo que não são transformadas quando usados para áreas de recorte.

No entanto, você pode aplicar transformações em regiões para fins de pintura. O **região de pintura** programa vividly demonstra a natureza interna de regiões. O [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe cria um `SKRegion` objeto com base em um `SKPath` de um círculo de raio de unidade de 10. Uma transformação, em seguida, expande esse círculo para preencher a página:

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

O `DrawRegion` chamada preenche a região em laranja, enquanto o `DrawPath` chamada traçados o caminho original em azul para comparação:

[![](clipping-images//regionpaint-small.png "Captura de tela da página da região de pintura tripla")](clipping-images/regionpaint-large.png#lightbox "tripla captura de tela da página da região de pintura")

A região é claramente uma série de coordenadas discretas.

Se você não precisa usar transformações em conjunto com suas áreas de recorte, você pode usar regiões de recorte, como o **quatro – folha trevos** demonstra da página. O [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe constrói uma região composta de quatro regiões circulares, define essa região composto como a área de recorte e, em seguida, desenha uma série de linhas retas 360 emanando do centro da página:

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

Ele realmente não parece um trevo de folha – quatro, mas é uma imagem que pode ser difícil de ser processado sem recorte:

[![](clipping-images//fourleafclover-small.png "Captura de tela da página quatro – folha trevos tripla")](clipping-images/fourleafclover-large.png#lightbox "tripla captura de tela da página quatro – folha trevo")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
