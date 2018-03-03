---
title: "Transformações não afins"
description: "Criar ponto de Vista e efeitos de diminuição com a terceira coluna da matriz de transformação"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: 3fda8524b824042aa4aba07853da2801baf47027
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="non-affine-transforms"></a>Transformações não afins

_Criar ponto de Vista e efeitos de diminuição com a terceira coluna da matriz de transformação_

Tradução, dimensionamento, rotação e inclinação são classificados como *afim* transformações. Transformações afins preservam linhas paralelas. Se duas linhas paralelas antes da transformação, eles permanecem paralelos após a transformação. Retângulos sempre são transformados em parallelograms.

No entanto, SkiaSharp também é capaz de transformações não afim, que tem a capacidade de transformar um retângulo em qualquer quadrilátero convexo:

![](non-affine-images/nonaffinetransformexample.png "Um bitmap transformado em um quadrilátero convexo")

Um quadrilátero convexo é uma figura de quatro lados com interior ângulos sempre menor que os lados que não cruzem e 180 graus.

Não afim transforma resultados quando a terceira linha da matriz de transformação é definida como valores diferente de 0, 0 e 1. Completa `SKMatrix` multiplicação é:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

As fórmulas de transformação resultantes são:

x' = ScaleX·x + SkewX·y + TransX

y' = SkewY·x + ScaleY·y + TransY

z` = Persp0·x + Persp1·y + Persp2

A regra fundamental do uso de uma matriz de 3 por 3 para transformações bidimensionais é que tudo permanece no plano onde Z é igual a 1. A menos que `Persp0` e `Persp1` são 0, e `Persp2` é igual a 1, a transformação moveu as coordenadas Z desse plano.

Para restaurar esse uma transformação bidimensional, as coordenadas devem ser movidas de volta para esse plano. Outra etapa é necessária. O x', y', e z 'valores devem ser divididos por z':

x" = x' / z'

y"= y' / z'

z" = z' / z' = 1

Eles são conhecidos como *coordenadas homogêneas* e foram desenvolvidos por matemático agosto Ferdinand Möbius, muito mais conhecidos para seu topológico estranho, faixa de Möbius.

Se z' é 0, os resultados de divisão em coordenadas de infinitos. Na verdade, uma das motivações do Möbius para o desenvolvimento de coordenadas homogêneas foi a capacidade de representar valores infinitos com números finitos.

No entanto, quando a exibição de gráficos, você deseja evitar o processamento de algo com coordenadas transform em valores de infinitos. Essas coordenadas não serão renderizadas. Tudo perto essas coordenadas será muito grande e provavelmente não visualmente coerente.

Nessa equação, você não deseja que o valor de z' se torna zero:

z` = Persp0·x + Persp1·y + Persp2

O `Persp2` célula pode ser zero ou não zero. Se `Persp2` for zero, z' é zero para o ponto (0, 0), e que normalmente não é desejável porque esse ponto é muito comum em gráficos bidimensionais. Se `Persp2` é igual a zero, não há nenhuma perda de generalidade se `Persp2` é fixo em 1. Por exemplo, se você determinar que `Persp2` deve ser 5, em seguida, você pode simplesmente dividir todas as células na matriz por 5, que torna `Persp2` igual a 1 e o resultado será o mesmo.

Por esses motivos, `Persp2` geralmente é fixo em 1, que é o mesmo valor na matriz de identidade.

Em geral, `Persp0` e `Persp1` são números de pequenas. Por exemplo, suponha que você começar com uma matriz de identidade, mas conjunto `Persp0` como 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

As fórmulas de transformação são:

x' = x / (0.01·x + 1)

y' = y / (0.01·x + 1)

Agora, use essa transformação para renderizar uma caixa quadrada 100 pixels posicionada na origem. Aqui está como quatro cantos são transformados:

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Quando x é 100 e, em seguida, z' Denominador é 2, portanto as coordenadas x e y são efetivamente reduzido à metade. O lado direito da caixa se torna menor do que o lado esquerdo:

![](non-affine-images/nonaffinetransform.png "Uma caixa sujeitada a uma transformação não é afim")

O `Persp` parte desses nomes de célula refere-se a "perspectiva" porque o desaparecendo no infinito sugere que a caixa está agora inclinada à direita do visualizador.

O **perspectiva de teste** página permite fazer experiências com valores de `Persp0` e `Pers1` para ter uma ideia de como eles funcionam. Valores razoáveis dessas células da matriz são tão pequenos que o `Slider` na plataforma Universal do Windows não pode lidar adequadamente com elas. Para acomodar o problema UWP, os dois `Slider` elementos o [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) precisam ser inicializados para o intervalo de -1 para 1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Manipuladores de eventos para os controles deslizantes no [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) arquivo code-behind divida os valores por 100 para que eles variam entre –0.01 e 0,01. Além disso, o construtor carrega em um bitmap:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

