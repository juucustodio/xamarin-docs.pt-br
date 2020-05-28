---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8d11add988828fa4e26d3f6728dd0b4319b3630
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133296"
---
# <a name="matrix-transforms-in-skiasharp"></a>Transformações de matriz em SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Aprofunde-se em transformações de SkiaSharp com a matriz de transformação versátil_

Todas as transformações aplicadas ao `SKCanvas` objeto são consolidadas em uma única instância da [`SKMatrix`](xref:SkiaSharp.SKMatrix) estrutura. Essa é uma matriz de transformação padrão de 3 por 3 semelhante àquelas em todos os sistemas de gráficos 2D modernos.

Como você viu, você pode usar transformações no SkiaSharp sem saber mais sobre a matriz de transformação, mas a matriz de transformação é importante de uma perspectiva teórica e é crucial ao usar transformações para modificar caminhos ou para lidar com a entrada de toque complexa, ambas demonstradas neste artigo e a próxima.

![](matrix-images/matrixtransformexample.png "A bitmap subjected to an affine transform")

A matriz de transformação atual aplicada ao `SKCanvas` está disponível a qualquer momento acessando a propriedade somente leitura [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) . Você pode definir uma nova matriz de transformação usando o [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) método e pode restaurar essa matriz de transformação para valores padrão chamando [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) .

O único outro `SKCanvas` membro que funciona diretamente com a transformação de matriz da tela é [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) que concatena duas matrizes multiplicando-as juntas.

A matriz de transformação padrão é a matriz de identidade e consiste em uma das células diagonais e 0 em todos os outros lugares:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Você pode criar uma matriz de identidade usando o [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) método estático:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

O `SKMatrix` construtor padrão não *not* retorna uma matriz de identidade. Ele retorna uma matriz com todas as células definidas como zero. Não use o `SKMatrix` Construtor, a menos que você planeje definir essas células manualmente.

Quando SkiaSharp renderiza um objeto gráfico, cada ponto (x, y) é efetivamente convertido em uma matriz 1-por-3 com um 1 na terceira coluna:

<pre>
| x  y  1 |
</pre>

Esta matriz 1-por-3 representa um ponto tridimensional com a coordenada Z definida como 1. Há motivos matemáticos (discutidos posteriormente) por que uma transformação de matriz bidimensional requer o trabalho em três dimensões. Você pode considerar essa matriz 1 por 3 como representando um ponto em um sistema de coordenadas 3D, mas sempre no plano 2D em que Z é igual a 1.

Essa matriz 1-por-3 é então multiplicada pela matriz de transformação e o resultado é o ponto renderizado na tela:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Usando a multiplicação de matriz padrão, os pontos convertidos são os seguintes:

`x' = x`

`y' = y`

`z' = 1`

Essa é a transformação padrão.

Quando o `Translate` método é chamado no `SKCanvas` objeto, os `tx` argumentos e `ty` para o `Translate` método se tornam as duas primeiras células na terceira linha da matriz de transformação:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

A multiplicação agora é a seguinte:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Aqui estão as fórmulas de transformação:

`x' = x + tx`

`y' = y + ty`

Fatores de dimensionamento têm um valor padrão de 1. Quando você chama o `Scale` método em um novo `SKCanvas` objeto, a matriz de transformação resultante contém os `sx` `sy` argumentos e nas células diagonais:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

As fórmulas de transformação são as seguintes:

`x' = sx · x`

`y' = sy · y`

A matriz de transformação após `Skew` a chamada contém os dois argumentos nas células da matriz adjacentes aos fatores de dimensionamento:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

As fórmulas de transformação são:

`x' = x + xSkew · y`

`y' = ySkew · x + y`

Para uma chamada para `RotateDegrees` ou `RotateRadians` para um ângulo de α, a matriz de transformação é a seguinte:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Aqui estão as fórmulas de transformação:

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Quando α é de 0 graus, é a matriz de identidade. Quando α é 180 graus, a matriz de transformação é a seguinte:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Uma rotação de 180 graus é equivalente a inverter um objeto horizontalmente e verticalmente, o que também é feito pela definição de fatores de escala de – 1.

