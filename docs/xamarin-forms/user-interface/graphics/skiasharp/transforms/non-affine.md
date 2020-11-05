---
title: Transformações não afins
description: Este artigo explica como criar efeitos de perspectiva e cônico com a terceira coluna da matriz de transformação e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16085f8bb3af57fdd0c00c7c111a91df1dd273f7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374544"
---
# <a name="non-affine-transforms"></a>Transformações não afins

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Criar efeitos de perspectiva e cônico com a terceira coluna da matriz de transformação_

A tradução, o dimensionamento, a rotação e a distorção são classificados como transformações *afim* . As transformações afim preservam as linhas paralelas. Se duas linhas forem paralelas antes da transformação, elas permanecerão paralelas após a transformação. Os retângulos sempre são transformados em paralelogramo.

No entanto, o SkiaSharp também é capaz de transformações não afim, que têm a capacidade de transformar um retângulo em qualquer diamante convexa:

![Um bitmap transformado em um convexa diamante](non-affine-images/nonaffinetransformexample.png)

Um convexa diamante é uma figura de quatro lados com ângulos interiores sempre menor que 180 graus e lados que não se cruzam.

As transformações não afim resultam quando a terceira linha da matriz de transformação é definida com valores diferentes de 0, 0 e 1. A `SKMatrix` multiplicidade completa é:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

As fórmulas de transformação resultantes são:

x ' = ScaleX · x + SkewX · y + TransX

y ' = distorção · x + ScaleY · y + trans

z ' = Persp0 · x + Persp1 · y + Persp2

A regra fundamental de usar uma matriz 3-by-3 para transformações bidimensionais é que tudo permanece no plano em que Z é igual a 1. A `Persp0` menos `Persp1` que e seja 0 e seja `Persp2` igual a 1, a transformação moveu as coordenadas Z para fora desse plano.

Para restaurar isso para uma transformação bidimensional, as coordenadas devem ser removidas para esse plano. Outra etapa é necessária. Os valores x ', y ' e z devem ser divididos por z ':

x "= x"/z "

y "= y"/z "

z "= z"/z "= 1

Elas são conhecidas como *coordenadas homogêneas* e foram desenvolvidas por matemático de agosto de Ferdinand Möbius, muito mais conhecidas por sua topológica singularidade, a faixa Möbius.

Se z ' for 0, a divisão resultará em coordenadas infinitas. Na verdade, uma das motivações Möbius's para o desenvolvimento de coordenadas homogêneas era a capacidade de representar valores infinitos com números finitos.

Ao exibir elementos gráficos, no entanto, você deseja evitar o processamento de algo com coordenadas que são transformadas em valores infinitos. Essas coordenadas não serão renderizadas. Tudo na vizinhança dessas coordenadas será muito grande e provavelmente não é visualmente coerente.

Nesta equação, você não deseja que o valor de z ' se torne zero:

z ' = Persp0 · x + Persp1 · y + Persp2

Consequentemente, esses valores têm algumas restrições práticas: 

A `Persp2` célula pode ser zero ou não zero. Se `Persp2` for zero, z ' será zero para o ponto (0, 0), e isso geralmente não é desejável porque esse ponto é muito comum em gráficos bidimensionais. Se `Persp2` não for igual a zero, não haverá perda de generalidade se `Persp2` for corrigido em 1. Por exemplo, se você determinar que `Persp2` deveria ser 5, poderá simplesmente dividir todas as células na matriz em 5, o que faz com que seja `Persp2` igual a 1 e o resultado será o mesmo.

Por esses motivos, `Persp2` geralmente é corrigido em 1, que é o mesmo valor na matriz de identidade.

Geralmente, `Persp0` e `Persp1` são números pequenos. Por exemplo, suponha que você comece com uma matriz de identidade, mas defina `Persp0` como 0, 1:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

As fórmulas de transformação são:

x ' = x/(0,01 · x + 1)

y ' = y/(0,01 · x + 1)

Agora, use essa transformação para renderizar uma caixa quadrada de 100 pixels posicionada na origem. Veja como os quatro cantos são transformados:

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Quando x é 100, o denominador z é 2, portanto, as coordenadas x e y são efetivamente divididas em metade. O lado direito da caixa torna-se menor do que o lado esquerdo:

![Um box que está sujeito a uma transformação não afim](non-affine-images/nonaffinetransform.png)

A `Persp` parte desses nomes de célula se refere a "Perspective" porque o foreshortening sugere que a caixa agora está inclinada no lado direito além do visualizador.

A página **perspectiva do teste** permite que você experimente os valores de `Persp0` e `Pers1` para ter uma ideia de como eles funcionam. Os valores razoáveis dessas células de matriz são tão pequenos que o `Slider` no plataforma universal do Windows não podem tratá-los corretamente. Para acomodar o problema UWP, os dois `Slider` elementos no [**TestPerspective. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) precisam ser inicializados para variar de – 1 a 1:

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

