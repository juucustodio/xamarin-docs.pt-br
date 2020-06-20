---
title: Rotações 3D no SkiaSharp
description: Este artigo explica como usar transformações não afim para girar objetos 2D em espaço 3D e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3706139a2c15d01af67203c2bd09b281de80ed52
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140199"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotações 3D no SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use transformações não afim para girar objetos 2D no espaço 3D._

Um aplicativo comum de transformações não afim é simular a rotação de um objeto 2D no espaço 3D:

![](3d-rotation-images/3drotationsexample.png "A text string rotated in 3D space")

Esse trabalho envolve o trabalho com rotações tridimensionais e, em seguida, derivar uma transformação não afim `SKMatrix` que executa essas rotações 3D.

É difícil desenvolver essa `SKMatrix` transformação trabalhando exclusivamente dentro de duas dimensões. O trabalho torna-se muito mais fácil quando essa matriz 3-by-3 é derivada de uma matriz 4 por 4 usada em gráficos 3D. O SkiaSharp inclui a [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) classe para essa finalidade, mas alguns planos de fundo em gráficos 3D são necessários para entender as rotações 3D e a matriz de transformação 4 por 4.

Um sistema de coordenadas tridimensional adiciona um terceiro eixo chamado Z. conceitualmente, o eixo Z está em ângulos retos na tela. Pontos de coordenadas no espaço 3D são indicados com três números: (x, y, z). No sistema de coordenadas 3D usado neste artigo, os valores crescentes de X estão à direita e os valores crescentes de Y ficam inativos, assim como em duas dimensões. O aumento dos valores Z positivos é proveniente da tela. A origem é o canto superior esquerdo, assim como em gráficos 2D. Você pode considerar a tela como um plano XY com o eixo Z em ângulos retos para este plano.

Isso é chamado de sistema de coordenadas à esquerda. Se você apontar o dedo indicador para a esquerda na direção de coordenadas X positivas (à direita) e o dedo médio na direção de aumento de coordenadas Y (para baixo), então, seus pontos deslizantes na direção das coordenadas Z crescentes — estendendo-se da tela.

Em gráficos 3D, as transformações se baseiam em uma matriz de 4 por 4. Aqui está a matriz de identidades de 4 por 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Ao trabalhar com uma matriz 4 por 4, é conveniente identificar as células com seus números de linha e coluna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

