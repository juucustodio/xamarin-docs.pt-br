---
title: Acessando os bits de pixel do bitmap de SkiaSharp
description: Descubra as várias técnicas para acessar e modificar os bits de pixel de SkiaSharp bitmaps.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642527"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Acessando os bits de pixel do bitmap de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como você viu neste artigo [ **SkiaSharp salvando bitmaps em arquivos**](saving.md), bitmaps geralmente são armazenados em arquivos em um formato compactado, como JPEG ou PNG. Em contraste, um bitmap de SkiaSharp armazenado na memória não é compactado. Ele é armazenado como uma série sequencial de pixels. Esse formato não compactado facilita a transferência de bitmaps a uma superfície de exibição.

O bloco de memória ocupado por um bitmap SkiaSharp é organizado de maneira muito simples: Ele começa com a primeira linha de pixels, da esquerda para a direita, e continua com a segunda linha. Para bitmaps colorida, cada pixel consiste em quatro bytes, que significa que o espaço de memória total necessário para o bitmap é quatro vezes o produto de sua largura e altura.

Este artigo descreve como um aplicativo pode obter acesso a esses pixels acessando diretamente o bloco de memória de pixel do bitmap, ou indiretamente. Em alguns casos, um programa talvez queira analisar os pixels de uma imagem e construir um histograma de algum tipo. Mais comumente, aplicativos podem construir a imagens exclusivas, criando algoritmicamente os pixels que compõem o bitmap:

![Exemplos de Bits de pixel](pixel-bits-images/PixelBitsSample.png "exemplo de Bits de Pixel")

## <a name="the-techniques"></a>As técnicas

SkiaSharp fornece várias técnicas para acessar os bits de pixel do bitmap. Qual deles você escolha geralmente é um meio-termo entre conveniência (que está relacionada à manutenção e facilitar a depuração) e o desempenho de codificação. Na maioria dos casos, você usará um dos seguintes métodos e propriedades de `SKBitmap` para acessar os pixels do bitmap:

- O `GetPixel` e `SetPixel` métodos permitem que você obtenha ou defina a cor de um único pixel.
- O `Pixels` propriedade obtém uma matriz de cores de pixel para o bitmap inteiro, ou define a matriz de cores.
- `GetPixels` Retorna o endereço da memória pixel usada pelo bitmap.
- `SetPixels` substitui o endereço da memória pixel usada pelo bitmap.

Você pode considerar as duas primeiras técnicas como "alto nível" e os segundos dois como "baixo nível". Há alguns métodos e propriedades que você pode usar, mas esses são os mais valiosos.

Para que você possa ver as diferenças de desempenho entre essas técnicas, o [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) aplicativo contém uma página chamada **Bitmap gradiente** que cria um bitmap com pixels que combinam tons de vermelhos e azuis para criar um gradiente. O programa cria oito cópias diferentes deste bitmap, todos usando técnicas diferentes para definir os pixels do bitmap. Cada um desses oito bitmaps é criada em um método separado que também define uma breve descrição da técnica e calcula o tempo necessário para definir todos os pixels. Cada método percorre a lógica de configuração de pixel 100 vezes para obter uma melhor estimativa do desempenho.

### <a name="the-setpixel-method"></a>O método SetPixel

Se você só precisa definir ou obter vários pixels individuais, o [ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) e [ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) métodos são ideais. Para cada um desses dois métodos, você pode especificar a coluna de inteiro e a linha. Independentemente do formato de pixel, esses dois métodos permitem obter ou definir o pixel como um `SKColor` valor:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

O `col` argumento deve variar de 0 para um menor do que o `Width` propriedade do bitmap, e `row` varia de 0 para um menor do que o `Height` propriedade.

Aqui está o método **gradiente Bitmap** que define o conteúdo de um bitmap usando o `SetPixel` método. O bitmap é 256 por 256 pixels e o `for` loops são embutidos em código com o intervalo de valores:

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

A cor definida para cada pixel tem um componente vermelho igual à coluna de bitmap e um componente azul igual à linha. O bitmap resultante é preto no canto superior esquerdo, vermelho no canto superior direito, azul no inferior esquerdo e magenta no canto inferior direito, com gradientes em outro lugar.

O `SetPixel` método é chamado 65.536 vezes e independentemente eficiente como esse método pode ser, geralmente ele não é uma boa ideia fazer chamadas à API que muitos, se uma alternativa está disponível. Felizmente, existem várias alternativas.

### <a name="the-pixels-property"></a>A propriedade de Pixels