Todos esses tipos de transformações são classificados como transformações *afim* . As transformações afim nunca envolvem a terceira coluna da matriz, que permanece com os valores padrão de 0, 0 e 1. O artigo [**transformações não afim**](non-affine.md) discute as transformações não afim.

## <a name="matrix-multiplication"></a>multiplicação de matrizes

Uma vantagem significativa com o uso da matriz de transformação é que as transformações de composição podem ser obtidas pela multiplicação de matriz, que é geralmente mencionada na documentação do SkiaSharp como *concatenação*. Muitos dos métodos relacionados à transformação em `SKCanvas` referem-se a "pre-concatenation" ou "pre-Concat". Isso se refere à ordem de multiplicação, que é importante porque a multiplicação de matriz não é comutada.

Por exemplo, a documentação para o [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método diz que "pré-secole a matriz atual com a tradução especificada", enquanto a documentação do [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método diz que "pré-secole a matriz atual com a escala especificada".

Isso significa que a transformação especificada pela chamada de método é o multiplicador (o operando esquerdo) e a matriz de transformação atual é a multiplicando (o operando à direita).

Suponha que `Translate` seja chamado seguido por `Scale` :

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

A `Scale` transformação é multiplicada pela `Translate` transformação da matriz de transformação composta:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale`pode ser chamado antes da `Translate` seguinte maneira:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

Nesse caso, a ordem da multiplicação é invertida e os fatores de dimensionamento são efetivamente aplicados aos fatores de Tradução:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Este é o `Scale` método com um ponto dinâmico:

```csharp
canvas.Scale(sx, sy, px, py);
```

Isso é equivalente às seguintes chamadas de conversão e escala:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

As três matrizes de transformação são multiplicadas na ordem inversa de como os métodos aparecem no código:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>A estrutura SKMatrix

A `SKMatrix` estrutura define nove Propriedades de leitura/gravação do tipo `float` correspondente às nove células da matriz de transformação:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix`também define uma propriedade chamada [`Values`](xref:SkiaSharp.SKMatrix.Values) de tipo `float[]` . Essa propriedade pode ser usada para definir ou obter os nove valores em uma captura na ordem,,,,,,, `ScaleX` `SkewX` `TransX` `SkewY` `ScaleY` `TransY` `Persp0` `Persp1` e `Persp2` .

As `Persp0` `Persp1` células, e `Persp2` são discutidas no artigo [**transformações não afim**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Se essas células tiverem seus valores padrão de 0, 0 e 1, a transformação será multiplicada por um ponto de coordenadas como este:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Esta é a transformação afim completa de duas dimensões. A transformação afim preserva as linhas paralelas, o que significa que um retângulo nunca é transformado em nada além de um paralelogramo.

A `SKMatrix` estrutura define vários métodos estáticos para criar `SKMatrix` valores. Todos esses valores de retorno `SKMatrix` :

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single))com um ponto dinâmico
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single))para um ângulo em radianos
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single))para um ângulo em radianos com um ponto dinâmico
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single))com um ponto dinâmico
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix`também define vários métodos estáticos que concatenam duas matrizes, o que significa multiplica-las. Esses métodos são nomeados [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) , [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) e [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) , e há duas versões de cada um. Esses métodos não têm valores de retorno; em vez disso, eles fazem referência `SKMatrix` a valores existentes por meio de `ref` argumentos. No exemplo a seguir, `A` , `B` e `R` (para "resultado") são todos os `SKMatrix` valores.

Os dois `Concat` métodos são chamados da seguinte maneira:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Eles executam a seguinte multiplicação:

`R = B × A`

Os outros métodos têm apenas dois parâmetros. O primeiro parâmetro é modificado e, ao retornar da chamada de método, contém o produto das duas matrizes. Os dois `PostConcat` métodos são chamados da seguinte maneira:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Essas chamadas executam a seguinte operação:

`A = A × B`

Os dois `PreConcat` métodos são semelhantes:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Essas chamadas executam a seguinte operação:

`A = B × A`

As versões desses métodos com todos os `ref` argumentos são um pouco mais eficientes na chamada de implementações subjacentes, mas pode ser confusa para alguém que lê seu código e supondo que qualquer coisa com um `ref` argumento seja modificada pelo método. Além disso, muitas vezes é conveniente passar um argumento que seja resultado de um dos `Make` métodos, por exemplo:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Isso cria a seguinte matriz:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Essa é a transformação de escala multiplicada pela transformação de conversão. Nesse caso em particular, a `SKMatrix` estrutura fornece um atalho com um método chamado [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) :

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Essa é uma das poucas ocasiões em que é seguro usar o `SKMatrix` Construtor. O `SetScaleTranslate` método define todas as nove células da matriz. Também é seguro usar o `SKMatrix` Construtor com os métodos estáticos `Rotate` e `RotateDegrees` :

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Esses métodos *não* concatenam uma transformação girar para uma transformação existente. Os métodos definem todas as células da matriz. Elas são funcionalmente idênticas aos `MakeRotation` `MakeRotationDegrees` métodos e, exceto pelo fato de que não instanciam o `SKMatrix` valor.

Suponha que você tenha um `SKPath` objeto que deseja exibir, mas você prefere que ele tenha uma orientação um pouco diferente ou um ponto central diferente. Você pode modificar todas as coordenadas desse caminho chamando o [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) método de `SKPath` com um `SKMatrix` argumento. A página de **transformação de caminho** demonstra como fazer isso. A [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) classe faz referência ao `HendecagramPath` objeto em um campo, mas usa seu construtor para aplicar uma transformação a esse caminho:

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

O `HendecagramPath` objeto tem um centro em (0, 0) e os 11 pontos da estrela se estendem para fora desse centro por 100 unidades em todas as direções. Isso significa que o caminho tem coordenadas positivas e negativas. A página de **transformação de caminho** prefere trabalhar com uma estrela três vezes maior e com todas as coordenadas positivas. Além disso, não quer um ponto da estrela para apontar diretamente. Ele deseja em vez de um ponto da estrela para apontar diretamente. (Como a estrela tem 11 pontos, ela não pode ter ambos.) Isso requer girar a estrela em 360 graus dividido por 22.

O construtor cria um `SKMatrix` objeto a partir de três transformações separadas usando o `PostConcat` método com o seguinte padrão, em que a, B e C são instâncias de `SKMatrix` :

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Esta é uma série de multiplicações sucessivas, portanto, o resultado é o seguinte:

`A × B × C`

As multiplicidades consecutivas ajudam a entender o que cada transformação faz. A transformação escala aumenta o tamanho das coordenadas de caminho por um fator de 3, portanto, as coordenadas variam de – 300 a 300. A transformação girar gira a estrela em sua origem. Em seguida, a transformação de conversão muda de 300 pixels para a direita e para baixo, portanto, todas as coordenadas se tornam positivas.

Há outras sequências que produzem a mesma matriz. Aqui está outro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Isso gira o caminho em todo o seu centro primeiro e, em seguida, converte 100 pixels à direita e abaixo para que todas as coordenadas sejam positivas. Em seguida, a estrela é aumentada em tamanho em relação ao novo canto superior esquerdo, que é o ponto (0, 0).

O `PaintSurface` manipulador pode simplesmente renderizar este caminho:

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Ele aparece no canto superior esquerdo da tela:

[![](matrix-images/pathtransform-small.png "Triple screenshot of the Path Transform page")](matrix-images/pathtransform-large.png#lightbox "Triple screenshot of the Path Transform page")

O Construtor deste programa aplica a matriz ao caminho com a seguinte chamada:

```csharp
transformedPath.Transform(matrix);
```

O caminho não *retém essa* matriz como uma propriedade. Em vez disso, ele aplica a transformação a todas as coordenadas do caminho. Se `Transform` for chamado novamente, a transformação será aplicada novamente e a única maneira de voltar é aplicar outra matriz que desfaz a transformação. Felizmente, a `SKMatrix` estrutura define um [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) método que obtém a matriz que reverte uma determinada matriz:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

O método é chamado `TryInverse` porque nem todas as matrizes são invertível, mas não é provável que uma matriz não invertível seja usada para uma transformação de gráficos.

Você também pode aplicar uma transformação de matriz a um `SKPoint` valor, uma matriz de pontos, um `SKRect` ou até mesmo apenas um único número dentro de seu programa. A `SKMatrix` estrutura oferece suporte a essas operações com uma coleção de métodos que começam com a palavra `Map` , como estas:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Se você usar esse último método, tenha em mente que a `SKRect` estrutura não é capaz de representar um retângulo girado. O método faz sentido apenas para um `SKMatrix` valor que representa conversão e dimensionamento.

## <a name="interactive-experimentation"></a>Experimentação interativa

Uma maneira de ter uma ideia para a transformação afim é mover interativamente três cantos de um bitmap pela tela e ver quais resultados da transformação. Essa é a ideia por trás da página **Mostrar matriz afim** . Esta página requer duas outras classes que também são usadas em outras demonstrações:

A [`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) classe exibe um círculo translúcida que pode ser arrastado pela tela. `TouchPoint`requer que um `SKCanvasView` ou um elemento que seja pai de um `SKCanvasView` tenha o [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) anexado. Defina a propriedade `Capture` como `true`. No `TouchAction` manipulador de eventos, o programa deve chamar o `ProcessTouchEvent` método em `TouchPoint` para cada `TouchPoint` instância. O método retorna `true` se o evento de toque resultou no movimento do ponto de toque. Além disso, o `PaintSurface` manipulador deve chamar o `Paint` método em cada `TouchPoint` instância, passando para ele o `SKCanvas` objeto.

