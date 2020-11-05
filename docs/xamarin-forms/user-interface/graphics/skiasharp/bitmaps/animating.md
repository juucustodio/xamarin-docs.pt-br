---
title: Animação de bitmaps SkiaSharp
description: Saiba como executar uma animação de bitmap exibindo sequencialmente uma série de bitmaps e renderizando arquivos GIF animados.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eeaf62c684100ff9fd5bb9ffd15162bdb4c6afd6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366510"
---
# <a name="animating-skiasharp-bitmaps"></a>Animação de bitmaps SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Os aplicativos que animam os gráficos SkiaSharp geralmente chamam `InvalidateSurface` em `SKCanvasView` uma taxa fixa, geralmente a cada 16 milissegundos. A invalidação da superfície dispara uma chamada para o `PaintSurface` manipulador para redesenhar a exibição. Como os visuais são redesenhados 60 vezes por segundo, eles parecem ser animados sem problemas.

No entanto, se os gráficos forem muito complexos para serem processados em 16 milissegundos, a animação poderá se tornar uma tremulação. O programador pode optar por reduzir a taxa de atualização para 30 vezes ou 15 vezes por segundo, mas às vezes mesmo isso não é suficiente. Às vezes, os elementos gráficos são tão complexos que simplesmente não podem ser renderizados em tempo real.

Uma solução é preparar a animação com antecedência renderizando os quadros individuais da animação em uma série de bitmaps. Para exibir a animação, só é necessário exibir esses bitmaps sequencialmente 60 vezes por segundo.

É claro que isso é potencialmente um grande volume de bitmaps, mas é assim que os filmes animados 3D de orçamento grande são feitos. Os gráficos 3D são muito complexos para serem processados em tempo real. Muito tempo de processamento é necessário para processar cada quadro. O que você vê quando assiste ao filme é essencialmente uma série de bitmaps.

Você pode fazer algo semelhante em SkiaSharp. Este artigo demonstra dois tipos de animação de bitmap. O primeiro exemplo é uma animação do conjunto de Mandelbrot:

![Exemplo de animação](animating-images/AnimatingSample.png "Exemplo de animação")

O segundo exemplo mostra como usar SkiaSharp para renderizar um arquivo GIF animado.

## <a name="bitmap-animation"></a>Animação de bitmap

O conjunto de Mandelbrot é um fascinante visualmente, mas computionally comprimento. (Para obter uma discussão sobre o conjunto de Mandelbrot e a matemática usada aqui, consulte o [capítulo 20 da _criação Xamarin.Forms de aplicativos móveis com a_](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) partir da página 666. A descrição a seguir pressupõe que o conhecimento em segundo plano.)

O exemplo de [**animação Mandelbrot**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) usa animação de bitmap para simular um zoom contínuo de um ponto fixo no conjunto de Mandelbrot. O zoom no é seguido pelo zoom e, em seguida, o ciclo se repete para sempre ou até que você encerre o programa.

O programa prepara para essa animação criando até 50 bitmaps que ele armazena no armazenamento local do aplicativo. Cada bitmap abrange metade da largura e da altura do plano complexo como o bitmap anterior. (No programa, esses bitmaps são considerados para representar níveis de _zoom_ integral.) Os bitmaps são então exibidos em sequência. O dimensionamento de cada bitmap é animado para fornecer uma progressão suave de um bitmap para outro.

Como o programa final descrito no capítulo 20 da _criação de aplicativos móveis Xamarin.Forms com_ o, o cálculo do conjunto de Mandelbrot na **animação Mandelbrot** é um método assíncrono com oito parâmetros. Os parâmetros incluem um ponto central complexo e uma largura e altura do plano complexo em torno desse ponto central. Os próximos três parâmetros são a largura do pixel e a altura do bitmap a ser criado e um número máximo de iterações para o cálculo recursivo. O `progress` parâmetro é usado para exibir o progresso desse cálculo. O `cancelToken` parâmetro não é usado neste programa:

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

O método retorna um objeto do tipo `BitmapInfo` que fornece informações para criar um bitmap:

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

O arquivo XAML de **animação Mandelbrot** inclui dois `Label` modos de exibição, um `ProgressBar` e um, `Button` bem como `SKCanvasView` :

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

O arquivo code-behind começa definindo três constantes cruciais e uma matriz de bitmaps:

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

