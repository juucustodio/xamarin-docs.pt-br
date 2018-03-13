---
title: "Polilinhas e equações paramétricas"
description: "Use SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas"
ms.topic: article
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e40fd215d23e7da6f1356bba17fac84ce91007ae
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="polylines-and-parametric-equations"></a>Polilinhas e equações paramétricas

_Use SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas_

Na parte posterior deste guia, você verá os vários métodos que `SKPath` define para processar determinados tipos de curvas. No entanto, às vezes, é necessário desenhar um tipo de curva não é diretamente compatível com `SKPath`. Nesse caso, você pode usar uma polilinha (uma coleção de linhas conectadas) para desenhar qualquer curva que você pode definir matematicamente. Se você fizer as linhas pequeno o suficiente e inúmeros suficiente, o resultado será semelhante a uma curva. Este espiral é realmente 3.600 pequenas linhas:

![](polylines-images/spiralexample.png "Uma espiral")

Geralmente é melhor definir uma curva em termos de um par de equações paramétricas. Essas são as equações de coordenadas X e Y que dependem de uma terceira variável, às vezes chamada de `t` por tempo. Por exemplo, as seguintes equações paramétricas definem um círculo com raio 1 centralizada no ponto (0, 0) para *t* de 0 a 1:

 x = cos(2πt) y = sin(2πt)

 Se você quiser um raio maior que 1, simplesmente pode multiplicar valores o seno e o cosseno que radius e se você precisar mover o centro para outro local, adicione esses valores:

 x = xCenter + radius·cos(2πt) y = yCenter + radius·sin(2πt)

Para uma elipse com paralelo os eixos horizontal e vertical, dois raios envolvidos:

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

Em seguida, você pode colocar o código SkiaSharp equivalente em um loop que calcula os vários pontos e adiciona as a um caminho. O código a seguir SkiaSharp cria um `SKPath` objeto para uma elipse que preenche a superfície de exibição. O loop ciclos diretamente por meio de 360 graus. O centro é metade de largura e altura da superfície de exibição e então são os dois raios:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Isso resulta em uma elipse definida pelo 360 pequenas linhas. Quando ele for renderizado, ela aparece suave.

Naturalmente, você não precisa criar uma elipse usando uma polilinha porque `SKPath` inclui um `AddOval` método que faz isso para você. Mas talvez você queira desenhar um objeto visual que não é fornecido pelo `SKPath`.

O **Archimedean espiral** página tiver um código que é semelhante ao código de elipse, mas com uma diferença fundamental. Ele executa um loop em torno de 360 graus do círculo 10 vezes, continuamente ajustando o radius:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

O resultado também é chamado um *espiral aritmético* porque o deslocamento entre cada loop é constante:

[![](polylines-images/archimedeanspiral-small.png "Captura de tela da página Archimedean espiral tripla")](polylines-images/archimedeanspiral-large.png#lightbox "tripla captura de tela da página Archimedean espiral")

Observe que o `SKPath` é criado em um `using` bloco. Isso `SKPath` consome mais memória do que o `SKPath` objetos nos programas do anteriores, o que sugere que um `using` bloco é mais apropriado para descartar todos os recursos não gerenciados.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
