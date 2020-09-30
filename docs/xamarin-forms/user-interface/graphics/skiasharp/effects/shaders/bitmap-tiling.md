---
title: SkiaSharp bitmap em blocos
description: Peça uma área usando bitmaps repetidos horizontal e verticalmente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c6d139e47974247ce4af6bfa6c32331fcf7c824
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563127"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp bitmap em blocos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Como você viu nos dois artigos anteriores, a [`SKShader`](xref:SkiaSharp.SKShader) classe pode criar gradientes lineares ou circulares. Este artigo se concentra no `SKShader` objeto que usa um bitmap para dividir uma área. O bitmap pode ser repetido horizontal e verticalmente, em sua orientação original ou invertido alternadamente horizontal e verticalmente. A inversão evita descontinuidades entre os blocos:

![Exemplo de bitmap em blocos](bitmap-tiling-images/BitmapTilingSample.png "Exemplo de bitmap em blocos")

O [`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) método estático que cria esse sombreador tem um `SKBitmap` parâmetro e dois membros da [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) enumeração:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Os dois parâmetros indicam os modos usados para divisão horizontal e divisão vertical. Essa é a mesma `SKShaderTileMode` enumeração que também é usada com os métodos de gradiente.

Uma [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga inclui um `SKMatrix` argumento para executar uma transformação nos bitmaps de lado ladrilho:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Este artigo contém vários exemplos de como usar essa transformação de matriz com bitmaps de ladrilhos.

## <a name="exploring-the-tile-modes"></a>Explorando os modos de bloco

O primeiro programa na seção de **bitmap em blocos gráficos** da página **sombreadores e outros efeitos** do exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) demonstra os efeitos dos dois `SKShaderTileMode` argumentos. O **bloco bitmap flip Modes** o arquivo XAML instancia uma `SKCanvasView` e duas `Picker` exibições que permitem selecionar um `SKShaderTilerMode` valor para divisão horizontal e vertical. Observe que uma matriz dos `SKShaderTileMode` Membros é definida na `Resources` seção:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

O construtor do arquivo code-behind carrega no recurso de bitmap que mostra um macaco sentado. Ele primeiro corta a imagem usando o [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) método de `SKBitmap` para que o cabeçalho e os pés estejam tocando nas bordas do bitmap. Em seguida, o construtor usa o [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) método para criar outro bitmap de metade do tamanho. Essas alterações tornam o bitmap um pouco mais adequado para colocação em blocos:

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

O `PaintSurface` manipulador obtém as `SKShaderTileMode` configurações das duas `Picker` exibições e cria um `SKShader` objeto com base no bitmap e nesses dois valores. Este sombreador é usado para preencher a tela:

[![Modos de deslocamento de bloco de bitmap](bitmap-tiling-images/BitmapTileFlipModes.png "Modos de deslocamento de bloco de bitmap")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

A tela do iOS à esquerda mostra o efeito dos valores padrão de `SKShaderTileMode.Clamp` . O bitmap fica no canto superior esquerdo. Abaixo do bitmap, a linha inferior de pixels é repetida em todo o caminho. À direita do bitmap, a coluna mais à direita de pixels é repetida em todo o caminho. O restante da tela é colorido pelo pixel marrom escuro no canto inferior direito do bitmap. Deve ser óbvio que a `Clamp` opção quase nunca é usada com blocos de bitmaps.

A tela do Android no centro mostra o resultado de `SKShaderTileMode.Repeat` para ambos os argumentos. O bloco é repetido horizontal e verticalmente. A tela de Plataforma Universal do Windows mostra `SKShaderTileMode.Mirror` . Os blocos são repetidos, mas alternados de forma horizontal e vertical. A vantagem dessa opção é que não há nenhum descontinuidades entre os blocos.

Tenha em mente que você pode usar opções diferentes para a repetição horizontal e vertical. Você pode especificar `SKShaderTileMode.Mirror` como o segundo argumento para `CreateBitmap` , mas `SKShaderTileMode.Repeat` como o terceiro argumento. Em cada linha, o Monkeys ainda alterna entre a imagem normal e a imagem espelho, mas nenhuma das Monkeys está de cabeça para baixo.

## <a name="patterned-backgrounds"></a>Planos de fundo padronizados

Em geral, o bitmap em blocos é usado para criar um plano de fundo padronizado de um bitmap relativamente pequeno. O exemplo clássico é uma parede de tijolos.

A página de **parede do Brick de algoritmo** cria um bitmap pequeno que se assemelha a um Brick inteiro e duas metades de um Brick separados por cimento. Como esse Brick é usado no exemplo a seguir também, ele é criado por um construtor estático e torna-se público com uma propriedade estática:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

O bitmap resultante tem 70 pixels de largura e 60 pixels de altura:

![Bloco de parede de Brick de algoritmo](bitmap-tiling-images/AlgorithmicBrickWallTile.png "Bloco de parede de Brick de algoritmo")

O restante da página de **parede do Brick de algoritmo** cria um `SKShader` objeto que repete essa imagem horizontal e verticalmente:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Eis o resultado:

[![Parede de Bricks de algoritmo](bitmap-tiling-images/AlgorithmicBrickWall.png "Parede de Bricks de algoritmo")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Você pode preferir algo um pouco mais realista. Nesse caso, você pode tirar uma fotografia de uma parede de tijolos real e, em seguida, cortá-la. Esse bitmap tem 300 pixels de largura e 150 pixels de altura:

![Bloco do Brick Wall](bitmap-tiling-images/BrickWallTile.jpg "Bloco do Brick Wall")

Esse bitmap é usado na página de **parede do Brick fotográfico** :

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Observe que os `SKShaderTileMode` argumentos para `CreateBitmap` são ambos `Mirror` . Essa opção é geralmente necessária quando você usa blocos criados a partir de imagens do mundo real. O espelhamento dos blocos evita descontinuidades:

[![Mural do Brick fotográfico](bitmap-tiling-images/PhotographicBrickWall.png "Mural do Brick fotográfico")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

É necessário algum trabalho para obter um bitmap adequado para o bloco. Este não funciona muito bem porque o Brick mais escuro se destaca muito. Ele aparece regularmente dentro das imagens repetidas, revelando o fato de que essa parede de Brick foi construída a partir de um bitmap menor.

A pasta de **mídia** do exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) também inclui essa imagem de uma parede de pedra:

![Bloco de parede de pedra](bitmap-tiling-images/StoneWallTile.jpg "Bloco de parede de pedra")

No entanto, o bitmap original é um pouco grande para um bloco. Ele pode ser redimensionado, mas o `SKShader.CreateBitmap` método também pode redimensionar o bloco aplicando uma transformação a ele. Essa opção é demonstrada na página de **parede da pedra** :

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Um `SKMatrix` valor é criado para dimensionar a imagem para metade do tamanho original:

[![Parede da pedra](bitmap-tiling-images/StoneWall.png "Parede da pedra")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

A transformação opera no bitmap original usado no `CreateBitmap` método? Ou ele transforma a matriz resultante de blocos? 

Uma maneira fácil de responder a essa pergunta é incluir uma rotação como parte da transformação:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Se a transformação for aplicada ao bloco individual, cada imagem repetida do bloco deverá ser girada e o resultado conterá muitos descontinuidades. Mas é óbvio nesta captura de tela que a matriz composta de blocos é transformada:

[![Parede de pedra girada](bitmap-tiling-images/StoneWallRotated.png "Parede de pedra girada")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Na seção [**alinhamento de bloco**](#tile-alignment), você verá um exemplo uma transformação traduzir aplicada ao sombreador.

O exemplo de [**relógio Cat**](/samples/xamarin/xamarin-forms-samples/catclock) autônomo (que não faz parte de **SkiaSharpFormsDemos**) simula um plano de fundo de granulação de madeira usando o bitmap com base em blocos de bits baseado neste bitmap quadrado de 240 pixels:

![Granulação de madeira](bitmap-tiling-images/WoodGrain.png "Granulação de madeira")

Essa é uma fotografia de um piso de madeira. A `SKShaderTileMode.Mirror` opção permite que ela apareça como uma área muito maior de madeira:

[![Relógio de gato](bitmap-tiling-images/CatClock.png "Relógio de gato")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alinhamento de bloco

Todos os exemplos mostrados até agora já usaram o sombreador criado pelo `SKShader.CreateBitmap` para cobrir a tela inteira. Na maioria dos casos, você usará a divisão de bitmap para o arquivamento de áreas menores ou (mais raramente) para preencher os interiores de linhas espessas. Este é o bloco de parede de tijolo fotográfico usado para um retângulo menor:

[![Alinhamento de bloco](bitmap-tiling-images/TileAlignment.png "Alinhamento de bloco")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Isso pode parecer bem para você ou talvez não. Talvez você tenha se incomodado de que o padrão de divisão não começa com um Brick completo no canto superior esquerdo do retângulo. Isso ocorre porque os sombreadores são alinhados com a tela e não com o objeto gráfico que adornam.

A correção é simples. Crie um `SKMatrix` valor com base em uma transformação de tradução. A transformação alterna efetivamente o padrão de lado do ladrilho para o ponto em que você deseja que o canto superior esquerdo do bloco seja alinhado. Essa abordagem é demonstrada na página **alinhamento do bloco** , que criou a imagem dos blocos não alinhados mostrados acima:

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

A página **alinhamento de bloco** inclui um `TapGestureRecognizer` . Toque ou clique na tela e o programa alternará para o `SKShader.CreateBitmap` método com um `SKMatrix` argumento. Essa transformação muda o padrão para que o canto superior esquerdo contenha um Brick completo:

[![Alinhamento de bloco tocado](bitmap-tiling-images/TileAlignmentTapped.png "Alinhamento de bloco tocado")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

Você também pode usar essa técnica para garantir que o padrão de bitmap lado a lado seja centralizado dentro da área que ele pinta. Na página **blocos centralizados** , o `PaintSurface` manipulador calcula primeiro as coordenadas como se vai exibir o bitmap único no centro da tela. Em seguida, ele usa essas coordenadas para criar uma transformação de conversão para `SKShader.CreateBitmap` . Essa transformação muda o padrão inteiro para que um bloco seja centralizado:

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

O `PaintSurface` manipulador é concluído ao desenhar um círculo no centro da tela. Claro que um dos blocos está exatamente no centro do círculo, e os outros são organizados em um padrão simétrico:

[![Blocos centralizados](bitmap-tiling-images/CenteredTiles.png "Blocos centralizados")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Outra abordagem de centralização é, na verdade, um pouco mais fácil. Em vez de construir uma transformação de conversão que coloca um bloco no centro, você pode centralizar um canto do padrão lado a lado. Na `SKMatrix.MakeTranslation` chamada, use argumentos para o centro da tela:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

O padrão ainda é centralizado e simétrico, mas nenhum bloco está no centro:

[![Blocos centralizados alternativos](bitmap-tiling-images/CenteredTilesAlternate.png "Blocos centralizados alternativos")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplificação por meio de rotação

Às vezes, usar uma transformação de rotação no `SKShader.CreateBitmap` método pode simplificar o bloco de bitmap. Isso fica evidente ao tentar definir um bloco para um limite de link de cadeia. O arquivo **ChainLinkTile.cs** cria o bloco mostrado aqui (com um plano de fundo rosa para fins de clareza):

![Bloco de link de cadeia rígida](bitmap-tiling-images/HardChainLinkTile.png "Bloco de link de cadeia rígida")

O bloco precisa incluir dois links, para que o código divida o bloco em quatro quadrantes. Os quadrantes superior esquerdo e inferior direito são os mesmos, mas não estão completos. Os cabos têm poucos chanfros que devem ser manipulados com um desenho adicional nos quadrantes superior direito e inferior esquerdo. O arquivo que faz todo esse trabalho é de 174 linhas de comprimento.

Torna-se muito mais fácil criar esse bloco:

![Bloco de link de cadeia mais fácil](bitmap-tiling-images/EasierChainLinkTile.png "Bloco de link de cadeia mais fácil")

Se o sombreador de bloco de bitmap for girado 90 graus, os visuais serão praticamente iguais.

O código para criar o bloco de link de cadeia mais fácil faz parte da página do **bloco link de cadeia** . O Construtor determina um tamanho de bloco com base no tipo de dispositivo em que o programa está sendo executado e, em seguida, chama `CreateChainLinkTile` , que desenha o bitmap usando linhas, caminhos e sombreadores de gradiente:

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

Exceto para os fios, o bloco é transparente, o que significa que você pode exibi-lo com base em algo mais. O programa é carregado em um dos recursos de bitmap, exibe-o para preencher a tela e, em seguida, desenha o sombreador na parte superior:

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Observe que o sombreador é girado 45 graus para que ele seja orientado como um limite de link de cadeia real:

[![Limite de link de cadeia](bitmap-tiling-images/ChainLinkFence.png "Limite de link de cadeia")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animação de blocos de bitmap

Você pode animar um padrão inteiro de bloco de bitmap animando a transformação de matriz. Talvez você queira que o padrão seja movido horizontalmente ou verticalmente ou ambos. Você pode fazer isso criando uma transformação de tradução com base nas coordenadas de mudança.

Também é possível desenhar em um bitmap pequeno ou manipular os bits de pixel do bitmap na taxa de 60 vezes por segundo. Esse bitmap, em seguida, pode ser usado para colocação em blocos, e todo o padrão do lado do ladrilho pode parecer animado. 

A página do **bloco bitmap animado** demonstra essa abordagem. Um bitmap é instanciado como um campo para ser quadrado de 64 pixels. O construtor chama `DrawBitmap` para dar a ele uma aparência inicial. Se o `angle` campo for zero (como é quando o método é chamado pela primeira vez), o bitmap conterá duas linhas cruzadas como um X. As linhas são feitas por tempo suficiente para sempre alcançar a borda do bitmap, independentemente do `angle` valor: 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

A sobrecarga da animação ocorre nas `OnAppearing` `OnDisappearing` substituições e. O `OnTimerTick` método anima o `angle` valor de 0 graus a 360 graus a cada 10 segundos para girar a figura X no bitmap:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
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
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Devido à simetria da figura X, isso é o mesmo que girar o `angle` valor de 0 graus a 90 graus a cada 2,5 segundos.

O `PaintSurface` manipulador cria um sombreador do bitmap e usa o objeto de pintura para colorir a tela inteira:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

As `SKShaderTileMode.Mirror` Opções garantem que os braços do x em cada bitmap ingressem com o X nos bitmaps adjacentes para criar um padrão animado geral que pareça muito mais complexo do que a animação simples sugere:

[![Bloco bitmap animado](bitmap-tiling-images/AnimatedBitmapTile.png "Bloco bitmap animado")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (exemplo)](/samples/xamarin/xamarin-forms-samples/catclock)