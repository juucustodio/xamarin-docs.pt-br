---
title: Transformações de matriz no SkiaSharp
description: Este artigo aprofunda em transformações de SkiaSharp com a matriz de transformação versátil e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2017
ms.openlocfilehash: 192f0745874b54989ab9070014dae2a5e9e98110
ms.sourcegitcommit: 605f7c480c3f7b5dd364fdb1bd4d983de8f7ed25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213771"
---
# <a name="matrix-transforms-in-skiasharp"></a>Transformações de matriz no SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Aprofunde-se em transformações de SkiaSharp com a matriz de transformação versátil_

Todas as transformações aplicadas para o `SKCanvas` objeto são consolidados em uma única instância do [ `SKMatrix` ](xref:SkiaSharp.SKMatrix) estrutura. Isso é uma matriz de transformação de 3 por 3 standard semelhante aos descritos em todos os sistemas de gráficos 2D modernos.

Como você viu, você pode usar transformações em SkiaSharp sem saber sobre a transformação de matriz, mas a matriz de transformação é importante de uma perspectiva teórica, e é fundamental ao usar transformações para modificar caminhos ou para lidar com a entrada por toque complexas, ambos quais são demonstradas neste artigo e a próxima.

![](matrix-images/matrixtransformexample.png "Um bitmap sujeito a uma transformação afim")

A matriz de transformação atual aplicada para o `SKCanvas` está disponível a qualquer momento acessando a somente leitura [ `TotalMatrix` ](xref:SkiaSharp.SKCanvas.TotalMatrix) propriedade. Você pode definir uma nova transformação de matriz usando o [ `SetMatrix` ](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) método e você pode restaurar essa matriz de transformação para valores padrão chamando [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix).

A única outra `SKCanvas` membro que funciona diretamente com a transformação de matriz da tela está [ `Concat` ](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) que concatena duas matrizes multiplicando-los juntos.

A matriz de transformação de padrão é a matriz de identidade e consiste em 1 na diagonais células e todos os outros lugares 0:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Você pode criar uma matriz de identidade usando estático [ `SKMatrix.MakeIdentity` ](xref:SkiaSharp.SKMatrix.MakeIdentity) método:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

O `SKMatrix` construtor padrão faz *não* retornar uma matriz de identidade. Ele retorna uma matriz com todas as células definidas como zero. Não use o `SKMatrix` construtor, a menos que você planeja definir às células manualmente.

Quando SkiaSharp processa um objeto gráfico, cada ponto (x, y) com eficiência é convertido em uma matriz de 1-por-3 com um 1 na terceira coluna:

<pre>
| x  y  1 |
</pre>

Esta matriz 1 por 3 representa um ponto tridimensional com a coordenada Z definida como 1. Há razões de matemáticas (discutidos posteriormente) por uma transformação de matriz bidimensional exige funcionando em três dimensões. Você pode pensar nessa matriz 1 por 3 como a representação de um ponto em um sistema de coordenadas 3D, mas sempre no plano 2D em que Z é igual a 1.

Esta matriz 1-por-3 é então multiplicado pela matriz de transformação, e o resultado é o ponto processado na tela:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Os pontos convertidos usando uma multiplicação de matriz padrão, são da seguinte maneira:

`x' = x`

`y' = y`

`z' = 1`

Essa é a transformação padrão.

Quando o `Translate` método é chamado em de `SKCanvas` objeto, o `tx` e `ty` argumentos para o `Translate` método tornam-se as duas primeiras células na terceira linha da matriz de transformação:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

A multiplicação agora é da seguinte maneira:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Aqui estão as fórmulas de transformação:

`x' = x + tx`

`y' = y + ty`

Fatores de dimensionamento tem um valor padrão de 1. Quando você chama o `Scale` método em um novo `SKCanvas` do objeto, a matriz de transformação resultante contém o `sx` e `sy` argumentos nas células diagonais:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

As fórmulas de transformação são da seguinte maneira:

`x' = sx · x`

`y' = sy · y`

