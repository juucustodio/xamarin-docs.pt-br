---
title: Enumeração e informações de caminho
description: Este artigo explica como obter informações sobre caminhos de SkiaSharp e enumerar o conteúdo e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 09/12/2017
ms.openlocfilehash: 65c614e9a6eb26bc0d027a4a67bec19b036d0a70
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615269"
---
# <a name="path-information-and-enumeration"></a>Enumeração e informações de caminho

_Obter informações sobre os caminhos e enumerar o conteúdo_

O [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) classe define várias propriedades e métodos que permitem que você obtenha informações sobre o caminho. O [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) e [ `TightBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.TightBounds/) propriedades (e métodos relacionados) obtêm as dimensões de métrica de um caminho. O [ `Contains` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Contains/p/System.Single/System.Single/) método permite que você determine se um determinado ponto está dentro de um caminho.

Às vezes é útil para determinar o comprimento total de todas as linhas e curvas que compõem um caminho. Isso não é uma tarefa algoritmicamente simple, portanto, toda uma classe denominada [ `PathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) é dedicada a ele.

Também, às vezes, é útil obter todas as operações de desenho e pontos que compõem um caminho. A princípio, esse recurso pode parecer desnecessário: se o programa tiver criado o caminho, o programa já sabe o conteúdo. No entanto, você viu que caminhos também podem ser criados por [efeitos de caminho](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) e convertendo [cadeias de caracteres de texto em demarcadores](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). Você também pode obter todas as operações de desenho e pontos que compõem esses caminhos. Uma possibilidade é aplicar uma transformação algorítmica para todos os pontos. Isso permite que as técnicas como quebra automática de texto em torno de um hemisfério:

![](information-images/pathenumerationsample.png "Texto encapsulado em um hemisfério")

## <a name="getting-the-path-length"></a>Obtendo o comprimento do caminho