Em algum momento, você provavelmente desejará alterar o `COUNT` valor para 50 para ver o intervalo completo da animação. Os valores acima de 50 não são úteis. Em um nível de zoom de 48 ou mais, a resolução de números de ponto flutuante de precisão dupla se torna insuficiente para o cálculo do conjunto de Mandelbrot. Esse problema é discutido na página 684 da _criação de aplicativos móveis Xamarin.Forms com_ o.

O `center` valor é muito importante. Esse é o foco do zoom da animação. Os três valores no arquivo são aqueles usados nas três capturas de tela finais no capítulo 20 da _criação de aplicativos móveis Xamarin.Forms com_ a página 684, mas você pode experimentar o programa nesse capítulo para criar um de seus próprios valores.

O exemplo de **animação Mandelbrot** armazena esses `COUNT` bitmaps no armazenamento de aplicativo local. 50 bitmaps exigem mais de 20 megabytes de armazenamento em seu dispositivo, portanto, talvez você queira saber quanto armazenamento esses bitmaps estão ocupando e, em algum momento, convém excluir todos eles. Essa é a finalidade desses dois métodos na parte inferior da `MainPage` classe:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

Você pode excluir os bitmaps no armazenamento local enquanto o programa está animando os mesmos bitmaps porque o programa os retém na memória. Mas na próxima vez que você executar o programa, será necessário recriar os bitmaps.

Os bitmaps armazenados no armazenamento de aplicativo local incorporam o `center` valor em seus nomes de File, portanto, se você alterar a `center` configuração, os bitmaps existentes não serão substituídos no armazenamento e continuarão ocupando espaço.

Aqui estão os métodos que o `MainPage` usa para construir os nomes de File, bem como um `MakePixel` método para definir um valor de pixel com base em componentes de cor:

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

O `zoomLevel` parâmetro para `FilePath` varia de 0 para a `COUNT` constante menos 1.

O `MainPage` construtor chama o `LoadAndStartAnimation` método:

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

O `LoadAndStartAnimation` método é responsável por acessar o armazenamento local do aplicativo para carregar os bitmaps que podem ter sido criados quando o programa foi executado anteriormente. Ele percorre `zoomLevel` valores de 0 a `COUNT` . Se o arquivo existir, ele o carregará na `bitmaps` matriz. Caso contrário, ele precisa criar um bitmap para os `center` valores específicos `zoomLevel` chamando `Mandelbrot.CalculateAsync` . Esse método obtém as contagens de iteração para cada pixel, que esse método converte em cores:

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

Observe que o programa armazena esses bitmaps no armazenamento de aplicativo local em vez de na biblioteca de fotos do dispositivo. A biblioteca .NET Standard 2,0 permite usar os `File.OpenRead` métodos e conhecidos `File.WriteAllBytes` para essa tarefa.

Depois que todos os bitmaps tiverem sido criados ou carregados na memória, o método iniciará um `Stopwatch` objeto e chamará `Device.StartTimer` . O `OnTimerTick` método é chamado a cada 16 milissegundos.

`OnTimerTick` Calcula um `time` valor em milissegundos que varia de 0 a 6000 vezes `COUNT` , que abrange seis segundos para a exibição de cada bitmap. O `progress` valor usa o `Math.Sin` valor para criar uma animação sinusoidal que será mais lenta no início do ciclo e mais lento no final, à medida que ela reverte a direção.

O `progress` valor varia de 0 a `COUNT` . Isso significa que a parte inteira do `progress` é um índice na `bitmaps` matriz, enquanto a parte fracionária do `progress` indica um nível de zoom para esse bitmap específico. Esses valores são armazenados nos `bitmapIndex` campos e e `bitmapProgress` são exibidos pelo `Label` e `Slider` no arquivo XAML. O `SKCanvasView` é invalidado para atualizar a exibição do bitmap:

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

Por fim, o `PaintSurface` manipulador do `SKCanvasView` calcula um retângulo de destino para exibir o bitmap o mais grande possível, mantendo a taxa de proporção. Um retângulo de origem é baseado no `bitmapProgress` valor. O `fraction` valor calculado aqui varia de 0 quando `bitmapProgress` é 0 para exibir o bitmap inteiro, para 0,25 quando `bitmapProgress` é 1 para exibir metade da largura e altura do bitmap, com eficiência no zoom:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