A matriz de transformação depois de chamar `Skew` contém dois argumentos nas células da matriz adjacentes aos fatores de dimensionamento:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

As fórmulas de transformação são:

`x' = x + xSkew · y`

`y' = ySkew · x + y`

Para uma chamada para `RotateDegrees` ou `RotateRadians` para um ângulo de α, a matriz de transformação é da seguinte maneira:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Aqui estão as fórmulas de transformação:

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Quando α é de 0 graus, é a matriz de identidade. Quando α é 180 graus, a matriz de transformação é da seguinte maneira:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Uma rotação de 180 graus é equivalente a inversão de um objeto horizontalmente e verticalmente, que também é feito por fatores de dimensionamento de configuração de – 1.

Todos esses tipos de transformações são classificados como *afim* transforma. Transformações afins nunca envolvem a terceira coluna da matriz, que permanece com os valores padrão de 0, 0 e 1. O artigo [ **transformações não afins** ](non-affine.md) discute as transformações não afins.

## <a name="matrix-multiplication"></a>Multiplicação de matriz

Uma vantagem significativa com usando a matriz de transformação é que transformações de composição podem ser obtidas pela multiplicação de matriz, que geralmente é referenciada na documentação como SkiaSharp *concatenação*. Muitos dos métodos relacionados à transformação na `SKCanvas` se referir a pré-""concatenação ou "pré-concat." Refere-se a ordem de multiplicação, que é importante porque uma multiplicação de matriz não é comutativa.

