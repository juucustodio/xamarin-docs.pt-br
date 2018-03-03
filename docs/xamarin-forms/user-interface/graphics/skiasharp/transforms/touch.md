---
title: "Manipulações de toque"
description: "Transformações de matriz de uso para implementar toque arrastando esmagamento e rotação"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: b418e0179c95a424c88d5f5063a09f984bb13ec0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="touch-manipulations"></a>Manipulações de toque

_Transformações de matriz de uso para implementar toque arrastando esmagamento e rotação_

Em ambientes de multitoque como os dispositivos móveis, os usuários geralmente usar seus dedos para manipular objetos na tela. Gestos comuns, como um arrastar um dedo e um pouco de dois dedos podem mover e dimensionar objetos ou até mesmo girar. Esses gestos geralmente são implementados usando matrizes de transformação, e este artigo mostra como fazer isso.

![](touch-images/touchmanipulationsexample.png "Um bitmap sujeitado a conversão, escala e rotação")

## <a name="manipulating-one-bitmap"></a>Manipulando um Bitmap

O **Touch manipulação** página demonstra manipulações de toque em um único bitmap.
Este exemplo usa o efeito de controle de toque apresentado no artigo [invocar eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Vários outros arquivos dão suporte para o **Touch manipulação** página. A primeira é a [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeração, que indica os diferentes tipos de manipulação de toque implementado pelo código que você terá:

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

`PanOnly` é um arrastar um dedo que é implementado com a conversão. Todas as opções subsequentes também incluem o movimento panorâmico mas envolvem dois dedos: `IsotropicScale` é uma operação de aperto resulta no objeto de dimensionamento igualmente nas direções horizontais e verticais. `AnisotropicScale` permite o dimensionamento diferentes.

O `ScaleRotate` opção é para dois dedos escala e rotação. O dimensionamento é isotropic. Implementação de dois dedos rotação com dimensionamento anisotrópico é problemático porque os movimentos dedo são essencialmente as mesmas.

O `ScaleDualRotate` opção adiciona um dedo rotação. Quando um único dedo arrasta o objeto, o objeto arrastado primeiro for girado em torno de seu centro para que o centro do objeto se alinhe com o vetor de arrastar.

O [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) inclui um `Picker` com os membros de `TouchManipulationMode` enumeração:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
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
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
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

Na parte inferior é uma `SKCanvasView` e um `TouchEffect` anexado à célula única `Grid` que inclui-lo.

O [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) arquivo code-behind tem um `bitmap` campo, mas ele não é do tipo `SKBitmap`. O tipo é `TouchManipulationBitmap` (uma classe que você verá em breve):

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            SKBitmap bitmap = SKBitmap.Decode(skStream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

O construtor instancia um `TouchManipulationBitmap` passagem para o construtor do objeto um `SKBitmap` obtido de um recurso incorporado. Construtor conclui definindo o `Mode` propriedade do `TouchManager` propriedade do `TouchManipulationBitmap` objeto a um membro do `TouchManipulationMode` enumeração.

O `SelectedIndexChanged` manipulador para o `Picker` também define esta `Mode` propriedade:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

O `TouchAction` manipulador do `TouchEffect` instanciado nas chamadas de arquivo XAML dois métodos em `TouchManipulationBitmap` chamado `HitTest` e `ProcessTouchEvent`:

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

Se o `HitTest` método retorna `true` & #x 2014; indicando que um dedo tocou tela na área ocupada pela bitmap & #x 2014; em seguida, a ID de toque é adicionada para o `TouchIds` coleção. Essa ID representa a sequência de eventos de toque para esse dedo até levanta o dedo na tela. Se vários dedos tocam o bitmap, o `touchIds` coleção contém uma ID de toque para cada dedo.

O `TouchAction` manipulador também chama o `ProcessTouchEvent` classe em `TouchManipulationBitmap`. Isso é onde alguns (mas não todos) do toque real processamento ocorre.

O [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe é uma classe wrapper para `SKBitmap` que contém o código para renderizar o bitmap e processar eventos de toque. Ele funciona em conjunto com mais generalizada de código em um `TouchManipulationManager` classe (que você verá em breve).

O `TouchManipulationBitmap` construtor salva o `SKBitmap` e cria duas propriedades, o `TouchManager` propriedade do tipo `TouchManipulationManager` e `Matrix` propriedade do tipo `SKMatrix`:

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

Isso `Matrix` propriedade é a transformação acumulada resultante de todas as atividades de toque. Como você verá cada evento de toque é resolvido como uma matriz, que é, em seguida, concatenada com a `SKMatrix` valor armazenado pelo `Matrix` propriedade.

O `TouchManipulationBitmap` objeto se desenha seu `Paint` método. O argumento é uma `SKCanvas` objeto. Isso `SKCanvas` talvez já tenha uma transformação aplicada a ele, portanto, o `Paint` método concatena o `Matrix` propriedade associado com o bitmap para a transformação existente e restaura a tela quando tiver concluído:

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

O `HitTest` método retorna `true` se o usuário toca a tela em um ponto dentro dos limites do bitmap. Como o usuário manipula o bitmap, o bitmap pode ser girado, ou até mesmo (por meio de uma combinação de anisotrópico escala e rotação) ser na forma de um paralelogramo. Você talvez tenha medo que o `HitTest` método precisa implementar bastante complexa da geometria analítica nesse caso.

No entanto, um atalho está disponível:

Determinar se um ponto está dentro dos limites de um retângulo transformado é igual a determinar se um ponto transformado inverso está dentro dos limites do retângulo não transformado. Um cálculo muito mais fácil e ele pode usar o conveniente `Contains` método definido pelo `SKRect`:

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

O segundo método público em `TouchManipulationBitmap` é `ProcessTouchEvent`. Quando este método é chamado, ele já foi estabelecido que o evento de toque pertence a este bitmap específico. O método mantém um dicionário de [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que é simplesmente o ponto anterior e o novo ponto de cada dedo:

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

No `Moved` e `Released` eventos, as chamadas de método `Manipulate`. Nos seguintes momentos, o `touchDictionary` contém um ou mais `TouchManipulationInfo` objetos. Se o `touchDictionary` contém um item, é provável que o `PreviousPoint` e `NewPoint` valores são diferentes e representam a movimentação de um dedo. Se vários dedos tocam o bitmap, o dicionário contiver mais de um item, mas apenas um desses itens tem diferentes `PreviousPoint` e `NewPoint` valores. Todo o resto têm igual `PreviousPoint` e `NewPoint` valores.

Isso é importante: O `Manipulate` método pode assumir que está processando a movimentação de apenas um dedo. No momento desta chamada em nenhum dos outros dedos estiver movendo e se realmente estão se movendo (como é provável que), essas movimentações serão processadas nas chamadas futuras para `Manipulate`.

O `Manipulate` método primeiro copia o dicionário para uma matriz para sua conveniência. Ignora qualquer coisa que não sejam as duas primeiras entradas. Se mais de dois dedos estão tentando para manipular o bitmap, os demais serão ignorados. `Manipulate` é o membro final do `TouchManipulationBitmap`:

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

Se um dedo é manipular o bitmap, `Manipulate` chama o `OneFingerManipulate` método o `TouchManipulationManager` objeto. Para dois dedos, ele chama `TwoFingerManipulate`. Os argumentos para esses métodos são os mesmos: o `prevPoint` e `newPoint` argumentos representam o dedo que está sendo movido. Mas o `pivotPoint` argumento é diferente para as duas chamadas:

Para um dedo manipulação, o `pivotPoint` é o centro do bitmap. Isso é para permitir um dedo rotação. Para a manipulação de dois dedos, o evento indica a movimentação de apenas um dedo, para que o `pivotPoint` é o dedo que não está migrando.

Em ambos os casos, `TouchManipulationManager` retorna um `SKMatrix` valor, que o método concatena com atual `Matrix` propriedade que `TouchManipulationPage` usa para processar o bitmap.

`TouchManipulationManager` é generalizada e não usa a nenhum outro arquivo exceto `TouchManipulationMode`. Você poderá usar essa classe sem alteração em seus próprios aplicativos. Define uma única propriedade de tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


No entanto, você provavelmente desejará evitar o `AnisotropicScale` opção. É muito fácil com essa opção para manipular o bitmap para que um dos fatores de dimensionamento se torna zero. Isso torna o bitmap desaparecem da Vista, nunca para retornar. Se você realmente precisar anisotrópico dimensionamento, você desejará aumentar a lógica para evitar resultados indesejáveis.

`TouchManipulationManager` faz uso de vetores, mas como não há nenhum `SKVector` estrutura em SkiaSharp, `SKPoint` é usado em vez disso. `SKPoint` o operador de subtração e o resultado podem ser tratado como um vetor de suporte. A lógica de apenas específicas do vetor que precisa ser adicionado é um `Magnitude` cálculo:

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

Sempre que a rotação tiver sido selecionada, ambos os métodos de manipulação de dedo um e dois dedos a lidar com a rotação primeiro. Se qualquer rotação for detectada, o componente de rotação efetivamente é removido. O que sobra é interpretado como o movimento panorâmico e a escala.

Aqui está o `OneFingerManipulate` método. Se um dedo rotação não foi habilitada, em seguida, a lógica é simples e #x 2014; ele simplesmente usa o ponto anterior e o novo ponto para construir um vetor chamado `delta` que corresponde exatamente a tradução. Com a rotação de um dedo habilitada, o método usa o ângulos do ponto dinâmico (o centro do bitmap) para o ponto anterior e o novo ponto de para construir uma matriz de rotação:

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

No `TwoFingerManipulate` método, o ponto pivô é a posição do dedo que não esteja migrando neste evento toque específico. A rotação é muito semelhante à rotação de um dedo e, em seguida, o vetor chamado `oldVector` (com base no ponto anterior) é ajustada para a rotação. A movimentação restante é interpretada como dimensionamento:

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

Você observará que não há nenhuma conversão explícita nesse método. No entanto, ambos os `MakeRotation` e `MakeScale` métodos baseiam-se no ponto dinâmico, e que inclui a conversão implícita. Se você estiver usando dois dedos em bitmap e arrastando-as na mesma direção, `TouchManipulation` obterá uma série de eventos de toque alternando entre dois dedos. Como cada dedo move em relação as outros, dimensionamento ou rotação resultados, mas ele será negado pela movimentação do outro dedo e o resultado é a tradução.

A única parte restante do **Touch manipulação** página é a `PaintSurface` manipulador no `TouchManipulationPage` arquivo code-behind. Chama o `Paint` método o `TouchManipulationBitmap`, que se aplica a matriz que representa a atividade de toque acumulado:

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

O `PaintSurface` manipulador conclui exibindo um `MatrixDisplay` mostrando a matriz de toque acumulado de objeto:

[![](touch-images/touchmanipulation-small.png "Tripla captura de tela da página de manipulação de toque")](touch-images/touchmanipulation-large.png "tripla captura de tela da página de manipulação de toque")

## <a name="manipulating-multiple-bitmaps"></a>Manipulando várias Bitmaps

Uma das vantagens de isolar o código de processamento de toque em classes como `TouchManipulationBitmap` e `TouchManipulationManager` é a capacidade de reutilizar essas classes em um programa que permite ao usuário manipular vários bitmaps.

O **exibição de dispersão de Bitmap** página demonstra como fazer isso. Em vez de definir um campo do tipo `TouchManipulationBitmap`, o [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe define um `List` de objetos de bitmap:

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
                using (SKManagedStream skStream = new SKManagedStream(stream))
                {
                    SKBitmap bitmap = SKBitmap.Decode(skStream);
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

O construtor carrega em todos os bitmaps disponíveis como recursos incorporados e adiciona-os para o `bitmapCollection`. Observe que o `Matrix` propriedade é inicializada em cada `TouchManipulationBitmap` do objeto, para os cantos superior esquerdo de cada bitmap são separados por 100 pixels.

O `BitmapScatterView` página também precisa manipular eventos de toque para vários bitmaps. Em vez de definir um `List` de toque IDs de manipulados atualmente `TouchManipulationBitmap` objetos, esse programa requer um dicionário:

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

Observe como o `Pressed` lógica percorre o `bitmapCollection` na ordem inversa. Os bitmaps geralmente se sobrepõem uns aos outros. Os bitmaps posteriormente na coleção visualmente ficam sobre os bitmaps anteriormente na coleção. Se houver vários bitmaps sob o dedo pressiona na tela, um nível superior deve ser aquele que é manipulado por esse dedo.

Observe também que o `Pressed` lógica mova esse bitmap ao final da coleção para que ele visualmente move para a parte superior da pilha de outros bitmaps.

No `Moved` e `Released` eventos, o `TouchAction` chamadas do manipulador do `ProcessingTouchEvent` método `TouchManipulationBitmap` assim como o programa anterior.

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

O código executa um loop pela coleção e exibe a pilha de bitmaps desde o início da coleção ao final:

[![](touch-images/bitmapscatterview-small.png "Captura de tela da página de visualização de dispersão de Bitmap tripla")](touch-images/bitmapscatterview-large.png "tripla captura de tela da página de exibição de dispersão de Bitmap")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [Invocação de eventos de efeitos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
