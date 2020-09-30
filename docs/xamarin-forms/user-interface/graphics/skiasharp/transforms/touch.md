---
title: Manipulações de toque
description: Este artigo explica como usar transformações de matriz para implementar arrastar, apertar e girar por toque e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6d568e948f02952fa71ed21af48160a53bfc419
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556536"
---
# <a name="touch-manipulations"></a>Manipulações de toque

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usar transformações de matriz para implementar arrastar, apertar e girar toque_

Em ambientes multitoque, como aqueles em dispositivos móveis, os usuários geralmente usam seus dedos para manipular objetos na tela. Gestos comuns, como um "arrastar de um dedo" e um pinça de dois dedos, podem mover e dimensionar objetos ou até mesmo girá-los. Esses gestos geralmente são implementados usando matrizes de transformação, e este artigo mostra como fazer isso.

![Um bitmap que está sujeito à tradução, ao dimensionamento e à rotação](touch-images/touchmanipulationsexample.png)

Todos os exemplos mostrados aqui usam o Xamarin.Forms efeito de controle de toque apresentado no artigo [**invocando eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Arrastar e tradução

Um dos aplicativos mais importantes das transformações de matriz é o processamento de toque. Um único [`SKMatrix`](xref:SkiaSharp.SKMatrix) valor pode consolidar uma série de operações de toque. 

Para arrastar com um único dedo, o `SKMatrix` valor executa a tradução. Isso é demonstrado na página de **arrastar bitmap** . O arquivo XAML instancia um `SKCanvasView` em um Xamarin.Forms `Grid` . Um `TouchEffect` objeto foi adicionado à `Effects` coleção de que `Grid` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Teoricamente, o `TouchEffect` objeto pode ser adicionado diretamente à `Effects` coleção do `SKCanvasView` , mas isso não funciona em todas as plataformas. Como o `SKCanvasView` tem o mesmo tamanho que o `Grid` nessa configuração, anexá-lo ao `Grid` também funciona.

O arquivo code-behind carrega em um recurso de bitmap em seu construtor e o exibe no `PaintSurface` manipulador:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

Sem nenhum código adicional, o `SKMatrix` valor é sempre a matriz de identificação e não teria efeito na exibição do bitmap. O objetivo do `OnTouchEffectAction` conjunto de manipuladores no arquivo XAML é alterar o valor da matriz para refletir as manipulações de toque.

O `OnTouchEffectAction` manipulador começa convertendo o Xamarin.Forms `Point` valor em um `SKPoint` valor SkiaSharp. Essa é uma simples questão de dimensionamento com base `Width` nas `Height` Propriedades e de `SKCanvasView` (que são unidades independentes de dispositivo) e a `CanvasSize` propriedade, que está em unidades de pixels:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point = 
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

Quando um dedo toca a tela pela primeira vez, um evento do tipo `TouchActionType.Pressed` é acionado. A primeira tarefa é determinar se o dedo está tocando no bitmap. Essa tarefa geralmente é chamada _de teste de colisão_. Nesse caso, o teste de ocorrências pode ser realizado criando um `SKRect` valor correspondente ao bitmap, aplicando a transformação de matriz a ele com `MapRect` e, em seguida, determinando se o ponto de toque está dentro do retângulo transformado.

Se esse for o caso, o `touchId` campo será definido como a ID de toque e a posição do dedo será salva.

Para o `TouchActionType.Moved` evento, os fatores de conversão do `SKMatrix` valor são ajustados com base na posição atual do dedo e na nova posição do dedo. Essa nova posição é salva pela próxima vez e a `SKCanvasView` é invalidada.

Ao experimentar esse programa, observe que você só pode arrastar o bitmap quando o dedo tocar em uma área em que o bitmap é exibido. Embora essa restrição não seja muito importante para esse programa, ela se torna crucial ao manipular vários bitmaps.

## <a name="pinching-and-scaling"></a>Pinçagem e dimensionamento

O que você deseja que aconteça quando dois dedos tocam o bitmap? Se os dois dedos forem movidos em paralelo, você provavelmente desejará que o bitmap se mova junto com os dedos. Se os dois dedos executarem uma operação de pinçagem ou de ampliação, talvez você queira que o bitmap seja girado (para ser discutido na próxima seção) ou dimensionado. Ao dimensionar um bitmap, faz mais sentido que os dois dedos permaneçam nas mesmas posições em relação ao bitmap e que o bitmap seja dimensionado de acordo.

Lidar com dois dedos de uma vez parece complicado, mas tenha em mente que o `TouchAction` manipulador só recebe informações sobre um dedo por vez. Se dois dedos estiverem manipulando o bitmap, em seguida, para cada evento, um dedo terá a posição alterada, mas o outro não será alterado. No código de página de **dimensionamento de bitmap** abaixo, o dedo que não mudou a posição é chamado de ponto _dinâmico_ porque a transformação é relativa a esse ponto.

Uma diferença entre esse programa e o programa anterior é que várias IDs de toque devem ser salvas. Um dicionário é usado para essa finalidade, em que a ID de toque é a chave de dicionário e o valor do dicionário é a posição atual desse dedo:

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If something bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

A manipulação da `Pressed` ação é quase igual ao programa anterior, exceto pelo fato de que a ID e o ponto de toque são adicionados ao dicionário. As `Released` `Cancelled` ações e removem a entrada do dicionário.

`Moved`No entanto, o tratamento para a ação é mais complexo. Se houver apenas um dedo envolvido, o processamento será muito semelhante ao programa anterior. Para dois ou mais dedos, o programa também deve obter informações do dicionário que envolve o dedo que não está se movendo. Ele faz isso copiando as chaves de dicionário em uma matriz e, em seguida, comparando a primeira chave com a ID do dedo que está sendo movida. Isso permite que o programa obtenha o ponto dinâmico correspondente ao dedo que não está sendo movido.

Em seguida, o programa calcula dois vetores da nova posição do dedo em relação ao ponto dinâmico e a posição do dedo antigo em relação ao ponto dinâmico. As proporções desses vetores são fatores de dimensionamento. Como a divisão por zero é uma possibilidade, elas devem ser verificadas quanto a valores infinitos ou valores NaN (não um número). Se tudo estiver bem, uma transformação de dimensionamento será concatenada com o `SKMatrix` valor salvo como um campo.

Ao experimentar essa página, você observará que pode arrastar o bitmap com um ou dois dedos ou dimensioná-lo com dois dedos. O dimensionamento é _anisotropic_, o que significa que o dimensionamento pode ser diferente nas direções horizontal e vertical. Isso distorce a taxa de proporção, mas também permite que você vire o bitmap para criar uma imagem espelho. Você também pode descobrir que pode reduzir o bitmap para uma dimensão zero e ele desaparece. No código de produção, você desejará se proteger contra isso.

## <a name="two-finger-rotation"></a>Rotação de dois dedos

A página de **giro de bitmap** permite que você use dois dedos para a rotação ou o dimensionamento de isotropic. O bitmap sempre retém sua taxa de proporção correta. Usar dois dedos para a rotação e o dimensionamento de anisotropic não funciona muito bem porque o movimento dos dedos é muito semelhante para ambas as tarefas.

A primeira grande diferença nesse programa é a lógica de teste de colisão. Os programas anteriores usaram o `Contains` método de `SKRect` para determinar se o ponto de toque está dentro do retângulo transformado que corresponde ao bitmap. Mas como o usuário manipula o bitmap, o bitmap pode ser girado e `SKRect` não pode representar corretamente um retângulo girado. Você pode temer que a lógica de teste de ocorrências precise implementar uma geometria analítica complexa nesse caso.

No entanto, um atalho está disponível: determinar se um ponto está dentro dos limites de um retângulo transformado é o mesmo que determinar se um ponto transformado inversa está dentro dos limites do retângulo não convertido. Esse é um cálculo muito mais fácil, e a lógica pode continuar a usar o `Contains` método conveniente:

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

A lógica para o `Moved` evento começa como o programa anterior. Dois vetores nomeados `oldVector` e `newVector` são calculados com base nos pontos anterior e atual do dedo móvel e do ponto dinâmico do dedo não movido. Mas, em seguida, os ângulos desses vetores são determinados e a diferença é o ângulo de rotação.

O dimensionamento também pode estar envolvido, portanto, o vetor antigo é girado com base no ângulo de rotação. A magnitude relativa dos dois vetores agora é o fator de dimensionamento. Observe que o mesmo `scale` valor é usado para dimensionamento horizontal e vertical para que o dimensionamento seja isotropic. O `matrix` campo é ajustado pela matriz de rotação e uma matriz de escala.

Se seu aplicativo precisar implementar o processamento de toque para um único bitmap (ou outro objeto), você poderá adaptar o código desses três exemplos para seu próprio aplicativo. Mas se você precisar implementar o processamento de toque para vários bitmaps, provavelmente desejará encapsular essas operações de toque em outras classes.

## <a name="encapsulating-the-touch-operations"></a>Encapsulando as operações de toque

A página de **manipulação de toque** demonstra a manipulação de toque de um único bitmap, mas usando vários outros arquivos que encapsulam grande parte da lógica mostrada acima. O primeiro desses arquivos é a [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeração, que indica os diferentes tipos de manipulação de toque implementados pelo código que você verá:

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` é um arrastar de um dedo que é implementado com a tradução. Todas as opções subsequentes também incluem panorâmica, mas envolvem dois dedos: `IsotropicScale` é uma operação de pinçagem que resulta na escala de objetos igualmente nas direções horizontal e vertical. `AnisotropicScale` permite o dimensionamento diferente.

A `ScaleRotate` opção é para dimensionamento e rotação de dois dedos. O dimensionamento é isotropic. Como mencionado anteriormente, a implementação de rotação de dois dedos com dimensionamento de anisotropic é problemática porque os movimentos de dedo são essencialmente os mesmos.

A `ScaleDualRotate` opção adiciona a rotação de um dedo. Quando um único dedo arrasta o objeto, o objeto arrastado é girado primeiro em volta de seu centro para que o centro do objeto fique alinhado com o vetor de arrasto.

O arquivo [**TouchManipulationPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) inclui um `Picker` com os membros da `TouchManipulationMode` enumeração:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>
        
        <Grid BackgroundColor="White"
              Grid.Row="1">
            
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Em direção à parte inferior `SKCanvasView` , está um e um `TouchEffect` anexado à célula única `Grid` que o inclui.

O arquivo code-behind [**TouchManipulationPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) tem um `bitmap` campo, mas ele não é do tipo `SKBitmap` . O tipo é `TouchManipulationBitmap` (uma classe que você verá em breve):

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

O construtor instancia um `TouchManipulationBitmap` objeto, passando para o construtor `SKBitmap` obtido de um recurso inserido. O Construtor conclui definindo a `Mode` propriedade da `TouchManager` Propriedade do `TouchManipulationBitmap` objeto como um membro da `TouchManipulationMode` enumeração.

O `SelectedIndexChanged` manipulador para o `Picker` também define essa `Mode` Propriedade:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

O `TouchAction` manipulador da `TouchEffect` instanciado no arquivo XAML chama dois métodos em `TouchManipulationBitmap` denominado `HitTest` e `ProcessTouchEvent` :

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Se o `HitTest` método retornar `true` &mdash; significando que um dedo tocou na tela dentro da área ocupada pelo bitmap &mdash; , a ID de toque será adicionada à `TouchIds` coleção. Essa ID representa a sequência de eventos de toque para esse dedo até que o dedo se retire da tela. Se vários dedos tocam o bitmap, a `touchIds` coleção contém uma ID de toque para cada dedo.

O `TouchAction` manipulador também chama a `ProcessTouchEvent` classe em `TouchManipulationBitmap` . É aí que alguns (mas não todos) do processamento real de toque ocorrem.

A [`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe é uma classe wrapper para `SKBitmap` que contém o código para renderizar os eventos de toque e de processo de bitmap. Ele funciona em conjunto com código mais generalizado em uma `TouchManipulationManager` classe (que você verá em breve).

O `TouchManipulationBitmap` Construtor salva o `SKBitmap` e instancia duas propriedades, a `TouchManager` Propriedade do tipo `TouchManipulationManager` e a `Matrix` Propriedade do tipo `SKMatrix` :

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Essa `Matrix` propriedade é a transformação acumulada resultante de toda a atividade de toque. Como você verá, cada evento de toque é resolvido em uma matriz, que é concatenada com o `SKMatrix` valor armazenado pela `Matrix` propriedade.

O `TouchManipulationBitmap` objeto se desenha em seu `Paint` método. O argumento é um `SKCanvas` objeto. Isso `SKCanvas` pode já ter uma transformação aplicada a ela, portanto, o `Paint` método concatena a `Matrix` propriedade associada ao bitmap para a transformação existente e restaura a tela quando ela termina:

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

O `HitTest` método retorna `true` se o usuário toca a tela em um ponto dentro dos limites do bitmap. Isso usa a lógica mostrada anteriormente na página **rotação de bitmaps** :

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

O segundo método público no `TouchManipulationBitmap` é `ProcessTouchEvent` . Quando esse método é chamado, ele já foi estabelecido que o evento de toque pertence a este bitmap específico. O método mantém um dicionário de [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que é simplesmente o ponto anterior e o novo ponto de cada dedo:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Este é o dicionário e o `ProcessTouchEvent` próprio método:

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

Nos `Moved` eventos e `Released` , o método chama `Manipulate` . Nesses momentos, o `touchDictionary` contém um ou mais `TouchManipulationInfo` objetos. Se o `touchDictionary` contiver um item, é provável que os `PreviousPoint` `NewPoint` valores e sejam desiguais e representem a movimentação de um dedo. Se vários dedos estiverem tocando no bitmap, o dicionário conterá mais de um item, mas apenas um desses itens terá `PreviousPoint` valores diferentes `NewPoint` . Todos os REST têm `PreviousPoint` valores iguais e `NewPoint` .

Isso é importante: o `Manipulate` método pode assumir que está processando a movimentação de apenas um dedo. No momento da chamada, nenhum dos outros dedos está se movendo e, se eles realmente estiverem sendo movidos (como é provável), esses movimentos serão processados em chamadas futuras para `Manipulate` .

O `Manipulate` método primeiro copia o dicionário para uma matriz para sua conveniência. Ele ignora algo diferente das duas primeiras entradas. Se mais de dois dedos estiverem tentando manipular o bitmap, os outros serão ignorados. `Manipulate` é o membro final de `TouchManipulationBitmap` :

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Se um dedo estiver manipulando o bitmap, `Manipulate` o chamará o `OneFingerManipulate` método do `TouchManipulationManager` objeto. Para dois dedos, ele chama `TwoFingerManipulate` . Os argumentos para esses métodos são os mesmos: os `prevPoint` `newPoint` argumentos e representam o dedo que está sendo movido. Mas o `pivotPoint` argumento é diferente para as duas chamadas:

Para a manipulação de um dedo, o `pivotPoint` é o centro do bitmap. Isso é para permitir uma rotação de um dedo. Para a manipulação de dois dedos, o evento indica a movimentação de apenas um dedo, de modo que o `pivotPoint` é o dedo que não está se movendo.

Em ambos os casos, `TouchManipulationManager` retorna um `SKMatrix` valor, que o método concatena com a `Matrix` propriedade atual que o `TouchManipulationPage` usa para renderizar o bitmap.

`TouchManipulationManager` é generalizado e não usa outros arquivos, exceto `TouchManipulationMode` . Talvez você possa usar essa classe sem alterar seus próprios aplicativos. Ela define uma única propriedade do tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

No entanto, você provavelmente desejará evitar a `AnisotropicScale` opção. É muito fácil com essa opção manipular o bitmap para que um dos fatores de dimensionamento se torne zero. Isso faz com que o bitmap desapareça da visão, nunca para retornar. Se você realmente precisa de dimensionamento anisotropic, convém aprimorar a lógica para evitar resultados indesejáveis.

`TouchManipulationManager` faz uso de vetores, mas como não há nenhuma `SKVector` estrutura em SkiaSharp, `SKPoint` é usado em vez disso. `SKPoint` dá suporte ao operador de subtração e o resultado pode ser tratado como um vetor. A única lógica específica de vetor que precisava ser adicionada é um `Magnitude` cálculo:

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Sempre que a rotação for selecionada, os métodos de manipulação One-Finger e Two-Finger lidarão primeiro com a rotação. Se qualquer rotação for detectada, o componente de rotação será efetivamente removido. O que resta é interpretado como movimento panorâmico e dimensionamento.

Este é o `OneFingerManipulate` método. Se a rotação de um dedo não tiver sido habilitada, a lógica será simples &mdash; simplesmente usa o ponto anterior e o novo ponto para construir um vetor chamado `delta` que corresponde precisamente à tradução. Com a rotação de um dedo habilitada, o método usa ângulos do ponto dinâmico (o centro do bitmap) para o ponto anterior e o novo ponto para construir uma matriz de rotação:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

No `TwoFingerManipulate` método, o ponto dinâmico é a posição do dedo que não está se movendo neste evento de toque específico. A rotação é muito semelhante à rotação de um dedo e, em seguida, o vetor nomeado `oldVector` (com base no ponto anterior) é ajustado para a rotação. O movimento restante é interpretado como dimensionamento:

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Você perceberá que não há nenhuma tradução explícita nesse método. No entanto, `MakeRotation` os `MakeScale` métodos e são baseados no ponto dinâmico, e isso inclui a tradução implícita. Se você estiver usando dois dedos no bitmap e arrastá-los na mesma direção, o receberá `TouchManipulation` uma série de eventos de toque alternando entre os dois dedos. Como cada dedo se move em relação aos outros resultados, escala ou rotação, mas é negado pelo movimento do outro dedo e o resultado é tradução.

A única parte restante da página de **manipulação de toque** é o `PaintSurface` manipulador no `TouchManipulationPage` arquivo code-behind. Isso chama o `Paint` método do `TouchManipulationBitmap` , que aplica a matriz que representa a atividade de toque acumulada:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

O `PaintSurface` manipulador é concluído exibindo um `MatrixDisplay` objeto que mostra a matriz de toque acumulada:

[![Captura de tela tripla da página de manipulação de toque](touch-images/touchmanipulation-small.png)](touch-images/touchmanipulation-large.png#lightbox "Captura de tela tripla da página de manipulação de toque")

## <a name="manipulating-multiple-bitmaps"></a>Manipulando vários bitmaps

Uma das vantagens de isolar o código de processamento de toque em classes como `TouchManipulationBitmap` e `TouchManipulationManager` é a capacidade de reutilizar essas classes em um programa que permite ao usuário manipular vários bitmaps.

A página de **exibição de dispersão de bitmap** demonstra como isso é feito. Em vez de definir um campo do tipo `TouchManipulationBitmap` , a [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe define um `List` dos objetos de bitmap:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

O construtor é carregado em todos os bitmaps disponíveis como recursos incorporados e os adiciona ao `bitmapCollection` . Observe que a `Matrix` propriedade é inicializada em cada `TouchManipulationBitmap` objeto, de modo que os cantos superior esquerdo de cada bitmap são deslocados em 100 pixels.

A `BitmapScatterView` página também precisa lidar com eventos de toque para vários bitmaps. Em vez de definir as `List` IDs de toque dos objetos atualmente manipulados `TouchManipulationBitmap` , este programa requer um dicionário:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Observe como a `Pressed` lógica faz o loop pelo `bitmapCollection` inverso. Os bitmaps geralmente se sobrepõem. Os bitmaps mais adiante na coleção se encontram visualmente sobre os bitmaps anteriores na coleção. Se houver vários bitmaps sob o dedo que pressiona na tela, o primeiro deve ser aquele que é manipulado por esse dedo.

Observe também que a `Pressed` lógica move esse bitmap para o final da coleção para que ele se mova visualmente para a parte superior da pilha de outros bitmaps.

Nos `Moved` eventos e `Released` , o `TouchAction` manipulador chama o `ProcessingTouchEvent` método em `TouchManipulationBitmap` exatamente como o programa anterior.

Por fim, o `PaintSurface` manipulador chama o `Paint` método de cada `TouchManipulationBitmap` objeto:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

O código percorre a coleção e exibe a pilha de bitmaps desde o início da coleção até o final:

[![Captura de tela tripla da página de exibição de dispersão de bitmap](touch-images/bitmapscatterview-small.png)](touch-images/bitmapscatterview-large.png#lightbox "Captura de tela tripla da página de exibição de dispersão de bitmap")

## <a name="single-finger-scaling"></a>Dimensionamento de dedo único

Uma operação de dimensionamento geralmente requer um gesto de pinçagem usando dois dedos. No entanto, é possível implementar o dimensionamento com um único dedo fazendo com que o dedo mova os cantos de um bitmap.

Isso é demonstrado na página **escala de canto de um único dedo** . Como este exemplo usa um tipo um pouco diferente de dimensionamento do que o implementado na `TouchManipulationManager` classe, ele não usa essa classe ou a `TouchManipulationBitmap` classe. Em vez disso, toda a lógica de toque está no arquivo code-behind. Essa é uma lógica mais simples do que o normal, pois rastreia apenas um dedo por vez e simplesmente ignora os dedos secundários que podem estar tocando na tela.

A página [**SingleFingerCornerScale. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) instancia a `SKCanvasView` classe e cria um `TouchEffect` objeto para controlar eventos de toque:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

O arquivo [**SingleFingerCornerScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) carrega um recurso de bitmap do diretório de **mídia** e o exibe usando um `SKMatrix` objeto definido como um campo:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

Esse `SKMatrix` objeto é modificado pela lógica de toque mostrada abaixo.

O restante do arquivo code-behind é o `TouchEffect` manipulador de eventos. Ele começa convertendo o local atual do dedo em um `SKPoint` valor. Para o `Pressed` tipo de ação, o manipulador verifica se nenhum outro dedo está tocando na tela e se o dedo está dentro dos limites do bitmap.

A parte crucial do código é uma `if` instrução que envolve duas chamadas para o `Math.Pow` método. Essa matemática verifica se o local do dedo está fora de uma elipse que preenche o bitmap. Nesse caso, essa é uma operação de dimensionamento. O dedo está próximo a um dos cantos do bitmap, e um ponto dinâmico é determinado que é o canto oposto. Se o dedo estiver dentro dessa elipse, será uma operação normal de panorâmica:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

O `Moved` tipo de ação calcula uma matriz correspondente à atividade de toque a partir do momento em que o dedo pressionou a tela até esse momento. Ele concatena essa matriz com a matriz em vigor no momento em que o dedo pressionou o bitmap pela primeira vez. A operação de dimensionamento é sempre relativa ao canto oposto ao que o dedo tocou.

Para bitmaps pequenos ou Oblong, uma elipse interior pode ocupar a maior parte do bitmap e deixar áreas pequenas nos cantos para dimensionar o bitmap. Você pode preferir uma abordagem um pouco diferente, caso em que você pode substituir `if` o bloco inteiro que `isScaling` define `true` com esse código:

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

Esse código divide efetivamente a área do bitmap em uma forma de losango interior e quatro triângulos nos cantos. Isso permite áreas muito maiores nos cantos para pegar e dimensionar o bitmap.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Invocando eventos por meio de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)