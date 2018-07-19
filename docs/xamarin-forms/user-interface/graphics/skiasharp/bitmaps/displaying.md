---
title: Exibição de bitmaps de SkiaSharp
description: Saiba como exibir SkiaSharp bitmaps em pixel dimensiona e expandidas para preencher a retângulos, preservando a taxa de proporção.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 31a5841c78b68b916e3df3d1d6598b86b0ec8bd4
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39131150"
---
# <a name="displaying-skiasharp-bitmaps"></a>Exibição de bitmaps de SkiaSharp

O assunto de bitmaps de SkiaSharp foi introduzido no artigo  **[Noções básicas de Bitmap no SkiaSharp](../basics/bitmaps.md)**. Esse artigo mostrou três maneiras de bitmaps de carga e de três maneiras de exibir bitmaps. Este artigo examina as técnicas para carregar bitmaps e o uso de se aprofundar a `DrawBitmap` métodos de `SKCanvas`.

![Exibindo a amostra](displaying-images/DisplayingSample.png "exibindo o exemplo")

O `DrawBitmapLattice` e `DrawBitmapNinePatch` métodos são discutidos neste artigo  **[segmentada a exibição de bitmaps de SkiaSharp](segmented.md)**.

Os exemplos nesta página são obtidos de **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** aplicativo. Na home page do aplicativo, escolha **SkiaSharp Bitmaps**e, em seguida, vá para o **Bitmaps exibindo** seção.

## <a name="loading-a-bitmap"></a>Carregar um bitmap

Um bitmap usado por um aplicativo de SkiaSharp geralmente vem de uma das três fontes diferentes:

- Na Internet
- De um recurso incorporado no executável
- Da biblioteca de fotos do usuário

Também é possível que um aplicativo de SkiaSharp criar um novo bitmap e, em seguida, desenhar nele ou definir os bits de bitmap algoritmicamente. Essas técnicas são discutidas nos artigos **[criação e desenho em SkiaSharp Bitmaps](drawing.md)** e **[acessando Pixels do Bitmap de SkiaSharp](pixel-bits.md)**.

