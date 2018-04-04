---
title: Rotações 3D
description: Use transformações não afins para girar objetos 2D em espaço 3D.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: 47daca2ab94f718aeb5ce474c87edce3c3bd77a4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="3d-rotations"></a>Rotações 3D

_Use transformações não afins para girar objetos 2D em espaço 3D._

Um aplicativo comum de transformações não afim está simulando a rotação de um objeto 2D em espaço 3D:

![](3d-rotation-images/3drotationsexample.png "Uma cadeia de caracteres de texto girada em espaço 3D")

Esse trabalho envolve trabalhar com rotações tridimensionais e, em seguida, derivando uma não-afim `SKMatrix` transformação que executa essas rotações 3D.

É difícil desenvolver isso `SKMatrix` transformação trabalha apenas dentro de duas dimensões. O trabalho se torna muito mais fácil quando esta matriz de 3 por 3 é derivado de uma matriz 4 por 4 usadas em gráficos 3D. SkiaSharp inclui o [ `SKMatrix44` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.PreConcat/p/SkiaSharp.SKMatrix44/) classe para essa finalidade, mas alguns em segundo plano em gráficos 3D é necessário para entender as rotações 3D e a matriz de transformação de 4 por 4.

Um sistema de coordenadas tridimensional adiciona um terceiro eixo z conceitualmente chamado, o eixo Z é ângulos direita para a tela. Pontos de coordenadas no espaço 3D são indicados com três números: (x, y, z). Em 3D do sistema de coordenadas usado neste artigo, aumentar os valores de X são à direita e aumentar os valores de Y, ficarem desativados, assim como ocorre em duas dimensões. Aumentar os valores Z positivos sair da tela. A origem é o canto superior esquerdo, assim como em gráficos 2D. Você pode pensar a tela como um plano XY com o eixo Z reto para esse plano.

Isso é chamado de um sistema de coordenadas esquerdo. Se você apontar o dedo indicador para a esquerda na direção de positivo X coordenadas (à direita) e coordena o dedo intermediária na direção do aumento Y (baixo), em seguida, o polegar pontos na direção de aumentar as coordenadas Z — estendendo fora do a tela.

Em gráficos 3D, as transformações são baseadas em uma matriz 4 por 4. Aqui está a matriz de identidade 4 por 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Ao trabalhar com uma matriz 4 por 4, é conveniente identificar as células com os números de linha e coluna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

No entanto, o SkiaSharp `Matrix44` classe é um pouco diferente. A única maneira de definir ou obter valores de célula individual `SKMatrix44` é usando o [ `Item` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Item/p/System.Int32/System.Int32/) indexador. Os índices de linha e coluna são com base em zero, em vez de com base em um, e as linhas e colunas são trocadas. A célula M14 no diagrama acima é acessada usando o indexador `[3, 0]` em uma `SKMatrix44` objeto.

Em um sistema de elementos gráficos 3D, um ponto 3D (x, y, z) é convertido em uma matriz de 1-por-4 para multiplicando a matriz de transformação de 4 por 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Transformações 3D análogo a 2D transforma que ocorrem em três dimensões, são assumidas ocorra em quatro dimensões. A dimensão do quarto é conhecida como W, e o espaço 3D é considerado como existe dentro do espaço de 4d onde W coordenadas são iguais a 1. As fórmulas de transformação são da seguinte maneira:

x' = M11·x + M21·y + M31·z + M41

y' = M12·x + M22·y + M32·z + M42

z' = M13·x + M23·y + M33·z + M43

w' = M14·x + M24·y + M34·z + M44

É óbvio das fórmulas de transformação que as células `M11`, `M22`, `M33` são fatores de dimensionamento em direções X, Y e Z, e `M41`, `M42`, e `M43` são fatores de conversão nos X, Y e Z direções.

Para converter essas coordenadas do espaço 3D onde W é igual a 1, x', y', e z 'coordenadas são todos divididas pelo w':

x" = x' / w'

y"= y' / ' w'

z" = z' / w'

w" = w' / w' = 1

Essa divisão por w' fornece perspectiva em espaço 3D. Se w' é igual a 1 e, em seguida, nenhuma perspectiva ocorre.

