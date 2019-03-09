---
title: Animando bitmaps de SkiaSharp
description: Saiba como executar a animação de bitmap ao sequencialmente exibindo uma série de bitmaps e renderizar os arquivos GIF animados.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 604067ac853bd53707e059b7db4abf2cfade21ce
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668952"
---
# <a name="animating-skiasharp-bitmaps"></a>Animando bitmaps de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Os aplicativos que animar elementos gráficos de SkiaSharp geralmente chamar `InvalidateSurface` sobre o `SKCanvasView` a uma taxa fixa, muitas vezes a cada 16 milissegundos. Invalidar a superfície dispara uma chamada para o `PaintSurface` manipulador redesenhar a exibição. Como os visuais são redesenhados 60 vezes por segundo, eles parecem ser animada suavemente.

No entanto, se os gráficos são muito complexos para ser renderizado em 16 milissegundos, a animação pode se tornar instável. O programador pode optar por reduzir a taxa de atualização para 30 vezes ou 15 vezes por segundo, mas, às vezes, mesmo que não é suficiente. Às vezes, os gráficos são tão complexos que eles simplesmente não podem ser renderizados em tempo real.

Uma solução é para se preparar para a animação com antecedência, renderizando os quadros individuais da animação em uma série de bitmaps. Para exibir a animação, só é necessário exibir esses bitmaps sequencialmente 60 vezes por segundo.

É claro que é potencialmente muito de bitmaps, mas que é como big-orçamento 3D filmes animados são feitos. Os gráficos 3D são muito muito complexos para ser renderizado em tempo real. Muito tempo de processamento é necessária para processar cada quadro. O que você vê quando você observar o filme é essencialmente uma série de bitmaps.

Você pode fazer algo semelhante no SkiaSharp. Este artigo demonstra dois tipos de animação de bitmap. O primeiro exemplo é uma animação de conjunto de Mandelbrot:

![Exemplo de animação](animating-images/AnimatingSample.png "animação de exemplo")

O segundo exemplo mostra como usar SkiaSharp para processar um arquivo GIF animado.

## <a name="bitmap-animation"></a>Animação de bitmap

O conjunto de Mandelbrot é visualmente fascinante mas computionally longas. (Para uma discussão sobre o conjunto de Mandelbrot e a matemática usada aqui, consulte [capítulo 20 _criação de aplicativos móveis com xamarin. Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) começando na página 666. A seguinte descrição pressupõe esse conhecimento em segundo plano).

O [ **animação Mandelbrot** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/MandelAnima/) exemplo usa a animação de bitmap para simular um zoom contínuo de um ponto fixo no conjunto de Mandelbrot. Aumentar o zoom é seguido por diminuir o zoom e, em seguida, o ciclo se repete indefinidamente ou até que você finalizar o programa.

O programa se prepara para essa animação criando até 50 bitmaps que ele armazena no armazenamento local do aplicativo. Cada bitmap engloba metade da largura e altura do plano complexo como o bitmap anterior. (No programa, esses bitmaps devem representar integral _níveis de zoom_.) Os bitmaps são exibidos na sequência. O dimensionamento de cada bitmap é animado para fornecer uma progressão suave de um bitmap para outro.

Como descrito no capítulo 20 do final do programa _criação de aplicativos móveis com xamarin. Forms_, o cálculo do conjunto de Mandelbrot na **Mandelbrot animação** é um método assíncrono com oito parâmetros. Os parâmetros incluem um ponto central complexas e uma largura e altura do plano complexo em torno desse ponto central. Os próximos três parâmetros são os pixels de largura e altura do bitmap a ser criado e um número máximo de iterações para o cálculo recursiva. O `progress` parâmetro é usado para exibir o progresso do cálculo. O `cancelToken` parâmetro não é usado neste programa:

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

O método retorna um objeto do tipo `BitmapInfo` que fornece informações sobre como criar um bitmap:

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

O **Mandelbrot animação** arquivo XAML inclui dois `Label` modos de exibição, uma `ProgressBar`e um `Button` , bem como a `SKCanvasView`:

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

O arquivo code-behind começa por definir três constantes cruciais e uma matriz de bitmaps:

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

Em algum momento, você provavelmente vai querer alterar o `COUNT` valor com 50 para ver o intervalo total da animação. Os valores acima de 50 não são úteis. Em torno de um nível de zoom de 48 mais ou menos, a resolução de números de ponto flutuante de precisão dupla torna-se insuficiente para o cálculo do conjunto de Mandelbrot. Esse problema é discutido na página 684 do _criação de aplicativos móveis com xamarin. Forms_.

