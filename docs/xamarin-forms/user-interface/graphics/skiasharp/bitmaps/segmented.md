---
title: Exibição segmentada de bitmaps de SkiaSharp
description: Exiba um bitmap de SkiaSharp para alguma área é alongado e algumas áreas não são.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 71997acde4545fec801dfdc8147ab1a9ace7ab24
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119222"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Exibição segmentada de bitmaps de SkiaSharp

O SkiaSharp `SKCanvas` objeto define um método chamado `DrawBitmapNinePatch` e dois métodos chamados `DrawBitmapLattice` que são muito semelhantes. Ambos esses métodos processam um bitmap para o tamanho de um retângulo de destino, mas em vez de ampliar o bitmap de maneira uniforme, eles exibem partes do bitmap em suas dimensões de pixel e outras partes do bitmap do stretch para que ele se adapta o retângulo:

![Segmentada amostras](segmented-images/SegmentedSample.png "segmentadas de exemplo")

Esses métodos são geralmente usados para renderizar bitmaps que fazem parte dos objetos de interface do usuário como botões. Ao projetar um botão, geralmente você deseja o tamanho de um botão deve ser baseado no conteúdo do botão, mas você provavelmente deseja que a borda do botão tenha a mesma largura, independentemente do conteúdo do botão. Que é um aplicativo ideal de `DrawBitmapNinePatch`.

`DrawBitmapNinePatch` é um caso especial de `DrawBitmapLattice` , mas é mais fácil dos dois métodos para uso e entendimento.

## <a name="the-nine-patch-display"></a>A exibição de nove-patch 

Conceitualmente, [ `DrawBitmapNinePatch` ](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divide um bitmap em nove retângulos:

![Patch nove](segmented-images/NinePatch.png "nove Patch")

Os retângulos nos quatro cantos são exibidos em seus tamanhos de pixel. Como as setas indicam, outras áreas nas bordas do bitmap são aumentadas horizontal ou verticalmente para a área do retângulo de destino. O retângulo no centro é alongado tanto horizontal como verticalmente. 

Se não houver espaço suficiente no retângulo de destino para exibir os quatro cantos até mesmo em suas dimensões de pixel, eles são dimensionados para baixo até o tamanho disponível e nada, mas os quatro cantos são exibidos.

Para dividir um bitmap nessas nove retângulos, somente é necessário especificar o retângulo no centro. Esta é a sintaxe do `DrawBitmapNinePatch` método:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

O retângulo central é relativo ao bitmap. É um `SKRectI` valor (a versão de inteiro de `SKRect`) e todos os tamanhos e as coordenadas são em unidades de pixels. O retângulo de destino é relativo a superfície de exibição. O argumento `paint` é opcional.

O **exibição de Patch de nove** página na [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) pela primeira vez, o exemplo usa um construtor estático para criar uma propriedade estática pública do tipo `SKBitmap`:

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

Duas outras páginas neste artigo usam esse bitmap mesmo. O bitmap é 500 pixels quadrados e consiste em uma matriz de 25 círculos, todas do mesmo tamanho, cada uma área de 100 pixels quadrada ocupando:

![Grade de círculo](segmented-images/CircleGrid.png "círculo de grade")

Construtor de instância do programa cria um `SKCanvasView` com um `PaintSurface` manipulador usa `DrawBitmapNinePatch` para exibir o bitmap ampliado para sua superfície de exibição inteiro:

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

O `centerRect` retângulo abrange a matriz de central de 16 círculos. Os círculos nos cantos são exibidos em suas dimensões de pixel e todo o resto é alongado adequadamente:

[![Exibição de Patch de nove](segmented-images/NinePatchDisplay.png "exibição nove-Patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

A página UWP seja 500 pixels de largura e, portanto, exibe as linhas superior e inferior como uma série de círculos do mesmo tamanho. Caso contrário, todos os círculos que não estão nos cantos são ampliados para reticências de formulário.

Para exibir uma estranha dos objetos que consiste em uma combinação de círculos e elipses, tente definir o retângulo central, de modo que ele se sobrepõe a linhas e colunas de círculos:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>A exibição de malha

Os dois `DrawBitmapLattice` métodos são semelhantes a `DrawBitmapNinePatch`, mas eles são generalizados para qualquer número de divisões horizontais ou verticais. Essas divisões são definidas pelas matrizes de inteiros correspondente a pixels. 

O [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) método com parâmetros para essas matrizes de inteiros parece não funcionar. O [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) método com um parâmetro de tipo `SKLattice` funciona, e que é usada nos exemplos mostrados abaixo.

O [ `SKLattice` ](xref:SkiaSharp.SKLattice) estrutura define quatro propriedades:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), uma matriz de inteiros
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), uma matriz de inteiros
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), uma matriz de `SKLatticeFlags`, um tipo de enumeração
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) do tipo `Nullable<SKRectI>` para especificar um retângulo de origem opcionais dentro do bitmap