Este é o programa em execução:

[![Animação Mandelbrot](animating-images/MandelbrotAnimation.png "Animação Mandelbrot")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animação GIF

A especificação de Graphics Interchange Format (GIF) inclui um recurso que permite que um único arquivo GIF contenha vários quadros sequenciais de uma cena que podem ser exibidos sucessivamente, geralmente em um loop. Esses arquivos são conhecidos como _gifs animados_. Os navegadores da Web podem reproduzir GIFs animados e o SkiaSharp permite que um aplicativo Extraia os quadros de um arquivo GIF animado e os exiba sequencialmente.

O exemplo [SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclui um recurso GIF animado chamado **Newtons_cradle_animation_book_2.gif** criado pelo DemonDeLuxe e baixado da página de [base do Newton](https://en.wikipedia.org/wiki/Newton%27s_cradle) na Wikipédia. A página **GIF animada** inclui um arquivo XAML que fornece essas informações e cria uma instância de `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

O arquivo code-behind não é generalizado para reproduzir qualquer arquivo GIF animado. Ele ignora algumas das informações disponíveis, em particular uma contagem de repetição, e simplesmente reproduz o GIF animado em um loop.

O uso de SkisSharp para extrair os quadros de um arquivo GIF animado não parece estar documentado em lugar algum, portanto, a descrição do código a seguir é mais detalhada do que o normal:

A decodificação do arquivo GIF animado ocorre no construtor da página e requer que o `Stream` objeto que faz referência ao bitmap seja usado para criar um `SKManagedStream` objeto e, em seguida, um [`SKCodec`](xref:SkiaSharp.SKCodec) objeto. A [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount) propriedade indica o número de quadros que compõem a animação.

Esses quadros são eventualmente salvos como bitmaps individuais, portanto, o construtor usa `FrameCount` para alocar uma matriz do tipo, `SKBitmap` bem como duas `int` matrizes para a duração de cada quadro e (para facilitar a lógica da animação) as durações acumuladas.

A [`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo) propriedade da `SKCodec` classe é uma matriz de [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) valores, uma para cada quadro, mas a única coisa que esse programa pega dessa estrutura é o [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) do quadro em milissegundos.

`SKCodec` define uma propriedade chamada [`Info`](xref:SkiaSharp.SKCodec.Info) de tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) , mas esse `SKImageInfo` valor indica (pelo menos para essa imagem) que o tipo de cor é `SKColorType.Index8` , o que significa que cada pixel é um índice em um tipo de cor. Para evitar incomodar com tabelas de cores, o programa usa [`Width`](xref:SkiaSharp.SKImageInfo.Width) as [`Height`](xref:SkiaSharp.SKImageInfo.Height) informações e dessa estrutura para construir seu próprio valor de cor completa `ImageInfo` . Cada uma `SKBitmap` é criada a partir dessa.

O `GetPixels` método de `SKBitmap` retorna uma `IntPtr` referência aos bits de pixel desse bitmap. Esses bits de pixel ainda não foram definidos. Que `IntPtr` é passado para um dos [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) métodos de `SKCodec` . Esse método copia o quadro do arquivo GIF para o espaço de memória referenciado pelo `IntPtr` . O [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions) Construtor indica o número do quadro:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

Apesar do `IntPtr` valor, nenhum `unsafe` código é necessário porque o `IntPtr` nunca é convertido em um valor de ponteiro em C#.

Depois que cada quadro tiver sido extraído, o Construtor totalizará as durações de todos os quadros e inicializará outra matriz com as durações acumuladas.

O restante do arquivo code-behind é dedicado à animação. O `Device.StartTimer` método é usado para iniciar um temporizador indo e o `OnTimerTick` retorno de chamada usa um `Stopwatch` objeto para determinar o tempo decorrido em milissegundos. O loop pela matriz de durações acumuladas é suficiente para localizar o quadro atual:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
    ···
    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

Cada vez que a `currentframe` variável é alterada, o `SKCanvasView` é invalidado e o novo quadro é exibido:

[![GIF animado](animating-images/AnimatedGif.png "GIF animado")](animating-images/AnimatedGif-Large.png#lightbox)

É claro que você vai querer executar o programa por conta própria para ver a animação.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animação Mandelbrot (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)