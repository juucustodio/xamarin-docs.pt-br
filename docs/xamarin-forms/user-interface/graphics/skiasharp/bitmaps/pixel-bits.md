---
Título: "acessando SkiaSharp bits de pixel de bitmap" Descrição: "Descubra as várias técnicas para acessar e modificar os bits de pixel de bitmaps SkiaSharp".
MS. Prod: xamarin MS. Technology: xamarin-skiasharp MS. AssetID: DBB58522-F816-4A8C-96A5-E0236F16A5C6 autor: davidbritch MS. Author: dabritch MS. Date: 07/11/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Acessando bits de pixel de bitmap SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vimos no artigo [**salvar bitmaps SkiaSharp em arquivos**](saving.md), os bitmaps são geralmente armazenados em arquivos em um formato compactado, como JPEG ou png. No constrast, um bitmap SkiaSharp armazenado na memória não é compactado. Ele é armazenado como uma série sequencial de pixels. Esse formato descompactado facilita a transferência de bitmaps para uma superfície de exibição.

O bloco de memória ocupado por um bitmap SkiaSharp é organizado de maneira muito simples: ele começa com a primeira linha de pixels, da esquerda para a direita e continua com a segunda linha. Para bitmaps de cor completa, cada pixel consiste em quatro bytes, o que significa que o espaço total de memória exigido pelo bitmap é quatro vezes o produto de sua largura e altura.

Este artigo descreve como um aplicativo pode obter acesso a esses pixels, seja diretamente acessando o bloco de memória de pixel do bitmap ou indiretamente. Em alguns casos, um programa pode querer analisar os pixels de uma imagem e construir um histograma de algum tipo. Mais comumente, os aplicativos podem construir imagens exclusivas forma algorítmica criando os pixels que compõem o bitmap:

![Amostras de bits de pixel](pixel-bits-images/PixelBitsSample.png "Exemplo de bits de pixel")

## <a name="the-techniques"></a>As técnicas

O SkiaSharp fornece várias técnicas para acessar os bits de pixel de um bitmap. O que você escolhe é geralmente um comprometimento entre a conveniência da codificação (que está relacionada à manutenção e à facilidade de depuração) e ao desempenho. Na maioria dos casos, você usará um dos seguintes métodos e propriedades do `SKBitmap` para acessar os pixels do bitmap:

- Os `GetPixel` `SetPixel` métodos e permitem que você obtenha ou defina a cor de um único pixel.
- A `Pixels` propriedade Obtém uma matriz de cores de pixel para o bitmap inteiro ou define a matriz de cores.
- `GetPixels`Retorna o endereço da memória de pixel usada pelo bitmap.
- `SetPixels`Substitui o endereço da memória de pixel usada pelo bitmap.

Você pode considerar as duas primeiras técnicas como "alto nível" e as segunda, como "nível baixo". Há alguns outros métodos e propriedades que você pode usar, mas esses são os mais valiosos.

Para permitir que você veja as diferenças de desempenho entre essas técnicas, o aplicativo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contém uma página chamada **bitmap gradiente** que cria um bitmap com pixels que combinam sombras vermelhas e azuis para criar um gradiente. O programa cria oito cópias diferentes desse bitmap, usando técnicas diferentes para definir os pixels de bitmap. Cada um desses oito bitmaps é criado em um método separado que também define uma breve descrição de texto da técnica e calcula o tempo necessário para definir todos os pixels. Cada método percorre a lógica de configuração de pixel 100 vezes para obter uma melhor estimativa do desempenho.

### <a name="the-setpixel-method"></a>O método SetPixel

Se você só precisa definir ou obter vários pixels individuais, os [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) métodos e [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) são ideais. Para cada um desses dois métodos, você especifica a coluna e a linha de inteiros. Independentemente do formato de pixel, esses dois métodos permitem obter ou definir o pixel como um `SKColor` valor:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

O `col` argumento deve variar de 0 a um menor que a `Width` Propriedade do bitmap e `row` varia de 0 para um menor que a `Height` propriedade.

Este é o método no **bitmap de gradiente** que define o conteúdo de um bitmap usando o `SetPixel` método. O bitmap é 256 por 256 pixels e os `for` loops são embutidos em código com o intervalo de valores:

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

O conjunto de cores para cada pixel tem um componente vermelho igual à coluna de bitmap e um componente azul igual à linha. O bitmap resultante é preto no canto superior esquerdo, vermelho na parte superior direita, azul no canto inferior esquerdo e magenta no canto inferior direito, com gradientes em outro lugar.

