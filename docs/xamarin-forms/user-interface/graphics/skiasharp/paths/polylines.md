---
title: "Descrição das polilinhas e paramétricas": Este artigo explica como usar o SkiaSharp para renderizar qualquer linha que você possa definir com equações paramétricas e demonstra isso com código de exemplo. "
MS. Prod: xamarin MS. AssetID: 85AEBB33-E954-4364-A6E1-808FAB197BEE MS. Technology: xamarin-skiasharp autor: davidbritch MS. Author: dabritch MS. Date: 03/10/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="polylines-and-parametric-equations"></a>Polilinhas e equações paramétricas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use SkiaSharp para renderizar qualquer linha que você possa definir com equações paramétricas_

Na seção [**curvas e caminhos do SkiaSharp**](../curves/index.md) deste guia, você verá os vários métodos que o [`SKPath`](xref:SkiaSharp.SKPath) define para processar determinados tipos de curvas. No entanto, às vezes é necessário desenhar um tipo de curva que não seja diretamente suportado pelo `SKPath` . Nesse caso, você pode usar uma polilinha (uma coleção de linhas conectadas) para desenhar qualquer curva que possa ser definida matematicamente. Se você tornar as linhas pequenas o suficiente e inúmeras, o resultado será semelhante a uma curva. Essa espiral tem, na verdade, 3.600 pequenas linhas:

![](polylines-images/spiralexample.png "A spiral")

Em geral, é melhor definir uma curva em termos de um par de equações paramétricas. Essas são equações para coordenadas X e Y que dependem de uma terceira variável, às vezes chamada `t` de tempo. Por exemplo, as equações paramétricas a seguir definem um círculo com um raio de 1 centralizado no ponto (0, 0) para *t* de 0 a 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Se você quiser um raio maior que 1, poderá simplesmente multiplicar os valores seno e cosseno por esse raio e, se precisar mover o centro para outro local, adicione esses valores:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Para uma elipse com os eixos paralelos a horizontal e vertical, dois raios são envolvidos:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

Em seguida, você pode colocar o código SkiaSharp equivalente em um loop que calcula os vários pontos e adiciona-os a um caminho. O código SkiaSharp a seguir cria um `SKPath` objeto para uma elipse que preenche a superfície de exibição. O loop percorre os 360 graus diretamente. O centro é metade da largura e da altura da superfície de exibição e, portanto, são os dois raios:

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

Isso resulta em uma elipse definida por 360 pequenas linhas. Quando é renderizado, parece suave.

É claro que você não precisa criar uma elipse usando uma polilinha porque o `SKPath` inclui um `AddOval` método que faz isso para você. Mas talvez você queira desenhar um objeto visual que não seja fornecido pelo `SKPath` .

A página de **espiral Archimedean** tem código semelhante ao código de elipse, mas com uma diferença crucial. Ele faz um loop em volta dos 360 graus do círculo 10 vezes, ajustando continuamente o raio:

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

O resultado também é chamado de *espiral aritmético* porque o deslocamento entre cada loop é constante:

[![](polylines-images/archimedeanspiral-small.png "Triple screenshot of the Archimedean Spiral page")](polylines-images/archimedeanspiral-large.png#lightbox "Triple screenshot of the Archimedean Spiral page")

Observe que o `SKPath` é criado em um `using` bloco. Isso `SKPath` consome mais memória do que os `SKPath` objetos nos programas anteriores, o que sugere que um `using` bloco é mais apropriado para descartar todos os recursos não gerenciados.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