Por exemplo, a documentação para o [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) método diz que ele "Pré-concats a matriz atual com a tradução especificada,", enquanto a documentação para o [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método diz que ele "Pré-concats a matriz atual com a escala especificada."

Isso significa que a transformação especificada pela chamada de método é o multiplicador (o operando esquerdo) e a matriz de transformação atual é o multiplicando (o operando direito).

Suponha que `Translate` é chamado seguido `Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

O `Scale` transformação é multiplicada pela `Translate` transformar para a matriz de transformação de composição:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` poderia ser chamado antes de `Translate` como este:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

Nesse caso, a ordem da multiplicação é invertida, e os fatores de dimensionamento são aplicados com eficiência para os fatores de conversão:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Aqui está o `Scale` método com um ponto dinâmico:

```csharp
canvas.Scale(sx, sy, px, py);
```

Isso é equivalente às seguintes chamadas translate e escala:

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

O `SKMatrix` estrutura define nove propriedades de leitura/gravação do tipo `float` correspondente a nove células da matriz de transformação:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` também define uma propriedade chamada [ `Values` ](xref:SkiaSharp.SKMatrix.Values) do tipo `float[]`. Essa propriedade pode ser usada para definir ou obter os valores de uma só vez na ordem de nove `ScaleX`, `SkewX`, `TransX`, `SkewY`, `ScaleY`, `TransY`, `Persp0`, `Persp1`, e `Persp2`.

O `Persp0`, `Persp1`, e `Persp2` células são discutidas no artigo [ **transformações não afins**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Se essas células têm seus valores padrão de 0, 0 e 1, a transformação é multiplicada por um ponto de coordenada como este:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Essa é a transformação afim bidimensional completa. A transformação afim preserva as linhas paralelas, o que significa que um retângulo nunca é transformado em algo diferente de um paralelogramo.

O `SKMatrix` estrutura define vários métodos estáticos para criar `SKMatrix` valores. Elas retornam `SKMatrix` valores:

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single)) com um ponto dinâmico
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single)) para um ângulo em radianos
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single)) para um ângulo em radianos, com um ponto dinâmico
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single)) com um ponto dinâmico
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix` também define vários métodos estáticos que concatenar duas matrizes, que significa multiplicá-los. Esses métodos são nomeados [ `Concat` ](xref:SkiaSharp.SKMatrix.Concat*), [ `PostConcat` ](xref:SkiaSharp.SKMatrix.PostConcat*), e [ `PreConcat` ](xref:SkiaSharp.SKMatrix.PreConcat*), e há duas versões de cada um. Esses métodos não têm nenhum valor de retorno; em vez disso, eles fazem referência existente `SKMatrix` valores por meio de `ref` argumentos. No exemplo a seguir `A`, `B`, e `R` (para "resultado") são todas `SKMatrix` valores.

Os dois `Concat` métodos são chamados como este:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Realizam a multiplicação a seguir:

`R = B × A`

Os outros métodos têm apenas dois parâmetros. O primeiro parâmetro é modificado e após o retorno da chamada de método, contém o produto de duas matrizes. Os dois `PostConcat` métodos são chamados como este:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Essas chamadas de executam a seguinte operação:

`A = A × B`

Os dois `PreConcat` métodos são semelhantes:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Essas chamadas de executam a seguinte operação:

`A = B × A`

As versões desses métodos com todos os `ref` argumentos são um pouco mais eficientes para chamar as implementações subjacentes, mas ele pode ser confuso para alguém leem seu código e supondo que tudo que for um `ref` argumento for modificado por o método. Além disso, muitas vezes é conveniente passar um argumento que é um resultado de uma da `Make` métodos, por exemplo:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Isso cria a matriz a seguir:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Essa é a transformação de escala multiplicada pela transformação de conversão. Nesse caso específico, o `SKMatrix` estrutura fornece um atalho com um método denominado [ `SetScaleTranslate` ](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Esse é um nas vezes quando é seguro usar o `SKMatrix` construtor. O `SetScaleTranslate` método define todas as nove células da matriz. Também é seguro usar o `SKMatrix` construtor com estático `Rotate` e `RotateDegrees` métodos:

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Esses métodos fazem *não* concatenar uma transformação a uma transformação existente. Os métodos de definir todas as células da matriz. Eles são funcionalmente idênticos do `MakeRotation` e `MakeRotationDegrees` métodos, exceto que eles não instancie a `SKMatrix` valor.

Suponha que você tenha um `SKPath` objeto que você deseja exibir, mas você prefere que ele tem uma orientação um pouco diferente, ou um ponto central diferente. Você pode modificar todas as coordenadas desse caminho chamando o [ `Transform` ](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) método `SKPath` com um `SKMatrix` argumento. O **transformar caminho** página demonstra como fazer isso. O [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) referências de classe a `HendecagramPath` objeto em um campo, mas usa seu construtor para aplicar uma transformação para o caminho:

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

O `HendecagramPath` objeto tem um centro em (0, 0) e 11 pontos da estrela estender para fora de que a Central de pelas unidades de 100 em todas as direções. Isso significa que o caminho tem coordenadas positivas e negativas. O **transformar caminho** página prefere trabalhar com uma estrela três vezes como grandes e com todas as coordenadas positivas. Além disso, ele não quer um ponto da estrela para apontar diretamente para cima. Ele quer em vez disso, para um ponto da estrela para apontar diretamente para baixo. (Como a estrela tem 11 pontos, ele não é possível ter ambos.) Isso requer girando a estrela em 360 graus dividido por 22.

O construtor cria um `SKMatrix` objeto de três transformações separados usando o `PostConcat` método com o padrão a seguir, onde A, B e C são instâncias de `SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Isso é uma série de multiplicações sucessivas, portanto, o resultado é da seguinte maneira:

`A × B × C`

Multiplicações consecutivas ajudam a compreender o que cada transformação faz. A transformação de escala aumenta o tamanho das coordenadas de caminho por um fator igual a 3, portanto, as coordenadas de intervalo de –300 para 300. A transformação de rotação gira a estrela em torno de sua origem. A transformação de conversão, em seguida, ele muda 300 pixels botão direito do mouse e para baixo, para que todos os as coordenadas que se tornam positivas.

Há outras sequências que produzem a mesma matriz. Aqui está outro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Isso gira o caminho ao redor de seu centro primeiro e, em seguida, converte-100 pixels à direita e para baixo de forma que todos as coordenadas são positivas. A estrela é, em seguida, o tamanho aumentada em relação ao seu canto superior esquerdo de novo, que é o ponto (0, 0).

O `PaintSurface` manipulador simplesmente pode renderizar esse caminho:

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

Ele é exibido no canto superior esquerdo da tela:

[![](matrix-images/pathtransform-small.png "Tripla captura de tela da página do caminho transformar")](matrix-images/pathtransform-large.png#lightbox "tripla captura de tela da página de transformação de caminho")

O construtor deste programa aplica-se a matriz para o caminho com a chamada a seguir:

```csharp
transformedPath.Transform(matrix);
```

O caminho faz *não* manter essa matriz como uma propriedade. Em vez disso, ele se aplica a transformação para todas as coordenadas do caminho. Se `Transform` é chamado novamente, a transformação é aplicada novamente e a única maneira que você pode voltar é por meio da aplicação de outra matriz que desfaz a transformação. Felizmente, o `SKMatrix` estrutura define uma [ `TryInvert` ](xref:SkiaSharp.SKMatrix.TryInvert*) método que obtém a matriz que reverte uma determinada matriz:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

O método é chamado `TryInverse` porque nem todas as matrizes são invertível, mas uma matriz não pode ser invertida não é provavelmente a ser usado para uma transformação de gráficos.

Você também pode aplicar uma transformação de matriz para um `SKPoint` valor, uma matriz de pontos, um `SKRect`, ou até mesmo um único número dentro do seu programa. O `SKMatrix` estrutura oferece suporte a essas operações com uma coleção de métodos que começam com a palavra `Map`, como estes:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Se você usar o último método que, tenha em mente que o `SKRect` estrutura não é capaz de representar um retângulo girado. O método só faz sentido um `SKMatrix` valor que representa a translação e dimensionamento.

## <a name="interactive-experimentation"></a>Experimentação interativa

É uma maneira de ter uma ideia de transformação afim interativamente movendo três cantos de um bitmap na tela e vendo quais transformação de resultados. Essa é a ideia por trás do **afim Mostrar** página. Esta página requer duas outras classes que também são usados em outras demonstrações:

O [ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) classe exibe um círculo translúcido que pode ser arrastado pela tela. `TouchPoint` requer que um `SKCanvasView` ou um elemento que é pai de um `SKCanvasView` têm a [ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) anexado. Defina a propriedade `Capture` como `true`. No `TouchAction` manipulador de eventos, o programa deve chamar o `ProcessTouchEvent` método na `TouchPoint` para cada `TouchPoint` instância. O método retorna `true` se o evento de toque resultou em mover o ponto de toque. Além disso, o `PaintSurface` manipulador deve chamar o `Paint` método em cada `TouchPoint` instância, passando a ele o `SKCanvas` objeto.

`TouchPoint` Demonstra um comum que um visual de SkiaSharp pode ser encapsulado em uma classe separada de forma. A classe pode definir propriedades para especificar as características do visual, e um método chamado `Paint` com um `SKCanvas` argumento pode processá-lo.

O `Center` propriedade de `TouchPoint` indica o local do objeto. Essa propriedade pode ser definida para inicializar o local; a propriedade é alterada quando o usuário arrasta o círculo ao redor da tela.

O **mostrar a página de matriz afim** também requer o [ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Essa classe exibe as células de uma `SKMatrix` objeto. Ele tem dois métodos públicos: `Measure` para obter as dimensões da matriz renderizada, e `Paint` para exibi-lo. A classe contém um `MatrixPaint` propriedade do tipo `SKPaint` que pode ser substituído para uma cor ou tamanho de fonte diferente.

O [ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) arquivo XAML instancia o `SKCanvasView` e anexa um `TouchEffect`. O [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) arquivo code-behind cria três `TouchPoint` objetos e, em seguida, define-as para posições correspondentes a três cantos de um bitmap que ele carrega de inserida recurso:

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

Uma matriz afim exclusivamente é definida por três pontos. Os três `TouchPoint` objetos correspondem aos cantos superior esquerdo, superior direito e inferior esquerdo do bitmap. Como uma matriz afim só é capaz de transformar um retângulo em um paralelogramo, o quarto ponto foi inferido por outros três. O construtor é concluído com uma chamada para `ComputeMatrix`, que calcula as células de uma `SKMatrix` objeto a partir desses três pontos.

O `TouchAction` chamadas do manipulador de `ProcessTouchEvent` método de cada `TouchPoint`. O `scale` valor convertido de coordenadas de xamarin. Forms em pixels:

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

Se houver `TouchPoint` foi movido, em seguida, chama o método `ComputeMatrix` novamente e invalida a superfície.

O `ComputeMatrix` método determina a matriz implicada por esses três pontos. A matriz chamada `A` transforma um retângulo quadrado de um pixel em um paralelogramo com base em três pontos, enquanto a transformação de escala chamada `S` dimensiona o bitmap a um retângulo quadrado de um pixel. É a matriz de composição `S` × `A`:

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

Por fim, o `PaintSurface` método renderiza o bitmap com base nessa matriz, exibe a matriz de na parte inferior da tela e renderiza os pontos de toque nos três cantos do bitmap:

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

A tela do iOS abaixo mostra o bitmap quando a página é carregada pela primeira vez, enquanto as outras duas telas mostrá-la após alguma manipulação:

[![](matrix-images/showaffinematrix-small.png "Tripla captura de tela da página mostram afim")](matrix-images/showaffinematrix-large.png#lightbox "tripla captura de tela da página mostram afim")

Embora pareça como se os pontos de toque arraste os cantos do bitmap, que é apenas uma ilusão. A matriz calculada a partir de pontos de toque transforma o bitmap para que os cantos coincidam com os pontos de toque.

Ele é mais natural para que os usuários mover, redimensionar e girar os bitmaps não arrastando os cantos, mas usando um ou dois dedos diretamente no objeto de arrastar, aperto em giram. Isso é abordado no próximo artigo [ **manipulação de toque**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

## <a name="the-reason-for-the-3-by-3-matrix"></a>O motivo para a matriz de 3 por 3

Ele pode ser esperado que um sistema de gráficos bidimensionais exigiria apenas uma matriz de transformação de 2 a 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Isso funciona para escala, rotação e até mesmo a inclinação, mas não é capaz de mais básica de transformações, que é a tradução.

O problema é que a matriz de 2 a 2 representa uma *linear* transformar em duas dimensões. Uma transformação linear preserva algumas operações aritméticas básicas, mas uma das implicações de é que uma transformação linear nunca altera o ponto (0, 0). Uma transformação linear impossibilita a tradução.

Em três dimensões, uma matriz de transformação linear tem esta aparência:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

A célula rotulada `SkewXY` significa que o valor inclina a coordenada X com base nos valores de Y; a célula `SkewXZ` significa que o valor inclina a coordenada X com base nos valores de Z; e valores de distorção da mesma forma para os outros `Skew` células.

É possível restringir essa matriz de transformação 3D a um plano bidimensional, definindo `SkewZX` e `SkewZY` como 0, e `ScaleZ` como 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Se os gráficos bidimensionais são desenhados inteiramente no plano no espaço 3D, em que Z é igual a 1, a multiplicação de transformação terá esta aparência:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Tudo o que permanece no plano bidimensional em que Z é igual a 1, mas o `SkewXZ` e `SkewYZ` células efetivamente se tornam fatores de conversão bidimensional.

Isso é como uma transformação linear tridimensional serve como uma transformação não-linear bidimensional. (Por analogia, transformações em gráficos 3D são baseadas em uma matriz 4 por 4).

O `SKMatrix` estrutura em SkiaSharp define propriedades para a terceira linha:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Valores diferentes de zero da `Persp0` e `Persp1` resultar em transformações que mover objetos, desativar o plano bidimensional em que Z é igual a 1. O que acontece quando esses objetos são movidos de volta para esse plano é abordado no artigo sobre [ **transformações não afins**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
