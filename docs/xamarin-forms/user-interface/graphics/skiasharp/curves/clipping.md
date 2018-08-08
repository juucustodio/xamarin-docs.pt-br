---
title: Recorte com caminhos de regiões
description: Este artigo explica como usar caminhos de SkiaSharp para gráficos de clipe para áreas específicas e como criar regiões e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 0c07d68535349004eeefeaa18daa9c59b889a6a7
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615282"
---
# <a name="clipping-with-paths-and-regions"></a>Recorte com caminhos de regiões

_Use caminhos para gráficos de clipe para áreas específicas e para criar regiões_

Às vezes, é necessário restringir a renderização de gráficos para uma determinada área. Isso é conhecido como *recorte*. Você pode usar o recorte de efeitos especiais, como essa imagem de um monkey visto por meio de um buraco de fechadura:

![](clipping-images/clippingsample.png "Monkey por meio de um buraco de fechadura")

O *área de recorte* é a área da tela na qual os gráficos são renderizados. Tudo o que é exibido fora da área de recorte não é renderizado. A área de recorte é geralmente definida por uma [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) objeto, mas você também pode definir uma área de recorte usando um [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto. Esses dois tipos de objetos no parecem a princípio relacionados porque você pode criar uma região de um caminho. No entanto, não é possível criar um caminho de uma região, e eles são muito diferentes internamente: um caminho consiste em uma série de linhas e curvas, enquanto uma região é definida por uma série de linhas de varredura horizontal.

A imagem acima foi criada pela **Monkey por meio de buraco de fechadura** página. O [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe define um caminho usando dados SVG e usa o construtor para carregar um bitmap de recursos do programa:

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

Embora o `keyholePath` objeto descreve o contorno de um buraco de fechadura, as coordenadas são completamente arbitrárias e refletem o que era prático quando os dados do caminho foi desenvolvidos. Por esse motivo, o `PaintSurface` manipulador obtém os limites desse caminho e chamadas `Translate` e `Scale` para mover o caminho para o centro da tela e transformá-la quase tão alto quanto a tela:


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

Mas o caminho não é renderizado. Em vez disso, as transformações a seguir o caminho é usado para definir uma área de recorte com esta instrução:

```csharp
canvas.ClipPath(keyholePath);
```

O `PaintSurface` manipulador, em seguida, redefine as transformações com uma chamada para `ResetMatrix` e desenha o bitmap para estender até a altura total da tela. Esse código supõe que o bitmap é quadrado, que é esse bitmap específico. O bitmap é renderizado somente na área definida pelo caminho de recorte:

[![](clipping-images/monkeythroughkeyhole-small.png "Captura de tela tripla do Monkey por meio da página de buraco de fechadura")](clipping-images/monkeythroughkeyhole-large.png#lightbox "tripla captura de tela do Monkey por meio da página de buraco de fechadura")

O caminho de recorte é sujeito às transformações quando na verdade o `ClipPath` método é chamado, e não para as transformações em vigor quando um objeto gráfico (por exemplo, um bitmap) é exibido. O caminho de recorte é parte do estado da tela é salva com o `Save` método e podem ser restaurados com o `Restore` método.

## <a name="combining-clipping-paths"></a>Combinar caminhos de recorte

Estritamente falando, a área de recorte é not "set" `ClipPath` método. Em vez disso, ele é combinado com o caminho de recorte existente, que inicia como um retângulo igual à tela. Você pode obter os limites retangulares da área de recorte usando o [ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/) propriedade ou o [ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/) propriedade. O `ClipBounds` propriedade retorna um `SKRect` valor reflete quaisquer transformações que pode estar em vigor. O `ClipDeviceBounds` propriedade retorna um `RectI` valor. Isso é um retângulo com dimensões de inteiro e descreve a área de recorte em dimensões de pixel reais.

Qualquer chamada para `ClipPath` reduz a área de recorte, combinando a área de recorte com uma nova área. A sintaxe completa dos [ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/) método é:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Há também uma [ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/) método que combina a área de recorte com um retângulo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Por padrão, a área de recorte resultante é uma interseção da área de recorte existente e o `SKPath` ou `SKRect` que é especificado na `ClipPath` ou `ClipRect` método. Isso é demonstrado na **clipes de interseção de círculos quatro** página. O `PaintSurface` manipulador na [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe reutiliza o mesmo `SKPath` objeto para criar quatro círculos sobrepostos, cada um deles reduz a área de recorte por meio de chamadas sucessivas para `ClipPath`:

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

O que resta é a interseção dessas quatro círculos:

[![](clipping-images//fourcircleintersectclip-small.png "Tripla captura de tela da página Clip quatro do círculo se cruzam")](clipping-images/fourcircleintersectclip-large.png#lightbox "tripla captura de tela da página quatro clipes de interseção de círculo")

O [ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/) enumeração tem apenas dois membros:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) Remove o caminho especificado ou o retângulo da área de recorte de existente

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) faz interseção com o caminho especificado ou um retângulo com a área de recorte existente

Se você substituir os quatro `SKClipOperation.Intersect` argumentos em de `FourCircleIntersectClipPage` classe com `SKClipOperation.Difference`, você verá o seguinte:

[![](clipping-images//fourcircledifferenceclip-small.png "Captura de tela da página Clip quatro do círculo se interseccionam com a operação de diferença tripla")](clipping-images/fourcircledifferenceclip-large.png#lightbox "tripla captura de tela da página Clip quatro do círculo se interseccionam com a operação de diferença")

Quatro círculos sobrepostos foram removidos da área de recorte.

O **operações de recorte** página ilustra a diferença entre essas duas operações com apenas um par de círculos. O círculo primeiro à esquerda é adicionado à área de recorte com a operação de recorte de padrão de `Intersect`, enquanto o segundo círculo no lado direito é adicionado à área de recorte com a operação de recorte indicada pelo rótulo de texto:

[![](clipping-images//clipoperations-small.png "Tripla captura de tela da página de operações de recorte")](clipping-images/clipoperations-large.png#lightbox "tripla captura de tela da página de operações de recorte")

O [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe define dois `SKPaint` objetos como campos e, em seguida, divide a tela para cima em duas áreas retangulares. Essas áreas são diferentes dependendo se o telefone está no modo retrato ou paisagem. O `DisplayClipOp` classe, em seguida, exibe o texto e chamadas `ClipPath` com os caminhos de dois círculo para ilustrar cada operação recortar:

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

Chamando `DrawPaint` normalmente faz com que a tela inteira a ser preenchido com que `SKPaint` objeto, mas nesse caso, o método apenas pinta dentro da área de recorte.

## <a name="exploring-regions"></a>Explorando as regiões

Se você já explorou a documentação da API para `SKCanvas`, você deve ter notado sobrecargas a `ClipPath` e `ClipRect` métodos que são semelhantes aos métodos descritos acima, mas em vez disso, ter um parâmetro denominado [ `SKRegionOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/) em vez de `SKClipOperation`. `SKRegionOperation` tem seis membros, proporcionando um pouco maior flexibilidade na combinação de caminhos para áreas de recorte do formulário:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

No entanto, as sobrecargas `ClipPath` e `ClipRect` com `SKRegionOperation` parâmetros são obsoletos e não podem ser usadas.

Você ainda pode usar o `SKRegionOperation` enumeração, mas ela requer que você defina uma área de recorte em termos de um [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) objeto.

Criada uma nova `SKRegion` objeto descreve uma área vazia. Geralmente é a primeira chamada no objeto [ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/) para que a região descrever uma área retangular. O parâmetro para `SetRect` é um uma `SKRectI` valor &mdash; o valor do retângulo com propriedades de inteiro. Em seguida, você pode chamar [ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/) com um `SKPath` objeto. Isso cria uma região que é o mesmo que o interior do caminho, mas recortado para a região retangular inicial.

O `SKRegionOperation` enumeração só entra em jogo quando você chama um dos [ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/) sobrecargas de método, como este:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

A região que você está fazendo a `Op` chamada na é combinada com a região especificada como um parâmetro com base no `SKRegionOperation` membro. Quando você finalmente chegar adequado para recorte de uma região, é possível definir isso como a área de recorte de tela usando o [ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/) método `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Captura de tela a seguir mostra as áreas de recorte baseadas nas operações de seis região. O círculo à esquerda é a região que o `Op` método é chamado em e o círculo à direita é a região passada para o `Op` método:

[![](clipping-images//regionoperations-small.png "Captura de tela da página de operações de região tripla")](clipping-images/regionoperations-large.png#lightbox "tripla captura de tela da página de operações de região")

São esses todas as possibilidades de combinar esses dois círculos? Considere a imagem resultante como uma combinação de três componentes, que por si só é vista na `Difference`, `Intersect`, e `ReverseDifference` operações. O número total de combinações é duas à terceira potência ou oito. Os dois que faltam são a região original (que resulta da chamada não `Op` em todos os) e uma região totalmente vazia.

É mais difícil para usar regiões de recorte porque você precisa primeiro criar um caminho e, em seguida, uma região do caminho e, em seguida, combinar várias regiões. A estrutura geral do **operações de região** página é muito semelhante à **operações de recorte** , mas o [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divide a tela para cima em seis áreas e mostra o trabalho extra necessário para usar as regiões para este trabalho:

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

Para entender a lógica para essa diferença, é útil entender é que uma região. Se você já pensou em como as operações de recorte ou a operações de região podem ser implementadas internamente, ele provavelmente parece muito complicado. Vários caminhos potencialmente muito complexos que estão sendo combinados e a estrutura de tópicos do caminho resultante provavelmente é um pesadelo algorítmico.

Mas esse trabalho é simplificado consideravelmente se cada caminho é reduzido a uma série de linhas de varredura horizontal, como aqueles no antiga câmara de vácuo TVs. Cada linha de verificação é simplesmente uma linha horizontal com um ponto inicial e um ponto de extremidade. Por exemplo, um círculo com um raio de 10 pode ser decomposto em 20 linhas de varredura horizontal, cada um deles inicia na parte esquerda do círculo e termina na parte direita. Combinar dois círculos com qualquer operação de região se torna muito simple, porque ele é simplesmente uma questão de examinar as coordenadas de início e término de cada par correspondente de linhas de varredura.

Isso é o que é uma região: uma série de linhas de varredura horizontal que define uma área.

No entanto, quando uma área é reduzida a uma série de verificação de linhas, essas linhas são baseadas em uma dimensão de pixel específico de verificação. Estritamente falando, a região não é um objeto de gráfico vetorial. É mais semelhante a um bitmap monocromático compactado que para um caminho. Consequentemente, regiões não podem ser dimensionadas ou giradas sem perder a fidelidade e por esse motivo que não são transformadas quando usado para áreas de recorte.

No entanto, você pode aplicar transformações a regiões para fins de pintura. O **região Paint** programa vívida demonstra a natureza interna das regiões. O [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe cria um `SKRegion` objeto com base em um `SKPath` de um círculo de raio de 10 unidades. Uma transformação, em seguida, expande esse círculo para preencher a página:

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

[![](clipping-images//regionpaint-small.png "Tripla captura de tela da página região Paint")](clipping-images/regionpaint-large.png#lightbox "tripla captura de tela da página de pintura de região")

A região é claramente uma série de coordenadas discretas.

Se você não precisar usar transformações em conjunto com suas áreas de recorte, você pode usar regiões de recorte, como o **quatro – folha Jetelové** página demonstra. O [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe constrói uma região composta de quatro regiões circulares, define essa região composta como a área de recorte e, em seguida, desenha uma série de linhas retas 360 procedentes do centro da página:

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

Ele realmente não parece um trevo de folha quatro – mas é uma imagem que pode ser difícil de renderização sem distorção:

[![](clipping-images//fourleafclover-small.png "Captura de tela da página quatro – folha Jetelové tripla")](clipping-images/fourleafclover-large.png#lightbox "tripla captura de tela da página quatro – folha Jetelové")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
