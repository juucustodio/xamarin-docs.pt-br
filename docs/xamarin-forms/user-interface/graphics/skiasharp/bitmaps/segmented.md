---
title: Exibição segmentada de bitmaps SkiaSharp
description: Exibir um bitmap SkiaSharp para que alguma área seja esticada e algumas áreas não.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a39433a0bad518055542adb190f4f441675ddfb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556367"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Exibição segmentada de bitmaps SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

O `SKCanvas` objeto SkiaSharp define um método chamado `DrawBitmapNinePatch` e dois métodos chamados `DrawBitmapLattice` que são muito semelhantes. Ambos os métodos renderizam um bitmap para o tamanho de um retângulo de destino, mas em vez de alongar o bitmap uniformemente, eles exibem partes do bitmap em suas dimensões de pixel e ampliam outras partes do bitmap para que ele se ajuste ao retângulo:

![Amostras segmentadas](segmented-images/SegmentedSample.png "Amostra segmentada")

Esses métodos geralmente são usados para renderizar bitmaps que formam parte dos objetos da interface do usuário, como botões. Ao criar um botão, geralmente você deseja que o tamanho de um botão seja baseado no conteúdo do botão, mas provavelmente deseja que a borda do botão tenha a mesma largura, independentemente do conteúdo do botão. Esse é um aplicativo ideal do `DrawBitmapNinePatch` .

`DrawBitmapNinePatch` é um caso especial do `DrawBitmapLattice` , mas é o mais fácil de usar e entender os dois métodos.

## <a name="the-nine-patch-display"></a>A exibição de nove patches 

Conceitualmente, [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divide um bitmap em nove retângulos:

![Nove patch](segmented-images/NinePatch.png "Nove patch")

Os retângulos nos quatro cantos são exibidos em seus tamanhos de pixel. Como as setas indicam, as outras áreas nas bordas do bitmap são esticadas horizontal ou verticalmente para a área do retângulo de destino. O retângulo no centro é alongado horizontalmente e verticalmente. 

Se não houver espaço suficiente no retângulo de destino para exibir até os quatro cantos em suas dimensões de pixel, eles serão reduzidos verticalmente para o tamanho disponível e nada, mas os quatro cantos serão exibidos.

Para dividir um bitmap nesses nove retângulos, só é necessário especificar o retângulo no centro. Esta é a sintaxe do `DrawBitmapNinePatch` método:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

O retângulo central é relativo ao bitmap. É um `SKRectI` valor (a versão de inteiro de `SKRect` ) e todas as coordenadas e tamanhos estão em unidades de pixels. O retângulo de destino é relativo à superfície de exibição. O argumento `paint` é opcional.

A página de **exibição de nove patches** no exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) primeiro usa um construtor estático para criar uma propriedade estática pública do tipo `SKBitmap` :

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

Duas outras páginas neste artigo usam o mesmo bitmap. O bitmap tem 500 pixels quadrados e consiste em uma matriz de 25 círculos, todo o mesmo tamanho, cada um ocupando uma área quadrada de 100 pixels:

![Grade de círculo](segmented-images/CircleGrid.png "Grade de círculo")

O construtor de instância do programa cria um `SKCanvasView` com um `PaintSurface` manipulador que usa `DrawBitmapNinePatch` para exibir o bitmap ampliado para toda a superfície de exibição:

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

O `centerRect` retângulo abrange a matriz central de 16 círculos. Os círculos nos cantos são exibidos em suas dimensões de pixel e todo o resto é alongado de acordo:

[![Nove-exibição de patch](segmented-images/NinePatchDisplay.png "Nove-exibição de patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

A página UWP tem de ser de 500 pixels de largura e, portanto, exibe as linhas superior e inferior como uma série de círculos do mesmo tamanho. Caso contrário, todos os círculos que não estão nos cantos são esticados para formar elipses.

Para uma exibição estranha de objetos que consistem em uma combinação de círculos e elipses, tente definir o retângulo central para que ele se sobreponha a linhas e colunas de círculos:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>A exibição malha

Os dois `DrawBitmapLattice` métodos são semelhantes a `DrawBitmapNinePatch` , mas são generalizados para qualquer número de divisões horizontais ou verticais. Essas divisões são definidas por matrizes de inteiros correspondentes a pixels. 

O [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) método com parâmetros para essas matrizes de inteiros não parece funcionar. O [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método com um parâmetro de tipo `SKLattice` funciona e é aquele usado nos exemplos mostrados abaixo.

A [`SKLattice`](xref:SkiaSharp.SKLattice) estrutura define quatro propriedades:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), uma matriz de inteiros
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), uma matriz de inteiros
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), uma matriz de `SKLatticeFlags` , um tipo de enumeração
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) de tipo `Nullable<SKRectI>` para especificar um retângulo de origem opcional dentro do bitmap

A `XDivs` matriz divide a largura do bitmap em faixas verticais. A primeira faixa se estende do pixel 0 à esquerda para `XDivs[0]` . Essa faixa é renderizada em sua largura de pixel. A segunda faixa se estende de `XDivs[0]` para `XDivs[1]` e é ampliada. A terceira faixa se estende de `XDivs[1]` para `XDivs[2]` e é renderizada em sua largura de pixel. A última faixa se estende do último elemento da matriz para a borda direita do bitmap. Se a matriz tiver um número par de elementos, ela será exibida na largura do pixel. Caso contrário, ele será ampliado. O número total de faixas verticais é um maior que o número de elementos na matriz.

A `YDivs` matriz é semelhante. Ele divide a altura da matriz em faixas horizontais.

Juntos, a `XDivs` `YDivs` matriz e divide o bitmap em retângulos. O número de retângulos é igual ao produto do número de faixas horizontais e o número de faixas verticais.

De acordo com a documentação do skia, a `Flags` matriz contém um elemento para cada retângulo, primeiro a linha superior de retângulos, a segunda linha e assim por diante. A `Flags` matriz é do tipo [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) , uma enumeração com os seguintes membros:

- `Default` com valor 0
- `Transparent` com valor 1

No entanto, esses sinalizadores parecem não funcionar conforme deveriam, e é melhor ignorá-los. Mas não defina a `Flags` propriedade como `null` . Defina-o como uma matriz de `SKLatticeFlags` valores grande o suficiente para abranger o número total de retângulos.

A página de **patch malha nove** usa `DrawBitmapLattice` para imitar `DrawBitmapNinePatch` . Ele usa o mesmo bitmap criado em `NinePatchDisplayPage` :

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

As `XDivs` Propriedades e `YDivs` são definidas como matrizes de apenas dois inteiros, dividindo o bitmap em três faixas horizontal e verticalmente: do pixel 0 para o pixel 100 (renderizado no tamanho do pixel), do pixel 100 ao pixel 400 (ampliado) e do pixel 400 para o pixel 500 (tamanho do pixel). Juntos, `XDivs` e `YDivs` definem um total de 9 retângulos, que é o tamanho da `Flags` matriz. Simplesmente criar a matriz é suficiente para criar uma matriz de `SKLatticeFlags.Default` valores.

A exibição é idêntica ao programa anterior:

[![Malha nove-patch](segmented-images/LatticeNinePatch.png "Malha nove-patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

A página de **exibição malha** divide o bitmap em 16 retângulos:

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

As `XDivs` `YDivs` matrizes e são um pouco diferentes, fazendo com que a exibição não seja tão simétrica quanto os exemplos anteriores:

[![Exibição do malha](segmented-images/LatticeDisplay.png "Exibição do malha")](segmented-images/LatticeDisplay-Large.png#lightbox)

Nas imagens do iOS e do Android à esquerda, somente os círculos menores são renderizados em seus tamanhos de pixel. Tudo o mais é alongado.

A página de **exibição malha** generaliza a criação da `Flags` matriz, permitindo que você experimente e com `XDivs` `YDivs` mais facilidade. Em particular, você desejará ver o que acontece quando você define o primeiro elemento da `XDivs` `YDivs` matriz ou como 0. 

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)