O `center` valor é muito importante. Isso é o foco do zoom animação. Os três valores no arquivo são aqueles usados nas três capturas de tela finais no capítulo 20 _criação de aplicativos móveis com xamarin. Forms_ na página 684, mas você pode fazer experiências com o programa de capítulo para inventar um dos seus próprios valores.

O **Mandelbrot animação** exemplo armazena esses `COUNT` bitmaps no armazenamento de aplicativo local. Cinquenta bitmaps exigem mais de 20 megabytes de armazenamento em seu dispositivo, portanto, você talvez queira saber quanto armazenamento ocupado por esses bitmaps e, em algum momento, você talvez queira excluir todos eles. Essa é a finalidade desses dois métodos na parte inferior do `MainPage` classe:

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

Você pode excluir os bitmaps no armazenamento local enquanto o programa estiver animando esses bitmaps mesmos porque o programa mantém na memória. Mas, na próxima vez que você executar o programa, ele será necessário recriar os bitmaps.

Incorporar os bitmaps armazenados no armazenamento local do aplicativo a `center` valor em seus nomes de arquivos, portanto, se você alterar o `center` configuração, os bitmaps existentes não serão substituídos no armazenamento e continuarão a ocupar espaço.

Aqui estão os métodos que `MainPage` usa para construir os nomes de arquivo, bem como um `MakePixel` método para definir um valor de pixel com base em componentes de cor:

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

O `zoomLevel` parâmetro para `FilePath` varia de 0 para o `COUNT` constante menos 1.

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

O `LoadAndStartAnimation` método é responsável por acessar o armazenamento local do aplicativo para carregar qualquer bitmaps que pode ter sido criado quando o programa foi executado anteriormente. Ele percorre `zoomLevel` valores de 0 a `COUNT`. Se o arquivo existir, ele carrega o `bitmaps` matriz. Caso contrário, ele precisa criar um bitmap para o determinado `center` e `zoomLevel` valores chamando `Mandelbrot.CalculateAsync`. Esse método obtém as contagens de iteração para cada pixel, que este método converte em cores:

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

Observe que o programa armazena esses bitmaps em armazenamento de aplicativo local em vez de na biblioteca de fotos do dispositivo. A biblioteca do .NET Standard 2.0 permite usando a familiar `File.OpenRead` e `File.WriteAllBytes` métodos para essa tarefa.

Depois que todos os bitmaps criados ou carregados na memória, o método inicia um `Stopwatch` objeto e chamadas `Device.StartTimer`. O `OnTimerTick` método é chamado a cada 16 milissegundos.

`OnTimerTick` calcula uma `time` valor em milissegundos que varia de 0 a 6000 vezes `COUNT`, qual apportions seis segundos para a exibição de cada bitmap. O `progress` valor usa o `Math.Sin` para criar uma animação sinusoidal que será mais lenta no início do ciclo de valor e mais lento no final como ela inverte a direção.

O `progress` valor varia de 0 a `COUNT`. Isso significa que a parte inteira de `progress` é um índice para o `bitmaps` array, enquanto a parte fracionária do `progress` indica um nível de zoom para esse bitmap específico. Esses valores são armazenados na `bitmapIndex` e `bitmapProgress` campos e são exibidos pelo `Label` e `Slider` no arquivo XAML. O `SKCanvasView` é invalidada para atualizar a exibição do bitmap:

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

Por fim, o `PaintSurface` manipulador do `SKCanvasView` calcula um retângulo de destino para exibir o bitmap tão grande quanto possível, mantendo a taxa de proporção. Um retângulo de origem se baseia o `bitmapProgress` valor. O `fraction` valor aqui calculado varia de 0 quando `bitmapProgress` é 0 para exibir o bitmap inteiro, como quando 0,25 `bitmapProgress` é 1 para exibir a metade da largura e altura do bitmap, efetivamente aumentar o zoom:

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

Aqui está o programa em execução:

[![Animação de Mandelbrot](animating-images/MandelbrotAnimation.png "Mandelbrot animação")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animação de GIF

A especificação de formato GIF (Graphics Interchange) inclui um recurso que permite que um único arquivo GIF contêm vários quadros sequenciais de uma cena que podem ser exibidos em sucessão, geralmente em um loop. Esses arquivos são conhecidos como _GIFs animados_. Navegadores da Web podem desempenhar GIFs animados e SkiaSharp permite que um aplicativo para extrair os quadros de um arquivo GIF animado e exibi-los em sequência.

O [SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo inclui um recurso de GIF animado denominado **Newtons_cradle_animation_book_2.gif** criado pelo DemonDeLuxe e baixado do [Cradle do Newton ](https://en.wikipedia.org/wiki/Newton%27s_cradle) página na Wikipédia. O **GIF animado** página inclui um arquivo XAML que fornece essas informações e cria uma instância de um `SKCanvasView`:

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

O arquivo code-behind não é generalizado para reproduzir qualquer arquivo GIF animado. Ele ignora algumas das informações que está disponíveis, em particular, uma contagem de repetição e simplesmente desempenha o GIF animado em um loop.

O uso de SkisSharp para extrair os quadros de um arquivo GIF animado parece não ser documentado em qualquer lugar, portanto, a descrição do código a seguir é mais detalhada do que o normal:

A decodificação do arquivo GIF animado ocorre no construtor da página e exige que o `Stream` objeto referenciando o bitmap a ser usado para criar um `SKManagedStream` objeto e, em seguida, um [ `SKCodec` ](xref:SkiaSharp.SKCodec) objeto. O [ `FrameCount` ](xref:SkiaSharp.SKCodec.FrameCount) propriedade indica o número de quadros que formam a animação.

Esses quadros eventualmente são salvos como bitmaps individuais, portanto, usa o construtor `FrameCount` para alocar uma matriz do tipo `SKBitmap` , bem como dois `int` matrizes para a duração de cada quadro e (para facilitar a lógica da animação) o acumulado durações.

O [ `FrameInfo` ](xref:SkiaSharp.SKCodec.FrameInfo) propriedade do `SKCodec` classe é uma matriz de [ `SKCodecFrameInfo` ](xref:SkiaSharp.SKCodecFrameInfo) valores, um para cada quadro, mas a única coisa que esse programa usa dessa estrutura é o [ `Duration` ](xref:SkiaSharp.SKCodecFrameInfo.Duration) do quadro em milissegundos.

`SKCodec` define uma propriedade chamada [ `Info` ](xref:SkiaSharp.SKCodec.Info) do tipo [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo), mas esse `SKImageInfo` valor indica (pelo menos para esta imagem) que o tipo de cor é `SKColorType.Index8`, o que significa que cada pixel é um índice em um tipo de cor. Para evitar que se preocupar com tabelas de cores, o programa usa o [ `Width` ](xref:SkiaSharp.SKImageInfo.Width) e [ `Height` ](xref:SkiaSharp.SKImageInfo.Height) informações dessa estrutura para construí-la é possui quatro cores `ImageInfo` valor. Cada `SKBitmap` é criada a partir do que.

O `GetPixels` método de `SKBitmap` retorna um `IntPtr` referenciar os bits de pixel desse bitmap. Esses bits de pixel ainda não foram definidas. Que `IntPtr` é passado para um dos [ `GetPixels` ](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) métodos de `SKCodec`. Esse método copia o quadro do arquivo GIF no espaço de memória referenciado pelo `IntPtr`. O [ `SKCodecOptions` ](xref:SkiaSharp.SKCodecOptions) construtor indica o número de quadro:

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

Apesar do `IntPtr` de valor, não `unsafe` código é necessário porque o `IntPtr` nunca é convertida em um valor de ponteiro em C#.

Depois de cada quadro foi extraído, o construtor as durações de todos os quadros de totais e, em seguida, inicializa a outra matriz com as durações acumuladas.

O restante do arquivo code-behind é dedicado a animação. O `Device.StartTimer` método é usado para iniciar um timer indo e o `OnTimerTick` retorno de chamada usa uma `Stopwatch` objeto para determinar o tempo decorrido em milissegundos. Um loop por meio da matriz de durações acumulado é suficiente para encontrar o quadro atual:

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

Cada vez que o `currentframe` alterações na variável, o `SKCanvasView` é invalidado e o novo quadro é exibido:

[![GIF animado](animating-images/AnimatedGif.png "GIF animado")](animating-images/AnimatedGif-Large.png#lightbox)

Obviamente, você vai querer executar o programa por conta própria para ver a animação.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Animação de Mandelbrot (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/MandelAnima/)
