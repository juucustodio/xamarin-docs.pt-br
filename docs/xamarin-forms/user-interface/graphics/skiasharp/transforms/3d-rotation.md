---
title: Rotações 3D em SkiaSharp
description: Este artigo explica como usar transformações não afins para girar objetos 2D no espaço 3D e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: c6a600b7ae05df626d4315796c7a5e425639e2f8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652791"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotações 3D em SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use transformações não afins para girar objetos 2D no espaço 3D._

Uma aplicação muito comum de transformações não afins está simulando a rotação de um objeto 2D no espaço 3D:

![](3d-rotation-images/3drotationsexample.png "Uma cadeia de caracteres de texto girada no espaço 3D")

Esse trabalho envolve a trabalhar com rotações tridimensionais e, em seguida, derivando uma não afim `SKMatrix` transformação que executa esses rotações 3D.

É difícil desenvolver isso `SKMatrix` transformação trabalhar exclusivamente em duas dimensões. O trabalho se torna muito mais fácil quando esta matriz de 3 por 3 é derivado de uma matriz de 4 por 4 usada em gráficos 3D. SkiaSharp inclui o [ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44) classe para essa finalidade, mas algumas informações básicas em gráficos 3D é necessária para compreender as rotações 3D e a matriz de transformação de 4 por 4.

Um sistema de coordenadas tridimensional adiciona um terceiro eixo chamado Z. conceitualmente, o eixo Z é ângulos retos na tela. Pontos de coordenadas no espaço 3D são indicados com três números: (x, y, z). Em 3D do sistema de coordenadas usado neste artigo, aumentar os valores de X são para a direita e aumentar os valores de Y ficarão inativos, assim como em duas dimensões. Aumentar os valores Z positivos sair da tela. A origem é o canto superior esquerdo, assim como nos gráficos 2D. Você pode pensar a tela como um plano XY com o eixo Z com ângulos retos desse plano.

Isso é chamado de um sistema de coordenadas à esquerda. Se você apontar o dedo indicador para a sua mão esquerda na direção de positivo X coordenadas (à direita) e o dedo médio na direção Y crescente de coordenadas (para baixo), em seguida, o polegar aponta na direção de aumentar as coordenadas Z — estendendo-out do a tela.

Em gráficos 3D, as transformações são com base em uma matriz 4 por 4. Aqui está a matriz de identidade 4 por 4:

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