O `SetPixel` método é chamado de 65.536 vezes e, independentemente do quão eficiente, esse método pode ser, geralmente não é uma boa ideia fazer tantas chamadas à API se uma alternativa estiver disponível. Felizmente, há várias alternativas.

### <a name="the-pixels-property"></a>A propriedade pixels

`SKBitmap`define uma [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) propriedade que retorna uma matriz de `SKColor` valores para o bitmap inteiro. Você também pode usar `Pixels` para definir uma matriz de valores de cor para o bitmap:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Os pixels são organizados na matriz a partir da primeira linha, da esquerda para a direita, da segunda linha e assim por diante. O número total de cores na matriz é igual ao produto da largura e altura do bitmap.

Embora essa propriedade pareça ser eficiente, tenha em mente que os pixels estão sendo copiados do bitmap para a matriz e da matriz de volta para o bitmap, e os pixels são convertidos de e para `SKColor` valores.

Aqui está o método na `GradientBitmapPage` classe que define o bitmap usando a `Pixels` propriedade. O método aloca uma `SKColor` matriz do tamanho necessário, mas pode ter usado a `Pixels` propriedade para criar essa matriz:

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

Observe que o índice da `pixels` matriz precisa ser calculado a partir das `row` variáveis e `col` . A linha é multiplicada pelo número de pixels em cada linha (256 nesse caso) e, em seguida, a coluna é adicionada.

`SKBitmap`também define uma `Bytes` Propriedade semelhante, que retorna uma matriz de bytes para todo o bitmap, mas é mais complicada para bitmaps de cor completa.

### <a name="the-getpixels-pointer"></a>O ponteiro de getPixels

Potencialmente, a técnica mais potente para acessar os pixels de bitmap é [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) , não deve ser confundida com o `GetPixel` método ou a `Pixels` propriedade. Você perceberá imediatamente uma diferença em `GetPixels` que ela retorna algo que não é muito comum na programação em C#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

O [`IntPtr`](xref:System.IntPtr) tipo .net representa um ponteiro. Ele é chamado `IntPtr` porque é o comprimento de um inteiro no processador nativo do computador no qual o programa é executado, geralmente 32 bits ou 64 bits de comprimento. O `IntPtr` que `GetPixels` retorna é o endereço do bloco real de memória que o objeto bitmap está usando para armazenar seus pixels.

Você pode converter o `IntPtr` em um tipo de ponteiro C# usando o [`ToPointer`](xref:System.IntPtr.ToPointer) método. A sintaxe do ponteiro do C# é igual a C e C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

A `ptr` variável é do tipo _ponteiro de byte_. Essa `ptr` variável permite que você acesse os bytes individuais de memória que são usados para armazenar os pixels do bitmap. Você usa um código como este para ler um byte desta memória ou gravar um byte na memória:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

Nesse contexto, o asterisco é o operador de _indireção_ de C# e é usado para fazer referência ao conteúdo da memória apontada pelo `ptr` . Inicialmente, `ptr` aponta para o primeiro byte do primeiro pixel da primeira linha do bitmap, mas você pode executar aritmética na `ptr` variável para movê-lo para outros locais no bitmap.

Uma desvantagem é que você pode usar essa `ptr` variável somente em um bloco de código marcado com a `unsafe` palavra-chave. Além disso, o assembly deve ser sinalizado como permitir blocos não seguros. Isso é feito nas propriedades do projeto.

O uso de ponteiros em C# é muito potente, mas também muito perigoso. Você precisa ter cuidado para não acessar a memória além da que o ponteiro deve referenciar. É por isso que o uso de ponteiro está associado à palavra "não seguro".

Este é o método na `GradientBitmapPage` classe que usa o `GetPixels` método. Observe o `unsafe` bloco que abrange todo o código usando o ponteiro de byte:

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

Quando a `ptr` variável é obtida pela primeira vez a partir do `ToPointer` método, ela aponta para o primeiro byte do pixel mais à esquerda da primeira linha do bitmap. Os `for` loops para `row` e `col` são configurados para que `ptr` possam ser incrementados com o `++` operador depois que cada byte de cada pixel é definido. Para os outros 99 loops pelos pixels, o `ptr` deve ser definido de volta para o início do bitmap.

Cada pixel tem quatro bytes de memória, portanto, cada byte precisa ser definido separadamente. O código aqui pressupõe que os bytes estão na ordem vermelha, verde, azul e alfa, que é consistente com o tipo de `SKColorType.Rgba8888` cor. Você pode se lembrar de que esse é o tipo de cor padrão para iOS e Android, mas não para o Plataforma Universal do Windows. Por padrão, o UWP cria bitmaps com o `SKColorType.Bgra8888` tipo de cor. Por esse motivo, espere ver alguns resultados diferentes nessa plataforma!