Rotações em espaço 3D podem ser bastante complexas, mas as rotações mais simples são aqueles em torno dos eixos X, Y e Z. Uma rotação de ângulo α ao redor do eixo X é essa matriz:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Valores de X permanecem os mesmos quando sujeitos a essa transformação. Rotação ao redor do eixo Y deixa os valores de Y inalterado:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Rotação ao redor do eixo Z é igual de gráficos 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

A direção de rotação indicada pela direção do sistema de coordenadas. Este é um sistema canhoto, portanto, se você apontar o thumb da sua mão esquerda para aumentar os valores para um determinado eixo — para a direita para rotação ao redor do eixo X, para baixo para rotação ao redor do eixo Y e na sua direção de rotação ao redor do eixo Z —, em seguida, a curva de seu os outros dedos indica a direção de rotação ângulos positivos.

`SKMatrix44` tenha generalizado estático [ `CreateRotation` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotation/p/System.Single/System.Single/System.Single/System.Single/) e [ `CreateRotationDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotationDegrees/p/System.Single/System.Single/System.Single/System.Single/) métodos que permitem que você especifique o eixo ao redor do qual a rotação ocorre:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Para rotação ao redor do eixo X, defina os três primeiros argumentos como 1, 0, 0. Para rotação ao redor do eixo Y, defini-los como 0, 1, 0 e para rotação ao redor do eixo Z, defini-los como 0, 0, 1.

A quarta coluna de 4 por 4 é para a perspectiva. O `SKMatrix44` não possui métodos para criar transformações de perspectiva, mas você pode criar uma usando o seguinte código:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

O motivo para o nome do argumento `depth` ficará evidente em breve. Esse código cria uma matriz:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

As fórmulas de transformação resultam no seguinte cálculo da w':

w' = – z / profundidade + 1

Isso serve para reduzir as coordenadas X e Y quando valores de Z são menores do que zero (conceitualmente por trás do plano XY) e aumentar as coordenadas X e Y para valores positivos de Z. Quando a coordenada Z é igual a `depth`, em seguida, w' for zero, e as coordenadas serão infinitas. Os sistemas gráficos tridimensionais são criados em torno de uma metáfora da câmera e o `depth` valor aqui representa a distância da câmera da origem do sistema de coordenadas. Se um objeto gráfico tem um Z que é coordenar `depth` unidades da origem, ele é conceitualmente tocar a lente da câmera e torna-se muito grande.

Tenha em mente que você provavelmente usará esse `perspectiveMatrix` valor em combinação com matrizes de rotação. Se um objeto de gráfico que está sendo girado coordenadas de X ou Y maior `depth`, em seguida, a rotação do objeto em espaço 3D é provável que envolvem Z coordenadas maior que `depth`. Isso deve ser evitado! Ao criar `perspectiveMatrix` você deseja definir `depth` para um valor grande o suficiente para todas as coordenadas do objeto de gráficos, independentemente de como ele é girado. Isso garante que não há qualquer divisão por zero.

Combinando perspectiva e rotações 3D requer a multiplicação de matrizes de 4 por 4 juntos. Para essa finalidade, `SKMatrix44` define os métodos de concatenação. Se `A` e `B` são `SKMatrix44` objetos, em seguida, o código a seguir define um igual a um × b:

```csharp
A.PostConcat(B);
```

Quando uma matriz de transformação de 4 por 4 é usada em um sistema de gráficos 2D, ele é aplicado a objetos 2D. Esses objetos são simples e devem para ter as coordenadas Z igual a zero. A multiplicação de transformação é um pouco mais simples do que a transformação mostrada anteriormente:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Esse valor de 0 para resultados de z em fórmulas de transformação que não envolvem as células na terceira linha da matriz:

x' = M11·x + M21·y + M41

y' = M12·x + M22·y + M42

z' = M13·x + M23·y + M43

w' = M14·x + M24·y + M44

Além disso, o z' coordenada é irrelevante bem aqui. Quando um objeto 3D é exibido em um sistema de gráficos 2D, estiver recolhida a um objeto bidimensional, ignorando os valores de coordenada Z. As fórmulas de transformação são realmente apenas esses dois:

x" = x' / w'

y"= y' / ' w'

Isso significa que a terceira linha *e* terceira coluna da matriz 4 por 4 pode ser ignorada.

Mas se for assim, por que é a matriz de 4 por 4 necessário até mesmo em primeiro lugar?

Embora a terceira linha e a terceira coluna de 4 por 4 são irrelevantes para transformações bidimensionais, a terceira linha e coluna *fazer* desempenham uma função antes que, quando vários `SKMatrix44` valores são multiplicados juntos. Por exemplo, suponha que você multiplique rotação ao redor do eixo Y com a transformação de perspectiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

O produto, a célula `M14` agora contém um valor de perspectiva. Se você deseja aplicar essa matriz de objetos 2D, a terceira linha e coluna são eliminados para convertê-la em uma matriz de 3 por 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Agora, ele pode ser usado para transformar um ponto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

As fórmulas de transformação são:

x' = cos(α)·x

y' = y

z' = (sin (α) / profundidade) ·x + 1

Agora tudo de divisão por z':

x"= cos ·x (α) / ((sin (α) / profundidade) ·x + 1)

y"= y / ((sin (α) / profundidade) ·x + 1)

Quando objetos 2D são girados com um ângulo positivo em torno do eixo Y, em seguida, positivo valores X recuam no plano de fundo ao negativo valores X vir em primeiro plano. Os valores de X parece estar se aproximar do eixo Y (que é controlado pelo valor cosseno) como coordenadas mais distante do eixo Y se torna menor ou maior, como eles se afastar do visualizador ou mais perto do visualizador.

Ao usar `SKMatrix44`, executar todas as operações de perspectiva e a rotação 3D multiplicando vários `SKMatrix44` valores. Em seguida, é possível extrair uma matriz bidimensional de 3 por 3 a 4 por 4 matriz usando o [ `Matrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Matrix/) propriedade o `SKMatrix44` classe. Essa propriedade retorna um familiar `SKMatrix` valor.

