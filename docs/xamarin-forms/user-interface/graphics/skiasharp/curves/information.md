---
title: Enumeração e informações de caminho
description: Este artigo explica como obter informações sobre caminhos SkiaSharp e enumerar o conteúdo e demonstra isso com código de exemplo.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 64a81b04961ebb0d03f661e99839b7ed78d710f2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368668"
---
# <a name="path-information-and-enumeration"></a>Enumeração e informações de caminho

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obter informações sobre caminhos e enumerar o conteúdo_

A [`SKPath`](xref:SkiaSharp.SKPath) classe define várias propriedades e métodos que permitem obter informações sobre o caminho. As [`Bounds`](xref:SkiaSharp.SKPath.Bounds) [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) Propriedades e (e os métodos relacionados) obtêm as dimensões métricas de um caminho. O [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) método permite determinar se um ponto específico está dentro de um caminho.

Às vezes, é útil determinar o comprimento total de todas as linhas e curvas que compõem um caminho. O cálculo desse comprimento não é uma tarefa simples forma algorítmica, portanto, uma classe inteira chamada [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) é dedicada a ela.

Às vezes, também é útil obter todas as operações de desenho e os pontos que compõem um caminho. A princípio, esse recurso pode parecer desnecessário: se o programa tiver criado o caminho, o programa já saberá o conteúdo. No entanto, você viu que os caminhos também podem ser criados por [efeitos de caminho](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) e convertendo [cadeias de caracteres de texto em caminhos](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). Você também pode obter todas as operações de desenho e os pontos que compõem esses caminhos. Uma possibilidade é aplicar uma transformação de algoritmo a todos os pontos, por exemplo, para encapsular texto em um hemisfério:

![Texto encapsulado em um hemisfério](information-images/pathenumerationsample.png)

## <a name="getting-the-path-length"></a>Obtendo o comprimento do caminho