É possível converter o valor retornado de `ToPointer` para um `uint` ponteiro em vez de um `byte` ponteiro. Isso permite que um pixel inteiro seja acessado em uma única instrução. A aplicação do `++` operador a esse ponteiro o incrementa em quatro bytes para apontar para o próximo pixel:

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

O pixel é definido usando o `MakePixel` método, que constrói um pixel inteiro dos componentes vermelho, verde, azul e alfa. Tenha em mente que o `SKColorType.Rgba8888` formato tem uma ordenação de bytes de pixel como esta:

RR GG BB AA

Mas o inteiro correspondente a esses bytes é:

AABBGGRR

O byte menos significativo do inteiro é armazenado primeiro de acordo com a arquitetura little-endian. Esse `MakePixel` método não funcionará corretamente para bitmaps com o `Bgra8888` tipo de cor.

O `MakePixel` método é sinalizado com a [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) opção de encorajar o compilador a evitar tornar esse um método separado, mas em vez de compilar o código no qual o método é chamado. Isso deve melhorar o desempenho.

Curiosamente, a `SKColor` estrutura define uma conversão explícita de `SKColor` para um inteiro não assinado, o que significa que um `SKColor` valor pode ser criado e uma conversão para `uint` pode ser usada em vez de `MakePixel` :

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

A única pergunta é: é o formato inteiro do `SKColor` valor na ordem do `SKColorType.Rgba8888` tipo de cor, ou o `SKColorType.Bgra8888` tipo de cor, ou é algo mais completo? A resposta para essa pergunta deve ser revelada em breve.

### <a name="the-setpixels-method"></a>O método setPixels

`SKBitmap`também define um método chamado [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) , que você chama da seguinte maneira:

```csharp
bitmap.SetPixels(intPtr);
```

Lembre-se de que `GetPixels` Obtém uma `IntPtr` referência ao bloco de memória usado pelo bitmap para armazenar seus pixels. A `SetPixels` chamada _substitui_ esse bloco de memória pelo bloco de memória referenciado pelo `IntPtr` especificado como o `SetPixels` argumento. Em seguida, o bitmap libera o bloco de memória que estava usando anteriormente. Na próxima vez `GetPixels` que for chamado, ele obterá o conjunto de blocos de memória com `SetPixels` .

A princípio, parece que se `SetPixels` não oferece mais energia e desempenho do que `GetPixels` embora seja menos conveniente. Com `GetPixels` o, você obtém o bloco de memória de bitmap e o acessa. Com `SetPixels` você alocar e acessar alguma memória e, em seguida, defini-la como o bloco de memória de bitmap.

Mas o uso de `SetPixels` oferece uma vantagem sintática distinta: ela permite que você acesse os bits de pixel de bitmap usando uma matriz. Este é o método `GradientBitmapPage` que demonstra essa técnica. O método primeiro define uma matriz de bytes multidimensional que corresponde aos bytes dos pixels do bitmap. A primeira dimensão é a linha, a segunda dimensão é a coluna e a terceira dimensão corresonds para os quatro componentes de cada pixel:

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

Depois, após a matriz ter sido preenchida com pixels, um `unsafe` bloco e uma `fixed` instrução são usados para obter um ponteiro de byte que aponta para essa matriz. Esse ponteiro de byte pode então ser convertido em um `IntPtr` para passar para `SetPixels` .

A matriz que você cria não precisa ser uma matriz de bytes. Pode ser uma matriz de inteiros com apenas duas dimensões para a linha e coluna:

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

O `MakePixel` método é usado novamente para combinar os componentes de cor em um pixel de 32 bits.

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

O construtor da página de **cores de gradiente** chama todos os oito métodos mostrados acima e salva os resultados:

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

O Construtor termina criando um `SKCanvasView` para exibir os bitmaps resultantes. O `PaintSurface` manipulador divide sua superfície em oito retângulos e chamadas `Display` para exibir cada uma delas:

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

Para permitir que o compilador Otimize o código, essa página foi executada no modo de **liberação** . Aqui está a página em execução em um simulador do iPhone 8 em um MacBook Pro, um telefone com o Nexus 5 Android e o Surface Pro 3 executando o Windows 10. Devido às diferenças de hardware, evite comparar os tempos de desempenho entre os dispositivos, mas em vez disso, examine os horários relativos em cada dispositivo:

[![Bitmap de gradiente](pixel-bits-images/GradientBitmap.png "Bitmap de gradiente")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Aqui está uma tabela que consolida os tempos de execução em milissegundos:

| API       | Tipo de dados | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3,17 |   10,77 | 3.49 |
| Pixels    |           | 0,32 |    1,23 | 0,07 |
| GetPixels | byte      | 0, 9 |    0.24 | 0.10 |
|           | uint      | 0.06 |    0,26 | 0,05 |
|           | SKColor   | 0,29 |    0.99 | 0,07 |
| SetPixels | byte      | 1.33 |    6,78 | 0,11 |
|           | uint      | 0.14 |    0,69 | 0.06 |
|           | SKColor   | 0,35 |    1,93 | 0.10 |

Como esperado, `SetPixel` a chamada a 65.536 vezes é a maneira menos effeicient de definir os pixels de um bitmap. Preencher uma `SKColor` matriz e definir a `Pixels` propriedade é muito melhor e até mesmo se comparam de maneira favorável com algumas das `GetPixels` `SetPixels` técnicas e. Trabalhar com `uint` valores de pixel geralmente é mais rápido do que definir `byte` componentes separados, e converter o `SKColor` valor em um inteiro sem sinal adiciona alguma sobrecarga ao processo.

Também é interessante comparar os vários gradientes: as principais linhas de cada plataforma são as mesmas e mostrar o gradiente como era pretendido. Isso significa que o `SetPixel` método e a `Pixels` Propriedade criam pixels corretamente a partir de cores, independentemente do formato de pixel subjacente.

As duas próximas linhas das capturas de tela do iOS e do Android também são as mesmas, o que confirma que o pequeno `MakePixel` método está corretamente definido para o `Rgba8888` formato de pixel padrão para essas plataformas.

A linha inferior das capturas de tela do iOS e do Android é retroativa, o que indica que o inteiro sem sinal obtido pela conversão de um `SKColor` valor está no formato:

AARRGGBB

Os bytes estão na ordem:

BB GG RR AA

Essa é a `Bgra8888` ordenação em vez da `Rgba8888` ordenação. O `Brga8888` formato é o padrão para a plataforma universal do Windows, que é o motivo pelo qual os gradientes na última linha da captura de tela são iguais à primeira linha. Mas as duas linhas intermediárias estão incorretas porque o código que cria esses bitmaps assumiu uma `Rgba8888` ordenação.

Se você quiser usar o mesmo código para acessar bits de pixel em cada plataforma, você pode criar explicitamente um `SKBitmap` usando o `Rgba8888` formato ou `Bgra8888` . Se você quiser converter `SKColor` valores em pixels de bitmap, use `Bgra8888` .

## <a name="random-access-of-pixels"></a>Acesso aleatório de pixels

Os `FillBitmapBytePtr` `FillBitmapUintPtr` métodos e na página de **bitmap de gradiente** se beneficiaram de `for` loops projetados para preencher o bitmap sequencialmente, de linha superior para inferior e em cada linha da esquerda para a direita. O pixel pode ser definido com a mesma instrução que incrementou o ponteiro.

Às vezes, é necessário acessar os pixels aleatoriamente em vez de sequencialmente. Se estiver usando a `GetPixels` abordagem, você precisará calcular ponteiros com base na linha e na coluna. Isso é demonstrado na página **arco arco-íris** , que cria um bitmap mostrando um arco-íris na forma de um ciclo de uma curva senoidal.

As cores do arco-íris são mais fáceis de criar usando o modelo de cores HSL (Matiz, saturação, luminosidade). O `SKColor.FromHsl` método cria um `SKColor` valor usando valores de matiz que variam de 0 a 360 (como os ângulos de um círculo, mas que vão de vermelho, por meio de verde e azul, e de volta para vermelho), e valores de saturação e luminosidade variando de 0 a 100. Para as cores de um arco-íris, a saturação deve ser definida como um máximo de 100 e a luminosidade para um ponto médio de 50.

O **arco-íris senoidal** cria essa imagem fazendo um loop pelas linhas do bitmap e, em seguida, fazendo um loop por meio de valores de matiz de 360. De cada valor de matiz, ele calcula uma coluna de bitmap que também se baseia em um valor seno:

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

Isso permite que o programa use a conversão de `SKColor` valores em `uint` pixels sem se preocupar. Embora não jogue uma função nesse programa em particular, sempre que você usar a `SKColor` conversão para definir pixels, você também deve especificar `SKAlphaType.Unpremul` porque `SKColor` o não multiplique os componentes de cor pelo valor alfa.

Em seguida, o construtor usa o `GetPixels` método para obter um ponteiro para o primeiro pixel do bitmap:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Para qualquer linha e coluna específica, um valor de deslocamento deve ser adicionado a `basePtr` . Esse deslocamento é a linha vezes a largura do bitmap, além da coluna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

O `SKColor` valor é armazenado na memória usando este ponteiro:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

No `PaintSurface` manipulador do `SKCanvasView` , o bitmap é alongado para preencher a área de exibição:

[![Seno de arco-íris](pixel-bits-images/RainbowSine.png "Seno de arco-íris")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>De um bitmap para outro

Muitas tarefas de processamento de imagens envolvem a modificação de pixels à medida que são transferidas de um bitmap para outro. Essa técnica é demonstrada na página de **ajuste de cor** . A página carrega um dos recursos de bitmap e, em seguida, permite que você modifique a imagem usando três `Slider` exibições:

[![Ajuste de cor](pixel-bits-images/ColorAdjustment.png "Ajuste de cor")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Para cada cor de pixel, o primeiro `Slider` adiciona um valor de 0 a 360 ao matiz, mas, em seguida, usa o operador de módulo para manter o resultado entre 0 e 360, deslocando efetivamente as cores ao longo do espectro (como demonstra a captura de tela do UWP). O segundo `Slider` permite que você selecione um fator de multiplicativa entre 0,5 e 2 para aplicar à saturação, e o terceiro `Slider` faz o mesmo para a luminosidade, conforme demonstrado na captura de tela do Android.

O programa mantém dois bitmaps, o bitmap de origem original chamado `srcBitmap` e o bitmap de destino ajustado chamado `dstBitmap` . Cada vez `Slider` que um é movido, o programa calcula todos os novos pixels no `dstBitmap` . É claro que os usuários experimentarão movendo as `Slider` exibições muito rapidamente, para que você queira o melhor desempenho que pode gerenciar. Isso envolve o `GetPixels` método para os bitmaps de origem e de destino.

A página de **ajuste de cor** não controla o formato de cor dos bitmaps de origem e de destino. Em vez disso, ele contém uma lógica ligeiramente diferente para `SKColorType.Rgba8888` `SKColorType.Bgra8888` formatos e. A origem e o destino podem ser formatos diferentes e o programa ainda funcionará.

Aqui está o programa, exceto o `TransferPixels` método crucial que transfere os pixels do formulário de origem para o destino. Os conjuntos de Construtor são `dstBitmap` iguais a `srcBitmap` . O `PaintSurface` manipulador exibe `dstBitmap` :

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

O `ValueChanged` manipulador para os `Slider` modos de exibição calcula os valores de ajuste e as chamadas `TransferPixels` .

O `TransferPixels` método inteiro é marcado como `unsafe` . Ele começa obtendo ponteiros de byte para os bits de pixel de ambos os bitmaps e, em seguida, executa um loop em todas as linhas e colunas. No bitmap de origem, o método obtém quatro bytes para cada pixel. Eles podem estar na `Rgba8888` `Bgra8888` ordem ou. A verificação do tipo de cor permite que um `SKColor` valor seja criado. Os componentes HSL são, então, extraídos, ajustados e usados para recriar o `SKColor` valor. Dependendo se o bitmap de destino é `Rgba8888` ou `Bgra8888` , os bytes são armazenados no bitmp de destino:

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

É provável que o desempenho desse método pudesse ser melhorado ainda mais criando métodos separados para as várias combinações de tipos de cor dos bitmaps de origem e de destino e evite verificar o tipo de cada pixel. Outra opção é ter vários `for` loops para a `col` variável com base no tipo de cor.

## <a name="posterization"></a>Posterização

Outro trabalho comum que envolve o acesso a bits de pixel é a _Posterização_. O número se as cores codificadas nos pixels de um bitmap forem reduzidas para que o resultado seja semelhante a um pôster desenhado por mão usando uma paleta de cores limitada.

A página de **Posterização** executa esse processo em uma das imagens de macaco:

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

O código no Construtor acessa cada pixel, executa uma operação AND de bit a bit com o valor 0xE0E0E0FF e, em seguida, armazena o resultado no bitmap. Os valores 0xE0E0E0FF mantêm os 3 bits altos de cada componente de cor e definem os 5 bits inferiores como 0. Em vez de 2<sup>24</sup> ou 16.777.216 cores, o bitmap é reduzido para 2<sup>9</sup> ou 512 cores:

[![Alto Contraste](pixel-bits-images/Posterize.png "Alto Contraste")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