Os manipuladores de eventos para os controles deslizantes no [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) arquivo code-behind dividem os valores por 100 para que eles alcancem entre – 0, 1 e 0, 1. Além disso, o construtor é carregado em um bitmap:

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
        {
            bitmap = SKBitmap.Decode(stream);
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

O `PaintSurface` manipulador calcula um `SKMatrix` valor nomeado `perspectiveMatrix` com base nos valores desses dois controles deslizantes divididos por 100. Isso é combinado com duas transformações de conversão que colocam o centro dessa transformação no centro do bitmap:

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

Aqui estão algumas imagens de exemplo:

[![Captura de tela tripla da página perspectiva do teste](non-affine-images/testperspective-small.png)](non-affine-images/testperspective-large.png#lightbox "Captura de tela tripla da página perspectiva do teste")

Ao experimentar os controles deslizantes, você descobrirá que os valores além de 0, 66 ou abaixo – 0, 66 fazem com que a imagem seja repentinamente interrompida e incoerente. O bitmap que está sendo transformado é quadrado de 300 pixels. Ele é transformado em relação ao seu centro, portanto, as coordenadas do intervalo de bitmap de – 150 a 150. Lembre-se de que o valor de z ' é:

z ' = Persp0 · x + Persp1 · y + 1

Se `Persp0` ou `Persp1` for maior que 0, 66 ou inferior – 0, 66, sempre haverá alguma coordenada do bitmap que resulta em um valor de z ' igual a zero. Isso causa a divisão por zero e a renderização se torna uma bagunça. Ao usar transformações não afim, você deseja evitar a renderização de qualquer coisa com coordenadas que causam divisão por zero.

Em geral, você não estará configurando `Persp0` e `Persp1` isoladamente. Também é necessário definir outras células na matriz para atingir determinados tipos de transformações não afim.

Uma transformação sem afinidade é uma *transformação cônico*. Esse tipo de transformação não afim retém as dimensões gerais de um retângulo, mas as fitas são um lado:

![Uma caixa sujeita a uma transformação cônico](non-affine-images/tapertransform.png)

A [`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe executa um cálculo generalizado de uma transformação não afim com base nesses parâmetros:

- o tamanho retangular da imagem que está sendo transformada,
- uma enumeração que indica o lado do retângulo que as fitas,
- outra enumeração que indica como as fitas de ti e
- a extensão da cônico.

O código é o seguinte:

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

Essa classe é usada na página **transformação cônico** . O arquivo XAML instancia dois `Picker` elementos para selecionar os valores de enumeração e um `Slider` para escolher a fração cônico. O [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) manipulador combina a transformação cônico com duas transformações de conversão para fazer a transformação em relação ao canto superior esquerdo do bitmap:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
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

[![Captura de tela tripla da página de transformação cônico](non-affine-images/tapertransform-small.png)](non-affine-images/tapertransform-large.png#lightbox "Captura de tela tripla da página de transformação cônico")

Outro tipo de transformações não afim generalizadas é a rotação 3D, que é demonstrada no próximo artigo, [**rotações 3D**](3d-rotation.md).

A transformação não afim pode transformar um retângulo em qualquer diamante convexa. Isso é demonstrado pela página **Mostrar matriz não-afim** . É muito semelhante à página **Mostrar matriz de afinidade** do artigo [**transformações de matriz**](matrix.md) , exceto que ele tem um quarto `TouchPoint` objeto para manipular o quarto canto do bitmap:

[![Captura de tela tripla da página mostrar matriz não-afim](non-affine-images/shownonaffinematrix-small.png)](non-affine-images/shownonaffinematrix-large.png#lightbox "Captura de tela tripla da página mostrar matriz não-afim")

Desde que você não tente criar um ângulo interior de um dos cantos do bitmap com mais de 180 graus ou fazer com que dois lados se cruzem, o programa calculará com êxito a transformação usando esse método da [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe:

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

Para facilitar o cálculo, esse método obtém a transformação total como um produto de três transformações separadas, que são simbolizadas aqui com setas mostrando como essas transformações modificam os quatro cantos do bitmap:

(0,0) → (0,0) → (0, 0) → (x0, y0) (superior esquerda)

(0, H) → (0, 1) → (0, 1) → (x1, y1) (inferior esquerda)

(W,0) → (1, 0) → (1, 0) → (x2, Y2) (superior direito)

(W, H) → (1, 1) → (a, b) → (X3, y3) (canto inferior direito)

As coordenadas finais à direita são os quatro pontos associados aos quatro pontos de toque. Essas são as coordenadas finais dos cantos do bitmap.

W e H representam a largura e a altura do bitmap. A primeira transformação `S` simplesmente dimensiona o bitmap para um quadrado de 1 pixel. A segunda transformação é a transformação não-afim `N` e a terceira é a transformação afim `A` . Essa transformação de afinidade se baseia em três pontos, portanto, é exatamente como o método afim anterior [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) e não envolve a quarta linha com o ponto (a, b).

Os `a` `b` valores e são calculados para que a terceira transformação seja afim. O código obtém o inverso da transformação afim e, em seguida, a usa para mapear o canto inferior direito. Esse é o ponto (a, b).

Outro uso de transformações não afim é a imitação de gráficos tridimensionais. No próximo artigo, [**rotações 3D**](3d-rotation.md) você verá como girar um gráfico bidimensional no espaço 3D.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)