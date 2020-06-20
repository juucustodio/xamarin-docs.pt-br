---
title: Criando e desenhando bitmaps SkiaSharp
description: Saiba como criar bitmaps SkiaSharp e, em seguida, desenhar esses bitmaps criando uma tela com base neles.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c045e297beca675c0582efc2f75b1d6b2bcedcf8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573283"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Criando e desenhando bitmaps SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Você viu como um aplicativo pode carregar bitmaps da Web, de recursos do aplicativo e da biblioteca de fotos do usuário. Também é possível criar novos bitmaps dentro de seu aplicativo. A abordagem mais simples envolve um dos construtores de [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) :

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

Os `width` `height` parâmetros e são inteiros e especificam as dimensões de pixel do bitmap. Esse construtor cria um bitmap de cor completa com quatro bytes por pixel: um byte cada para os componentes vermelho, verde, azul e alfa (opacidade).

Depois de criar um novo bitmap, você precisará obter algo na superfície do bitmap. Em geral, você faz isso de duas maneiras:

- Desenhe no bitmap usando métodos de `Canvas` desenho padrão.
- Acesse os bits de pixel diretamente.

Este artigo demonstra a primeira abordagem:

![Exemplo de desenho](drawing-images/DrawingSample.png "Exemplo de desenho")