O `PaintSurface` manipulador calcula um `SKMatrix` valor denominado `perspectiveMatrix` com base nos valores desses dois controles deslizantes divididos por 100. Isso é combinado com duas transformações que colocar o centro dessa transformação no centro do bitmap de conversão:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Aqui estão algumas imagens de amostra:

[![](non-affine-images/testperspective-small.png "Captura de tela da página da perspectiva de teste tripla")](non-affine-images/testperspective-large.png "tripla captura de tela da página da perspectiva de teste")

Como fazer experiências com os controles deslizantes, verá que valores além 0.0066 ou abaixo –0.0066 fazem com que a imagem a ser interrompido e incoerentes, repentinamente. O bitmap que está sendo transformado é quadrado de 300 pixels. Ele é transformado em relação a seu centro, para que as coordenadas do bitmap no intervalo de –150 até 150. Lembre-se de que o valor de z' é:

z' = Persp0·x + Persp1·y + 1

Se `Persp0` ou `Persp1` é maior do que 0.0066 ou abaixo de –0.0066, em seguida, há sempre alguma coordenada do bitmap que resulta em um z' valor igual a zero. Que faz com que divisão por zero, e o processamento se torna uma confusão. Ao usar transformações não afins, você deseja evitar o processamento de qualquer coisa com coordenadas que causam a divisão por zero.

Em geral, você não definir `Persp0` e `Persp1` em isolamento. Também geralmente é necessário definir outras células na matriz para alcançar certos tipos de transformações não afim.

Um essa transformação não é afim é um *diminuição da transformação*. Esse tipo de transformação não é afim retém as dimensões gerais de um retângulo, mas se estreita um lado:

![](non-affine-images/tapertransform.png "Uma caixa sujeitada a uma diminuição da transformação")

O [ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe executa um cálculo generalizado de uma transformação não é afim baseado nestes parâmetros:

- o tamanho retangular da imagem que está sendo transformado,
- uma enumeração que indica o lado do retângulo que se estreita,
- outra enumeração que indica como ele se estreita, e
- a extensão do afunilamento.

Aqui está o código:

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

Essa classe é usada no **diminuição da transformação** página. O arquivo XAML instancia dois `Picker` elementos para selecionar os valores de enumeração e um `Slider` para escolher a diminuição da fração. O [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) manipulador combina a diminuição da transformação com dois traduzir transformações para fazer a transformação em relação ao canto superior esquerdo do bitmap:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedIndex;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedIndex;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

Estes são alguns exemplos:

[![](non-affine-images/tapertransform-small.png "Tripla captura de tela da página diminuição da transformação")](non-affine-images/tapertransform-large.png "tripla captura de tela da página diminuição da transformação")

Outro tipo de transformações não afim generalizadas é rotação 3D, que é demonstrada no próximo artigo, [rotações 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md).

A transformação não é afim pode transformar um retângulo em qualquer quadrilátero convexo. Isso é demonstrado pelo **matriz afim de não mostrar** página. É muito semelhante do **afim de mostrar** página do [transformações de matriz](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md) artigo exceto que ela tem uma quarta `TouchPoint` objeto para manipular o quarto canto do bitmap:

[![](non-affine-images/shownonaffinematrix-small.png "Tripla captura de tela da página de matriz não afim Mostrar")](non-affine-images/shownonaffinematrix-large.png "tripla captura de tela da página matriz afim de não mostrar")

Desde que você não tente fazer um ângulo interior de um dos cantos do bitmap maior 180 graus, ou fazer com que dois lados cruzada entre si, o programa calcula com êxito usando esse método de transformação de [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe:

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
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

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

Para facilitar o cálculo, esse método obtém a transformação total como um produto de três transformações separadas, são indicado aqui com setas que mostram como essas transformações modificar quatro cantos do bitmap:

(0, 0) → (0, 0) → (0, 0) → (x 0, y0) (inferior)

(0, H) → (0, 1) → (0, 1) → (x1, y1) (inferior esquerdo)

(0 W) → (1, 0) → (1, 0) → (x2, y2) (superior direito)

(W, H) → (1, 1) → (a, b) → (x3 y3) (inferior direito)

As coordenadas finais à direita são os quatro pontos associados com os pontos de toque de quatro. Essas são as coordenadas finais dos cantos do bitmap.

L e a representam a largura e altura do bitmap. A primeira transformação (`S`) simplesmente dimensiona o bitmap a um quadrado de 1 pixel. A segunda transformação é a transformação não é afim `N`, e o terceiro é a transformação afim `A`. Essa transformação afim é baseada nos três pontos, portanto é exatamente como anteriormente afim [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) método e não envolve a quarta linha com o (a, b) ponto.

O `a` e `b` são calculadas para que a transformação de terceira é afim. O código obtém o inverso da transformação afim e, em seguida, usará para mapear o canto inferior direito. Este é o ponto (a, b).

Outro uso de transformações não afim é simular a elementos gráficos tridimensionais. No próximo artigo, [rotações 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md) consulte como girar um gráfico bidimensional em espaço 3D.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
