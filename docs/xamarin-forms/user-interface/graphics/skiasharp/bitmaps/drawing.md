---
title: Criando e desenho em bitmaps de SkiaSharp
description: Aprenda a criar bitmaps de SkiaSharp e, em seguida, desenhe nesses bitmaps, criando uma tela com base neles.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 68d6cb1df8557b6055feb81b21ed5513592c71c4
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198151"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Criando e desenho em bitmaps de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Você viu como um aplicativo pode carregar bitmaps da Web, de recursos do aplicativo e da biblioteca de fotos do usuário. Também é possível criar novos bitmaps dentro de seu aplicativo. A abordagem mais simples envolve um dos construtores de [ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

O `width` e `height` parâmetros são inteiros e especificar as dimensões de pixel do bitmap. Este construtor cria um bitmap colorido com quatro bytes por pixel: um byte cada para vermelho, verde, azul e os componentes alfa (opacity). 

Depois de criar um novo bitmap, você precisa obter algo na superfície do bitmap. Você geralmente pode fazer isso de duas maneiras:

- Desenhar o bitmap usando o padrão `Canvas` métodos de desenho.
- Acesse diretamente os bits de pixel.

Este artigo demonstra a primeira abordagem:

![Exemplo de desenho](drawing-images/DrawingSample.png "exemplo de desenho")