O **rotação 3D** página permite fazer experiências com rotação 3D. O [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) arquivo cria quatro controles deslizantes para definir a rotação em torno dos eixos X, Y e Z e para definir um valor de profundidade:

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

Observe que o `depthSlider` é inicializada com um `Minimum` valor de 250. Isso implica que o objeto 2D que está sendo girado aqui tem coordenadas X e Y, restritas a um círculo definido por um raio de 250 pixels ao redor da origem. Qualquer rotação do objeto em espaço 3D sempre resultará em valores de coordenada menor do que 250.

O [ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) arquivo code-behind carrega em um bitmap que possui 300 pixels quadrados:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

Se a transformação 3D é centralizada no bitmap, em seguida, coordenadas X e Y intervalo entre –150 e 150, enquanto os cantos são 212 pixels do centro, portanto, tudo o que está dentro do raio de 250 pixels.

O `PaintSurface` manipulador cria `SKMatrix44` objetos conforme os controles deslizantes e multiplica-los juntos usando `PostConcat`. O `SKMatrix` extraído do final do valor `SKMatrix44` objeto está circundado por transformações para centralizar a rotação no centro da tela de conversão:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

Quando você testar o controle deslizante quarto, você observará que as configurações de profundidade diferentes não movem o objeto mais distante do visualizador, mas em vez disso, alteram a extensão do efeito de perspectiva:

[![](3d-rotation-images/rotation3d-small.png "Captura de tela da página rotação 3D tripla")](3d-rotation-images/rotation3d-large.png#lightbox "tripla captura de tela da página rotação 3D")

O **animado rotação 3D** também usa `SKMatrix44` para animar uma cadeia de caracteres de texto em espaço 3D. O `textPaint` objeto definido como um campo é usado no construtor para determinar os limites do texto:

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

O `OnAppearing` substituição define três xamarin. Forms `Animation` objetos para animar a `xRotationDegrees`, `yRotationDegrees`, e `zRotationDegrees` campos em diferentes taxas. Observe que os períodos dessas animações são definidos como principal números — 5 segundos, 7 e 11 segundos — para a combinação geral somente se repete a cada 385 segundos ou mais de 10 minutos:

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

Como o programa anterior, o `PaintCanvas` manipulador cria `SKMatrix44` valores de rotação e perspectiva e multiplica-los juntos:

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

Essa rotação 3D é colocada entre várias transformações 2D para mover o Centro de rotação para o centro da tela e dimensionar o tamanho da cadeia de caracteres de texto para que seja a mesma largura de tela:

[![](3d-rotation-images/animatedrotation3d-small.png "Captura de tela da página de animação de rotação 3D tripla")](3d-rotation-images/animatedrotation3d-large.png#lightbox "tripla captura de tela da página de animação de rotação 3D")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
