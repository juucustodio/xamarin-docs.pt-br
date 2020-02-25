---
title: Filtros de cores de SkiaSharp
description: Use filtros de cor para converter cores com transformações ou tabelas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 5aa8b2e85d5a7d547af5333dcaf350025b86cc26
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647693"
---
# <a name="skiasharp-color-filters"></a>Filtros de cores de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtros de cor pode converter cores em um bitmap (ou outra imagem) para as outras cores para efeitos como posterização:

![Exemplo de filtros de cores](color-filters-images/ColorFiltersExample.png "exemplo de filtros de cores")

Para usar um filtro de cores, defina as [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) propriedade do `SKPaint` a um objeto do tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) criado por um dos métodos estáticos na classe. Este artigo demonstra: 

- uma transformação de cor criado com o [ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método.
- uma tabela de cores é criada com o [ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) método.

## <a name="the-color-transform"></a>A transformação de cor

A transformação de cor envolve o uso de uma matriz de modificar as cores. Como a maioria dos sistemas de gráficos 2D, SkiaSharp usa matrizes principalmente para transformar os pontos de coordenadas como iscussed no artigo [ **transformações de matriz em SkiaSharp**](../transforms/matrix.md). O [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) também dá suporte a transformações de matriz, mas as cores RGB de transformações de matriz. Alguma familiaridade com conceitos de matriz é necessária entender essas transformações de cor. 

A matriz de transformação de cor tem uma dimensão de quatro linhas e cinco colunas:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Transforma uma cor de fonte RGB (R, G, B, A) para a cor de destino (R', G', B ','). 

Em preparação para a multiplicação de matriz, a cor da fonte é convertida em uma matriz de 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Esses valores R, G, B e A são os bytes originais, variando de 0 a 255. Eles são _não_ normalizados para valores de ponto flutuante no intervalo de 0 a 1.

A célula adicional é necessária para um fator de translação. Isso é análogo ao uso de uma matriz 3 × 3 para transformar os pontos de coordenadas bidimensionais, conforme descrito na seção [ **o motivo para a matriz de 3 por 3** ](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) no artigo sobre como usar matrizes de transformação pontos de coordenadas.

A matriz 4 × 5 é multiplicada pela matriz de 5 × 1, e o produto é uma matriz 4 × 1 com a cor transformada:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Aqui estão as fórmulas separadas para R', G', 'B e A':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

A maior parte da matriz consiste em fatores de multiplicação que geralmente estão no intervalo de 0 a 2. No entanto, a última coluna (M15 por meio de M45) contém valores que são adicionados nas fórmulas. Geralmente, esses valores variam de 0 a 255. Os resultados são fixados entre os valores de 0 e 255.

A matriz de identidade é:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Isso faz com que nenhuma alteração para as cores. As fórmulas de transformação são:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

A célula M44 é muito importante porque ele preserva a opacidade. Ele é geralmente o caso que M41, M42 e M43 são todos zero, porque você provavelmente não quer opacidade deve ser baseado nos valores vermelhos, verdes e azuis. Mas se M44 for zero, então um ' será zero, e nada ficará visível.

Um dos usos mais comuns de matriz de cores é converter um bitmap de cor em um bitmap de escala de cinza. Isso envolve uma fórmula para uma média ponderada dos valores de vermelho, verdes e azuis. Para exibições de vídeo usando o espaço de cor sRGB ("standard vermelho verde azul"), essa fórmula é:

tonalidade cinza = 0.2126· R + 0.7152· G + 0.0722· B

Para converter um bitmap de cor em um bitmap de escala de cinza, o R', G', e os resultados de B' devem igual mesmo valor. A matriz é:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Não há nenhum tipo de dados de SkiaSharp que corresponde a esta matriz. Em vez disso, você deve representar a matriz como uma matriz de 20 `float` valores na ordem de linha: primeira linha, em seguida, segunda linha e assim por diante.

Estático [ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) método tem a seguinte sintaxe:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

em que `matrix` é uma matriz do 20 `float` valores. Ao criar a matriz em C#, é fácil formatar os números para que eles se parecer com a matriz 4 × 5. Isso é demonstrado no **matriz de escala de cinza** página de [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemplo:

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

O `DrawBitmap` provém do método usado neste código o **BitmapExtension.cs** arquivo incluído com o [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemplo. 

Aqui está o resultado em execução no iOS, Android e plataforma Universal do Windows:

[![Matriz de escala de cinza](color-filters-images/GrayScaleMatrix.png "matriz de escala de cinza")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Cuidado com o valor na quarta linha e quarta coluna. Que é o fator crucial que é multiplicado pelo valor da cor original para o A' valor da cor transformada. Se essa célula for zero, nada será exibido e o problema pode ser difícil localizar.

Ao experimentar com matrizes de cor, você pode tratar a transformação de perspectiva da fonte ou sob a perspectiva de destino. Como deve vermelho pixel da fonte de contribuir com os pixels de vermelhos, verdes e azuis do destino? Que é determinado pelos valores na primeira _coluna_ da matriz. Como alternativa, como o pixel vermelho de destino será afetado por pixels da fonte de vermelho, verdes e azuis? Que é determinado pelo primeiro _linha_ da matriz.

Para algumas ideias sobre como usar transformações de cor, consulte a [ **recolorindo imagens** ](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) páginas. A discussão se refere a formulários do Windows e a matriz é um formato diferente, mas os conceitos são os mesmos.

O **Pastel matriz** calcula o pixel do destino vermelho attenuating o pixel fonte vermelha e um pouco, enfatizando os pixels vermelhos e verdes. Esse processo ocorre da mesma forma para os pixels de verdes e azuis:

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

O resultado é diminuir a intensidade das cores, como você pode ver aqui:

[![Matriz pastel](color-filters-images/PastelMatrix.png "matriz Pastel")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tabelas de cores

Estático [ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) método vem em duas versões:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

As matrizes sempre contenham 256 entradas. No `CreateTable` método com uma tabela, a mesma tabela é usado para os componentes vermelhos, verdes e azuis. É uma tabela simples de pesquisa: Se a cor de origem for (r, g, B) e a cor de destino for (r ', B ', g '), os componentes de destino serão obtidos pela indexação `table` com os componentes de origem:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

O segundo método, cada um dos quatro componentes de cor pode ter uma tabela de cores separadas ou as mesmas tabelas de cor podem ser compartilhadas entre dois ou mais componentes.

Se você deseja definir um dos argumentos para o segundo `CreateTable` método para uma tabela de cores que contém os valores de 0 a 255 em sequência, você pode usar `null` em vez disso. Com muita frequência os `CreateTable` chamada tem um `null` primeiro argumento para o canal alfa.

Na seção **posterização** neste artigo sobre [bits de pixel do bitmap de SkiaSharp acessando](../bitmaps/pixel-bits.md#posterization), você viu como modificar os bits de pixel individuais de um bitmap para reduzir sua resolução de cor. Essa é uma técnica chamada _posterização_. 

Você também pode Posterizar um bitmap com uma tabela de cores. O construtor do **Posterizar tabela** página cria uma tabela de cores que mapeia seu índice para um byte com a parte inferior a 6 bits definidos como zero:

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

O programa opta por usar essa tabela de cores somente para os canais de verdes e azuis. O canal vermelho continua a ter uma solução completa:

[![Tabela de Posterizar](color-filters-images/PosterizeTable.png "Posterizar tabela")](color-filters-images/PosterizeTable-Large.png#lightbox)

Você pode usar várias tabelas de cores para os canais de cores diferentes para vários efeitos. 

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