`TouchPoint`demonstra uma maneira comum de que um Visual SkiaSharp possa ser encapsulado em uma classe separada. A classe pode definir propriedades para especificar características do Visual e um método chamado `Paint` com um `SKCanvas` argumento pode renderizá-la.

A `Center` propriedade de `TouchPoint` indica o local do objeto. Essa propriedade pode ser definida para inicializar o local; a propriedade é alterada quando o usuário arrasta o círculo em volta da tela.

A **página mostrar matriz afim** também requer a [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Essa classe exibe as células de um `SKMatrix` objeto. Ele tem dois métodos públicos: `Measure` para obter as dimensões da matriz renderizada e `Paint` para exibi-las. A classe contém uma `MatrixPaint` Propriedade do tipo `SKPaint` que pode ser substituída por uma cor ou tamanho de fonte diferente.

O arquivo [**ShowAffineMatrixPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) instancia o `SKCanvasView` e anexa um `TouchEffect` . O arquivo code-behind [**ShowAffineMatrixPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) cria três `TouchPoint` objetos e os define como posições correspondentes a três cantos de um bitmap que ele carrega de um recurso incorporado:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Uma matriz afim é definida exclusivamente por três pontos. Os três `TouchPoint` objetos correspondem aos cantos superior esquerdo, superior direito e inferior esquerdo do bitmap. Como uma matriz afim só é capaz de transformar um retângulo em um paralelogramo, o quarto ponto é implícito pelos outros três. O Construtor termina com uma chamada para `ComputeMatrix` , que calcula as células de um `SKMatrix` objeto desses três pontos.

O `TouchAction` manipulador chama o `ProcessTouchEvent` método de cada `TouchPoint` . O `scale` valor converte de Xamarin.Forms coordenadas em pixels:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Se algum `TouchPoint` tiver sido movido, o método chamará `ComputeMatrix` novamente e invalidará a superfície.

O `ComputeMatrix` método determina a matriz implícita por esses três pontos. A matriz chamada transforma `A` um retângulo quadrado de um pixel em um paralelogramo com base nos três pontos, enquanto a transformação de escala chamada `S` dimensiona o bitmap para um retângulo quadrado de um pixel. A matriz composta é `S` × `A` :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
    {
        // Scale transform
        SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

        // Affine transform
        SKMatrix A = new SKMatrix
        {
            ScaleX = ptUR.X - ptUL.X,
            SkewY = ptUR.Y - ptUL.Y,
            SkewX = ptLL.X - ptUL.X,
            ScaleY = ptLL.Y - ptUL.Y,
            TransX = ptUL.X,
            TransY = ptUL.Y,
            Persp2 = 1
        };

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Por fim, o `PaintSurface` método renderiza o bitmap com base nessa matriz, exibe a matriz na parte inferior da tela e renderiza os pontos de toque nos três cantos do bitmap:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

A tela do iOS abaixo mostra o bitmap quando a página é carregada pela primeira vez, enquanto as duas outras telas mostram isso após alguma manipulação:

[![](matrix-images/showaffinematrix-small.png "Triple screenshot of the Show Affine Matrix page")](matrix-images/showaffinematrix-large.png#lightbox "Triple screenshot of the Show Affine Matrix page")

Embora pareça que os pontos de toque arrastam os cantos do bitmap, isso é apenas uma ilusão. A matriz calculada a partir de pontos de toque transforma o bitmap para que os cantos coincidam com os pontos de toque.

É mais natural que os usuários movam, redimensionem e girem bitmaps não arrastando os cantos, mas usando um ou dois dedos diretamente no objeto para arrastar, pinçar e girar. Isso é abordado no próximo artigo [**manipulação de toque**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

## <a name="the-reason-for-the-3-by-3-matrix"></a>O motivo para a matriz 3-by-3

Pode ser esperado que um sistema de gráficos bidimensional exija apenas uma matriz de transformação 2-by-2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Isso funciona para dimensionamento, rotação e até mesmo distorção, mas não é capaz da mais básica de transformações, que é a tradução.

O problema é que a matriz 2-por-2 representa uma transformação *linear* em duas dimensões. Uma transformação linear preserva algumas operações aritméticas básicas, mas uma das implicações é que uma transformação linear nunca altera o ponto (0, 0). Uma transformação linear torna impossível A tradução.

Em três dimensões, uma matriz de transformação linear é parecida com esta:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

A célula rotulada `SkewXY` significa que o valor inclina a coordenada x com base nos valores de Y; a célula `SkewXZ` significa que o valor inclina a coordenada x com base nos valores de Z; e os valores se distorcem de forma semelhante para as outras `Skew` células.

É possível restringir essa matriz de transformação 3D a um plano bidimensional definindo `SkewZX` e `SkewZY` como 0, e `ScaleZ` como 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Se os gráficos bidimensionais forem desenhados inteiramente no plano no espaço 3D em que Z é igual a 1, a multiplicação de transformação terá esta aparência:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Tudo permanece no plano bidimensional em que Z é igual a 1, mas as `SkewXZ` `SkewYZ` células e efetivamente se tornam fatores de conversão bidimensionais.

É assim que uma transformação linear tridimensional serve como uma transformação não linear bidimensional. (Por analogia, as transformações em gráficos 3D são baseadas em uma matriz 4 por 4.)

A `SKMatrix` estrutura em SkiaSharp define propriedades para essa terceira linha:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Valores diferentes de zero `Persp0` e `Persp1` resultam em transformações que movem objetos para fora do plano bidimensional em que Z é igual a 1. O que acontece quando esses objetos são movidos de volta para esse plano é abordado no artigo sobre [**transformações não-afim**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