No entanto, a `Matrix44` classe SkiaSharp é um pouco diferente. A única maneira de definir ou obter valores de células individuais no `SKMatrix44` é usando o [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indexador. Os índices de linha e coluna são baseados em zero, e não em um, e as linhas e colunas são trocadas. A célula M14 no diagrama acima é acessada usando o indexador `[3, 0]` em um `SKMatrix44` objeto.

Em um sistema gráfico 3D, um ponto 3D (x, y, z) é convertido em uma matriz 1-por-4 para multiplicação pela matriz de transformação 4 por 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Análogo a transformações 2D que ocorrem em três dimensões, as transformações 3D são presumidas em vigor em quatro dimensões. A quarta dimensão é referida como W, e o espaço 3D deve existir no espaço 4D em que as coordenadas W são iguais a 1. As fórmulas de transformação são as seguintes:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

É óbvio que as fórmulas de transformação que as células `M11` , `M22` , `M33` são fatores de dimensionamento nas direções x, y e z, e `M41` , `M42` e `M43` são fatores de tradução nas direções x, y e z.

Para converter essas coordenadas de volta para o espaço 3D em que W é igual a 1, as coordenadas x ', y ' e z ' são todas divididas por W ':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Essa divisão por w ' fornece a perspectiva no espaço 3D. Se w ' for igual a 1, não ocorrerá nenhuma perspectiva.

As rotações no espaço 3D podem ser bastante complexas, mas as rotações mais simples são aquelas em relação aos eixos X, Y e Z. Uma rotação de α de ângulo em torno do eixo X é esta matriz:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Os valores de X permanecem os mesmos quando estão sujeitos a essa transformação. A rotação ao contrário do eixo Y deixa os valores de Y inalterados:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

A rotação em volta do eixo Z é igual a em gráficos 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

A direção da rotação é implícita pela destros do sistema de coordenadas. Esse é um sistema canhoto, portanto, se você apontar o polegar do lado esquerdo para aumentar os valores de um eixo específico — à direita da rotação em volta do eixo X, para a rotação em volta do eixo Y e para a rotação em volta do eixo Z, então a curva dos outros dedos indica a direção da rotação para ângulos positivos.

`SKMatrix44`tem métodos e estática generalizados [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) que permitem especificar o eixo ao qual a rotação ocorre:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Para a rotação em volta do eixo X, defina os três primeiros argumentos como 1, 0, 0. Para rotação em volta do eixo Y, defina-os como 0, 1, 0 e, para rotação em volta do eixo Z, defina-os como 0, 0, 1.

A quarta coluna do 4-by-4 é para a perspectiva. O `SKMatrix44` não tem métodos para criar transformações em perspectiva, mas você mesmo pode criar um usando o código a seguir:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

O motivo do nome do argumento `depth` será evidente em breve. Esse código cria a matriz:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

As fórmulas de transformação resultam no seguinte cálculo de w ':

`w' = –z / depth + 1`

Isso serve para reduzir as coordenadas X e Y quando os valores de Z forem menores que zero (conceitualmente atrás do plano XY) e para aumentar as coordenadas X e Y para valores positivos de Z. Quando a coordenada Z é igual a `depth` , w ' é zero e as coordenadas se tornam infinitas. Os sistemas gráficos tridimensionais são criados em uma metáfora de câmera, e o `depth` valor aqui representa a distância da câmera da origem do sistema de coordenadas. Se um objeto gráfico tiver uma coordenada Z que seja `depth` unidades da origem, será conceitualmente tocar a lente da câmera e se tornar infinitamente grande.

Tenha em mente que você provavelmente usará esse `perspectiveMatrix` valor em combinação com matrizes de rotação. Se um objeto gráfico que está sendo girado tiver coordenadas X ou Y maiores que `depth` , a rotação desse objeto no espaço 3D provavelmente envolverá coordenadas Z maiores que `depth` . Isso deve ser evitado! Ao criar `perspectiveMatrix` , você deseja definir `depth` com um valor suficientemente grande para todas as coordenadas no objeto gráfico, independentemente de como ele é girado. Isso garante que nunca haja nenhuma divisão por zero.

A combinação de rotações 3D e perspectiva requer a multiplicação de matrizes de 4 a 4 juntas. Para essa finalidade, `SKMatrix44` define métodos de concatenação. Se `A` e `B` forem `SKMatrix44` objetos, o código a seguir definirá um igual a × B:

```csharp
A.PostConcat(B);
```

Quando uma matriz de transformação de 4 por 4 é usada em um sistema de gráficos 2D, ela é aplicada a objetos 2D. Esses objetos são simples e supostamente têm coordenadas Z de zero. A multiplicação de transformação é um pouco mais simples do que a transformação mostrada anteriormente:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Esse valor de 0 para z resulta em fórmulas de transformação que não envolvem nenhuma célula na terceira linha da matriz:

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z ' = M13 · x + M23 · y + M43

w ' = M14 · x + M24 · y + M44

Além disso, a coordenada z também é irrelevante aqui. Quando um objeto 3D é exibido em um sistema de gráficos 2D, ele é recolhido para um objeto bidimensional, ignorando os valores da coordenada Z. As fórmulas de transformação são, na verdade, apenas estas duas:

`x" = x' / w'`

`y" = y' / w'`

Isso significa que a terceira linha *e* a terceira coluna da matriz 4-by-4 podem ser ignoradas.

Mas se for assim, por que a matriz 4 por 4 é igualmente necessária em primeiro lugar?

Embora a terceira linha e a terceira coluna de 4-by-4 sejam irrelevantes para transformações bidimensionais, a terceira linha e *coluna* desempenham uma função antes disso, quando vários `SKMatrix44` valores são multiplicados juntos. Por exemplo, suponha que você multiplique a rotação em todo o eixo Y com a transformação perspectiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

No produto, a célula `M14` agora contém um valor de perspectiva. Se você quiser aplicar essa matriz a objetos 2D, a terceira linha e coluna serão eliminadas para convertê-la em uma matriz 3-por-3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Agora ele pode ser usado para transformar um ponto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

As fórmulas de transformação são:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Agora, divida tudo em z ':

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Quando objetos 2D são girados com um ângulo positivo em torno do eixo Y, os valores X positivos recedem ao plano de fundo, enquanto valores X negativos chegam ao primeiro plano. Os valores X parecem avançar para o eixo Y (que é regido pelo valor do cosseno), já que as coordenadas mais distantes do eixo Y se tornam menores ou maiores à medida que se movem mais do visualizador ou mais perto do visualizador.

Ao usar `SKMatrix44` o, execute todas as operações de rotação e perspectiva 3D multiplicando vários `SKMatrix44` valores. Em seguida, você pode extrair uma matriz bidimensional 3-by-3 da matriz 4 por 4 usando a [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) propriedade da `SKMatrix44` classe. Essa propriedade retorna um `SKMatrix` valor familiar.

A página **3D de rotação** permite que você experimente a rotação 3D. O arquivo [**Rotation3DPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) instancia quatro controles deslizantes para definir a rotação em volta dos eixos X, Y e Z e para definir um valor de profundidade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Observe que o `depthSlider` é inicializado com um `Minimum` valor de 250. Isso significa que o objeto 2D que está sendo girado aqui tem coordenadas X e Y restritas a um círculo definido por um raio de 250 pixels em volta da origem. Qualquer rotação desse objeto no espaço 3D sempre resultará em valores de coordenadas menores que 250.

As cargas de arquivo [**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) code-behind em um bitmap que é de 300 pixels quadrados:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Se a transformação 3D for centralizada nesse bitmap, as coordenadas X e Y variam entre – 150 e 150, enquanto os cantos são 212 pixels do centro, de modo que tudo esteja dentro do raio de 250 pixels.

O `PaintSurface` manipulador cria `SKMatrix44` objetos com base nos controles deslizantes e os multiplica usando `PostConcat` . O `SKMatrix` valor extraído do `SKMatrix44` objeto final está circundado por transformações de conversão para centralizar a rotação no centro da tela:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

Ao experimentar o quarto controle deslizante, você observará que as diferentes configurações de profundidade não movem o objeto mais para fora do visualizador, mas, em vez disso, alteram a extensão do efeito de perspectiva:

[![](3d-rotation-images/rotation3d-small.png "Triple screenshot of the Rotation 3D page")](3d-rotation-images/rotation3d-large.png#lightbox "Triple screenshot of the Rotation 3D page")

O **3D de rotação animada** também usa `SKMatrix44` para animar uma cadeia de texto em espaço 3D. O `textPaint` objeto definido como um campo é usado no construtor para determinar os limites do texto:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

A `OnAppearing` substituição define três Xamarin.Forms `Animation` objetos para animar `xRotationDegrees` os `yRotationDegrees` campos, e `zRotationDegrees` em taxas diferentes. Observe que os períodos dessas animações são definidos para números primos (5 segundos, 7 segundos e 11 segundos) para que a combinação geral se repita apenas a cada 385 segundos ou mais de 10 minutos:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

Como no programa anterior, o `PaintCanvas` manipulador cria `SKMatrix44` valores para rotação e perspectiva e os multiplica:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Essa rotação 3D é cercada por várias transformações 2D para mover o centro de rotação para o centro da tela e para dimensionar o tamanho da cadeia de caracteres de texto para que ela tenha a mesma largura que a tela:

[![](3d-rotation-images/animatedrotation3d-small.png "Triple screenshot of the Animated Rotation 3D page")](3d-rotation-images/animatedrotation3d-large.png#lightbox "Triple screenshot of the Animated Rotation 3D page")

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
