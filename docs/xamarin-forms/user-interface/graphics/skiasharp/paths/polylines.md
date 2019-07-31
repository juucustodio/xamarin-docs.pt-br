---
title: Polilinhas e equações paramétricas
description: Este artigo explica como ao usar SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f326a2ef449b7c807be150a002a4afc600d9908d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652700"
---
# <a name="polylines-and-parametric-equations"></a>Polilinhas e equações paramétricas

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar SkiaSharp para processar qualquer linha que você pode definir com equações paramétricas_

No [ **SkiaSharp curvas e caminhos** ](../curves/index.md) seção deste guia, você verá os vários métodos que [ `SKPath` ](xref:SkiaSharp.SKPath) define para processar determinados tipos de curvas. No entanto, às vezes, é necessário desenhar um tipo de curva que não é diretamente compatível com `SKPath`. Nesse caso, você pode usar uma polilinha (uma coleção de linhas conectadas) para desenhar qualquer curva que você pode definir matematicamente. Se você fizer as linhas pequeno o suficiente e inúmeros suficiente, o resultado se parecerá com uma curva. Este espiral é realmente 3.600 pequenas linhas:

![](polylines-images/spiralexample.png "Uma espiral")

Geralmente é melhor definir uma curva em termos de um par de equações paramétricas. Esses são equações para coordenadas X e Y que dependem de uma variável de terceiro, às vezes chamada de `t` por tempo. Por exemplo, as seguintes equações paramétricas definem um círculo com um raio de 1 centralizado no ponto (0, 0) para *t* de 0 a 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Se você quiser um raio maior do que 1, simplesmente pode multiplicar os valores do seno e cosseno por esse radius e se você precisar mover o centro em outro local, adicione esses valores:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Para uma elipse com o paralelo de eixos horizontal e vertical, dois raios estão envolvidos:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

Em seguida, você pode colocar o código equivalente do SkiaSharp em um loop que calcula os vários pontos e adiciona-os para um caminho. O código de SkiaSharp a seguir cria um `SKPath` objeto para uma elipse que preenche a superfície de exibição. O loop ciclos diretamente por meio de 360 graus. O centro é metade da largura e altura da superfície de exibição e, portanto, são os dois raios:

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

Isso resulta em uma elipse definida por 360 pequenas linhas. Quando ela é processada, ele aparece suave.

Obviamente, você não precisa criar uma ellipse usando uma polilinha porque `SKPath` inclui um `AddOval` método que faz isso para você. Mas você pode desenhar um objeto visual que não é fornecido pela `SKPath`.

O **Archimedean espiral** possui código similares ou o código da elipse, mas com uma diferença fundamental. Ele executa um loop em torno de 360 graus do círculo 10 vezes, ajustando continuamente o radius:

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

O resultado também é chamado de um *espiral aritmético* porque o deslocamento entre cada loop é constante:

[![](polylines-images/archimedeanspiral-small.png "Captura de tela da página Archimedean espiral tripla")](polylines-images/archimedeanspiral-large.png#lightbox "tripla captura de tela da página Archimedean espiral")

Observe que o `SKPath` é criado em um `using` bloco. Isso `SKPath` consome mais memória do que o `SKPath` objetos nos programas do anteriores, o que sugere que um `using` bloco é mais apropriado para descartar todos os recursos não gerenciados.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