No entanto, o SkiaSharp `Matrix44` classe é um pouco diferente. A única maneira de definir ou obter os valores de célula individual `SKMatrix44` está usando o [ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indexador. Os índices de linha e coluna são com base em zero e não baseado em um, e as linhas e colunas são trocadas. A célula M14 no diagrama acima é acessada usando o indexador `[3, 0]` em um `SKMatrix44` objeto.

Em um sistema de elementos gráficos 3D, um ponto 3D (x, y, z) é convertido em uma matriz de 1 por 4 de multiplicação de matriz de transformação de 4 por 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Transformações 3D análogo para 2D transformações que ocorrem em três dimensões, são consideradas para entrar em vigor em quatro dimensões. A dimensão do quarto é conhecida como W e o espaço 3D deve para existir dentro do espaço de 4d onde W coordenadas são iguais a 1. As fórmulas de transformação são da seguinte maneira:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

É óbvio das fórmulas de transformação que as células `M11`, `M22`, `M33` são fatores de dimensionamento nas direções X, Y e Z, e `M41`, `M42`, e `M43` são fatores de conversão nos X, Y e Z direções.

Para converter essas coordenadas para o espaço 3D onde W é igual a 1, o x', y', e z 'coordenadas são todos divididas por w':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Essa divisão por w' fornece perspectiva no espaço 3D. Se w' é igual a 1 e, em seguida, nenhuma perspectiva ocorre.

As rotações no espaço 3D podem ser bastante complexas, mas as rotações mais simples são aquelas em torno dos eixos X, Y e Z. Uma rotação do ângulo α em torno do eixo X é essa matriz:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Valores de X permanecem os mesmos quando sujeitos a essa transformação. Rotação em torno do eixo Y deixa os valores de Y inalterado:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Rotação em torno do eixo Z é o mesmo gráficos 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

A direção de rotação é indicada de destro/canhoto do sistema de coordenadas. Este é um sistema canhoto, portanto, se você apontar o polegar da mão esquerda para aumentar os valores para um eixo específico — para a direita para a rotação em torno do eixo X, para baixo para a rotação em torno do eixo Y e na sua direção para a rotação em torno do eixo Z —, em seguida, a curva de yo outros dedos indica a direção de rotação para ângulos positivos.

`SKMatrix44` tenha generalizado estática [ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) e [ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) métodos que permitem que você especifique o eixo em torno da qual ocorrerá a rotação:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Para a rotação em torno do eixo X, defina os três primeiros argumentos como 1, 0, 0. Para rotação em torno do eixo Y, defini-los como 0, 1, 0 e para a rotação em torno do eixo Z, defini-los como 0, 0, 1.

A quarta coluna de 4 por 4 destina-se a perspectiva. O `SKMatrix44` não tem métodos para criar transformações de perspectiva, mas você pode criar um por conta própria usando o seguinte código:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

O motivo para o nome do argumento `depth` ficará evidente em breve. Esse código cria a matriz:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

As fórmulas de transformação resultam no seguinte cálculo da w':

`w' = –z / depth + 1`

Isso serve para reduzir as coordenadas X e Y quando valores de Z são menor que zero (conceitualmente por trás do plano XY) e aumentar as coordenadas X e Y para valores positivos de Z. Quando a coordenada Z é igual a `depth`, em seguida, w' é zero, e coordenadas se tornar infinitas. Sistemas de elementos gráficos tridimensionais são criados em torno de uma metáfora de câmera e o `depth` valor aqui representa a distância da câmera da origem do sistema de coordenadas. Se um objeto gráfico tem um Z coordenar que é `depth` unidades da origem, conceitualmente tocando a lente da câmera e torna-se muito grande.

Lembre-se de que você provavelmente usará isso `perspectiveMatrix` valor em combinação com matrizes de rotação. Se um objeto de gráfico que está sendo girado tiver coordenadas de X ou Y maior `depth`, em seguida, é provável envolvem as coordenadas Z maior que a rotação do objeto no espaço 3D `depth`. Isso deve ser evitado! Ao criar `perspectiveMatrix` você deseja definir `depth` para um valor grande o suficiente para todas as coordenadas do objeto de elementos gráficos, independentemente de como ele é girado. Isso garante que nunca haverá qualquer divisão por zero.

Combinação de perspectiva e rotações 3D requer a multiplicação de matrizes de 4 por 4 juntos. Para essa finalidade, `SKMatrix44` define os métodos de concatenação. Se `A` e `B` são `SKMatrix44` objetos, em seguida, o código a seguir define uma igual a um × b:

```csharp
A.PostConcat(B);
```

Quando uma matriz de transformação de 4 por 4 é usada em um sistema de gráficos 2D, ela é aplicada aos objetos 2D. Esses objetos são simples e devem para ter as coordenadas Z igual a zero. A multiplicação de transformação é um pouco mais simples do que a transformação mostrada anteriormente:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Esse valor de 0 para resultados de z em fórmulas de transformação que não envolvem qualquer célula na terceira linha da matriz:

x' = M11·x + M21·y + M41

y' = M12·x + M22·y + M42

z' = M13·x + M23·y + M43

w' = M14·x + M24·y + M44

Além disso, o z' coordenada é irrelevante, bem aqui. Quando um objeto 3D é exibido em um sistema de gráficos 2D, ele estiver recolhido para um objeto bidimensional, ignorando os valores de coordenada Z. As fórmulas de transformação são realmente apenas esses dois:

`x" = x' / w'`

`y" = y' / w'`

Isso significa que a terceira linha *e* terceira coluna da matriz 4 por 4 pode ser ignorada.

Mas se for assim, por que é realmente necessário para a matriz 4 por 4 em primeiro lugar?

Embora a terceira linha e terceira coluna de 4 por 4 são irrelevantes para transformações bidimensionais, a terceira linha e coluna *fazer* desempenham uma função antes que, quando vários `SKMatrix44` valores são multiplicados juntos. Por exemplo, suponha que você multiplique rotação em torno do eixo Y com a transformação de perspectiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

No produto, a célula `M14` agora contém um valor de ponto de vista. Se você quiser aplicar essa matriz aos objetos 2D, a terceira linha e coluna são eliminadas para convertê-la em uma matriz de 3 por 3:

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

Dividir tudo por z':

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Quando objetos 2D são girados com um ângulo positivo em torno do eixo Y, em seguida, positivo valores X recuam ao plano de fundo ao negativo valores X vir em primeiro plano. Os valores de X parecem se aproximar do eixo Y (que é controlado pelo valor do cosseno) como coordenadas mais distante do eixo Y se torna menor ou maior à medida que eles se afastar do visualizador ou mais perto do visualizador.

Ao usar `SKMatrix44`, realize todas as operações de perspectiva e a rotação 3D multiplicando vários `SKMatrix44` valores. Em seguida, você pode extrair uma matriz bidimensional de 3 por 3 de 4 por 4 matriz usando o [ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix) propriedade do `SKMatrix44` classe. Essa propriedade retorna um familiar `SKMatrix` valor.

O **rotação 3D** página permite que você experimente a rotação 3D. O [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) arquivo instancia quatro controles deslizantes para definir a rotação em torno dos eixos X, Y e Z e para definir um valor de profundidade:

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

Observe que o `depthSlider` é inicializada com um `Minimum` valor de 250. Isso implica que o objeto 2D que está sendo girado aqui tem coordenadas X e Y, restritas a um círculo definido por um raio de 250 pixels em torno da origem. Qualquer rotação desse objeto no espaço 3D sempre resultará em valores de coordenada menor do que 250.

O [ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) arquivo code-behind carrega em um bitmap que é de 300 pixels quadrados:

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

Se a transformação 3D é centralizada nesse bitmap, em seguida, coordenadas X e Y intervalo entre –150 e 150, enquanto os cantos são 212 pixels do centro, portanto, tudo o que está dentro do raio de 250 pixels.

O `PaintSurface` manipulador cria `SKMatrix44` objetos com base nos controles deslizantes e multiplica-los juntos usando `PostConcat`. O `SKMatrix` valor extraído do final `SKMatrix44` objeto está circundado por transformações para centralizar a rotação no centro da tela de conversão:

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

Quando você fazer experiências com o controle deslizante a quarto, você observará que as configurações de profundidade diferentes não movem o objeto ainda mais distante do visualizador, mas em vez disso, alteram a extensão do efeito de perspectiva:

[![](3d-rotation-images/rotation3d-small.png "Captura de tela da página rotação 3D tripla")](3d-rotation-images/rotation3d-large.png#lightbox "tripla captura de tela da página rotação 3D")

O **animada rotação 3D** também usa `SKMatrix44` para animar uma cadeia de caracteres de texto no espaço 3D. O `textPaint` objeto definido como um campo é usado no construtor para definir os limites do texto:

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

O `OnAppearing` substituição define três xamarin. Forms `Animation` objetos para animar a `xRotationDegrees`, `yRotationDegrees`, e `zRotationDegrees` campos em taxas diferentes. Observe que os períodos dessas animações são definidos para preparar os números (5 segundos, 7 segundos e 11 segundos) para que a combinação geral somente se repete a cada 385 segundos ou mais de 10 minutos:

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

Como o programa anterior, o `PaintCanvas` manipulador cria `SKMatrix44` valores de rotação e perspectiva e, em seguida, multiplica-los juntos:

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

Essa rotação 3D é colocada entre várias transformações 2D para mover o Centro de rotação para o centro da tela e dimensionar o tamanho da cadeia de caracteres de texto para que ele seja a mesma largura que a tela:

[![](3d-rotation-images/animatedrotation3d-small.png "Tripla captura de tela da página 3D rotação animado")](3d-rotation-images/animatedrotation3d-large.png#lightbox "tripla captura de tela da página animada rotação 3D")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
