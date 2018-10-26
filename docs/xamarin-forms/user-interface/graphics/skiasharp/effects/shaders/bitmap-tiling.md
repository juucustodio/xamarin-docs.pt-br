---
title: Lado a lado o bitmap de SkiaSharp
description: Lado a lado de uma área através de bitmaps repetida horizontal e verticalmente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 5bd063f82cc1d09c6b2e9100429889a23a2eda7f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111445"
---
# <a name="skiasharp-bitmap-tiling"></a>Lado a lado o bitmap de SkiaSharp

Como você viu nos dois artigos anteriores, o [ `SKShader` ](xref:SkiaSharp.SKShader) classe pode criar gradientes lineares ou circulares. Este artigo enfoca o `SKShader` objeto que usa um bitmap para uma área lado a lado. O bitmap pode ser repetido horizontal e verticalmente, em sua orientação original ou como alternativa invertida horizontalmente e verticalmente. A inversão evita o descontinuidades entre os blocos:

![Exemplo de lado a lado de bitmap](bitmap-tiling-images/BitmapTilingSample.png "lado a lado exemplo de Bitmap")

Estático [ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) método que cria esse sombreador tem um `SKBitmap` parâmetro e dois membros a [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumeração:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Os dois parâmetros seguintes indicam os modos usados para o lado a lado horizontal e vertical lado a lado. Isso é o mesmo `SKShaderTileMode` enumeração também é usada com os métodos de gradiente.

Um [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) sobrecarga inclui um `SKMatrix` argumento para executar uma transformação em bitmaps de lado a lado:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Este artigo contém vários exemplos de como usar essa transformação de matriz com bitmaps lado a lado.

## <a name="exploring-the-tile-modes"></a>Explorando os modos de bloco

O primeiro programa na **lado a lado do Bitmap** seção o **sombreadores e outros efeitos** página da [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo Demonstra os efeitos dos dois `SKShaderTileMode` argumentos. O **Bitmap bloco Inverter modos** arquivo XAML instancia um `SKCanvasView` e duas `Picker` modos de exibição que permitem que você selecione um `SKShaderTilerMode` valor para o lado a lado horizontal e vertical. Observe que uma matriz do `SKShaderTileMode` membros é definido no `Resources` seção:

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

O construtor do arquivo code-behind carrega no recurso de bitmap que mostra um monkey sentado. Primeiro corta a imagem usando o [ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) método `SKBitmap` , de modo que o head e pés tocam as bordas do bitmap. O construtor, em seguida, usa o [ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) método para criar outro bitmap de metade do tamanho. Essas alterações tornam o bitmap de um pouco mais adequado para o lado a lado:

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

O `PaintSurface` manipulador obtém o `SKShaderTileMode` as configurações dos dois `Picker` modos de exibição e cria um `SKShader` objeto com base em bitmap e esses dois valores. Esse sombreador é usado para preencher a tela:

[![Modos de bloco invertido de bitmap](bitmap-tiling-images/BitmapTileFlipModes.png "modos de bloco invertido de Bitmap")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

A tela do iOS à esquerda mostra o efeito dos valores padrão de `SKShaderTileMode.Clamp`. O bitmap fica no canto superior esquerdo. Abaixo do bitmap, a linha inferior de pixels é repetida todo o caminho para baixo. À direita do bitmap, a coluna mais à direita de pixels é repetida por toda. O restante da tela é colorido por pixel marrom-escuro no canto inferior direito do bitmap. Deve ser óbvio que o `Clamp` opção quase nunca é usada com lado a lado de bitmap!

A tela Android no centro mostra o resultado de `SKShaderTileMode.Repeat` para ambos os argumentos. O bloco é repetido horizontal e verticalmente. Mostra a tela a plataforma Universal do Windows `SKShaderTileMode.Mirror`. Os blocos são repetidos mas alternadamente invertidos horizontalmente e verticalmente. A vantagem dessa opção é que não há nenhum o descontinuidades entre os blocos.

Tenha em mente que você pode usar opções diferentes para a repetição horizontal e vertical. Você pode especificar `SKShaderTileMode.Mirror` como o segundo argumento para `CreateBitmap` mas `SKShaderTileMode.Repeat` como o terceiro argumento. Em cada linha, o Monkeys a (Macacos) ainda alternativo entre a imagem normal e a imagem espelhada, mas nenhuma do Monkeys (Macacos) é de cabeça para baixo.

## <a name="patterned-backgrounds"></a>Planos de fundo

Lado a lado de bitmap é comumente usada para criar um plano de fundo com padrão de um bitmap relativamente pequeno. O exemplo clássico é uma parede de tijolos.

O **algorítmica parede de tijolos** página cria um bitmap pequeno que é semelhante a um brick inteiro e duas metades de um brick separados por elementos básicos. Como esse bloco é usado no próximo exemplo, ela tem criada por um construtor estático e tornar público com uma propriedade estática:

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

O bitmap resultante é 70 pixels de largura e 60 pixels de altura:

![Lado a lado de parede de tijolos algorítmicos](bitmap-tiling-images/AlgorithmicBrickWallTile.png "algorítmica Brick de parede lado a lado")

O restante dos **algorítmica parede de tijolos** página cria um `SKShader` objeto que se repete essa imagem horizontalmente e verticalmente:

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

Aqui está o resultado:

[![Parede de tijolos algorítmica](bitmap-tiling-images/AlgorithmicBrickWall.png "parede de tijolos algorítmica")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Talvez você prefira algo um pouco mais realista. Nesse caso, você pode tirar uma fotografia de uma parede de tijolos real e, em seguida, cortá-la. Esse bitmap é 300 pixels de largura e 150 pixels de altura:

![Bloco de parede de tijolos](bitmap-tiling-images/BrickWallTile.jpg "Brick de parede lado a lado")

Esse bitmap é usado na **parede de tijolos fotográfica** página:

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

Observe que o `SKShaderTileMode` argumentos a serem `CreateBitmap` são ambos `Mirror`. Essa opção geralmente é necessária quando você usa blocos criados a partir de imagens do mundo real. Os blocos de espelhamento evita o descontinuidades:

[![Parede de tijolos fotográfica](bitmap-tiling-images/PhotographicBrickWall.png "parede de tijolos fotográfica")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Algum trabalho é necessário para obter um bitmap adequado para o bloco. Isso não funciona muito bem porque o bloco mais escuro se destaca muito. Regularmente, ele aparece nas imagens repetidas, revelando o fato de que a parede de tijolos foi construído a partir de um bitmap menor.

O **Media** pasta da [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo também inclui a imagem de um muro:

![Pedra de parede lado a lado](bitmap-tiling-images/StoneWallTile.jpg "pedra de parede lado a lado")

No entanto, o bitmap original é um pouco grande demais para um bloco. Ele pode ser redimensionado, mas o `SKShader.CreateBitmap` método também pode redimensionar o bloco, aplicando uma transformação a ele. Essa opção é demonstrada na **pedra parede** página:

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

Um `SKMatrix` valor é criado para a escala da imagem para metade de seu tamanho original:

[![Pedra parede](bitmap-tiling-images/StoneWall.png "pedra parede")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

A transformação funciona no bitmap original usado no `CreateBitmap` método? Ou ele transformar a matriz resultante dos blocos? 

Uma maneira fácil de responder a essa pergunta é incluir uma rotação como parte da transformação:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Se a transformação é aplicada ao bloco individual, em seguida, deve ser girada de cada imagem repetida do bloco e o resultado conteria o descontinuidades muitos. Mas é óbvio que a matriz composta de blocos é transformada nesta captura de tela:

[![Pedra parede girado](bitmap-tiling-images/StoneWallRotated.png "pedra parede girado")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Na seção [ **bloco alinhamento**](#tile-alignment), você verá um exemplo de uma transformação de conversão aplicada para o sombreador.

O standalone [ **relógio Cat** ](https://developer.xamarin.com/samples/xamarin-forms/CatClock) exemplo (não fazem parte do **SkiaSharpFormsDemos**) simula um plano de fundo de madeira granulação usando lado a lado de bitmap com base nesse bitmap de 240 pixels quadrado:

![Granularidade de madeira](bitmap-tiling-images/WoodGrain.png "granulação de madeira")

Essa é uma fotografia de um chão de madeira. O `SKShaderTileMode.Mirror` opção permite que ele seja exibido como uma área maior de madeira:

[![CAT relógio](bitmap-tiling-images/CatClock.png "Cat relógio")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alinhamento de bloco

Todos os exemplos mostrados até agora têm usado o sombreador criado pelo `SKShader.CreateBitmap` para cobrir a tela inteira. Na maioria dos casos, você estará usando o lado a lado de bitmap para preenchimento de áreas pequenas ou (raramente mais) para preencher os interiores das linhas espessas. Aqui está o bloco de parede de tijolos fotográfica usado para um retângulo menor:

[![Alinhamento de bloco](bitmap-tiling-images/TileAlignment.png "alinhamento de bloco")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Isso pode parecer bem para você, ou talvez não. Talvez você seja incomodado que o padrão de lado a lado não começa com um brick completo no canto superior esquerdo do retângulo. Isso ocorre porque os sombreadores são alinhados com a tela e não o objeto de gráfico que eles Adornar.

A correção é simples. Criar um `SKMatrix` valor com base em uma transformação de conversão. A transformação efetivamente desloca o padrão lado a lado para o ponto onde você deseja que o canto superior esquerdo do bloco para ser alinhado. Essa abordagem é demonstrada na **alinhamento de bloco** página, que criou a imagem dos blocos não alinhados mostrado acima:

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

O **alinhamento de bloco** página inclui uma `TapGestureRecognizer`. Toque ou clique em tela e o programa alterna para o `SKShader.CreateBitmap` método com um `SKMatrix` argumento. Essa transformação muda o padrão para que o canto superior esquerdo contém um brick completo:

[![Bloco alinhamento tocado](bitmap-tiling-images/TileAlignmentTapped.png "tocado de alinhamento de bloco")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

Você também pode usar essa técnica para garantir que o padrão de bitmap lado a lado é centralizado dentro da área que ela pinta. No **blocos centralizado** página, o `PaintSurface` manipulador primeiro calcula as coordenadas como se ele vai exibir o bitmap único no centro da tela. Ele usa essas coordenadas para criar uma transformação de translação para `SKShader.CreateBitmap`. Essa transformação muda o padrão inteiro para que um bloco é centralizado:

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

O `PaintSurface` manipulador conclui desenhando um círculo no centro da tela. Claro, um dos blocos é exatamente no centro do círculo, e os outros são organizados em um padrão de simétrico:

[![Centralizado blocos](bitmap-tiling-images/CenteredTiles.png "centralizada de blocos")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Outra abordagem de centralização é realmente um pouco mais fácil. Em vez de construir uma transformação de translação que coloca um bloco no centro, você pode centralizar um canto do padrão lado a lado. No `SKMatrix.MakeTranslation` chamar, use os argumentos para o centro da tela:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

O padrão é ainda centralizado e simétricos, mas nenhum bloco está no Centro de:

[![Centralizado alternativo de blocos](bitmap-tiling-images/CenteredTilesAlternate.png "centralizado alternativo de blocos")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplificação por meio de rotação

Às vezes, usando uma transformação no `SKShader.CreateBitmap` método pode simplificar o bloco de bitmap. Isso fica evidente quando a tentativa de definir um bloco para um limite de link da cadeia. O **ChainLinkTile.cs** arquivo cria o bloco mostrado aqui (com um plano de fundo rosa para fins de clareza):

![Bloco de cadeia por link físico](bitmap-tiling-images/HardChainLinkTile.png "bloco cadeia por link físico")

O bloco deve incluir dois links, para que o código no bloco se divide em quatro quadrantes. Os quadrantes do canto superior esquerdo e direito inferior são os mesmos, mas eles não estão completos. Os fios têm pouco cortes que devem ser tratados com alguns de desenho adicionais nos quadrantes canto superior direito e inferior esquerdo. O arquivo que faz todo esse trabalho é 174 linhas de comprimento.

Na verdade, para ser muito mais fácil de criar este bloco:

![Bloco de vínculo de cadeia mais fácil](bitmap-tiling-images/EasierChainLinkTile.png "mais fáceis de bloco de vínculo de cadeia")

Se o sombreador de bloco de bitmap é girada em 90 graus, os elementos visuais são praticamente as mesmas.

O código para criar o bloco de vínculo de cadeia mais fácil é parte do **Link cadeia bloco** página. O construtor determina um tamanho de bloco com base no tipo de dispositivo que o programa está sendo executado e, em seguida, chama `CreateChainLinkTile`, que desenha o bitmap usando linhas, caminhos e sombreadores de gradiente:

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

Com exceção de fios, no bloco é transparente, que significa que você pode exibi-lo na parte superior de outra coisa. O programa carrega em um dos recursos de bitmap, exibe-o para preencher a tela e, em seguida, desenha o sombreador na parte superior:

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

Observe que o sombreador é girada 45 graus, portanto, é orientado, como um limite de link de cadeia real:

[![Limite de Link cadeia](bitmap-tiling-images/ChainLinkFence.png "cadeia metálica")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animando blocos de bitmap

Você pode animar um padrão de bloco de bitmap inteiro Animando a transformação de matriz. Talvez você queira que o padrão a ser mover horizontal ou verticalmente, ou ambos. Você pode fazer isso criando uma transformação de conversão com base nas coordenadas adaptação.

Também é possível desenhar em um bitmap pequeno, ou para manipular bits de pixel do bitmap em uma taxa de 60 vezes por segundo. Esse bitmap pode ser usado para o lado a lado e o padrão de lado a lado inteiro pode parecer ser animada. 

O **bloco de Bitmap animado** página demonstra essa abordagem. Um bitmap é instanciado como um campo para ser 64 pixels quadrados. O construtor chama `DrawBitmap` para dar a ele uma aparência inicial. Se o `angle` campo é zero (como ele é quando o método é chamado pela primeira vez), em seguida, o bitmap conterá duas linhas transversais como um X. As linhas são feitas longo o suficiente para sempre chegar até a borda do bitmap independentemente do `angle` valor: 

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

A sobrecarga de animação ocorre na `OnAppearing` e `OnDisappearing` substitui. O `OnTimerTick` método anima a `angle` valor de graus de 0 a 360 graus a cada 10 segundos para girar a Figura X dentro do bitmap:

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

Devido a simetria da figura X, isso é o mesmo como girar o `angle` valor de 0 graus a cada 2,5 segundos de 90 graus.

O `PaintSurface` manipulador cria um sombreador de bitmap e usa o objeto de pintura para a tela inteira de cores:

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

O `SKShaderTileMode.Mirror` opções Certifique-se de que os braços da X em cada bitmap join com um X em bitmaps adjacentes para criar um padrão animado geral que parece muito mais complexo do que a animação simple sugeriria:

[![Animada Bitmap lado a lado](bitmap-tiling-images/AnimatedBitmapTile.png "animada Bitmap lado a lado")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [CatClock (amostra)](https://developer.xamarin.com/samples/xamarin-forms/CatClock/)