No artigo [**caminhos e texto**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) , você viu como usar o [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método para desenhar uma cadeia de texto cuja linha de base segue o curso de um caminho. Mas e se você quiser dimensionar o texto para que ele se ajuste precisamente ao caminho? Desenhar texto em um círculo é fácil porque a circunferência de um círculo é simples de calcular. Mas a circunferência de uma elipse ou o comprimento de uma curva Bézier não é tão simples.

A [`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure) classe pode ajudar. O [Construtor](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) aceita um `SKPath` argumento e a [`Length`](xref:SkiaSharp.SKPathMeasure.Length) Propriedade revela seu comprimento.

Essa classe é demonstrada no exemplo de **comprimento de caminho** , que se baseia na página de **curva de Bézier** . O arquivo [**PathLengthPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) deriva de `InteractivePage` e inclui uma interface touch:

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

O arquivo code-behind [**PathLengthPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) permite que você mova quatro pontos de toque para definir os pontos de extremidade e os pontos de controle de uma curva Bézier cúbica. Três campos definem uma cadeia de texto, um `SKPaint` objeto e uma largura calculada do texto:

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

O `baseTextWidth` campo é a largura do texto com base em uma `TextSize` configuração de 10.

O `PaintSurface` manipulador desenha a curva de Bézier e, em seguida, dimensiona o texto para se ajustar ao seu comprimento completo:

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

A `Length` Propriedade do `SKPathMeasure` objeto recém-criado Obtém o comprimento do caminho. O comprimento do caminho é dividido pelo `baseTextWidth` valor (que é a largura do texto com base em um tamanho de texto de 10) e, em seguida, multiplicado pelo tamanho de texto base de 10. O resultado é um novo tamanho de texto para exibir o texto ao longo desse caminho:

[![Captura de tela tripla da página comprimento do caminho](information-images/pathlength-small.png)](information-images/pathlength-large.png#lightbox "Captura de tela tripla da página comprimento do caminho")

Como a curva de Bézier fica mais longa ou mais curta, você pode ver a alteração do tamanho do texto.

## <a name="traversing-the-path"></a>Atravessando o caminho

`SKPathMeasure` pode fazer mais do que apenas medir o comprimento do caminho. Para qualquer valor entre zero e o comprimento do caminho, um `SKPathMeasure` objeto pode obter a posição no caminho e a tangente para a curva de caminho nesse ponto. A tangente está disponível como um vetor na forma de um `SKPoint` objeto ou como uma rotação encapsulada em um `SKMatrix` objeto. Aqui estão os métodos de `SKPathMeasure` que obtêm essas informações de maneiras diferentes e flexíveis:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Os membros da [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) enumeração são:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

A página de **meio-pipe Unicycle** anima uma figura de aderência em um unicycle que parece passar para a frente e para trás ao longo de uma curva Bézier cúbica:

[![Captura de tela tripla da página de Half-Pipe de unicycle](information-images/unicyclehalfpipe-small.png)](information-images/unicyclehalfpipe-large.png#lightbox "Captura de tela tripla da página de Half-Pipe de unicycle")

O `SKPaint` objeto usado para traçar o meio-pipe e o Unicycle é definido como um campo na `UnicycleHalfPipePage` classe. Também definido é o `SKPath` objeto para o Unicycle:

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

A classe contém as substituições padrão dos `OnAppearing` `OnDisappearing` métodos e para animação. O `PaintSurface` manipulador cria o caminho para o meio-pipe e, em seguida, o desenha. `SKPathMeasure`Em seguida, um objeto é criado com base neste caminho:

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

O `PaintSurface` manipulador calcula um valor `t` que vai de 0 a 1 a cada cinco segundos. Em seguida, ele usa a `Math.Cos` função para converter isso em um valor `t` que varia de 0 a 1 e de volta para 0, em que 0 corresponde ao Unicycle no início da parte superior esquerda, enquanto 1 corresponde ao Unicycle no canto superior direito. A função cosseno faz com que a velocidade fique mais lenta na parte superior do pipe e mais rápida na parte inferior.

Observe que esse valor de `t` deve ser multiplicado pelo comprimento do caminho para o primeiro argumento `GetMatrix` . Em seguida, a matriz é aplicada ao `SKCanvas` objeto para desenhar o caminho Unicycle.

## <a name="enumerating-the-path"></a>Enumerando o caminho

Duas classes inseridas do `SKPath` permitem que você enumere o conteúdo do caminho. Essas classes são [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) e [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator) . As duas classes são muito semelhantes, mas `SKPath.Iterator` podem eliminar elementos no caminho com um comprimento zero ou perto de um comprimento zero. O `RawIterator` é usado no exemplo a seguir.

Você pode obter um objeto do tipo `SKPath.RawIterator` chamando o [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) método de `SKPath` . A enumeração por meio do caminho é realizada com a chamada repetida do [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) método. Passe para ele uma matriz de quatro `SKPoint` valores:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

O `Next` método retorna um membro do [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) tipo de enumeração. Esses valores indicam o comando de desenho específico no caminho. O número de pontos válidos inseridos na matriz depende desse verbo:

- `Move` com um único ponto
- `Line` com dois pontos
- `Cubic` com quatro pontos
- `Quad` com três pontos
- `Conic` com três pontos (e também chamar o [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) método para o peso)
- `Close` com um ponto
- `Done`

O `Done` verbo indica que a enumeração de caminho está concluída.

Observe que não há `Arc` verbos. Isso indica que todos os arcos são convertidos em curvas Bézier quando adicionados ao caminho.

Algumas das informações na `SKPoint` matriz são redundantes. Por exemplo, se um `Move` verbo é seguido por um `Line` verbo, o primeiro dos dois pontos que acompanha o `Line` é o mesmo que o `Move` ponto. Na prática, essa redundância é muito útil. Quando você recebe um `Cubic` verbo, ele é acompanhado por todos os quatro pontos que definem a curva Bézier cúbica. Você não precisa manter a posição atual estabelecida pelo verbo anterior.

O verbo problemático, no entanto, é `Close` . Este comando desenha uma linha reta da posição atual até o início da delimitação estabelecida anteriormente pelo `Move` comando. O ideal é `Close` que o verbo forneça esses dois pontos em vez de apenas um ponto. O que é pior é que o ponto que acompanha o `Close` verbo é sempre (0, 0). Ao enumerar por meio de um caminho, você provavelmente precisará manter o `Move` ponto e a posição atual.

## <a name="enumerating-flattening-and-malforming"></a>Enumerando, nivelando e Malforming

Às vezes, é desejável aplicar uma transformação de algoritmo a um caminho para malform-la de alguma maneira:

![Texto encapsulado em um hemisfério](information-images/pathenumerationsample.png)

A maioria dessas letras consiste em linhas retas, embora essas linhas retas aparentemente tenham sido torcidas em curvas. Como isso é possível?

A chave é que as linhas retas originais são divididas em uma série de linhas retas menores. Essas linhas retas menores podem ser manipuladas de maneiras diferentes para formar uma curva.

Para ajudar com esse processo, o exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contém uma [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) classe estática com um `Interpolate` método que divide uma linha reta em várias linhas curtas que são apenas uma unidade de comprimento. Além disso, a classe contém vários métodos que convertem os três tipos de curvas Bézier em uma série de pequenas linhas retas que aproximam a curva. (As fórmulas paramétricas foram apresentadas no artigo [**três tipos de curvas Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Esse processo é chamado de _achatamento_ da curva:

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
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Todos esses métodos são referenciados do método de extensão `CloneWithTransform` também incluído nessa classe e mostrados abaixo. Esse método clona um caminho enumerando os comandos de caminho e construindo um novo caminho com base nos dados. No entanto, o novo caminho consiste apenas em `MoveTo` `LineTo` chamadas e. Todas as curvas e linhas retas são reduzidas a uma série de linhas pequenas.

Ao chamar `CloneWithTransform` , você passa para o método a `Func<SKPoint, SKPoint>` , que é uma função com um `SKPaint` parâmetro que retorna um `SKPoint` valor. Essa função é chamada para cada ponto para aplicar uma transformação de algoritmos personalizada:

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

Como o caminho clonado é reduzido a pequenas linhas retas, a função transformar tem a capacidade de converter linhas retas em curvas.

Observe que o método retém o primeiro ponto de cada contorno na variável chamada `firstPoint` e a posição atual após cada comando de desenho na variável `lastPoint` . Essas variáveis são necessárias para construir a linha de fechamento final quando um `Close` verbo é encontrado.

O exemplo **GlobularText** usa esse método de extensão para, aparentemente, encapsular o texto em um hemisfério em um efeito 3D:

[![Captura de tela tripla da página de texto globular](information-images/globulartext-small.png)](information-images/globulartext-large.png#lightbox "Captura de tela tripla da página de texto globular")

O [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) Construtor de classe executa essa transformação. Ele cria um `SKPaint` objeto para o texto e, em seguida, obtém um `SKPath` objeto do `GetTextPath` método. Esse é o caminho passado para o `CloneWithTransform` método de extensão junto com uma função de transformação:

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

A função Transform calcula primeiro os dois valores chamados `longitude` e o `latitude` intervalo de – π/2 na parte superior e esquerda do texto, para π/2 na direita e na parte inferior do texto. O intervalo desses valores não é visualmente satisfatório, portanto, eles são reduzidos multiplicando-se por 0,75. (Experimente o código sem esses ajustes. O texto se torna muito obscuro no norte e no Sul polos e muito fino nos lados.) Essas coordenadas esféricas tridimensionais são convertidas em bidimensionais `x` e `y` coordenadas por fórmulas padrão.

O novo caminho é armazenado como um campo. O `PaintSurface` manipulador, em seguida, precisa apenas centralizar e dimensionar o caminho para exibi-lo na tela:

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

Essa é uma técnica muito versátil. Se a matriz de efeitos de caminho descrito no artigo de [**efeitos de caminho**](effects.md) não abranger muito algo que você sentiu, essa é uma maneira de preencher as lacunas.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)