`SKBitmap` define uma [ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels) propriedade que retorna uma matriz de `SKColor` valores para o bitmap inteiro. Você também pode usar `Pixels` para definir uma matriz de valores de cores do bitmap:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Os pixels são organizados na matriz, começando com a primeira linha, da esquerda para a direita, em seguida, a segunda linha e assim por diante. O número total de cores na matriz é igual ao produto da altura e largura do bitmap.

Embora essa propriedade é exibida ser eficiente, tenha em mente que estão sendo copiados os pixels do bitmap na matriz e da matriz de volta para o bitmap, e os pixels são convertidos de e para `SKColor` valores.

Aqui está o método `GradientBitmapPage` classe que define o bitmap usando o `Pixels` propriedade. O método aloca uma `SKColor` matriz, o tamanho necessário, mas ele poderia ter usado o `Pixels` propriedade para criar essa matriz:

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Observe que o índice do `pixels` matriz precisa ser calculado a partir de `row` e `col` variáveis. A linha é multiplicada pelo número de pixels em cada linha (256 neste caso) e, em seguida, a coluna é adicionada.

`SKBitmap` também define um semelhante `Bytes` propriedade, que retorna uma matriz de bytes para o bitmap inteiro, mas é mais complicado para bitmaps de quatro cores.

### <a name="the-getpixels-pointer"></a>O ponteiro GetPixels

Potencialmente a técnica mais avançada para acessar os pixels de bitmap está [ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels), não deve ser confundido com o `GetPixel` método ou o `Pixels` propriedade. Imediatamente, você notará uma diferença com `GetPixels` em que ele retorna algo não muito comum na programação em c#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

O .NET [ `IntPtr` ](xref:System.IntPtr) tipo representa um ponteiro. Ele é chamado `IntPtr` porque ele é o comprimento de um número inteiro no processador nativo da máquina em que o programa é executado, geralmente 32 bits ou 64 bits de comprimento. O `IntPtr` que `GetPixels` retorna é o endereço do bloco de memória que o objeto de bitmap está usando para armazenar seus pixels real.

Você pode converter o `IntPtr` em c# ponteiro tipo usando o [ `ToPointer` ](xref:System.IntPtr.ToPointer) método. A sintaxe de ponteiro c# é o mesmo que o C e C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

O `ptr` variável é do tipo _ponteiro de bytes_. Isso `ptr` variável permite que você acesse os bytes individuais de memória que são usados para armazenar os pixels do bitmap. Você pode usar código como este para ler um byte essa memória ou gravar um byte na memória:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

Nesse contexto, o asterisco é a linguagem c# _operador de indireção_ e é usado para fazer referência ao conteúdo da memória apontada por `ptr`. Inicialmente, `ptr` aponta para o primeiro byte do primeiro pixel da primeira linha do bitmap, mas você pode executar cálculos aritméticos no `ptr` variável para movê-lo para outros locais dentro do bitmap.

Uma desvantagem é que você pode usar isso `ptr` variável apenas em um bloco de código marcado com o `unsafe` palavra-chave. Além disso, o assembly deve ser sinalizado como permitindo blocos inseguros. Isso é feito nas propriedades do projeto.

Usando ponteiros em c# é muito eficiente, mas também é muito perigoso. Você precisa ter cuidado para que você não acessar a memória além do que o ponteiro deve fazer referência. Isso ocorre porque o uso de ponteiro é associado com a palavra "unsafe".

Aqui está o método `GradientBitmapPage` classe que usa o `GetPixels` método. Observe que o `unsafe` bloco que abrange todo o código usando o ponteiro de bytes:

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Quando o `ptr` variável é obtida pela primeira vez o `ToPointer` método, ele aponta para o primeiro byte do pixel mais à esquerda da primeira linha do bitmap. O `for` executa um loop para `row` e `col` são configuradas para que `ptr` pode ser incrementado com o `++` operador depois de cada byte de cada pixel é definido. Para os 99 loops por meio de pixels, o `ptr` deve ser definido de volta para o início do bitmap.

Cada pixel é de quatro bytes de memória, então cada byte deve ser definido separadamente. O código aqui pressupõe que os bytes são vermelho de ordem, verde, azul e alfa, que é consistente com o `SKColorType.Rgba8888` tipo de cor. Você deve se lembrar de que isso é o tipo de cor padrão para iOS e Android, mas não para a plataforma Universal do Windows. Por padrão, a UWP cria bitmaps com o `SKColorType.Bgra8888` tipo de cor. Por esse motivo, espere ver alguns resultados diferentes nessa plataforma!

