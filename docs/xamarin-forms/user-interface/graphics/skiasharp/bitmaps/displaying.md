---
title: Exibição de bitmaps de SkiaSharp
description: Saiba como exibir SkiaSharp bitmaps em pixel dimensiona e expandidas para preencher a retângulos, preservando a taxa de proporção.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9955b68346c74435a3a141c69d02e1bec5856bd3
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304012"
---
# <a name="displaying-skiasharp-bitmaps"></a>Exibição de bitmaps de SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

O assunto dos bitmaps SkiaSharp foi introduzido no artigo **[noções básicas de bitmap em SkiaSharp](../basics/bitmaps.md)** . Esse artigo mostrou três maneiras de bitmaps de carga e de três maneiras de exibir bitmaps. Este artigo revisa as técnicas para carregar bitmaps e aprofundar-se no uso dos métodos de `DrawBitmap` de `SKCanvas`.

![Exibindo exemplo](displaying-images/DisplayingSample.png "Exibindo exemplo")

Os métodos `DrawBitmapLattice` e `DrawBitmapNinePatch` são discutidos no artigo **[exibição segmentada de bitmaps SkiaSharp](segmented.md)** .

Os exemplos nesta página são do aplicativo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . Na home page desse aplicativo, escolha **bitmaps SkiaSharp**e, em seguida, vá para a seção **exibindo bitmaps** .

## <a name="loading-a-bitmap"></a>Carregar um bitmap

Um bitmap usado por um aplicativo de SkiaSharp geralmente vem de uma das três fontes diferentes:

- Na Internet
- De um recurso incorporado no executável
- Da biblioteca de fotos do usuário

Também é possível que um aplicativo de SkiaSharp criar um novo bitmap e, em seguida, desenhar nele ou definir os bits de bitmap algoritmicamente. Essas técnicas são discutidas nos artigos **[criando e desenhando bitmaps SkiaSharp](drawing.md)** e **[acessando pixels de bitmap SkiaSharp](pixel-bits.md)** .