Neste artigo [ **caminhos e texto** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) você viu como usar o [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método para desenhar uma cadeia de caracteres de texto cuja linha de base segue o curso de um caminho. Mas e se você deseja dimensionar o texto para que ele se adapta o caminho com precisão? Para desenhar texto em torno de um círculo, isso é fácil porque a circunferência de um círculo é simple calcular. Mas a circunferência de uma elipse ou o comprimento de uma curva de Bézier não é tão simple.

O [ `SKPathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) classe pode ajudar. O [construtor](https://developer.xamarin.com/api/constructor/SkiaSharp.SKPathMeasure.SKPathMeasure/p/SkiaSharp.SKPath/System.Boolean/System.Single/) aceita um `SKPath` argumento e o [ `Length` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPathMeasure.Length/) propriedade revela seu comprimento.

Isso é demonstrado na **comprimento do caminho** exemplo, que se baseia o **curva de Bézier** página. O [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) arquivo deriva `InteractivePage` e inclui uma interface de toque:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

O [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) arquivo code-behind permite que você mova os quatro pontos de toque para definir os pontos de extremidade e pontos de uma curva de Bézier cúbica de controle. Três campos de definem uma cadeia de caracteres de texto, um `SKPaint` objeto e uma largura calculada do texto:

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

O `baseTextWidth` campo é a largura do texto com base em um `TextSize` configuração de 10.

O `PaintSurface` manipulador desenha a curva de Bézier e tamanhos, em seguida, o texto a ser ajustado ao longo de seu tamanho total:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

O `Length` propriedade de recém-criado `SKPathMeasure` objeto obtém o comprimento do caminho. Isso é dividido pelo `baseTextWidth` valor (que é a largura do texto com base em um tamanho de texto de 10) e, em seguida, multiplicado pelo tamanho da base de texto de 10. O resultado é um novo tamanho de texto para exibir o texto ao longo desse caminho:

[![](information-images/pathlength-small.png "Tripla captura de tela da página de comprimento de caminho")](information-images/pathlength-large.png#lightbox "tripla captura de tela da página do comprimento do caminho")

Conforme a curva de Bézier fica mais ou menos, você pode ver o tamanho do texto alterar.

## <a name="traversing-the-path"></a>Percorrer o caminho

`SKPathMeasure` pode fazer mais do que apenas a medida do comprimento do caminho. Para qualquer valor entre zero e o comprimento do caminho, uma `SKPathMeasure` objeto pode obter a posição do caminho e a tangente da curva do caminho naquele ponto. A tangente está disponível como um vetor na forma de um `SKPoint` do objeto ou como uma rotação encapsulado em um `SKMatrix` objeto. Aqui estão os métodos de `SKPathMeasure` que obter essas informações de maneiras variadas e flexíveis:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

O [ `SKPathMeasureMatrixFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasureMatrixFlags/) são:

- [`GetPosition`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPosition/)
- [`GetTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)
- [`GetPositionAndTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)

O **Pipe monociclo metade** página anima uma figura de pilha em um monociclo que parece andar e para trás ao longo de uma curva de Bézier cúbica:

[![](information-images/unicyclehalfpipe-small.png "Tripla captura de tela da página Pipe monociclo metade")](information-images/unicyclehalfpipe-large.png#lightbox "tripla captura de tela da página Pipe monociclo metade")

O `SKPaint` objeto usado para traçar o pipe de meia e o monociclo é definido como um campo de [ `UnicycleHalfPipePage` ]() classe. Também é definido é o `SKPath` objeto para o monociclo:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

A classe contém substituições de padrão de `OnAppearing` e `OnDisappearing` métodos para animação. O `PaintSurface` manipulador cria o caminho para o pipe do semestre e, em seguida, desenha-lo. Um `SKPathMeasure` objeto, em seguida, é criado com base nesse caminho:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

O `PaintSurface` manipulador calcula um valor de `t` que vai de 0 a 1 a cada cinco segundos. Ele usa o `Math.Cos` função para convertê-lo em um valor de `t` que varia de 0 a 1 e de volta para 0, onde 0 corresponde para o monociclo no início na parte superior esquerda, enquanto 1 corresponde ao monociclo no canto superior direito. A função cosseno faz com que a velocidade seja mais lenta na parte superior do pipe e mais rápido na parte inferior.

Observe que esse valor de `t` deve ser multiplicado pelo comprimento de caminho para o primeiro argumento para `GetMatrix`. A matriz é então aplicada ao `SKCanvas` objeto para o caminho monociclo de desenho.

## <a name="enumerating-the-path"></a>Enumerando o caminho

Dois inseridos classes de `SKPath` permitem enumerar o conteúdo do caminho. Essas classes são [ `SKPath.Iterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+Iterator/) e [ `SKPath.RawIterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+RawIterator/). As duas classes são muito semelhantes, mas `SKPath.Iterator` pode eliminar elementos no caminho com comprimento zero ou perto de comprimento zero. O `RawIterator` é usado no exemplo a seguir.

Você pode obter um objeto do tipo `SKPath.RawIterator` chamando o [ `CreateRawIterator` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CreateRawIterator()/) método de `SKPath`. Enumerando através do caminho é realizado repetidamente chamando o [ `Next` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.Next/p/SkiaSharp.SKPoint[]/) método. Passar a ele uma matriz de quatro `SKPoint` valores:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

O `Next` método retorna um membro de [ `SKPathVerb` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathVerb/) enumeração. Esses valores indicam que o comando de desenho específico no caminho. O número de pontos válidos inserido na matriz depende esse verbo:

- [`Move`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Move/) com um único ponto
- [`Line`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Line/) com dois pontos
- [`Cubic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Cubic/) com quatro pontos
- [`Quad`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Quad/) com três pontos
- [`Conic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Conic/) com três pontos (e também chamar o [ `ConicWeight` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.ConicWeight/) método para o peso)
- [`Close`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Close/) com um ponto
- [`Done`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Done/)

O `Done` verbo indica que a enumeração foi concluída.

Observe que há nenhum `Arc` verbos. Isso indica que todos os arcos são convertidos em curvas de Bézier quando adicionado ao caminho.

Algumas das informações no `SKPoint` matriz é redundante. Por exemplo, se um `Move` verbo é seguido por um `Line` verbo e, em seguida, o primeiro dos dois pontos que acompanham o `Line` é o mesmo que o `Move` apontar. Na prática, essa redundância é muito útil. Quando chegar um `Cubic` verbo, ela é acompanhada por todos os quatro pontos que definem a curva de Bézier cúbica. Você não precisa manter a posição atual estabelecida pelo verbo anterior.

O verbo problemático, no entanto, é `Close`. Esse comando desenha uma linha reta da posição atual para o início da delimitação estabelecida anteriormente pelo `Move` comando. O ideal é que o `Close` verbo deve fornecer esses dois pontos em vez de apenas um ponto. O que é pior é que o ponto que acompanha o `Close` verbo sempre é (0, 0). Isso significa que quando você enumerar por meio de um caminho, você provavelmente precisará reter o `Move` ponto e a posição atual.

Estático [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) classe contém vários métodos que convertem os três tipos de curvas de Bézier em uma série de linhas retas pequenas que se aproximam da curva. (As fórmulas paramétricas eram apresentadas neste artigo [ **tipos de três das curvas de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) O `Interpolate` método divide uma linha reta em várias linhas curtas que são apenas uma unidade de comprimento:

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Todos esses métodos são referenciados do método de extensão `CloneWithTransform` mostrado abaixo. Esse método clona um caminho, enumerando os comandos de caminho e construir um novo caminho com base nos dados. No entanto, o novo caminho consiste apenas `MoveTo` e `LineTo` chamadas. Todas as linhas retas e curvas sejam reduzidas a uma série de linhas pequenas.

Ao chamar `CloneWithTransform`, você passa para o método de um `Func<SKPoint, SKPoint>`, que é uma função com um `SKPaint` parâmetro retorna um `SKPoint` valor. Essa função é chamada para cada ponto de aplicar uma transformação algorítmica personalizada:

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Porque o caminho clonado é reduzido para pequenas linhas retas, a função de transformação tem a capacidade de converter linhas retas em curvas.

Observe que o método retém o primeiro ponto de cada delimitação na variável chamada `firstPoint` e a posição atual depois de cada comando de desenho na variável `lastPoint`. Eles são necessários para construir o fechamento de final de linha quando um `Close` verbo é encontrado.

O **GlobularText** exemplo usa esse método de extensão para quebrar o texto ao redor de um hemisfério aparentemente um efeito 3D:

[![](information-images/globulartext-small.png "Captura de tela da página de texto Globular tripla")](information-images/globulartext-large.png#lightbox "tripla captura de tela da página de texto Globular")

O [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) construtor da classe executa essa transformação. Ele cria um `SKPaint` do objeto para o texto e, em seguida, obtém um `SKPath` objeto o `GetTextPath` método. Esse é o caminho passado para o `CloneWithTransform` método de extensão juntamente com uma função de transformação:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

A função de transformação primeiro calcula dois valores denominados `longitude` e `latitude` que variam de – π/2 na parte superior e esquerda do texto, a π/2 à direita e inferior do texto. O intervalo desses valores não é visualmente satisfatório, para que eles são reduzidos pela multiplicação por 0,75. (Tente o código sem esses ajustes. O texto se torna muito fino nas laterais e muito obscuro no Centro-Norte e Sul polos.) Essas coordenadas esféricas tridimensionais são convertidas para bidimensional `x` e `y` coordenadas por fórmulas padrão.

O novo caminho é armazenado como um campo. O `PaintSurface` manipulador, em seguida, simplesmente precisa centralizar e dimensionar o caminho para exibi-la na tela:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Essa é uma técnica muito versáteis. Se a matriz de efeitos de caminho é descrito na [ **efeitos de caminho** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artigo bastante não abrange algo sentiram a deve ser incluída, essa é uma maneira para preencher as lacunas.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