Nos seguintes três exemplos de código de carregamento de um bitmap, a classe deve para conter um campo do tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Como o artigo **[Noções básicas de Bitmap no SkiaSharp](../basics/bitmaps.md)** mencionado, a melhor maneira de carregar um bitmap na Internet é com o [ `HttpClient` ](xref:System.Net.Http.HttpClient) classe. Uma única instância da classe pode ser definida como um campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Ao usar `HttpClient` com aplicativos iOS e Android, você vai querer definir propriedades do projeto, conforme descrito nos documentos  **[Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

O código que usa `HttpClient` geralmente envolve a `await` operador, portanto, ele deve residir em um `async` método:

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

Observe que o `Stream` objeto obtido `GetStreamAsync` é copiado para um `MemoryStream`. Android não permite que o `Stream` de `HttpClient` a serem processados pelo thread principal, exceto em métodos assíncronos. 

O [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/System.IO.Stream/) faz grande parte do trabalho: O `Stream` objeto passado para ele faz referência a um bloco de memória que contém um bitmap inteiro em um do comuns formatos de arquivo de bitmap, geralmente, JPEG, PNG ou GIF. O `Decode` método deve determinar o formato e, em seguida, decodificar o arquivo de bitmap em formato de bitmap interno do SkiaSharp.

Depois de seu código chama `SKBitmap.Decode`, provavelmente invalidará os `CanvasView` para que o `PaintSurface` manipulador pode exibir o bitmap carregado recentemente.

A segunda maneira de carregar um bitmap é, incluindo o bitmap como um recurso incorporado na biblioteca do .NET Standard referenciada pelos projetos de plataforma individual. Um recurso que ID é transmitida para o [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) método. Esta ID de recurso consiste do nome do assembly, o nome da pasta e o nome do arquivo de recurso separado por pontos:

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

Arquivos de bitmap também podem ser armazenados como recursos do projeto de plataforma individual para iOS, Android e Universal Windows Platform (UWP). No entanto, ao carregar esses bitmaps requer código que está localizado no projeto da plataforma.

Uma terceira abordagem para obtenção de um bitmap é da biblioteca de imagens do usuário. O código a seguir usa um serviço de dependência que está incluído na **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** aplicativo. O **SkiaSharpFormsDemo** inclui o .NET Standard Library a `IPhotoLibrary` interface, enquanto cada um dos projetos de plataforma contém um `PhotoLibrary` classe que implementa essa interface.

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

Em geral, esse código também invalida a `CanvasView` para que o `PaintSurface` manipulador pode exibir o novo bitmap.

O `SKBitmap` classe define várias propriedades úteis, incluindo [ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/), que revelam as dimensões de pixel do bitmap, bem como muitos métodos, incluindo métodos para criar bitmaps, copiá-los e expor os bits de pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Exibindo em dimensões de pixel

O SkiaSharp [ `Canvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/) classe define quatro `DrawBitmap` métodos. Esses métodos permitem que bitmaps sejam exibidos de duas maneiras fundamentalmente diferentes: 

- Especificando um `SKPoint` valor (ou separados `x` e `y` valores) exibe o bitmap em suas dimensões de pixel. Os pixels do bitmap são mapeados diretamente para pixels da tela do vídeo.
- Especificar um retângulo faz com que o bitmap a ser ampliada para o tamanho e a forma do retângulo. 

Exibir um bitmap em suas dimensões de pixel usando [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKPoint/SkiaSharp.SKPaint/) com um `SKPoint` parâmetro ou [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/) com separado `x` e `y` parâmetros:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Esses dois métodos são funcionalmente idênticos. O ponto especificado indica o local do canto superior esquerdo do bitmap em relação à tela. Como a resolução de pixel de dispositivos móveis é tão alta, menores bitmaps normalmente são exibidos um pouco pequenos nesses dispositivos.

Opcional `SKPaint` parâmetro permite que você exiba o bitmap usando modos de mesclagem ou efeitos de filtro. Eles serão demonstrados nos artigos posteriores.

O **dimensões de Pixel** página de **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** programa de exemplo exibe um recurso de bitmap é 320 pixels de largura por 240 pixels de altura:

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

O `PaintSurface` manipulador centraliza o bitmap, calculando `x` e `y` valores com base nas dimensões de pixel da superfície de exibição e as dimensões de pixel do bitmap:

[![Dimensões de pixel](displaying-images/PixelDimensions.png "dimensões de Pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Se o aplicativo deseja exibir o bitmap no canto superior esquerdo, ele simplesmente passaria coordenadas de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Um método de carregamento de bitmaps de recurso

Muitos dos exemplos em breve serão preciso carregar recursos de bitmap. Estático `BitmapExtensions` classe de **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** solução contém um método para ajudar:

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

Observe que o `Type` parâmetro. Isso pode ser o `Type` objeto associado a qualquer tipo no assembly que armazena o recurso de bitmap.

Isso `LoadBitmapResource` método será usado em todos os exemplos subsequentes que exigem recursos de bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>Alongando para preencher um retângulo

O `SKCanvas` classe define também uma [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/) método que renderiza o bitmap de um retângulo e outro [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) método que renderiza um subconjunto retangular do bitmap para um retângulo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

Em ambos os casos, o bitmap é esticado para preencher o retângulo denominado `dest`. No segundo método, o `source` retângulo permite que você selecione um subconjunto do bitmap. O `dest` retângulo é relativo ao dispositivo de saída; o `source` retângulo é relativo ao bitmap.

O **preencher o retângulo** página demonstra o primeiro desses dois métodos, exibindo o bitmap mesmo usado no exemplo anterior em um retângulo do mesmo tamanho como a tela: 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Observe o uso do novo `BitmapExtensions.LoadBitmapResource` método para definir o `SKBitmap` campo. O retângulo de destino é obtido o [ `Rect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Rect/) propriedade do `SKImageInfo`, que descreve o tamanho da superfície de exibição:

[![Preencher o retângulo](displaying-images/FillRectangle.png "preencher o retângulo")](displaying-images/FillRectangle-Large.png#lightbox)

Isso é geralmente _não_ o que você deseja. A imagem é distorcida pelo sendo ampliada de forma diferente nas direções horizontais e verticais. Ao exibir um bitmap em algo diferente de seu tamanho de pixel, geralmente você deseja preservar a taxa de proporção original do bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Alongando enquanto preserva a taxa de proporção

Ampliar um bitmap, preservando a taxa de proporção é um processo também conhecido como _dimensionamento uniforme_. Esse termo sugere uma abordagem algorítmica. Uma possível solução é mostrada na **dimensionamento uniforme** página:

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

O `PaintSurface` manipulador calcula uma `scale` fator é o mínimo, a proporção entre a largura de exibição e a altura e a altura e largura do bitmap. O `x` e `y` valores, em seguida, podem ser calculados para centralizar o bitmap com escala dentro a altura e largura de exibição. O retângulo de destino tem um canto superior esquerdo do `x` e `y` e um canto inferior direito desses valores mais a largura dimensionada e a altura do bitmap:

[![Dimensionamento uniforme](displaying-images/UniformScaling.png "dimensionamento uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Ative virar o telefone para ver o bitmap ampliado para essa área:

[![Uniforme Scaling paisagem](displaying-images/UniformScaling-Landscape.png "dimensionamento uniforme paisagem")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

A vantagem de usar este `scale` fator se tornará óbvio quando você deseja implementar um algoritmo um pouco diferente. Suponha que você deseja preservar a taxa de proporção do bitmap, mas também preencher o retângulo de destino. A única maneira que isso é possível é cortando parte da imagem, mas você pode implementar esse algoritmo simplesmente alterando `Math.Min` para `Math.Max` no código acima. Aqui está o resultado: 

[![Alternativa de colocação em escala uniforme](displaying-images/UniformScaling-Alternative.png "alternativa dimensionamento uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Taxa de proporção do bitmap é preservada mas áreas à esquerda e à direita do bitmap são cortadas.

## <a name="a-versatile-bitmap-display-function"></a>Uma função de exibição do bitmap versátil

Os ambientes de programação baseado em XAML (por exemplo, UWP e xamarin. Forms) tem um recurso para expandir ou reduzir o tamanho de bitmaps, preservando a suas proporções. Embora SkiaSharp não inclui esse recurso, você pode implementá-lo você mesmo. O `BitmapExtensions` classe incluída na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) aplicativo mostra como. A classe define dois novos `DrawBitmap` métodos que executam o cálculo de taxa de proporção. Esses novos métodos são métodos de extensão de `SKCanvas`.

O novo `DrawBitmap` métodos incluem um parâmetro do tipo `BitmapStretch`, uma enumeração definida na **BitmapExtensions.cs** arquivo:

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

O `None`, `Fill`, `Uniform`, e `UniformToFill` membros são os mesmos na UWP [ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) enumeração. O xamarin. Forms semelhante [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeração define os membros `Fill`, `AspectFit`, e `AspectFill`.

O **dimensionamento uniforme** página mostrada acima centraliza o bitmap dentro do retângulo, mas você pode querer outras opções, como posicionamento de bitmap na parte esquerda ou direita do retângulo, ou a parte superior ou inferior. Essa é a finalidade de `BitmapAlignment` enumeração:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Configurações de alinhamento não têm nenhum efeito quando usado com `BitmapStretch.Fill`.

A primeira `DrawBitmap` função de extensão contém um retângulo de destino, mas nenhum retângulo de origem. Os padrões são definidos para que se você quiser que o bitmap centralizado, você precisa apenas especificar um `BitmapStretch` membro:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

A principal finalidade desse método é calcular um fator de dimensionamento denominado `scale` que é aplicado, em seguida, para a altura e largura do bitmap ao chamar o `CalculateDisplayRect` método. Esse é o método que calcula um retângulo para exibir o bitmap com base no alinhamento horizontal e vertical:

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

O `BitmapExtensions` classe contém adicional `DrawBitmap` método com um retângulo de origem para especificar um subconjunto do bitmap. Esse método é semelhante à primeira, exceto pelo fato do fator de escala é calculado com base na `source` retângulo e, em seguida, aplicada para o `source` retângulo na chamada para `CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

A primeira dessas duas novas `DrawBitmap` métodos é demonstrada em de **modos de dimensionamento** página. O arquivo XAML contém três `Picker` elementos que permitem que você selecione os membros de `BitmapStretch` e `BitmapAlignment` enumerações:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

O arquivo code-behind simplesmente invalida as `CanvasView` quando qualquer `Picker` item foi alterado. O `PaintSurface` manipulador acessa os três `Picker` modos de exibição para chamar o `DrawBitmap` método de extensão:

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

Aqui estão algumas combinações de opções:

[![Modos de dimensionamento](displaying-images/ScalingModes.png "modos de dimensionamento")](displaying-images/ScalingModes-Large.png#lightbox)

O **retângulo subconjunto** página tem praticamente o mesmo arquivo XAML que **modos de dimensionamento**, mas o arquivo code-behind define um subconjunto retangular do bitmap fornecido pelo `SOURCE` campo: 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

Esta fonte de retângulo isola head do monkey, conforme mostrado nessas capturas de tela:

[![Subconjunto do retângulo](displaying-images/RectangleSubset.png "subconjunto de retângulo")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