Nos três exemplos de código a seguir de carregamento de um bitmap, pressupõe-se que a classe contenha um campo do tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Como as **[noções básicas de bitmap do artigo no SkiaSharp](../basics/bitmaps.md)** declararam, a melhor maneira de carregar um bitmap pela Internet é com a classe [`HttpClient`](xref:System.Net.Http.HttpClient) . Uma única instância da classe pode ser definida como um campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Ao usar o `HttpClient` com aplicativos iOS e Android, você desejará definir as propriedades do projeto conforme descrito nos documentos em **[TLS (segurança da camada de transporte) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

O código que usa `HttpClient` geralmente envolve o operador `await`, portanto, ele deve residir em um método `async`:

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

Observe que o objeto `Stream` obtido de `GetStreamAsync` é copiado em um `MemoryStream`. O Android não permite que o `Stream` de `HttpClient` seja processado pelo thread principal, exceto em métodos assíncronos. 

O [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) faz muito trabalho: o objeto `Stream` passado a ele faz referência a um bloco de memória que contém um bitmap inteiro em um dos formatos de arquivo de bitmap comuns, geralmente JPEG, png ou GIF. O método `Decode` deve determinar o formato e, em seguida, decodificar o arquivo de bitmap no próprio formato de bitmap interno do SkiaSharp.

Depois que seu código chama `SKBitmap.Decode`, ele provavelmente invalidará o `CanvasView` para que o manipulador de `PaintSurface` possa exibir o bitmap recém carregado.

A segunda maneira de carregar um bitmap é, incluindo o bitmap como um recurso incorporado na biblioteca do .NET Standard referenciada pelos projetos de plataforma individual. Uma ID de recurso é passada para o método [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) . Esta ID de recurso consiste do nome do assembly, o nome da pasta e o nome do arquivo de recurso separado por pontos:

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

Uma terceira abordagem para obtenção de um bitmap é da biblioteca de imagens do usuário. O código a seguir usa um serviço de dependência que está incluído no aplicativo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . A biblioteca de .NET Standard **SkiaSharpFormsDemo** inclui a interface `IPhotoLibrary`, enquanto cada um dos projetos de plataforma contém uma classe `PhotoLibrary` que implementa essa interface.

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

Em geral, esse código também invalida o `CanvasView` para que o manipulador de `PaintSurface` possa exibir o novo bitmap.

A classe `SKBitmap` define várias propriedades úteis, incluindo [`Width`](xref:SkiaSharp.SKBitmap.Width) e [`Height`](xref:SkiaSharp.SKBitmap.Height), que revelam as dimensões de pixel do bitmap, bem como muitos métodos, incluindo métodos para criar bitmaps, para copiá-los e para expor os bits de pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Exibindo em dimensões de pixel

A classe [`Canvas`](xref:SkiaSharp.SKCanvas) SkiaSharp define quatro métodos `DrawBitmap`. Esses métodos permitem que bitmaps sejam exibidos de duas maneiras fundamentalmente diferentes: 

- Especificar um valor de `SKPoint` (ou valores `x` e `y` separados) exibe o bitmap em suas dimensões de pixel. Os pixels do bitmap são mapeados diretamente para pixels da tela do vídeo.
- Especificar um retângulo faz com que o bitmap a ser ampliada para o tamanho e a forma do retângulo. 

Você exibe um bitmap em suas dimensões de pixel usando [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) com um parâmetro `SKPoint` ou [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) com os parâmetros `x` e `y` separados:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Esses dois métodos são funcionalmente idênticos. O ponto especificado indica o local do canto superior esquerdo do bitmap em relação à tela. Como a resolução de pixel de dispositivos móveis é tão alta, menores bitmaps normalmente são exibidos um pouco pequenos nesses dispositivos.

O parâmetro `SKPaint` opcional permite que você exiba o bitmap usando transparência. Para fazer isso, crie um objeto `SKPaint` e defina a propriedade `Color` como qualquer valor de `SKColor` com um canal alfa menor que 1. Por exemplo:

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

O 0x80 passado como o último argumento indica 50% de transparência. Você também pode definir um canal alfa em uma das cores predefinidas:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

No entanto, a cor em si é irrelevante. Somente o canal alfa é examinado quando você usa o objeto `SKPaint` em uma chamada `DrawBitmap`.

O objeto `SKPaint` também desempenha uma função ao exibir bitmaps usando modos de mesclagem ou efeitos de filtro. Eles são demonstrados nos artigos [SkiaSharp composição e mesclagem modos](../effects/blend-modes/index.md) e [filtros de imagem SkiaSharp](../effects/image-filters.md).

A página **dimensões de pixel** no programa de exemplo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** exibe um recurso de bitmap que tem 320 pixels de largura por 240 pixels de altura:

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

O manipulador de `PaintSurface` centraliza o bitmap calculando `x` e `y` valores com base nas dimensões de pixel da superfície de exibição e nas dimensões de pixel do bitmap:

[![Dimensões de pixel](displaying-images/PixelDimensions.png "Dimensões de pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Se o aplicativo deseja exibir o bitmap no canto superior esquerdo, ele simplesmente passaria coordenadas de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Um método de carregamento de bitmaps de recurso

Muitos dos exemplos em breve serão preciso carregar recursos de bitmap. A classe de `BitmapExtensions` estática na solução **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** contém um método para ajudar:

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

Observe o parâmetro `Type`. Pode ser o objeto `Type` associado a qualquer tipo no assembly que armazena o recurso de bitmap.

Esse método de `LoadBitmapResource` será usado em todos os exemplos subsequentes que exigem recursos de bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>Alongando para preencher um retângulo

A classe `SKCanvas` também define um método [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) que renderiza o bitmap para um retângulo e outro método [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) que renderiza um subconjunto retangular do bitmap para um retângulo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

Em ambos os casos, o bitmap é alongado para preencher o retângulo chamado `dest`. No segundo método, o retângulo `source` permite que você selecione um subconjunto do bitmap. O retângulo de `dest` é relativo ao dispositivo de saída; o retângulo `source` é relativo ao bitmap.

A página **retângulo de preenchimento** demonstra o primeiro desses dois métodos exibindo o mesmo bitmap usado no exemplo anterior em um retângulo com o mesmo tamanho que a tela: 

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

Observe o uso do novo método `BitmapExtensions.LoadBitmapResource` para definir o campo `SKBitmap`. O retângulo de destino é obtido da propriedade [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) de `SKImageInfo`, que descreve o tamanho da superfície de exibição:

[![Retângulo de preenchimento](displaying-images/FillRectangle.png "Retângulo de preenchimento")](displaying-images/FillRectangle-Large.png#lightbox)

Normalmente, isso _não_ é o que você deseja. A imagem é distorcida pelo sendo ampliada de forma diferente nas direções horizontais e verticais. Ao exibir um bitmap em algo diferente de seu tamanho de pixel, geralmente você deseja preservar a taxa de proporção original do bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Alongando enquanto preserva a taxa de proporção

Esticar um bitmap enquanto preserva a taxa de proporção é um processo também conhecido como _dimensionamento uniforme_. Esse termo sugere uma abordagem algorítmica. Uma solução possível é mostrada na página de **dimensionamento uniforme** :

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

O manipulador de `PaintSurface` calcula um fator de `scale` que é o mínimo da proporção da largura e altura da exibição para a largura e a altura do bitmap. Os valores de `x` e `y` podem então ser calculados para centralizar o bitmap dimensionado dentro da largura e altura de exibição. O retângulo de destino tem um canto superior esquerdo de `x` e `y` e um canto inferior direito desses valores, além da largura e altura da escala do bitmap:

[![Dimensionamento uniforme](displaying-images/UniformScaling.png "Dimensionamento uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Ative virar o telefone para ver o bitmap ampliado para essa área:

[![Paisagem de dimensionamento uniforme](displaying-images/UniformScaling-Landscape.png "Paisagem de dimensionamento uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

A vantagem de usar esse `scale` fator se torna óbvio quando você deseja implementar um algoritmo um pouco diferente. Suponha que você deseja preservar a taxa de proporção do bitmap, mas também preencher o retângulo de destino. A única maneira possível é cortar parte da imagem, mas você pode implementar esse algoritmo simplesmente alterando `Math.Min` para `Math.Max` no código acima. Este é o resultado: 

[![Alternativa de dimensionamento uniforme](displaying-images/UniformScaling-Alternative.png "Alternativa de dimensionamento uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Taxa de proporção do bitmap é preservada mas áreas à esquerda e à direita do bitmap são cortadas.

## <a name="a-versatile-bitmap-display-function"></a>Uma função de exibição do bitmap versátil

Os ambientes de programação baseado em XAML (por exemplo, UWP e xamarin. Forms) tem um recurso para expandir ou reduzir o tamanho de bitmaps, preservando a suas proporções. Embora SkiaSharp não inclui esse recurso, você pode implementá-lo você mesmo. A classe `BitmapExtensions` incluída no aplicativo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) mostra como. A classe define dois novos métodos de `DrawBitmap` que executam o cálculo de taxa de proporção. Esses novos métodos são métodos de extensão do `SKCanvas`.

Os novos métodos de `DrawBitmap` incluem um parâmetro do tipo `BitmapStretch`, uma enumeração definida no arquivo **BitmapExtensions.cs** :

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

Os membros `None`, `Fill`, `Uniform`e `UniformToFill` são os mesmos da enumeração do UWP [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) . A enumeração de [`Aspect`](xref:Xamarin.Forms.Aspect) Xamarin. Forms semelhante define Membros `Fill`, `AspectFit`e `AspectFill`.

A página de **dimensionamento uniforme** mostrada acima centraliza o bitmap dentro do retângulo, mas você pode desejar outras opções, como posicionar o bitmap no lado esquerdo ou direito do retângulo, ou na parte superior ou inferior. Essa é a finalidade da enumeração de `BitmapAlignment`:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

As configurações de alinhamento não têm efeito quando usadas com `BitmapStretch.Fill`.

A primeira função de extensão `DrawBitmap` contém um retângulo de destino, mas nenhum retângulo de origem. Os padrões são definidos de forma que, se você quiser centralizar o bitmap, precisará especificar apenas um membro `BitmapStretch`:

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

A principal finalidade desse método é calcular um fator de dimensionamento chamado `scale` que é então aplicado à largura e altura do bitmap ao chamar o método `CalculateDisplayRect`. Esse é o método que calcula um retângulo para exibir o bitmap com base no alinhamento horizontal e vertical:

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

A classe `BitmapExtensions` contém um método `DrawBitmap` adicional com um retângulo de origem para especificar um subconjunto do bitmap. Esse método é semelhante ao primeiro, exceto pelo fato de que o fator de dimensionamento é calculado com base no retângulo de `source` e, em seguida, aplicado ao retângulo de `source` na chamada para `CalculateDisplayRect`:

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

O primeiro desses dois novos métodos de `DrawBitmap` é demonstrado na página **modos de dimensionamento** . O arquivo XAML contém três elementos `Picker` que permitem selecionar membros do `BitmapStretch` e `BitmapAlignment` enumerações:

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

O arquivo code-behind simplesmente invalida o `CanvasView` quando qualquer item de `Picker` foi alterado. O manipulador de `PaintSurface` acessa as três `Picker` exibições para chamar o método de extensão `DrawBitmap`:

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

[![Modos de dimensionamento](displaying-images/ScalingModes.png "Modos de dimensionamento")](displaying-images/ScalingModes-Large.png#lightbox)

A página de **subconjunto de retângulo** tem praticamente o mesmo arquivo XAML que os **modos de dimensionamento**, mas o arquivo code-behind define um subconjunto retangular do bitmap fornecido pelo campo `SOURCE`: 

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

[![Subconjunto de retângulo](displaying-images/RectangleSubset.png "Subconjunto de retângulo")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
