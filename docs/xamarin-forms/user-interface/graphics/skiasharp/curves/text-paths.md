---
title: Caminhos e texto
description: Explore a intersecção de caminhos e texto
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/01/2017
ms.openlocfilehash: 77005665d163e7f9f62325b94cc5c779a7873f78
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="paths-and-text"></a>Caminhos e texto

_Explore a intersecção de caminhos e texto_

Em sistemas de gráfico moderno, fontes de texto são coleções de estruturas de tópicos de caractere, geralmente é definidas por curvas de Bézier quadráticas. Consequentemente, muitos sistemas gráficos modernos incluem um recurso para converter caracteres de texto em um caminho de gráficos. 

Você já viu que você pode traçar o contorno de caracteres de texto, bem como preenchê-los. Isso permite que você exiba os contornos de caracteres com uma largura específica traço e até mesmo um efeito de caminho, conforme descrito no [ **caminho efeitos** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artigo. Mas também é possível converter uma cadeia de caracteres em uma `SKPath` objeto. Isso significa que as estruturas de texto podem ser usadas para recorte com técnicas que foram descritos no [ **com regiões e caminhos de recorte** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md) artigo. 

Além de usar um efeito de caminho para traçar um esboço de caractere, você também pode criar efeitos se baseiam em um caminhos são derivados de cadeias de caracteres de caminho, e também é possível combinar os dois efeitos:

![](text-paths-images/pathsandtextsample.png "Efeito do caminho de texto")

No [artigo anterior](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) você viu como o [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) método `SKPaint` pode obter uma descrição de um caminho traçado. Você também pode usar esse método com caminhos derivados contornos de caracteres.

Por fim, este artigo demonstra outra interseção de caminhos e texto: O [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método `SKCanvas` permite que você exiba uma cadeia de caracteres de texto para que a linha de base do texto segue um caminho curvo.

## <a name="text-to-path-conversion"></a>Texto para conversão de caminho

O [ `GetTextPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetTextPath/p/System.String/System.Single/System.Single/) método `SKPaint` converte uma cadeia de caracteres para um `SKPath` objeto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

O `x` e `y` argumentos indicam o ponto de partida da linha de base do lado esquerdo do texto. Eles executar a mesma função aqui, como no `DrawText` método `SKCanvas`. No caminho, a linha de base do lado esquerdo do texto terá as coordenadas (x, y). 

O `GetTextPath` método é um exagero se você simplesmente deseja preencher ou traçar o caminho resultante. O normal `DrawText` método permite que você faça isso. O `GetTextPath` método é mais útil para outras tarefas que envolvem caminhos.

Uma dessas tarefas é de recorte. O **recorte texto** página cria um demarcador de recorte com base em dos contornos do caractere da palavra "Código". Esse caminho é estendido para o tamanho da página para recortar um bitmap que contém uma imagem do **recorte texto** código-fonte:

[![](text-paths-images/clippingtext-small.png "Tripla captura de tela da página de recorte texto")](text-paths-images/clippingtext-large.png#lightbox "tripla captura de tela da página de texto de recorte")

O [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) construtor da classe carrega o bitmap que é armazenado como um recurso incorporado no **mídia** pasta da solução:

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

O `PaintSurface` manipulador começa criando um `SKPaint` adequado para o texto do objeto. O `Typeface` estiver definida, bem como a `TextSize`, embora para esse aplicativo específico de `TextSize` propriedade é puramente arbirtrary. Também Observe que não há nenhum `Style` configuração.

O `TextSize` e `Style` as configurações de propriedade não são necessárias porque isso `SKPaint` objeto é usado somente para o `GetTextPath` chamada usando a cadeia de caracteres de texto "Código". O manipulador de medidas, em seguida, o resultante `SKPath` do objeto e aplica três transformações para centralizá-lo e dimensioná-lo para o tamanho da página. O caminho, em seguida, pode ser definido como o caminho de recorte:

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, 
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

Depois que o caminho de recorte for definido, o bitmap pode ser exibido e será recortado para o contorno de caractere. Observe o uso do [ `AspectFill` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRect.AspectFill/p/SkiaSharp.SKSize/) método `SKRect` que calcula um retângulo para preencher a página preservando a taxa de proporção.

O **texto caminho efeito** página converte um caractere único e comercial em um caminho para criar um efeito de caminho 1D. Um objeto de pintura com esse efeito de caminho, em seguida, é usado para traçar o contorno de uma versão maior do mesmo caractere:

[![](text-paths-images/textpatheffect-small.png "Tripla captura de tela da página de texto caminho efeito")](text-paths-images/textpatheffect-large.png#lightbox "tripla captura da página do efeito de caminho de texto")

Volume de trabalho no [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe ocorre no construtor e campos. Os dois `SKPaint` objetos definidos como campos são usados para finalidades diferentes: A primeira (denominado `textPathPaint`) é usado para converter o e comercial com um `TextSize` de 50 para um caminho para o efeito de caminho 1D. O segundo (`textPaint`) é usado para exibir a versão maior do e comercial com o efeito desse caminho. Por esse motivo, o `Style` de pintura Este segundo objeto é definido como `Stroke`, mas o `StrokeWidth` propriedade não está definida como essa propriedade não é necessária ao usar um efeito de caminho 1D:

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint 
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds;
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character, 
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

O construtor usa primeiro o `textPathPaint` objeto para medir o e comercial com um `TextSize` de 50. Negativos de coordenadas do centro do retângulo, em seguida, são passados para o `GetTextPath` método para converter o texto em um caminho. O caminho resultante tem o (0, 0) no Centro de caractere, que é ideal para um efeito de 1-D caminho do ponto.

Talvez você ache que o `SKPathEffect` criado no final do construtor de objeto pode ser definido como o `PathEffect` propriedade `textPaint` em vez de salvo como um campo. Mas esse tornou não funcionar muito bem porque ele distorcida os resultados do `MeasureText` chamar o `PaintSurface` manipulador:

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds;
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Que `MeasureText` chamada é usada para centralizar o caractere na página. Para evitar problemas, o `PathEffect` propriedade é definida para o objeto de pintura depois que o texto foi medido, mas antes de ser exibido.

## <a name="outlines-of-character-outlines"></a>Contornos das estruturas de caractere

Normalmente o [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) método `SKPaint` converte um caminho para outro, aplicando as propriedades de pintura, principalmente a largura e o caminho efeito traçado. Quando usada sem efeitos de caminho `GetFillPath` efetivamente cria um caminho que descreve a outro caminho. Isso foi demonstrado no **toque, a estrutura de tópicos, o caminho** página o [ **caminho efeitos** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artigo.

Você também pode chamar `GetFillPath` no caminho retornado de `GetTextPath` , mas primeiro talvez não seja totalmente se esse procuraria o seguinte.

O **caractere da estrutura de tópicos descreve** página demonstra a técnica. É o código relevante no `PaintSurface` manipulador do [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

O construtor começa criando um `SKPaint` objeto chamado `textPaint` com um `TextSize` propriedade com base no tamanho da página. Isso é convertido em um caminho usando o `GetTextPath` método. Os argumentos de coordenadas para `GetTextPath` center efetivamente o caminho na tela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds;
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

O `PaintSurface` manipulador, em seguida, cria um novo caminho denominado `outlinePath`. Isso torna-se o caminho de destino na chamada para `GetFillPath`. O `StrokeWidth` propriedade 25 causas `outlinePath` para descrever a estrutura de tópicos de um caminho de 25 pixels de largura traça os caracteres de texto. Esse caminho, em seguida, é exibido em vermelho com uma largura de 5 traçado:

[![](text-paths-images/characteroutlineoutlines-small.png "Tripla captura da página do caractere da estrutura de tópicos descreve")](text-paths-images/characteroutlineoutlines-large.png#lightbox "tripla captura de tela da página de estruturas de tópicos de caractere")

Preste atenção e você verá sobreposições em que a estrutura de tópicos do caminho faz uma curva fechada. Esses são os artefatos normais desse processo.

## <a name="text-along-a-path"></a>Texto em um demarcador

Normalmente, o texto é exibido em uma linha de base horizontal. Texto pode ser girado para executar vertical ou diagonalmente, mas a linha de base ainda é uma linha reta.

Há ocasiões, entretanto, quando deseja que o texto a ser executado ao longo de uma curva. Essa é a finalidade do [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) método `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

O texto especificado no primeiro argumento é feito para executar no caminho especificado como o segundo argumento. Você pode começar o texto em um deslocamento do início do caminho com o `hOffset` argumento. Normalmente o caminho de forma a linha de base do texto: ascendentes de texto estão em um lado do caminho e os descendentes de texto estão em outro. Mas você pode deslocar a linha de base de texto do caminho com o `vOffset` argumento.

Esse método não têm recursos para fornecer orientação sobre como configurar o `TextSize` propriedade `SKPaint` para tornar o texto dimensionado perfeitamente para executados desde o início do caminho até o fim. Às vezes, você pode descobrir esse tamanho de texto por conta própria. Outras vezes, você precisará usar as funções de medição de caminho para ser descrita no artigo futuras.

O **texto Circular** programa quebra o texto em torno de um círculo. É fácil determinar a circunferência de um círculo, portanto, é fácil dimensionar o texto de acordo com exatamente. O `PaintSurface` manipulador do [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcula um raio de um círculo com base no tamanho da página. Torna-se de que círculo `circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

O `TextSize` propriedade `textPaint` , em seguida, é ajustado para que a largura do texto corresponda a circunferência do círculo:

[![](text-paths-images/circulartext-small.png "Captura de tela da página de texto Circular tripla")](text-paths-images/circulartext-large.png#lightbox "tripla captura de tela da página de texto Circular")

O texto foi escolhido será um pouco circular: A palavra "círculo" é o assunto da frase e o objeto de uma frase de prepositional. 

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