A segunda abordagem é discutida no artigo [**acessando SkiaSharp pixels de bitmap**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Desenho no bitmap

O desenho na superfície de um bitmap é o mesmo que desenhar em uma tela de vídeo. Para desenhar em uma exibição de vídeo, você obtém um `SKCanvas` objeto dos `PaintSurface` argumentos do evento. Para desenhar em um bitmap, você cria um `SKCanvas` objeto usando o [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) Construtor:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Quando tiver terminado de desenhar no bitmap, você poderá descartar o `SKCanvas` objeto. Por esse motivo, o `SKCanvas` Construtor é geralmente chamado em uma `using` instrução:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

O bitmap pode ser exibido. Posteriormente, o programa pode criar um novo `SKCanvas` objeto com base no mesmo bitmap e desenhá-lo mais uma vez.

A página de **bitmap de saudação** no aplicativo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** grava o texto "Olá, bitmap!" em um bitmap e, em seguida, exibe esse bitmap várias vezes.

O construtor do `HelloBitmapPage` começa criando um `SKPaint` objeto para exibir o texto. Ele determina as dimensões de uma cadeia de texto e cria um bitmap com essas dimensões. Em seguida, ele cria um `SKCanvas` objeto com base no bitmap, chamadas `Clear` e, em seguida, chama `DrawText` . É sempre uma boa ideia chamar `Clear` um novo bitmap, pois um bitmap recém-criado pode conter dados aleatórios.

O Construtor conclui criando um `SKCanvasView` objeto para exibir o bitmap:

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

O `PaintSurface` manipulador renderiza o bitmap várias vezes em linhas e colunas da exibição. Observe que o `Clear` método no `PaintSurface` manipulador tem um argumento de `SKColors.Aqua` , que colore a tela de fundo da superfície de exibição:

[![Olá, bitmap!](drawing-images/HelloBitmap.png "Olá, bitmap!")](drawing-images/HelloBitmap-Large.png#lightbox)

A aparência do plano de fundo da água revela que o bitmap é transparente, exceto pelo texto.

## <a name="clearing-and-transparency"></a>Limpeza e transparência

A exibição da página de **bitmap de saudação** demonstra que o bitmap criado pelo programa é transparente, exceto pelo texto em preto. É por isso que a cor da água da superfície de exibição é mostrada.

A documentação dos `Clear` métodos de `SKCanvas` Descrição deles com a instrução: "substitui todos os pixels no clipe atual da tela". O uso da palavra "substitui" revela uma característica importante desses métodos: todos os métodos de desenho de `SKCanvas` adicionar algo à superfície de exibição existente. Os `Clear` métodos _substituem_ o que já existe.

`Clear`existe em duas versões diferentes:

- O [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) método com um `SKColor` parâmetro substitui os pixels da superfície de exibição por pixels dessa cor.

- O [`Clear`](xref:SkiaSharp.SKCanvas.Clear) método sem parâmetros substitui os pixels pela [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) cor, que é uma cor na qual todos os componentes (vermelho, verde, azul e alfa) são definidos como zero. Essa cor às vezes é chamada de "preto transparente".

Chamar `Clear` sem argumentos em um novo bitmap Inicializa todo o bitmap para ser totalmente transparente. Qualquer coisa posteriormente desenhada no bitmap geralmente será opaca ou parcialmente opaca.

Aqui está algo a ser experimentado: na página de **bitmap de saudação** , substitua o `Clear` método aplicado ao `bitmapCanvas` com este:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

A ordem dos `SKColor` parâmetros do construtor é vermelho, verde, azul e alfa, em que cada valor pode variar de 0 a 255. Tenha em mente que um valor alfa de 0 é transparente, enquanto um valor alfa de 255 é opaco.

O valor (255, 0, 0, 128) limpa os pixels de bitmap em vermelho em pixels com uma opacidade de 50%. Isso significa que o plano de fundo do bitmap é semitransparente. O plano de fundo semitransparente vermelho do bitmap combina com a tela de fundo azul da superfície de exibição para criar um plano de fundo cinza.

Tente definir a cor do texto como preto transparente, colocando a seguinte atribuição no `SKPaint` inicializador:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Você pode pensar que esse texto transparente criaria áreas totalmente transparentes do bitmap por meio do qual você veria a tela de fundo azul da superfície de exibição. Mas isso não é tão. O texto é desenhado sobre o que já está no bitmap. O texto transparente não será visível.

Nenhum `Draw` método sempre torna um bitmap mais transparente. `Clear`Pode fazer isso apenas.

## <a name="bitmap-color-types"></a>Tipos de cores de bitmap

O Construtor mais simples `SKBitmap` permite que você especifique uma largura e altura de pixel inteiro para o bitmap. Outros `SKBitmap` construtores são mais complexos. Esses construtores exigem argumentos de dois tipos de enumeração: [`SKColorType`](xref:SkiaSharp.SKColorType) e [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) . Outros construtores usam a [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) estrutura, que consolida essas informações.

A `SKColorType` enumeração tem 9 membros. Cada um desses membros descreve uma maneira específica de armazenar os pixels de bitmap:

- `Unknown`
- `Alpha8`&mdash;cada pixel é de 8 bits, representando um valor alfa de totalmente transparente para totalmente opaco
- `Rgb565`&mdash;cada pixel tem 16 bits, 5 bits para vermelho e azul e 6 para verde
- `Argb4444`&mdash;cada pixel é 16 bits, 4 cada para alfa, vermelho, verde e azul
- `Rgba8888`&mdash;cada pixel é de 32 bits, 8 cada para vermelho, verde, azul e alfa
- `Bgra8888`&mdash;cada pixel é de 32 bits, 8 cada para azul, verde, vermelho e alfa
- `Index8`&mdash;cada pixel tem 8 bits e representa um índice em um[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;cada pixel tem 8 bits representando uma tonalidade cinza de preto a branco
- `RgbaF16`&mdash;cada pixel é 64 bits, com vermelho, verde, azul e alfa em um formato de ponto flutuante de 16 bits

Os dois formatos em que cada pixel é 32 pixels (4 bytes) geralmente são chamados _de formatos de cor completa_ . Muitos dos outros formatos de data de um horário em que os vídeos são exibidos não eram capazes de colorir totalmente. Bitmaps de cor limitada eram adequados para esses monitores e bitmaps permitidos para ocupar menos espaço na memória.

Hoje em dia, os programadores quase sempre usam bitmaps de cor completa e não se preocupam com outros formatos. A exceção é o `RgbaF16` formato, que permite uma maior resolução de cor do que até os formatos de cor completa. No entanto, esse formato é usado para fins especializados, como imagens médicas, e não faz muito sentido quando usado com monitores de cores completas padrão.

Esta série de artigos se restringirá aos `SKBitmap` formatos de cores usados por padrão quando nenhum `SKColorType` membro for especificado. Esse formato padrão é baseado na plataforma subjacente. Para as plataformas com suporte Xamarin.Forms no, o tipo de cor padrão é:

- `Rgba8888`para iOS e Android
- `Bgra8888`para o UWP

A única diferença é a ordem dos 4 bytes na memória, e isso só se torna um problema quando você acessa diretamente os bits de pixel. Isso não se tornará importante até chegar ao artigo [**acessando os pixels de bitmap SkiaSharp**](pixel-bits.md).

A `SKAlphaType` enumeração tem quatro membros:

- `Unknown`
- `Opaque`&mdash;o bitmap não tem transparência
- `Premul`&mdash;os componentes de cor são previamente multiplicados pelo componente alfa
- `Unpremul`&mdash;os componentes de cor não são previamente multiplicados pelo componente alfa

Aqui está um pixel de bitmap vermelho de 4 bytes com transparência de 50%, com os bytes mostrados na ordem vermelho, verde, azul, alfa:

0xFF 0x00 0x00 0x80

Quando um bitmap contendo pixels semitransparentes é renderizado em uma superfície de exibição, os componentes de cor de cada pixel de bitmap devem ser multiplicados pelo valor alfa desse pixel e os componentes de cor do pixel correspondente da superfície de exibição devem ser multiplicados por 255 menos o valor alfa. Os dois pixels podem então ser combinados. O bitmap pode ser renderizado mais rapidamente se os componentes de cor nos pixels de bitmap já tiverem sido mulitplied pelo valor alfa. Esse mesmo pixel vermelho seria armazenado como este em um formato previamente multiplicado:

0x80 0x00 0x00 0x80

Essa melhoria de desempenho é o motivo `SkiaSharp` pelo qual os bitmaps por padrão são criados com um `Premul` formato. Mas, novamente, é necessário saber isso somente quando você acessa e manipula bits de pixel.

## <a name="drawing-on-existing-bitmaps"></a>Desenho de bitmaps existentes

Não é necessário criar um novo bitmap para desenhar nele. Você também pode desenhar em um bitmap existente.

A página **macaco Moustache** usa seu construtor para carregar a imagem de **MonkeyFace.png** . Em seguida, ele cria um `SKCanvas` objeto com base nesse bitmap e `SKPaint` usa `SKPath` objetos e para desenhar um Moustache nele:

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

O Construtor termina criando um `SKCanvasView` cujo `PaintSurface` manipulador simplesmente exibe o resultado:

[![Macaco Moustache](drawing-images/MonkeyMoustache.png "Macaco Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copiando e modificando bitmaps

Os métodos do `SKCanvas` que você pode usar para desenhar em um bitmap incluem `DrawBitmap` . Isso significa que você pode desenhar um bitmap em outro, geralmente modificando-o de alguma forma.

A maneira mais versátil de modificar um bitmap é acessar os bits de pixel reais, um assunto abordado no artigo **[acessando os pixels de bitmap SkiaSharp](pixel-bits.md)**. Mas há muitas outras técnicas para modificar os bitmaps que não exigem o acesso aos bits de pixel.

O bitmap a seguir incluído no aplicativo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** tem 360 pixels de largura e 480 pixels de altura:

![Escalares de montanha](drawing-images/MountainClimbers.jpg "Escalares de montanha")

Suponha que você não tenha recebido permissão do macaco à esquerda para publicar esta fotografia. Uma solução é obscurecer a face do macaco usando uma técnica chamada de _pixelização_. Os pixels da face são substituídos por blocos de cor para que você não possa tornar os recursos. Os blocos de cor geralmente são derivados da imagem original com a média das cores dos pixels correspondentes a esses blocos. Mas você não precisa executar essa média. Isso ocorre automaticamente quando você copia um bitmap em uma dimensão de pixel menor.

A face da macaco à esquerda ocupa aproximadamente uma área quadrada de 72 pixels com um canto superior esquerdo no ponto (112, 238). Vamos substituir essa área quadrada de 72 pixels por uma matriz 9 por 9 de blocos coloridos, cada um dos quais é quadrado de 8 a 8 pixels.

A página de **imagem de Pixelize** é carregada nesse bitmap e primeiro cria um pequeno bitmap quadrado de 9 pixels chamado `faceBitmap` . Esse é um destino para copiar apenas a face do macaco. O retângulo de destino é apenas de 9 pixels quadrado, mas o retângulo de origem é um quadrado de 72 pixels. Cada bloco de pixels de origem 8 por 8 é consolidado para apenas um pixel com a média das cores.

A próxima etapa é copiar o bitmap original em um novo bitmap com o mesmo tamanho chamado `pixelizedBitmap` . O pequeno `faceBitmap` é então copiado sobre isso com um retângulo de destino quadrado de 72 pixels para que cada pixel de `faceBitmap` seja expandido para 8 vezes seu tamanho:

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

O construtor é concluído criando um `SKCanvasView` para exibir o resultado:

[![Imagem de pixelização](drawing-images/PixelizeImage.png "Imagem de pixelização")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>Rotação de bitmaps

Outra tarefa comum é a rotação de bitmaps. Isso é particularmente útil ao recuperar bitmaps de uma biblioteca de Fotos iPhone ou iPad. A menos que o dispositivo tenha sido mantido em uma orientação específica quando a foto foi tirada, é provável que a imagem fique de cabeça para baixo ou para a lateral.

Transformar um bitmap de cabeça para baixo requer a criação de outro bitmap com o mesmo tamanho do primeiro e, em seguida, a definição de uma transformação para girar em 180 graus ao copiar o primeiro para o segundo. Em todos os exemplos nesta seção, `bitmap` é o `SKBitmap` objeto que você precisa girar:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Ao girar por 90 graus, você precisa criar um bitmap que tenha um tamanho diferente do original alternando a altura e a largura. Por exemplo, se o bitmap original tiver 1200 pixels de largura e 800 pixels de altura, o bitmap girado tem 800 pixels de largura e 1200 pixels de largura. Defina conversão e rotação para que o bitmap seja girado ao lado do seu canto superior esquerdo e, em seguida, deslocado para a exibição. (Tenha em mente que os `Translate` `RotateDegrees` métodos e são chamados na ordem oposta da maneira como são aplicados.) Este é o código para girar 90 graus no sentido horário:

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

Esses dois métodos são usados nas páginas de **quebra-cabeça de foto** descritas no artigo [**recortando bitmaps SkiaSharp**](cropping.md#cropping-skiasharp-bitmaps).

Um programa que permite ao usuário girar um bitmap em incrementos de 90 graus só precisa implementar uma função para girar em 90 graus. Em seguida, o usuário pode girar em qualquer incremento de 90 graus por meio da execução repetida dessa função.

Um programa também pode girar um bitmap por qualquer valor. Uma abordagem simples é modificar a função que gira em 180 graus substituindo 180 por uma variável generalizada `angle` :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

No entanto, no caso geral, essa lógica cortará os cantos do bitmap girado. Uma abordagem melhor é calcular o tamanho do bitmap girado usando trigonometria para incluir esses cantos.

Essa trigonometria é mostrada na página de **rotor de bitmap** . O arquivo XAML instancia um `SKCanvasView` e um `Slider` que pode variar de 0 a 360 graus com um `Label` mostrando o valor atual:

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

O arquivo code-behind carrega um recurso de bitmap e o salva como um campo somente leitura estático chamado `originalBitmap` . O bitmap exibido no `PaintSurface` manipulador é `rotatedBitmap` , que é inicialmente definido como `originalBitmap` :

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

O `ValueChanged` manipulador de `Slider` executa as operações que criam um novo `rotatedBitmap` com base no ângulo de rotação. A nova largura e altura baseiam-se em valores absolutos de senos e cosenos das larguras e alturas originais. As transformações usadas para desenhar o bitmap original no bitmap girado movem o centro de bitmap original para a origem, girando-o pelo número especificado de graus e, em seguida, convertem esse centro no centro do bitmap girado. (Os `Translate` `RotateDegrees` métodos e são chamados na ordem oposta à de como são aplicados.)

Observe o uso do `Clear` método para tornar o plano de fundo de `rotatedBitmap` rosa claro. Isso é apenas para ilustrar o tamanho de `rotatedBitmap` na exibição:

[![Rotor de bitmap](drawing-images/BitmapRotator.png "Rotor de bitmap")](drawing-images/BitmapRotator-Large.png#lightbox)

O bitmap girado é muito grande o suficiente para incluir o bitmap original inteiro, mas não é maior.

## <a name="flipping-bitmaps"></a>Invertendo bitmaps

Outra operação normalmente executada em bitmaps é chamada de _inversão_. Conceitualmente, o bitmap é girado em três dimensões em um eixo vertical ou em um eixo horizontal por meio do centro do bitmap. A inversão vertical cria uma imagem espelho.

A página de **palheta do bitmap** no aplicativo **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** demonstra esses processos. O arquivo XAML contém um `SKCanvasView` e dois botões para inverter vertical e horizontalmente:

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

O arquivo code-behind implementa essas duas operações nos `Clicked` manipuladores para os botões:

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

A Flip vertical é realizada por uma transformação de dimensionamento com um fator de dimensionamento horizontal de &ndash; 1. O centro de dimensionamento é o centro vertical do bitmap. O flip horizontal é uma transformação de dimensionamento com um fator de dimensionamento vertical de &ndash; 1.

Como você pode ver nas letras invertidas na camisa da macaco, a inversão não é a mesma que a rotação. Mas como a captura de tela UWP à direita demonstra, Inverter horizontalmente e verticalmente é o mesmo que girar 180 graus:

[![Palheta do bitmap](drawing-images/BitmapFlipper.png "Palheta do bitmap")](drawing-images/BitmapFlipper-Large.png#lightbox)

Outra tarefa comum que pode ser tratada usando técnicas semelhantes é cortar um bitmap em um subconjunto retangular. Isso é descrito no próximo artigo [**recortando os bitmaps SkiaSharp**](cropping.md).

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
