---
title: Corte bitmaps de SkiaSharp
description: Saiba como usar SkiaSharp para criar uma interface do usuário para desribing interativamente um retângulo de corte.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: cf31f3bd6f84a040d21420e865737417c374d947
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077906"
---
# <a name="cropping-skiasharp-bitmaps"></a>Corte bitmaps de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

O [ **criação e o desenho de Bitmaps de SkiaSharp** ](drawing.md) artigo descrito como um `SKBitmap` objeto pode ser passado para um `SKCanvas` construtor. Qualquer método de desenho chamado em gráficos de causas dessa tela a ser renderizado no bitmap. Esses métodos de desenho incluem `DrawBitmap`, o que significa que essa técnica permite a transferência de parte ou todo um bitmap para outro bitmap, talvez com transformações aplicadas.

Você pode usar essa técnica para cortar um bitmap chamando o [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método com retângulos de origem e de destino:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

No entanto, os aplicativos que implementam o corte geralmente fornecem uma interface do usuário selecionar o retângulo de corte de forma interativa:

![Amostra de corte](cropping-images/CroppingSample.png "corte de exemplo")

Este artigo se concentra na interface.

## <a name="encapsulating-the-cropping-rectangle"></a>Encapsulando o retângulo de corte

É útil isolar parte da lógica de corte em uma classe chamada `CroppingRectangle`. Os parâmetros do construtor incluem um retângulo máximo, que geralmente é o tamanho do bitmap que está sendo cortado, e uma taxa de proporção opcional. Primeiro, o construtor define um retângulo de corte inicial, o que torna o público na `Rect` propriedade do tipo `SKRect`. Esse retângulo de corte inicial é 80% da largura e altura do retângulo de bitmap, mas, em seguida, ele será ajustado se uma taxa de proporção é especificada:

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

Uma informação útil que `CroppingRectangle` também disponibiliza é uma matriz de `SKPoint` valores correspondentes para os quatro cantos do retângulo de corte na ordem do canto superior esquerdo, superior direito, inferior direito e inferior esquerdo:

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

Esta matriz é usada no método seguinte, que é chamado `HitTest`. O `SKPoint` parâmetro é um ponto correspondente a um toque de dedo ou um clique do mouse. O método retorna um índice (0, 1, 2 ou 3) correspondente ao que o ponteiro do mouse ou o dedo tocado, dentro de uma distância fornecida pelo canto o `radius` parâmetro: 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

Se o ponto de toque ou mouse não estava dentro `radius` unidades de qualquer canto, o método retorna &ndash;1.

O método final no `CroppingRectangle` é chamado `MoveCorner`, que é chamado em resposta a touch ou movimentação do mouse. Os dois parâmetros seguintes indicam o índice do canto que está sendo movido e o novo local do que o canto. Na primeira metade do método ajusta o retângulo de corte com base no novo local do canto, mas sempre dentro dos limites de `maxRect`, que é o tamanho do bitmap. Essa lógica também leva em conta o `MINIMUM` campo para evitar o retângulo de corte de recolhimento em nada:

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

A segunda metade do método ajusta-se para a taxa de proporção opcional.

Tenha em mente que tudo o que essa classe está em unidades de pixels.

## <a name="a-canvas-view-just-for-cropping"></a>Um modo de exibição de tela apenas de corte

O `CroppingRectangle` classe que acabamos de ver é usada pelo `PhotoCropperCanvasView` classe, que deriva de `SKCanvasView`. Essa classe é responsável por exibir o bitmap e o retângulo de corte, bem como a manipulação de eventos de toque ou mouse para alterar o retângulo de corte.

O `PhotoCropperCanvasView` construtor requer um bitmap. Uma taxa de proporção é opcional. O construtor instancia um objeto do tipo `CroppingRectangle` com base nesse bitmap e a taxa de proporção e o salva como um campo:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

Porque essa classe deriva `SKCanvasView`, ele não precisa instalar um manipulador para o `PaintSurface` eventos. Em vez disso, ele pode substituir seu `OnPaintSurface` método. O método exibe o bitmap e usa alguns `SKPaint` objetos salvos como campos para desenhar o retângulo de corte atual:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

O código a `CroppingRectangle` classe bases do retângulo de recorte no tamanho de pixel do bitmap. No entanto, a exibição do bitmap pelo `PhotoCropperCanvasView` classe é dimensionado com base no tamanho da área de exibição. O `bitmapScaleMatrix` calculado no `OnPaintSurface` substituir mapas de pixels de bitmap para o tamanho e posição do bitmap, como ele é exibido. Esta matriz é usada para transformar o retângulo de corte para que ela possa ser exibida em relação ao bitmap.

A última linha do `OnPaintSurface` substituição leva o inverso dos `bitmapScaleMatrix` e salva-o como o `inverseBitmapMatrix` campo. Isso é usado para processamento de toque.

Um `TouchEffect` objeto é instanciado como um campo, e o construtor anexa um manipulador para o `TouchAction` evento, mas o `TouchEffect` precisa ser adicionada ao `Effects` coleção da _pai_ do `SKCanvasView`derivados, para que terminar `OnParentSet` substituir:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

Os eventos de toque é processado pelo `TouchAction` manipulador estão em unidades independentes de dispositivo. Primeiro, eles precisam ser convertidas em pixels usando o `ConvertToPixel` método na parte inferior da classe e, em seguida, convertido em `CroppingRectangle` unidades usando `inverseBitmapMatrix`.

Para `Pressed` eventos, o `TouchAction` chamadas do manipulador a `HitTest` método `CroppingRectangle`. Se isso retornar um índice diferente de &ndash;1, em seguida, um dos cantos do retângulo de corte está sendo manipulado. Que o índice e um deslocamento do ponto de toque real do canto é armazenada em uma `TouchPoint` do objeto e adicionado ao `touchPoints` dicionário.

Para o `Moved` evento, o `MoveCorner` método `CroppingRectangle` é chamado para mover o canto, com possíveis ajustes para a taxa de proporção.

A qualquer momento, um programa que usa `PhotoCropperCanvasView` pode acessar o `CroppedBitmap` propriedade. Esta propriedade usa a `Rect` propriedade do `CroppingRectangle` para criar um novo bitmap do tamanho cortado. A versão do `DrawBitmap` com origem e destino retângulos, em seguida, extrai um subconjunto do bitmap original:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>O modo de exibição de tela de cropper fotos de hospedagem

Com essas duas classes manipulando a lógica de corte, o **cortar fotos** página na **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** aplicativo tem muito pouco trabalho a fazer. O arquivo XAML instancia uma `Grid` para hospedar o `PhotoCropperCanvasView` e uma **feito** botão:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

O `PhotoCropperCanvasView` não pode ser instanciada no arquivo XAML, porque ele requer um parâmetro de tipo `SKBitmap`.

Em vez disso, o `PhotoCropperCanvasView` é instanciado no construtor do arquivo code-behind usando um dos bitmaps de recurso:

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

O usuário pode manipular o retângulo de corte:

[![O Cropper 1 de fotos](cropping-images/PhotoCropping1.png "Cropper 1 de fotos")](cropping-images/PhotoCropping1-Large.png#lightbox)

Quando um retângulo de corte boa tiver sido definido, clique no **feito** botão. O `Clicked` manipulador obtém o bitmap cortado do `CroppedBitmap` propriedade de `PhotoCropperCanvasView`e substitui todo o conteúdo da página com um novo `SKCanvasView` objeto que exibe esse bitmap cortado:

[![O Cropper 2 de fotos](cropping-images/PhotoCropping2.png "Cropper 2 de fotos")](cropping-images/PhotoCropping2-Large.png#lightbox)

Tente definir o segundo argumento do `PhotoCropperCanvasView` para 1.78f (por exemplo):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Você verá o retângulo de corte restrito a uma taxa de proporção de 16 a 9 característica de televisão de alta definição.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Dividindo um bitmap em blocos

Uma versão de xamarin. Forms do famoso quebra-cabeça de 14 a 15 apareceu no Capítulo 22 do livro [ _criação de aplicativos móveis com xamarin. Forms_ ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) e pode ser baixado como [  **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). No entanto, o quebra-cabeça se torna mais divertido (e geralmente mais desafiador) quando ela se basear em uma imagem de sua própria biblioteca de fotos.

Esta versão do quebra-cabeça 14 ou 15 é parte do **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** aplicativo e consiste em uma série de páginas intitulado **foto do quebra-cabeça**.

O **PhotoPuzzlePage1.xaml** arquivo consiste em um `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

O arquivo code-behind implementa um `Clicked` manipulador que usa o `IPhotoLibrary` serviço de dependência para permitir que o usuário escolher uma foto da biblioteca de fotos:

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

O método, em seguida, navega para `PhotoPuzzlePage2`, passando para o construtor o bitmap selecionado.

É possível que a foto selecionada da biblioteca não é orientada conforme ele apareceu na biblioteca de fotos, mas é girado ou de cabeça para baixo. (Isso é especialmente um problema com dispositivos iOS.) Por esse motivo, `PhotoPuzzlePage2` permite girar a imagem para uma orientação desejada. O arquivo XAML contém três botões rotulados **90&#x00B0; à direita** (ou seja, no sentido horário), **90&#x00B0; esquerda** (no sentido anti-horário), e **feito**.

O arquivo code-behind implementa a lógica de rotação do bitmap mostrada no artigo  **[criação e desenho em SkiaSharp Bitmaps](drawing.md#rotating-bitmaps)**. O usuário pode girar a imagem 90 graus no sentido horário ou anti-horário qualquer número de vezes: 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

Quando o usuário clica o **feito** botão, o `Clicked` manipulador navega para `PhotoPuzzlePage3`, passando o bitmap invertido final no construtor da página.

`PhotoPuzzlePage3` permite que a foto ser recortada. O programa requer um bitmap quadrado para dividir em uma grade 4 por 4 de blocos.

O **PhotoPuzzlePage3.xaml** arquivo contém uma `Label`, uma `Grid` para hospedar o `PhotoCropperCanvasView`e outro **feito** botão:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

O arquivo code-behind instancia o `PhotoCropperCanvasView` com o bitmap passado para seu construtor. Observe que um 1 é passado como o segundo argumento para `PhotoCropperCanvasView`. Essa taxa de proporção de 1 força o retângulo de corte para ser um quadrado:

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

O **feito** manipulador do botão obtém a largura e altura do bitmap cortado (esses dois valores devem ser o mesmo) e, em seguida, a divide em 15 bitmaps separados, cada um deles é 1 e 4 a largura e altura do original. (O último dos bitmaps de 16 possíveis não é criado). O `DrawBitmap` método com o retângulo de origem e de destino permite que um bitmap a ser criado com base em um subconjunto de um bitmap maior.

## <a name="converting-to-xamarinforms-bitmaps"></a>Convertendo em bitmaps de xamarin. Forms

No `OnDoneButtonClicked` método, a matriz criada para os bitmaps de 15 é do tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` é o tipo de base do xamarin. Forms que encapsula um bitmap. Felizmente, SkiaSharp permite a conversão de bitmaps de SkiaSharp em xamarin. Forms bitmaps. O **SkiaSharp.Views.Forms** assembly define um [ `SKBitmapImageSource` ](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) classe que deriva de `ImageSource` , mas podem ser criados com base em um SkiaSharp `SKBitmap` objeto. `SKBitmapImageSource` até mesmo define conversões entre `SKBitmapImageSource` e `SKBitmap`e isso como `SKBitmap` são armazenados em uma matriz como bitmaps de xamarin. Forms:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Essa matriz de bitmaps é passado como um construtor para `PhotoPuzzlePage4`. Essa página é totalmente xamarin. Forms e não usa qualquer SkiaSharp. Ele é muito semelhante à [ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), portanto, não será descrita aqui, mas ele exibe sua foto selecionada dividida em blocos quadrados 15:

[![1 de quebra-cabeça de fotos](cropping-images/PhotoPuzzle1.png "quebra-cabeça 1 de fotos")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Pressionar o **Randomize** botão combina o backup de todos os blocos:

[![2 de quebra-cabeça de fotos](cropping-images/PhotoPuzzle2.png "quebra-cabeça 2 de fotos")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Agora você pode colocá-los na ordem correta. Os blocos na mesma linha ou coluna como o quadrado em branco podem ser tocados movê-los para o quadrado em branco. 

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
