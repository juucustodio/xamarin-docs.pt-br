---
title: Manipulações de toque
description: Este artigo explica como usar transformações de matriz para implementar o arrastar de toque, gestos de pinçar e rotação e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
ms.openlocfilehash: 6f7236a3650c04098edbef92f3d6ed620be501c3
ms.sourcegitcommit: a635312ffec816ba357a92b66c8c5221c8d9044c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "39615386"
---
# <a name="touch-manipulations"></a>Manipulações de toque

_Transformações de matriz de uso para implementar o arrastar de toque, gestos de pinçar e rotação_

Em ambientes de multitoque, como os dispositivos móveis, os usuários geralmente usam seus dedos para manipular objetos na tela. Gestos comuns, como uma operação de arrastar um dedo e uma situação de emergência com dois dedos podem mover e dimensionar objetos ou até mesmo girá-las. Esses gestos geralmente são implementados usando matrizes de transformação, e este artigo mostra como fazer isso.

![](touch-images/touchmanipulationsexample.png "Um bitmap sujeito a translação, dimensionamento e rotação")

Todos os exemplos mostrados aqui usam o efeito de controle de toque do xamarin. Forms apresentado neste artigo [ **invocar eventos de efeitos**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Arrastando e tradução

Um dos aplicativos mais importantes de transformações de matriz é o processamento de toque. Uma única [ `SKMatrix` ](xref:SkiaSharp.SKMatrix) valor possível consolidar uma série de operações de toque. 

Para arrastar dedo, o `SKMatrix` executa a conversão do valor. Isso é demonstrado na **Bitmap arrastando** página. O arquivo XAML instancia uma `SKCanvasView` em um xamarin. Forms `Grid`. Um `TouchEffect` objeto foi adicionado para o `Effects` coleção de que `Grid`:

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

Em teoria, o `TouchEffect` objeto poderia ser adicionado diretamente para o `Effects` coleção do `SKCanvasView`, mas isso não funciona em todas as plataformas. Porque o `SKCanvasView` é o mesmo tamanho que o `Grid` nessa configuração, anexá-lo para o `Grid` funciona tão bem.

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

Sem nenhum código adicional, o `SKMatrix` valor sempre é a matriz de identidade, e ele teria nenhum efeito sobre a exibição do bitmap. O objetivo de `OnTouchEffectAction` manipulador definido no arquivo XAML é alterar o valor de matriz para refletir as manipulações de toque.

O `OnTouchEffectAction` manipulador começa, convertendo o xamarin. Forms `Point` valor em uma SkiaSharp `SKPoint` valor. Isso é uma simples questão de dimensionamento com base nas `Width` e `Height` propriedades de `SKCanvasView` (que são unidades independentes de dispositivo) e o `CanvasSize` propriedade, que está em unidades de pixels:

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

Quando um dedo toca na tela, um evento do tipo primeiro `TouchActionType.Pressed` é disparado. A primeira tarefa é determinar se o dedo tocando o bitmap. Essa tarefa geralmente é chamada _teste de clique_. Nesse caso, teste de clique pode ser feito com a criação de um `SKRect` valor correspondente no bitmap, aplicando a transformação de matriz a ela com `MapRect`e, em seguida, determinando se o ponto de toque está dentro do retângulo transformado.

Se esse for o caso, o `touchId` campo é definido como a ID de toque e a posição do dedo é salvo.

Para o `TouchActionType.Moved` evento, os fatores de conversão do `SKMatrix` valor são ajustados com base na posição atual do dedo e a nova posição do dedo. Que nova posição é salvo para a próxima vez e o `SKCanvasView` é invalidado.

Ao testar com esse programa, observe que somente você pode arrastar o bitmap quando seu dedo toca a uma área em que o bitmap é exibido. Embora essa restrição não é muito importante para este programa, ele se torna crucial quando manipular vários bitmaps.

## <a name="pinching-and-scaling"></a>Pinçar e dimensionamento

O que você deseja acontecer quando dois dedos tocam o bitmap? Se mover dois dedos em paralelo, em seguida, você provavelmente desejará o bitmap para mover, juntamente com os dedos. Se dois dedos efetuar uma situação de emergência ou operação de ampliação, talvez você queira o bitmap a ser girada (para ser discutido na próxima seção) ou em escala. Ao dimensionar um bitmap, faz mais sentido para dois dedos permanecer nas mesmas posições em relação ao bitmap e para o bitmap a ser dimensionado adequadamente.

Tratamento de dois dedos de uma vez parece complicado, mas lembre-se de que o `TouchAction` manipulador só recebe informações sobre um dedo em um tempo. Se dois dedos estão manipulando o bitmap, para cada evento, um dedo mudou de posição, mas o outro não foi alterado. No **escala de Bitmap** página de código abaixo, o dedo que não foi alterado a posição é chamado de _pivot_ ponto porque a transformação é em relação a esse ponto.

Uma diferença entre este programa e o programa anterior é que toque várias que IDs devem ser salvas. Um dicionário é usado para essa finalidade, onde a ID de toque é a chave de dicionário e o valor do dicionário é a posição atual desse dedo:

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
                            // If smething bad hasn't happened, calculate a scale and translation matrix
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

A manipulação do `Pressed` ação é quase o mesmo que a ID do programa, exceto que o anterior e o ponto de contato é adicionado ao dicionário. O `Released` e `Cancelled` ações remover a entrada do dicionário.

O tratamento para o `Moved` ação é mais complexa, no entanto. Se houver apenas um dedo envolvida, o processamento é praticamente o mesmo que o programa anterior. Para duas ou mais dedos, o programa também deve obter informações do dicionário que envolvem o dedo que não está se movendo. Ele faz isso copiando as chaves de dicionário em uma matriz e, em seguida, comparando a primeira chave com a ID do dedo que está sendo movido. Isso permite que o programa obter o ponto dinâmico correspondente não está se movendo a dedo a tocar.

Em seguida, o programa calcula dois vetores da nova posição do dedo em relação ao ponto dinâmico e a antiga posição de dedo em relação ao ponto dinâmico. Fatores de dimensionamento são as proporções desses vetores. Como a divisão por zero é uma possibilidade, eles devem ser verificados para valores de infinitos ou NaN (não um número). Se tudo estiver bem, uma transformação de escala é concatenada com o `SKMatrix` salvo como um campo de valor.

Ao testar com essa página, você observará que você pode arrastar o bitmap com um ou dois dedos ou dimensioná-lo com dois dedos. O dimensionamento é _anisotrópica_, que significa que o dimensionamento pode ser diferente nas direções horizontais e verticais. Distorce a taxa de proporção, mas também permite que você virar o bitmap para tornar uma imagem espelhada. Você também pode descobrir o que é possível reduzir o bitmap para uma dimensão de zero, e ele desaparece. No código de produção, você desejará se proteger contra isso.

## <a name="two-finger-rotation"></a>Rotação com dois dedos

O **girar Bitmap** página permite que você use dois dedos de rotação ou dimensionamento isotropic. O bitmap sempre retém sua taxa de proporção correta. Usando dois dedos para rotação e dimensionamento anisotrópica não funciona muito bem porque o movimento dos dedos é muito semelhante para as duas tarefas.

A primeira grande diferença nesse programa é a lógica de teste de clique. Os programas anteriores usados os `Contains` método de `SKRect` para determinar se o ponto de toque é dentro do retângulo transformado que corresponde ao bitmap. Mas como o usuário manipula o bitmap, o bitmap pode ser girado, e `SKRect` corretamente não pode representar um retângulo girado. Você talvez tenha medo de que a lógica do teste de clique precisa implementar da geometria analítica complexa em vez disso, nesse caso.

No entanto, um atalho está disponível: determinar se um ponto está dentro dos limites de um retângulo transformado é igual a determinar se um ponto transformado inverso se encontra dentro dos limites do retângulo não transformado. Que é um cálculo muito mais fácil, e a lógica pode continuar a usar o conveniente `Contains` método:

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

A lógica para o `Moved` evento é iniciado, como o programa anterior. Dois vetores denominados `oldVector` e `newVector` são calculados com base no anterior e o ponto atual do dedo movimentação e o ponto dinâmico do dedo não mover. Mas, em seguida, ângulos desses vetores são determinados, e a diferença é o ângulo de rotação.

Dimensionamento também pode estar envolvido, portanto, o vetor antigo é girado com base no ângulo de rotação. Agora, a magnitude relativa de dois vetores é o fator de escala. Observe que o mesmo `scale` valor é usado para horizontal e o dimensionamento vertical, de modo que o dimensionamento é isotropic. O `matrix` campo é ajustado a matriz de rotação e uma matriz de escala.

Se seu aplicativo precisa implementar o toque do processamento para um único bitmap (ou outro objeto), você poderá adaptar o código desses três exemplos para seu próprio aplicativo. Mas se você precisar implementar o processamento para vários bitmaps de toque, você provavelmente desejará encapsular essas operações em outras classes de toque.

## <a name="encapsulating-the-touch-operations"></a>Encapsulando as operações de toque

O **manipulação de toque** página demonstra a manipulação de toque de um único bitmap, mas usando vários outros arquivos que encapsulam a maior parte da lógica mostrada acima. O primeiro desses arquivos é o [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeração, que indica os diferentes tipos de manipulação de toque implementada pelo código que você vai estar vendo:

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

`PanOnly` é uma operação de arrastar um dedo que é implementada com a conversão. Todas as opções subsequentes também incluem o movimento panorâmico, mas envolvem dois dedos: `IsotropicScale` é uma operação de pinch resulta no objeto de dimensionamento igualmente nas direções horizontais e verticais. `AnisotropicScale` permite o dimensionamento diferentes.

O `ScaleRotate` opção destina-se com dois dedos de escala e rotação. O dimensionamento é isotropic. Como mencionado anteriormente, implementar dois dedos rotação com o dimensionamento anisotrópica é problemático porque os movimentos de dedo são essencialmente as mesmas.

O `ScaleDualRotate` opção adiciona a rotação com um dedo. Quando um único dedo arrasta o objeto, o objeto arrastado primeiro é girado em torno de seu centro, de modo que o centro do objeto alinhado com o vetor de arrastamento.

O [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) arquivo inclui uma `Picker` com os membros de `TouchManipulationMode` enumeração:

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

Na parte inferior é uma `SKCanvasView` e uma `TouchEffect` anexado à célula única `Grid` que circunscreve a ele.

O [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) arquivo code-behind tem um `bitmap` campo, mas ele não é do tipo `SKBitmap`. O tipo é `TouchManipulationBitmap` (uma classe que você verá em breve):

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

O construtor instancia uma `TouchManipulationBitmap` objeto, passando ao construtor um `SKBitmap` obtido de um recurso inserido. O construtor é concluído, definindo o `Mode` propriedade do `TouchManager` propriedade da `TouchManipulationBitmap` objeto a um membro do `TouchManipulationMode` enumeração.

O `SelectedIndexChanged` manipulador para o `Picker` também define isso `Mode` propriedade:

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

O `TouchAction` manipulador do `TouchEffect` instanciado nas chamadas de arquivo XAML dois métodos no `TouchManipulationBitmap` denominada `HitTest` e `ProcessTouchEvent`:

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

Se o `HitTest` método retorna `true` &mdash; que significa que um dedo tocou a tela dentro da área ocupada por bitmap &mdash; e em seguida, a ID de toque é adicionada ao `TouchIds` coleção. Essa ID representa a sequência de eventos de toque desse dedo até que o dedo Levante a partir da tela. Se vários dedos tocam o bitmap, em seguida, a `touchIds` coleção contém uma ID de toque para cada dedo.

O `TouchAction` manipulador também chama o `ProcessTouchEvent` classe `TouchManipulationBitmap`. É aí que alguns (mas não todos) da interatividade real ocorre o processamento.

O [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) é uma classe wrapper para `SKBitmap` que contém o código para renderizar o bitmap e processar eventos de toque. Ele funciona em conjunto com mais generalizada código em um `TouchManipulationManager` classe (que você verá em breve).

O `TouchManipulationBitmap` construtor salva a `SKBitmap` e cria uma instância de duas propriedades, o `TouchManager` propriedade do tipo `TouchManipulationManager` e o `Matrix` propriedade do tipo `SKMatrix`:

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

Isso `Matrix` propriedade é a transformação acumulada resultante de toda a atividade de toque. Como você verá, cada evento de toque é resolvido em uma matriz, que, em seguida, é concatenada com o `SKMatrix` valor armazenado pelo `Matrix` propriedade.

O `TouchManipulationBitmap` objeto desenha a mesmo no seu `Paint` método. O argumento é um `SKCanvas` objeto. Isso `SKCanvas` talvez já tenha uma transformação aplicada a ele, portanto, o `Paint` método concatena o `Matrix` propriedade associado com o bitmap para a transformação existente e restaura a tela quando tiver concluído:

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

O `HitTest` método retorna `true` se o usuário toca na tela em um ponto dentro dos limites do bitmap. Isso usa a lógica mostrada anteriormente na **rotação do Bitmap** página:

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

O segundo método público no `TouchManipulationBitmap` é `ProcessTouchEvent`. Quando este método é chamado, ele já foi estabelecido que o evento de toque pertence a esse bitmap específico. O método mantém um dicionário [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que é simplesmente o ponto anterior e o novo ponto de cada dedo:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Aqui está o dicionário e o `ProcessTouchEvent` próprio método:

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

No `Moved` e `Released` eventos, as chamadas de método `Manipulate`. Nesses momentos, o `touchDictionary` contém um ou mais `TouchManipulationInfo` objetos. Se o `touchDictionary` contém um item, é provável que o `PreviousPoint` e `NewPoint` valores são diferentes e representam a movimentação de um dedo. Se vários dedos tocam o bitmap, o dicionário contiver mais de um item, mas apenas um desses itens tem diferentes `PreviousPoint` e `NewPoint` valores. Todo o resto ter igual `PreviousPoint` e `NewPoint` valores.

Isso é importante: O `Manipulate` método pode assumir que está processando a movimentação de um só dedo. No momento desta chamada nenhum dos outros dedos estão mudando e se eles realmente estão movendo (como é provável), esses movimentos serão processados em futuras chamadas para `Manipulate`.

O `Manipulate` método primeiro copia o dicionário para uma matriz para sua conveniência. Ignora qualquer coisa que não sejam as duas primeiras entradas. Se mais de dois dedos estão tentando para manipular o bitmap, os outros são ignorados. `Manipulate` é o membro final do `TouchManipulationBitmap`:

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

Se um dedo está manipulando o bitmap `Manipulate` chamadas a `OneFingerManipulate` método o `TouchManipulationManager` objeto. Para dois dedos, ele chama `TwoFingerManipulate`. Os argumentos para esses métodos são os mesmos: o `prevPoint` e `newPoint` argumentos representam o dedo que está se movendo. Mas o `pivotPoint` argumento é diferente para as duas chamadas:

Para a manipulação com um dedo, o `pivotPoint` é o centro do bitmap. Isso é para permitir a rotação com um dedo. Para a manipulação de dois dedos, o evento indica o movimento de apenas um dedo, para que o `pivotPoint` é o dedo que não está se movendo.

Em ambos os casos `TouchManipulationManager` retorna um `SKMatrix` valor, que o método concatena com o atual `Matrix` propriedade que `TouchManipulationPage` usa para renderizar o bitmap.

`TouchManipulationManager` é generalizado e não usa a nenhum outro arquivo, exceto `TouchManipulationMode`. Você poderá usar essa classe sem alteração em seus próprios aplicativos. Ele define uma única propriedade do tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


No entanto, você provavelmente desejará evitar o `AnisotropicScale` opção. É muito fácil com esta opção para manipular o bitmap para que um dos fatores de dimensionamento se torna zero. Isso faz com que o bitmap desaparecem da visão, nunca para retornar. Se você realmente precisar anisotrópica de dimensionamento, você desejará aprimorar a lógica para evitar resultados indesejáveis.

`TouchManipulationManager` faz uso de vetores, mas como não há nenhuma `SKVector` estrutura de SkiaSharp, `SKPoint` é usado em vez disso. `SKPoint` suporta que o operador de subtração e o resultado podem ser tratado como um vetor. É a lógica de apenas específica do vetor que precisava ser adicionados um `Magnitude` cálculo:

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

Sempre que a rotação tiver sido selecionada, ambos os métodos de manipulação com um dedo e com dois dedos lidar com a rotação pela primeira vez. Se for detectada nenhuma rotação, em seguida, o componente de rotação efetivamente é removido. O que sobra é interpretado como movimento panorâmico e dimensionamento.

Aqui está o `OneFingerManipulate` método. Se a rotação com um dedo não foi habilitada, então a lógica é simples &mdash; simplesmente usa o ponto anterior e o novo ponto para construir um vetor chamado `delta` que corresponde exatamente a tradução. Com a rotação com um dedo habilitada, o método usa o ângulos do ponto do pivô (o centro do bitmap) para o ponto anterior e o novo ponto para construir uma matriz de rotação:

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

No `TwoFingerManipulate` método, o ponto dinâmico é a posição do dedo que não está se movendo nesse evento de toque particular. A rotação é muito semelhante para a rotação com um dedo e, em seguida, o vetor chamado `oldVector` (com base no ponto anterior) é ajustado para a rotação. A movimentação restante é interpretada como o dimensionamento:

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

Você observará que não há nenhuma conversão explícita nesse método. No entanto, ambos os `MakeRotation` e `MakeScale` métodos se baseiam no ponto dinâmico, e que inclui a conversão implícita. Se você estiver usando dois dedos em bitmap e arrastá-los na mesma direção, `TouchManipulation` obterá uma série de eventos de toque, alternando entre dois dedos. Como cada dedo se move em relação as outros, escala ou rotação resultados, mas ele é negado por um movimento do outro dedo e o resultado é a tradução.

A única parte restante dos **manipulação de toque** página é a `PaintSurface` manipulador no `TouchManipulationPage` arquivo code-behind. Isso chama o `Paint` método da `TouchManipulationBitmap`, que se aplica a matriz que representa a atividade da acumulados de toque:

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

O `PaintSurface` manipulador conclui exibindo um `MatrixDisplay` objeto mostrando a matriz da acumulados de toque:

[![](touch-images/touchmanipulation-small.png "Captura de tela da página de manipulação de toque tripla")](touch-images/touchmanipulation-large.png#lightbox "tripla captura de tela da página de manipulação de toque")

## <a name="manipulating-multiple-bitmaps"></a>Manipulando várias Bitmaps

Uma das vantagens de isolar o código de processamento de toque em classes, como `TouchManipulationBitmap` e `TouchManipulationManager` é a capacidade de reutilizar essas classes em um programa que permite ao usuário manipular vários bitmaps.

O **exibição de dispersão de Bitmap** página demonstra como isso é feito. Em vez de definir um campo do tipo `TouchManipulationBitmap`, o [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe define um `List` de objetos de bitmap:

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

O construtor carrega em todos os bitmaps disponíveis como recursos incorporados e adiciona-os para o `bitmapCollection`. Observe que o `Matrix` propriedade é inicializada em cada `TouchManipulationBitmap` do objeto, para que os cantos superior esquerdo de cada bitmap são deslocados por 100 pixels.

O `BitmapScatterView` página também precisa lidar com eventos de toque para vários bitmaps. Em vez de definir uma `List` do toque IDs de manipulado no momento `TouchManipulationBitmap` objetos, esse programa requer um dicionário:

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

Observe como o `Pressed` lógica percorre o `bitmapCollection` na ordem inversa. Os bitmaps geralmente se sobrepõem uns aos outros. Os bitmaps posteriormente na coleção visualmente se encontra na parte superior de bitmaps anteriormente na coleção. Se houver vários bitmaps sob o dedo que pressiona na tela, um nível mais alto deve ser aquele que é manipulado por desse dedo.

Observe também que o `Pressed` lógica mova esse bitmap ao final da coleção para que ele se mova visualmente na parte superior da pilha de outros bitmaps.

No `Moved` e `Released` eventos, o `TouchAction` chamadas do manipulador a `ProcessingTouchEvent` método na `TouchManipulationBitmap` assim como o programa anterior.

Por fim, o `PaintSurface` chamadas do manipulador de `Paint` método de cada `TouchManipulationBitmap` objeto:

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

O código faz um loop por meio da coleção e exibe a pilha de bitmaps desde o início da coleção ao final:

[![](touch-images/bitmapscatterview-small.png "Captura de tela da página de exibição de dispersão de Bitmap tripla")](touch-images/bitmapscatterview-large.png#lightbox "tripla captura de tela da página de exibição de dispersão de Bitmap")

## <a name="single-finger-scaling"></a>Dimensionamento de dedo

Uma operação de dimensionamento geralmente requer um gesto de pinçagem com dois dedos. No entanto, é possível implementar o dimensionamento com um único dedo, fazendo com que o dedo mover os cantos de um bitmap.

Isso é demonstrado na **único dedo canto dimensionamento** página. Porque este exemplo usa um tipo um pouco diferentes de dimensionamento que que implementado de `TouchManipulationManager` classe, não usa essa classe ou o `TouchManipulationBitmap` classe. Em vez disso, toda a lógica de toque é no arquivo code-behind. Isso é lógico um pouco mais simples do que o usual porque ele acompanha apenas um dedo em um tempo e simplesmente ignora qualquer secundários dedos que podem ser tocar na tela.

O [ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) página instancia o `SKCanvasView` classe e cria um `TouchEffect` objeto para acompanhar eventos de toque:

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

O [ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) um recurso de bitmap do arquivo é carregado o **mídia** diretório e exibe-o usando um `SKMatrix` objeto definido como um campo:

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

Isso `SKMatrix` objeto é modificado pela lógica de toque, mostrada abaixo.

O restante do arquivo code-behind é o `TouchEffect` manipulador de eventos. Ele começa, convertendo o local atual do dedo para um `SKPoint` valor. Para o `Pressed` verifica se o manipulador de tipo de ação, que não há outros dedos está tocando a tela, e que o dedo estiver dentro dos limites do bitmap.

A parte fundamental do código é um `if` instrução que envolvem duas chamadas para o `Math.Pow` método. Este matemática verifica se a localização do dedo está fora de uma elipse que preenche o bitmap. Nesse caso, que é uma operação de dimensionamento. Está perto de um dos cantos do bitmap e um ponto dinâmico for determinado que o canto oposto. Se o dedo estiver dentro dessa elipse, é uma operação de panorâmica regular:

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

O `Moved` tipo de ação calcula uma matriz correspondente para a atividade de toque do momento em que o dedo pressionado a tela até o momento. Ele concatena dessa matriz com a matriz em vigor no momento em que o dedo pressionada pela primeira vez o bitmap. A operação de dimensionamento sempre é relativo ao canto oposto aquele que o dedo tocado.

Para bitmaps pequenos ou alongada, uma elipse interior pode ocupar a maior parte do bitmap e deixar as áreas pequenas nos cantos para dimensionar o bitmap. Você pode preferir uma abordagem um pouco diferente, nesse caso, você pode substituir esse toda `if` bloco define `isScaling` para `true` com este código:

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

Esse código efetivamente divide a área do bitmap em uma forma de losango interiores e quatro triângulos nos cantos. Isso permite que muito maiores áreas nos cantos para capturar e dimensionar o bitmap.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
