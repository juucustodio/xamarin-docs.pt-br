---
title: SkiaSharp filtros de cores
description: Use filtros de cores para converter cores com transformações ou tabelas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 809477fe466ee7a8f0985308896c14341f2dd460
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561931"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp filtros de cores

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de cores podem traduzir cores em um bitmap (ou outra imagem) para outras cores para efeitos como a Posterização:

![Exemplo de filtros de cor](color-filters-images/ColorFiltersExample.png "Exemplo de filtros de cor")

Para usar um filtro de cores, defina a [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) propriedade de `SKPaint` como um objeto do tipo [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) criado por um dos métodos estáticos nessa classe. Este artigo demonstra: 

- uma transformação de cor criada com o [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método.
- uma tabela de cores criada com o [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) método.

## <a name="the-color-transform"></a>A transformação de cor

A transformação de cor envolve o uso de uma matriz para modificar as cores. Assim como a maioria dos sistemas de gráficos 2D, o SkiaSharp usa matrizes principalmente para transformar pontos de coordenadas como iscussed na matriz do artigo [**transformações em SkiaSharp**](../transforms/matrix.md). O [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) também dá suporte a transformações de matriz, mas a matriz transforma as cores RGB. É necessário ter alguma familiaridade com os conceitos de matriz para entender essas transformações de cores. 

A matriz de transformação de cor tem uma dimensão de quatro linhas e cinco colunas:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Ele transforma uma cor de origem RGB (R, G, B, a) na cor de destino (R ', G ', B ', A '). 

Na preparação para a multiplicação de matriz, a cor de origem é convertida em uma matriz de 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Esses valores R, G, B e a são os bytes originais que variam de 0 a 255. Eles _não_ são normalizados para valores de ponto flutuante no intervalo de 0 a 1.

A célula extra é necessária para um fator de conversão. Isso é análogo ao uso de uma matriz 3 × 3 para transformar pontos de coordenadas bidimensionais conforme descrito na seção [**o motivo da matriz 3-by-3**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) no artigo sobre como usar matrizes para transformar pontos de coordenadas.

A matriz 4 × 5 é multiplicada pela matriz 5 × 1 e o produto é uma matriz 4 × 1 com a cor transformada:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Aqui estão as fórmulas separadas para R ', G ', B ' e um ':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

A maior parte da matriz consiste em fatores multiplicativa que geralmente estão no intervalo de 0 a 2. No entanto, a última coluna (M15 até M45) contém valores que são adicionados nas fórmulas. Esses valores geralmente variam de 0 a 255. Os resultados são clamped entre os valores de 0 e 255.

A matriz de identidade é:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Isso não causa nenhuma alteração nas cores. As fórmulas de transformação são:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

A célula M44 é muito importante porque preserva a opacidade. Geralmente, é o caso de M41, M42 e M43 serem todos zero, porque você provavelmente não quer que a opacidade seja baseada nos valores vermelho, verde e azul. Mas se M44 for zero, um ' será zero e nada será visível.

Um dos usos mais comuns da matriz de cores é converter um bitmap de cor em um bitmap de escala cinza. Isso envolve uma fórmula para uma média ponderada dos valores vermelho, verde e azul. Para exibições de vídeo usando o espaço de cores sRGB ("padrão vermelho verde"), essa fórmula é:

cinza-tonalidade = 0,2126 · R + 0,7152 · G + 0, 722 · B

Para converter um bitmap de cor em um bitmap de escala cinza, os resultados de R ', G ' e B ' devem ser iguais ao mesmo valor. A matriz é:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Não há nenhum tipo de dados SkiaSharp que corresponda a esta matriz. Em vez disso, você deve representar a matriz como uma matriz de 20 `float` valores em ordem de linha: primeira linha, depois segunda linha e assim por diante.

O [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método estático tem a seguinte sintaxe:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

em que `matrix` é uma matriz dos 20 `float` valores. Ao criar a matriz em C#, é fácil formatar os números para que eles se assemelhem à matriz 4 × 5. Isso é demonstrado na página **matriz de escala cinza** no exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

O `DrawBitmap` método usado neste código é do arquivo **BitmapExtension.cs** incluído no exemplo de [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . 

Este é o resultado em execução em iOS, Android e Plataforma Universal do Windows:

[![Matriz cinza-escala](color-filters-images/GrayScaleMatrix.png "Matriz cinza-escala")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Observe o valor na quarta linha e na quarta coluna. Esse é o fator crucial que é multiplicado pelo valor da cor original para o valor ' a ' da cor transformada. Se essa célula for zero, nada será exibido e o problema poderá ser difícil de localizar.

Ao experimentar as matrizes de cores, você pode tratar a transformação da perspectiva da origem ou da perspectiva do destino. Como o pixel vermelho da origem contribui para os pixels vermelho, verde e azul do destino? Isso é determinado pelos valores na primeira _coluna_ da matriz. Como alternativa, como o pixel vermelho de destino deve ser afetado pelos pixels vermelho, verde e azul da fonte? Isso é determinado pela primeira _linha_ da matriz.

Para algumas ideias sobre como usar transformações de cores, consulte as páginas [**Recolorindo imagens**](/dotnet/framework/winforms/advanced/recoloring-images) . A discussão se preocupa Windows Forms e a matriz é um formato diferente, mas os conceitos são os mesmos.

A **matriz pastel** calcula o pixel vermelho de destino atenuando o pixel vermelho de origem e enfatizando ligeiramente os pixels vermelho e verde. Esse processo ocorre de forma semelhante para os pixels verde e azul:

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

O resultado é fazer mudo da intensidade das cores como você pode ver aqui:

[![Matriz pastel](color-filters-images/PastelMatrix.png "Matriz pastel")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tabelas de cores

O [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) método estático vem em duas versões:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

As matrizes sempre contêm 256 entradas. No `CreateTable` método com uma tabela, a mesma tabela é usada para os componentes vermelho, verde e azul. É uma tabela simples de pesquisa: se a cor de origem for (R, G, B) e a cor de destino for (R ', B ', G '), os componentes de destino serão obtidos pela indexação `table` com os componentes de origem:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

No segundo método, cada um dos quatro componentes de cor pode ter uma tabela de cores separada ou as mesmas tabelas de cores podem ser compartilhadas entre dois ou mais componentes.

Se você quiser definir um dos argumentos para o segundo `CreateTable` método para uma tabela de cores que contém os valores de 0 a 255 em sequência, você pode usar `null` em vez disso. Muitas vezes, a `CreateTable` chamada tem um `null` primeiro argumento para o canal alfa.

Na seção sobre a **Posterização** no artigo sobre como [acessar bits de pixel de bitmap SkiaSharp](../bitmaps/pixel-bits.md#posterization), você viu como modificar os bits de pixel individuais de um bitmap para reduzir sua resolução de cor. Essa é uma técnica chamada de _Posterização_. 

Você também pode posterização de um bitmap com uma tabela de cores. O construtor da página de **tabela de Posterização** cria uma tabela de cores que mapeia seu índice para um byte com os 6 bits inferiores definidos como zero:

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
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

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

O programa escolhe usar esta tabela de cores somente para os canais verdes e azuis. O canal vermelho continua tendo a resolução completa:

[![Tabela de Posterização](color-filters-images/PosterizeTable.png "Tabela de Posterização")](color-filters-images/PosterizeTable-Large.png#lightbox)

Você pode usar várias tabelas de cores para diferentes canais de cores para vários efeitos. 

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)