A segunda abordagem é discutida no artigo [ **acessando Pixels do Bitmap de SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>O bitmap de desenho

Desenho na superfície de um bitmap é o mesmo que um monitor de vídeo de desenho. Para desenhar em um monitor de vídeo, você deve obter um `SKCanvas` objeto o `PaintSurface` argumentos do evento. Para desenhar em um bitmap, você cria um `SKCanvas` do objeto usando o [ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) construtor:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Quando você terminar de desenhar o bitmap, você pode descartar o `SKCanvas` objeto. Por esse motivo, o `SKCanvas` construtor geralmente é chamado um `using` instrução:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

O bitmap pode ser exibido. Em um momento posterior, o programa pode criar um novo `SKCanvas` objeto com base no mesmo de bitmap e desenhar nele mais alguns.

O **Bitmap Hello** página de **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicativo grava o texto "Olá, Bitmap!" em um bitmap e, em seguida, exibe o bitmap várias vezes.  

O construtor do `HelloBitmapPage` começa criando um `SKPaint` objeto para exibir o texto. Ele determina as dimensões de uma cadeia de caracteres de texto e cria um bitmap com essas dimensões. Em seguida, ele cria um `SKCanvas` objeto baseado nesse bitmap, chamadas `Clear`e, em seguida, chama `DrawText`. É sempre uma boa ideia chamar `Clear` com um novo bitmap porque um bitmap criado recentemente pode conter dados aleatórios.

O construtor é concluído com a criação de um `SKCanvasView` objeto para exibir o bitmap:

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

O `PaintSurface` manipulador processa o bitmap várias vezes em linhas e colunas da exibição. Observe que o `Clear` método na `PaintSurface` manipulador tem um argumento de `SKColors.Aqua`, quais cores de plano de fundo da superfície de exibição:

[![Olá, Bitmap! ](drawing-images/HelloBitmap.png "Hello, Bitmap!")](drawing-images/HelloBitmap-Large.png#lightbox)

A aparência da tela de fundo aqua revela que o bitmap é transparente, exceto para o texto.

## <a name="clearing-and-transparency"></a>Limpando e transparência

A exibição do **Bitmap Hello** página demonstra que o programa criado será transparente, exceto para o texto em preto. É por isso que a cor azul-piscina da superfície de exibição mostra por meio de.

A documentação dos `Clear` métodos de `SKCanvas` descreve-os com a instrução: "Substitui todos os pixels no clipe atual da tela." O uso da palavra "substitui" revela uma característica importante desses métodos: Todos os métodos de desenho `SKCanvas` de adicionar algo à superfície de exibição existente. O `Clear` métodos _substituir_ o que já está lá.

`Clear` existe em duas versões diferentes: 

- O [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) método com um `SKColor` parâmetro substitui os pixels da superfície de exibição com pixels dessa cor.

- O [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) método sem parâmetros substitui os pixels com o [ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty) cor, que é uma cor em que todos os componentes (vermelho, verde, azul e alfa) são definidos como zero. Às vezes, essa cor é conhecida como "preto transparente".

Chamando `Clear` sem argumentos em um novo bitmap inicializa o bitmap inteiro para ser totalmente transparente. Nada desenhado subsequentemente o bitmap geralmente será opacos ou parcialmente opacos.

Aqui está algo para tentar: Na página de **bitmap** de saudação, `Clear` substitua o `bitmapCanvas` método aplicado ao com este:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

A ordem dos `SKColor` parâmetros do construtor é vermelho, verde, azul e alfa, onde cada valor pode variar de 0 a 255. Tenha em mente que um valor alfa de 0 é transparente, enquanto um valor alfa de 255 é opaco.

O valor (255, 0, 0, 128) limpa os pixels de bitmap para pixels vermelhos com uma opacidade de 50%. Isso significa que o plano de fundo de bitmap é semitransparente. O plano de fundo vermelho semitransparente do bitmap combina com aqua plano de fundo da superfície de exibição para criar um plano de fundo cinza.

Tente definir a cor do texto para preto transparente, colocando a atribuição a seguir `SKPaint` inicializador:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Você pode pensar que esse texto transparente criaria áreas totalmente transparentes do bitmap por meio do qual você veria aqua plano de fundo da superfície de exibição. Mas que é não. O texto é desenhado na parte superior, o que já está no bitmap. O texto transparente não será visível em todos os.

Não `Draw` método nunca faz um bitmap mais transparente. Somente `Clear` pode fazer isso.

## <a name="bitmap-color-types"></a>Tipos de cores do bitmap

A forma mais simples `SKBitmap` construtor permite que você especifique uma largura de pixel inteiro e a altura do bitmap. Outros `SKBitmap` construtores são mais complexos. Esses construtores exigem argumentos de dois tipos de enumeração: [ `SKColorType` ](xref:SkiaSharp.SKColorType) e [ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType). Outros construtores usam o [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo) estrutura, que consolida essas informações.

O `SKColorType` enumeração tem 9 membros. Cada um desses membros descreve uma maneira específica de armazenar os pixels do bitmap:

- `Unknown`
- `Alpha8` &mdash; cada pixel é de 8 bits, que representa um valor alfa de totalmente transparente para completamente opaco
- `Rgb565` &mdash; cada pixel é de 16 bits, 5 bits para vermelho e azul e 6 para verde
- `Argb4444` &mdash; cada pixel é de 16 bits, 4 para alfa, vermelha, verde e azul
- `Rgba8888` &mdash; cada pixel é de 32 bits, 8 para vermelho, verde, azul e alfa
- `Bgra8888` &mdash; cada pixel é de 32 bits, 8 para alfa, vermelho, verde e azul
- `Index8` &mdash; cada pixel é de 8 bits e representa um índice em um [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; cada pixel é de 8 bits que representa um tom de cinza de preto para branco
- `RgbaF16` &mdash; cada pixel é de 64 bits, com vermelho, verde, azul e alfa em um formato de ponto flutuante de 16 bits

Os dois formatos em que cada pixel é 32 pixels (4 bytes) são chamados _colorida_ formatos. Muitos dos outra data de formatos de uma hora de quando o vídeo exibe em si não eram capazes de quatro cores. Bitmaps da cor limitado eram adequadas para estes vídeos e permitido bitmaps ocupar menos espaço na memória. 

Hoje em dia, os programadores quase sempre usam bitmaps colorida e não se preocupe com outros formatos. A exceção é o `RgbaF16` formato, o que permite a resolução de cor maior do que até mesmo os formatos de quatro cores. No entanto, esse formato é usado para fins especializados, como imagens médicas e não faz muito sentido quando usadas com exibições de quatro cores padrão.

Esta série de artigos restringirá a mesmo para o `SKBitmap` cor os formatos usados por padrão quando nenhum `SKColorType` membro for especificado. Esse formato padrão baseia-se na plataforma subjacente. Para plataformas compatíveis com o xamarin. Forms, o tipo de cor padrão é:

- `Rgba8888` para iOS e Android
- `Bgra8888` para a UWP

A única diferença é a ordem de 4 bytes na memória, e isso só se torna um problema quando você acessar diretamente os bits de pixel. Isso não se tornar importante até chegar ao artigo [ **acessando Pixels do Bitmap de SkiaSharp**](pixel-bits.md).

O `SKAlphaType` enumeração tem quatro membros:

- `Unknown`
- `Opaque` &mdash; o bitmap possui sem transparência
- `Premul` &mdash; componentes de cor previamente são multiplicados pelo componente alfa
- `Unpremul` &mdash; componentes de cor não são previamente multiplicados pelo componente alfa

Aqui está um pixel de bitmap vermelho de 4 bytes com 50% de transparência, com os bytes mostrado vermelho de ordem, verde, azul, alfa:

0xFF 0x00 0x00 0x80

Quando um bitmap que contém os pixels semitransparentes é renderizado em uma superfície de exibição, os componentes de cor de cada pixel de bitmap devem ser multiplicados pelo valor de alfa do pixel, que, e os componentes de cor do pixel correspondente da superfície de exibição devem ser multiplicados. por 255 menos o valor alfa. Os dois pixels, em seguida, podem ser combinados. O bitmap pode ser processado mais rapidamente se componentes de cor de pixels de bitmap já foram pré-mulitplied pelo valor alfa. Em um formato pré-multiplicado esse mesmo pixel vermelho seria armazenado como este:

0x80 0x00 0x00 0x80

Essa melhoria de desempenho é por isso `SkiaSharp` bitmaps por padrão são criados com um `Premul` formato. Mas, novamente, ele se torna necessário saber isso somente quando você acessar e manipular os bits de pixel.

## <a name="drawing-on-existing-bitmaps"></a>Desenho de bitmaps existente

Não é necessário criar um novo bitmap para desenhar nele. Você também pode desenhar em um bitmap existente. 

O **Monkey moustache (bigode)** página usa seu construtor para carregar o **MonkeyFace.png** imagem. Em seguida, ele cria um `SKCanvas` objeto baseado nesse bitmap e usa `SKPaint` e `SKPath` objetos para desenhar um moustache (bigode) nele:

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result 
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

O construtor é concluído com a criação de um `SKCanvasView` cujo `PaintSurface` manipulador simplesmente exibe o resultado:

[![Testar moustache (bigode)](drawing-images/MonkeyMoustache.png "testar moustache (bigode)")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copiar e modificar os bitmaps

Os métodos de `SKCanvas` que você pode usar para desenhar em um bitmap incluem `DrawBitmap`. Isso significa que você pode desenhar um bitmap em outro, normalmente, modificá-lo de alguma forma.

A maneira mais versátil para modificar um bitmap é por meio de acessar os bits de pixel real, um assunto abordado neste artigo  **[pixels de bitmap acessando SkiaSharp](pixel-bits.md)** . Mas há muitas outras técnicas para modificar a bitmaps que não exigem a acessar os bits de pixel.

O bitmap seguir incluído com o **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicativo é 360 pixels de largura e 480 pixels de altura:

![Mountain Climbers](drawing-images/MountainClimbers.jpg "Climbers Mountain")

Suponha que você ainda não recebeu permissão de publicar essa fotografia monkey à esquerda. Uma solução é obscurecer a face do monkey usando uma técnica chamada _pixelization_. Os pixels da face são substituídos por blocos de cor para que você não pode fazer os recursos. Os blocos de cor geralmente são derivados da imagem original pela média entre as cores dos pixels correspondentes a esses blocos. Mas você não precisa realizar essa média. Ela ocorre automaticamente quando você copia um bitmap em uma dimensão de pixel menor. 

Face do esquerdo monkey ocupa aproximadamente 72 pixels área quadrada com um canto superior esquerdo no ponto (112, 238). Vamos substituir essa área de 72 pixels quadrada com uma matriz de 9 por 9 de blocos coloridos, cada um deles é 8 por 8 pixels quadrados.

O **imagem Pixelize** página é carregada nesse bitmap e cria primeiro um bitmap pequeno de quadrado 9 pixels chamado `faceBitmap`. Isso é um destino para a cópia apenas do monkey de detecção facial. O retângulo de destino tem apenas 9-pixels quadrados, mas o retângulo de origem é 72 pixels quadrados. Cada bloco de 8 por 8 de pixels de origem é consolidadas para baixo para apenas um pixel pela média entre as cores.

A próxima etapa é copiar o bitmap original em um novo bitmap do mesmo tamanho chamado `pixelizedBitmap`. O pequeno `faceBitmap` , em seguida, é copiado na parte superior com um retângulo de destino de 72 pixels quadrados, de modo que cada pixel de `faceBitmap` é expandido para 8 vezes seu tamanho:

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap, 
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

O construtor é concluído com a criação de um `SKCanvasView` para exibir o resultado:

[![Imagem de pixelize](drawing-images/PixelizeImage.png "Pixelize imagem")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>Rotação de bitmaps

Outra tarefa comum é girando bitmaps. Isso é particularmente útil ao recuperar os bitmaps de uma biblioteca de fotos do iPhone ou iPad. A menos que o dispositivo foi mantido em uma orientação específica quando a foto foi feita, a imagem é provavelmente será de cabeça para baixo ou para os lados.

A ativação de um bitmap de cabeça para baixo exige criando outro bitmap do mesmo tamanho que o primeiro e, em seguida, definindo uma transformação para Girar 180 graus ao copiar o primeiro para o segundo. Em todos os exemplos nesta seção, `bitmap` é o `SKBitmap` que você precisa para girar o objeto:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Girar 90 graus, você precisará criar um bitmap que é um tamanho diferente do original, trocando a altura e largura. Por exemplo, se o bitmap original é 1200 pixels de largura e 800 pixels de altura, o bitmap invertido é 800 pixels de largura e 1200 pixels de largura. Definir conversão e rotação para que o bitmap é girado em torno de seu canto superior esquerdo e, em seguida, deslocado para o modo de exibição. (Tenha em mente que o `Translate` e `RotateDegrees` métodos são chamados na ordem oposta da maneira como elas são aplicadas.) Aqui está o código para girar 90 graus no sentido horário:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```                        

E aqui está uma função semelhante para girar 90 graus no sentido anti-horário:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Esses dois métodos são usados na **foto do quebra-cabeça** páginas descrito no artigo [ **corte Bitmaps de SkiaSharp**](cropping.md#tile-division).

Um programa que permite ao usuário girar um bitmap em incrementos de 90 graus precisa implementar apenas uma função para girar 90 graus. O usuário pode girar em qualquer incrementos de 90 graus por execução repetida dessa uma função.

Um programa também pode girar um bitmap por qualquer valor. Uma abordagem simples é modificar a função que gira 180 graus, substituindo 180 por um generalizado `angle` variável:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

No entanto, em geral, essa lógica será cortar os cantos do bitmap invertido. Uma abordagem melhor é calcular o tamanho do bitmap invertido usando trigonometria para incluir os cantos. 

Este trigonometria é mostrada na **Bitmap rotor** página. O arquivo XAML instancia uma `SKCanvasView` e uma `Slider` que pode variar de 0 a 360 graus com um `Label` mostrando o valor atual:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega um recurso de bitmap e o salva como um campo somente leitura estático chamado `originalBitmap`. O bitmap exibido na `PaintSurface` manipulador é `rotatedBitmap`, que é inicialmente definido como `originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap = 
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

O `ValueChanged` manipulador do `Slider` executa as operações que criar um novo `rotatedBitmap` com base no ângulo de rotação. A nova largura e altura são com base em absolute values de senos e co-senos do originais larguras e alturas. As transformações usadas para desenhar o bitmap original em bitmap invertido mover centro do bitmap original para a origem, e em seguida, girá-lo pelo número especificado de graus e, em seguida, convertem esse centro para o centro do bitmap invertido. (O `Translate` e `RotateDegrees` métodos são chamados na ordem oposta de como elas são aplicadas.)

Observe o uso do `Clear` método para tornar o plano de fundo de `rotatedBitmap` um rosa-claro. Isso é apenas para ilustrar o tamanho de `rotatedBitmap` na tela:

[![Rotor de bitmap](drawing-images/BitmapRotator.png "rotor de Bitmap")](drawing-images/BitmapRotator-Large.png#lightbox)

O bitmap invertido é grande o suficiente para incluir o bitmap inteiro original, mas não deve exceder.

## <a name="flipping-bitmaps"></a>A inversão de bitmaps

Outra operação comumente realizada em bitmaps é chamada _inversão_. Conceitualmente, o bitmap é girado em três dimensões em torno de um eixo vertical ou eixo horizontal por meio do centro do bitmap. A inversão vertical cria uma imagem espelhada.

O **palheta de Bitmap** página de **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** aplicativo demonstra esses processos. O arquivo XAML contém um `SKCanvasView` e dois botões para Inverter verticalmente e horizontalmente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        
        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

O arquivo code-behind implementa essas duas operações no `Clicked` manipuladores para os botões: 

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

A inversão vertical é realizada por uma transformação de escala com um fator de escala horizontal de &ndash;1. O Centro de colocação em escala é o centro vertical do bitmap. A inversão horizontal é uma transformação de escala com um fator de escala vertical de &ndash;1. 

Como você pode ver na mortas invertida na camisa do monkey, inversão não é igual a rotação. Mas, como demonstra a captura de tela UWP à direita, tanto a inversão horizontal e verticalmente é o mesmo que a rotação de 180 graus:

[![Bitmap palheta](drawing-images/BitmapFlipper.png "palheta de Bitmap")](drawing-images/BitmapFlipper-Large.png#lightbox)

Outra tarefa comum que pode ser tratada usando técnicas semelhantes é cortar um bitmap para um subconjunto retangular. Isso é descrito no próximo artigo [ **corte Bitmaps de SkiaSharp**](cropping.md).

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