É possível converter o valor retornado de `ToPointer` para um `uint` ponteiro em vez de um `byte` ponteiro. Isso permite que um pixel inteiro a ser acessado em uma instrução. Aplicando o `++` operador para esse ponteiro incrementa a ele por quatro bytes para apontar para o próximo pixel:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

O pixel é definido usando o `MakePixel` método, que constrói um pixel inteiro dos componentes vermelhos, verdes, azuis e alfabéticos. Tenha em mente que o `SKColorType.Rgba8888` formato tem um byte de pixel ordenação como este:

RR GG BB AA

Mas o inteiro correspondente a esses bytes é:

AABBGGRR

O byte menos significativo do inteiro é armazenado em primeiro de acordo com a arquitetura little endian. Isso `MakePixel` método não funcionará corretamente para bitmaps com o `Bgra8888` tipo de cor.

O `MakePixel` método é marcado com o [ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions) opção a fim de incentivar o compilador Evite fazer isso um método separado, mas em vez disso, para compilar o código em que o método é chamado. Isso deve melhorar o desempenho.

Curiosamente, o `SKColor` estrutura define uma conversão explícita do `SKColor` em um inteiro sem sinal, o que significa que um `SKColor` valor pode ser criado e uma conversão `uint` pode ser usado em vez de `MakePixel`:

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

A única pergunta é: É o formato inteiro do `SKColor` valor na ordem `SKColorType.Rgba8888` do tipo de cor, ou o `SKColorType.Bgra8888` tipo de cor, ou é algo mais completo? A resposta para essa pergunta deverão ser revelada em breve.

### <a name="the-setpixels-method"></a>O método SetPixels

`SKBitmap` também define um método chamado [ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), que você chama assim:

```csharp
bitmap.SetPixels(intPtr);
```

Lembre-se de que `GetPixels` obtém um `IntPtr` referenciando o bloco de memória usado pelo bitmap para armazenar seus pixels. O `SetPixels` chamar _substitui_ esse bloco de memória com o bloco de memória referenciado pela `IntPtr` especificado como o `SetPixels` argumento. O bitmap, em seguida, libera o bloco de memória que ele estava usando anteriormente. Na próxima vez que `GetPixels` é chamado, ele obtém o bloco de memória definido com `SetPixels`.

A princípio, parece como se `SetPixels` lhe não mais potência e desempenho que `GetPixels` sendo menos conveniente. Com `GetPixels` obter o bloco de memória do bitmap e acessá-lo. Com `SetPixels` você alocar memória de acesso e, em seguida, defina-a como o bloco de memória do bitmap.

Mas o `SetPixels` uso de oferece uma vantagem sintática distinta: Ele permite que você acesse os bits de pixel de bitmap usando uma matriz. Aqui está o método `GradientBitmapPage` que demonstra essa técnica. Primeiro, o método define uma matriz de bytes multidimensionais correspondentes aos bytes de pixels do bitmap. A primeira dimensão é a linha, a segunda dimensão é a coluna e corresponde de opção a terceira dimensão para os quatro componentes de cada pixel:

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Em seguida, após a matriz tem sido repleta de pixels, uma `unsafe` bloco e um `fixed` instrução é usada para obter um ponteiro de bytes que aponta para essa matriz. Esse ponteiro de bytes, em seguida, pode ser convertido em um `IntPtr` para passar para `SetPixels`.

A matriz que você cria não precisa ser uma matriz de bytes. Ele pode ser uma matriz de inteiros com apenas duas dimensões para a linha e coluna:

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

O `MakePixel` método novamente é usado para combinar os componentes de cor em um pixel de 32 bits.

Apenas para fins de integridade, aqui está o mesmo código, mas com um `SKColor` valor convertido em um inteiro sem sinal:

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>Comparando as técnicas

O construtor do **cor gradiente** página chama os oito métodos mostrados acima e salva os resultados:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

O construtor é concluído com a criação de um `SKCanvasView` para exibir os bitmaps resultantes. O `PaintSurface` manipulador divide sua superfície em oito retângulos e chamadas `Display` para exibir cada um deles:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

Para permitir que o compilador otimizar o código, esta página foi executada no **versão** modo. Aqui está a página em execução em um simulador de iPhone 8 em um MacBook Pro, um telefone Android do Nexus 5 e 3 do Surface Pro executando o Windows 10. Por causa das diferenças de hardware, evite comparando os tempos de desempenho entre os dispositivos, mas em vez disso, examinar os horários relativos em cada dispositivo:

[![Bitmap de gradiente](pixel-bits-images/GradientBitmap.png "Bitmap gradiente")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Aqui está uma tabela que consolida os tempos de execução em milissegundos:

| API       | Tipo de dados | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3,49 |
| Pixels    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0.10 |
|           | uint      | 0,06 |    0.26 | 0,05 |
|           | SKColor   | 0.29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0,14 |    0.69 | 0,06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

Conforme o esperado, chamar `SetPixel` vezes 65.536 é a maneira effeicient mínimo definido pixels do bitmap. Preenchendo um `SKColor` matriz e definindo o `Pixels` propriedade é muito melhor e ainda é comparativamente com alguns do `GetPixels` e `SetPixels` técnicas. Trabalhando com `uint` valores de pixel é geralmente mais rápido do que a configuração separada `byte` componentes e convertendo o `SKColor` valor a ser um inteiro sem sinal adiciona alguma sobrecarga para o processo.

Também é interessante comparar os vários gradientes: As linhas superiores de cada plataforma são as mesmas e mostram o gradiente como era pretendido. Isso significa que o `SetPixel` método e o `Pixels` propriedade criar corretamente pixels de cores, independentemente do formato de pixel subjacente.

As próximas duas linhas do iOS e Android capturas de tela também são os mesmos, o que confirma que o pouco `MakePixel` método está definido corretamente para o padrão `Rgba8888` formato de pixel para essas plataformas.

A linha inferior do iOS e Android capturas de tela é com versões anteriores, que indica que o inteiro sem sinal é obtida ao converter um `SKColor` valor está no formato:

AARRGGBB

Os bytes estão na ordem:

BB GG RR AA

Esse é o `Bgra8888` ordenação em vez de `Rgba8888` ordenação. O `Brga8888` formato é o padrão para a plataforma Windows Universal, que é o motivo pelo qual os gradientes na última linha nessa captura de tela são o mesmo que a primeira linha. Mas as duas linhas intermediárias estiverem incorretas, porque o código de criar esses bitmaps presume um `Rgba8888` ordenação.

Se você quiser usar o mesmo código para acessar os bits de pixel em cada plataforma, você pode criar explicitamente uma `SKBitmap` usando o `Rgba8888` ou `Bgra8888` formato. Se você quiser converter `SKColor` valores aos pixels do bitmap, use `Bgra8888`.

## <a name="random-access-of-pixels"></a>Acesso aleatório de pixels

O `FillBitmapBytePtr` e `FillBitmapUintPtr` métodos na **Bitmap gradiente** página se beneficiou da `for` loops projetados para preencher o bitmap sequencialmente, na linha superior, a linha inferior e, em cada linha da esquerda para a direita. O pixel poderia ser definido com a mesma instrução que incrementado o ponteiro.

Às vezes, é necessário acessar os pixels aleatoriamente, em vez de sequencialmente. Se você estiver usando o `GetPixels` abordagem, você precisará calcular ponteiros com base em linha e coluna. Isso é demonstrado na **Rainbow seno** página, que cria um bitmap mostrando um rainbow na forma de um ciclo de uma curva de seno.

As cores do arco-íris são mais fáceis de criar usando o modelo de cor HSL (matiz, saturação, luminosidade). O `SKColor.FromHsl` método cria um `SKColor` valor usando os valores de matiz que variam de 0 a 360 (como os ângulos de um círculo, mas vai de vermelho, por meio de verde e azul e de volta para vermelho) e os valores de saturação e luminosidade variando de 0 a 100. Para as cores de um arco-íris, a saturação deve ser definida como um máximo de 100 e a luminosidade para um ponto médio de 50.

**Seno de Rainbow** cria essa imagem fazendo um loop pelas linhas de bitmap e, em seguida, um loop por meio de valores de matiz 360. Cada valor de matiz, ele calcula uma coluna de bitmap que também se baseia em um valor do seno:

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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

Observe que o construtor cria o bitmap com base no `SKColorType.Bgra8888` formato:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Isso permite que o programa para usar a conversão de `SKColor` valores em `uint` pixels sem se preocupar. Embora ele não desempenham um papel nesse programa específico, sempre que você usar o `SKColor` conversão de definir pixels, você também deve especificar `SKAlphaType.Unpremul` porque `SKColor` não pré-multiplicar seus componentes de cor pelo valor alfa.

O construtor, em seguida, usa o `GetPixels` método para obter um ponteiro para o primeiro pixel do bitmap:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Para qualquer linha e coluna específicas, um valor de deslocamento deve ser adicionado à `basePtr`. Esse deslocamento é a linha vezes a largura do bitmap, além da coluna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

O `SKColor` valor é armazenado na memória usando esse ponteiro:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

No `PaintSurface` manipulador do `SKCanvasView`, o bitmap é esticado para preencher a área de exibição:

[![Seno de Rainbow](pixel-bits-images/RainbowSine.png "Rainbow seno")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>De um bitmap para outro

Muitas tarefas de processamento de imagem envolvem a modificação pixels como eles são transferidos de um bitmap para outro. Essa técnica é demonstrada na **ajuste de cores** página. A página carrega um dos recursos de bitmap e, em seguida, permite que você modifique a imagem usando três `Slider` modos de exibição:

[![Ajuste de cor](pixel-bits-images/ColorAdjustment.png "ajuste de cor")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Para cada cor do pixel, o primeiro `Slider` adiciona um valor de 0 a 360 para o matiz, mas, em seguida, usa o operador de módulo para manter o resultado entre 0 e 360, efetivamente mudando as cores espectro (como demonstra a captura de tela UWP). A segunda `Slider` permite que você selecione um fator de multiplicação entre 0,5 e 2 para aplicar a saturação e o terceiro `Slider` faz o mesmo para a luminosidade, conforme demonstrado na captura de tela Android.

O programa mantém dois bitmaps, o bitmap de origem original denominado `srcBitmap` e o bitmap de destino ajustado chamado `dstBitmap`. Cada vez que um `Slider` for movido, o programa calcula todos os pixels de novo no `dstBitmap`. Obviamente, os usuários serão experimentar, movendo o `Slider` exibições muito rapidamente, portanto, você o melhor desempenho, você pode gerenciar. Isso envolve a `GetPixels` método para bitmaps de origem e de destino.

O **ajuste de cores** página não controla o formato de cor dos bitmaps de origem e de destino. Em vez disso, ele contém a lógica ligeiramente diferente para `SKColorType.Rgba8888` e `SKColorType.Bgra8888` formatos. A origem e destino podem ser diferentes formatos e o programa ainda funcionará.

Este é o programa, exceto o crucial `TransferPixels` a origem para o destino de formulário do método que transfere os pixels. O construtor define `dstBitmap` igual a `srcBitmap`. O `PaintSurface` manipulador exibe `dstBitmap`:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

O `ValueChanged` manipulador para o `Slider` modos de exibição calcula os valores de ajuste e chamadas `TransferPixels`.

Todo o `TransferPixels` método é marcado como `unsafe`. Ele começa com a obtenção de ponteiros de byte para os bits de pixel de ambos os bitmaps e, em seguida, executa um loop em todas as linhas e colunas. Do bitmap de origem, o método obtém quatro bytes para cada pixel. Eles podem ser em qualquer um de `Rgba8888` ou `Bgra8888` ordem. Verificar para o tipo de cor permite que um `SKColor` valor a ser criado. Os componentes HSL, em seguida, são extraídos, ajustados e usados para recriar o `SKColor` valor. Dependendo se o bitmap de destino for `Rgba8888` ou `Bgra8888`, os bytes são armazenados em bitmp o destino:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

É provável que o desempenho desse método poderia ser melhorada ainda mais com a criação de métodos separados para as várias combinações de tipos de cor dos bitmaps de origem e de destino e evitar a verificação de tipo para cada pixel. Outra opção é ter vários `for` executa um loop para o `col` variável com base no tipo de cor.

## <a name="posterization"></a>Posterização

Há outro trabalho comum que envolve acessar os bits de pixel _posterização_. O número se cores codificado em pixels do bitmap é reduzido para que o resultado é semelhante a um pôster desenhado usando uma paleta de cores limitado.

O **Posterizar** página executa esse processo em uma das imagens monkey:

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

O código no construtor acessa cada pixel, executa uma operação AND bit a bit com o valor 0xE0E0E0FF e, em seguida, armazena o resultado voltar no bitmap. Os valores 0xE0E0E0FF mantém 3 bits altos de cada componente de cor e define os bits inferiores de 5 como 0. Em vez de 2<sup>24</sup> ou 16.777.216 cores, o bitmap é reduzido para 2<sup>9</sup> ou 512 cores:

[![Posterizar](pixel-bits-images/Posterize.png "Posterizar")](pixel-bits-images/Posterizar-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