O `XDivs` matriz divide a largura do bitmap em faixas verticais. A primeira faixa se estende do pixel 0 à esquerda para `XDivs[0]`. Esta faixa é renderizada em sua largura de pixel. A segunda faixa se estende do `XDivs[0]` para `XDivs[1]`e é estendida. A terceira faixa se estende do `XDivs[1]` para `XDivs[2]` e é renderizado em sua largura de pixel. A última fita se estende do último elemento da matriz para a borda direita do bitmap. Se a matriz tem um número par de elementos, em seguida, ele será exibido em sua largura de pixel. Caso contrário, ele é estendido. O número total de faixas verticais é um mais do que o número de elementos na matriz.

O `YDivs` matriz é semelhante. Ele divide a altura da matriz em faixas horizontais.

Juntos, o `XDivs` e `YDivs` matriz dividir o bitmap em retângulos. O número de retângulos é igual ao produto do número de faixas horizontais e o número de faixas verticais.

De acordo com a documentação de Skia a `Flags` matriz contém um elemento de cada retângulo, primeiro a linha superior dos retângulos, em seguida, a segunda linha e assim por diante. O `Flags` matriz é do tipo [ `SKLatticeFlags` ](xref:SkiaSharp.SKLatticeFlags), uma enumeração com os seguintes membros:

- `Default` com o valor 0
- `Transparent` com o valor 1

No entanto, esses sinalizadores não parecem funcionar como eles se destinam a, e é recomendado para ignorá-los. Mas não defina as `Flags` propriedade para `null`. Defina-o como uma matriz de `SKLatticeFlags` valores grandes o suficiente para abranger o número total de retângulos.

O **Treliça nove Patch** página usa `DrawBitmapLattice` imitar `DrawBitmapNinePatch`. Ele usa o mesmo bitmap criado no `NinePatchDisplayPage`:

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

Tanto a `XDivs` e `YDivs` propriedades são definidas como matrizes de apenas dois inteiros, dividindo o bitmap em três faixas horizontalmente e verticalmente: do pixel 0 a 100 (renderizado no tamanho de pixel), de pixel do pixel 100 a 400 de pixel (estendido), e do pixel 400 a 500 (tamanho de pixel) de pixel. Juntos, `XDivs` e `YDivs` definem um total de 9 retângulos, que é o tamanho do `Flags` matriz. Basta criar a matriz é suficiente para criar uma matriz de `SKLatticeFlags.Default` valores.

A exibição é idêntica do programa anterior:

[![Treliça nove-Patch](segmented-images/LatticeNinePatch.png "Treliça nove-Patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

O **Treliça exibição** página divide o bitmap em 16 retângulos:

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

O `XDivs` e `YDivs` matrizes são um pouco diferentes, fazendo com que a exibição não seja bem como simétrico que os exemplos anteriores:

[![Exibição de Treliça](segmented-images/LatticeDisplay.png "Treliça exibição")](segmented-images/LatticeDisplay-Large.png#lightbox)

No iOS e Android imagens à esquerda, apenas os círculos menores são renderizados em seus tamanhos de pixel. Todo o resto é alongado.

O **exibição de Treliça** página generaliza a criação da `Flags` array, permitindo que você pode experimentar com `XDivs` e `YDivs` com mais facilidade. Em particular, você desejará ver o que acontece quando você define o primeiro elemento do `XDivs` ou `YDivs` matriz como 0. 

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
