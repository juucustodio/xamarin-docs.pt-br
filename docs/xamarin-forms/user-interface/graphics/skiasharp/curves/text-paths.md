---
title: Caminhos e texto em SkiaSharp
description: Este artigo explora a interseção de caminhos de SkiaSharp e texto e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: 366a6e9585817c5a47ba5bec14fb2f238ab23a6b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050177"
---
# <a name="paths-and-text-in-skiasharp"></a>Caminhos e texto em SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Explore a intersecção de caminhos e texto_

Em sistemas de gráficos modernos, fontes de texto são coleções de contornos de caracteres, geralmente definidos por curvas de Bézier quadráticas. Consequentemente, muitos sistemas gráficos modernos incluem um recurso para converter caracteres de texto em um caminho de gráficos.

Você já viu que você pode traçar os contornos de caracteres de texto, bem como preenchê-los. Isso permite que você exiba os contornos de caracteres com uma largura de traço específica e até mesmo um efeito de caminho, conforme descrito na [ **efeitos de caminho** ](effects.md) artigo. Mas também é possível converter uma cadeia de caracteres em um `SKPath` objeto. Isso significa que os contornos de texto podem ser usados para recorte com as técnicas descritas na [ **recorte com caminhos de regiões** ](clipping.md) artigo.

Além de usar um efeito de caminho para traçar um contorno de caractere, você também pode criar efeitos de caminho com base em um caminho que é derivado de uma cadeia de caracteres e você pode até combinar os dois efeitos:

![](text-paths-images/pathsandtextsample.png "Efeito de caminho do texto")

Neste artigo anterior sobre [ **efeitos de caminho**](effects.md), você viu como o [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) método `SKPaint` pode obter uma descrição de um caminho traçado. Você também pode usar esse método com caminhos derivados de contornos de caracteres.

Por fim, este artigo demonstra outra interseção dos caminhos e texto: O [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método `SKCanvas` permite que você exiba uma cadeia de caracteres de texto para que a linha de base do texto segue um caminho curvo.

## <a name="text-to-path-conversion"></a>Texto para a conversão de caminho

O [ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) método `SKPaint` converte uma cadeia de caracteres para um `SKPath` objeto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

O `x` e `y` argumentos indicam o ponto de partida da linha de base do lado esquerdo do texto. Eles têm o mesmo papel aqui, como mostra a `DrawText` método de `SKCanvas`. No caminho, a linha de base do lado esquerdo do texto terá as coordenadas (x, y).

O `GetTextPath` método é um exagero, se você quiser apenas preencher ou traçar o caminho resultante. O vetor perpendicular `DrawText` método permite que você faça isso. O `GetTextPath` método é mais útil para outras tarefas que envolvem caminhos.

Uma dessas tarefas é de recorte. O **recorte texto** página cria um caminho de recorte com base nos contornos de caracteres da palavra "Código". Esse caminho é alongado para o tamanho da página para recortar um bitmap que contém uma imagem do **recorte texto** código-fonte:

[![](text-paths-images/clippingtext-small.png "Captura de tela da página de recorte texto tripla")](text-paths-images/clippingtext-large.png#lightbox "tripla captura de tela da página de texto de recorte")

O [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) construtor de classe carrega o bitmap que é armazenado como um recurso inserido no **mídia** pasta da solução:

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
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

O `PaintSurface` manipulador começa criando um `SKPaint` adequado para o texto do objeto. O `Typeface` estiver definida, bem como a `TextSize`, embora para este aplicativo específico a `TextSize` propriedade é puramente arbitrária. Observe também que há nenhum `Style` configuração.

O `TextSize` e `Style` configurações de propriedade não são necessárias porque isso `SKPaint` objeto é usado exclusivamente para o `GetTextPath` chamada usando a cadeia de caracteres de texto "Código". O manipulador, em seguida, mede o resultante `SKPath` de objeto e aplica-se três transformações para centralizá-la e dimensioná-lo para o tamanho da página. O caminho, em seguida, pode ser definido como o caminho de recorte:

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

Depois que o caminho de recorte é definido, o bitmap pode ser exibido e ele será recortado para os contornos de caracteres. Observe o uso do [ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) método `SKRect` que calcula um retângulo para preencher a página enquanto preserva a taxa de proporção.

O **efeito de caminho do texto** página converte um caractere único e comercial em um caminho para criar um efeito de caminho 1D. Um objeto de pintura com esse efeito de caminho, em seguida, é usado para traçar o contorno de uma versão maior do que mesmo caractere:

[![](text-paths-images/textpatheffect-small.png "Tripla captura de tela da página de texto caminho efeito")](text-paths-images/textpatheffect-large.png#lightbox "tripla captura de tela da página do efeito de caminho do texto")

Muito do trabalho na [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe ocorre no construtor e campos. Os dois `SKPaint` objetos definidos como campos são usados para duas finalidades diferentes: O primeiro (chamado `textPathPaint`) é usado para converter o e comercial com um `TextSize` de 50 para um caminho para o efeito de caminho 1D. A segunda (`textPaint`) é usado para exibir a versão maior do e comercial com o efeito desse caminho. Por esse motivo, o `Style` de pintura Este segundo objeto é definido como `Stroke`, mas o `StrokeWidth` propriedade não é definida porque essa propriedade não é necessária ao usar um efeito de caminho 1D:

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
        SKRect textPathPaintBounds = new SKRect();
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

O construtor usa primeiro a `textPathPaint` objeto para medir o e comercial com um `TextSize` de 50. Os negativos das coordenadas de centro do retângulo, em seguida, são passados para o `GetTextPath` método para converter o texto em um caminho. O caminho resultante tem a (0, 0) no Centro de caractere, o que é ideal para um efeito de caminho 1D de ponto.

Talvez você ache que o `SKPathEffect` criado no final do construtor de objeto pode ser definido como o `PathEffect` propriedade de `textPaint` em vez de salva como um campo. Mas esse não tornou deve funcionar muito bem porque ele distorcida os resultados do `MeasureText` chamar no `PaintSurface` manipulador:

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
        SKRect textBounds = new SKRect();
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

Que `MeasureText` chamada é usada para centralizar o caractere na página. Para evitar problemas, o `PathEffect` propriedade é definida como o objeto de pintura depois que o texto foi medido, mas antes de ser exibido.

## <a name="outlines-of-character-outlines"></a>Contornos dos contornos de caracteres

Normalmente o [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) método `SKPaint` converte um caminho para outro, aplicando as propriedades de pintura, mais notavelmente o traço largura e o caminho em vigor. Quando usada sem efeitos de caminho, `GetFillPath` efetivamente cria um caminho que descreve o outro caminho. Isso foi demonstrado na **toque, a estrutura de tópicos, o caminho** página na [ **efeitos de caminho** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) artigo.

Você também pode chamar `GetFillPath` no caminho retornado do `GetTextPath` , mas primeiro você pode não estar totalmente certo o que seria a aparência.

O **contornos de contorno de caracteres** página demonstra a técnica. O código relevante é na `PaintSurface` manipulador do [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

O construtor começa criando um `SKPaint` objeto nomeado `textPaint` com um `TextSize` propriedade com base no tamanho da página. Isso é convertido em um caminho usando o `GetTextPath` método. Os argumentos de coordenadas para `GetTextPath` centralizar com eficiência o caminho na tela:

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
        SKRect textBounds = new SKRect();
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

O `PaintSurface` manipulador, em seguida, cria um novo caminho chamado `outlinePath`. Isso se torna o caminho de destino na chamada para `GetFillPath`. O `StrokeWidth` propriedade das 25 causas `outlinePath` para descrever o contorno de um caminho de 25 pixels de largura traçar os caracteres de texto. Esse caminho é exibido em vermelho com uma largura de traço de 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Captura de tela da página contornos de contorno de caracteres tripla")](text-paths-images/characteroutlineoutlines-large.png#lightbox "tripla captura de tela da página contornos de contorno de caracteres")

Observe atentamente e você verá sobreposições em que a estrutura de tópicos do caminho faz uma curva fechada. Esses são os artefatos normais desse processo.

## <a name="text-along-a-path"></a>Texto ao longo de um caminho

Normalmente, o texto é exibido em uma linha de base horizontal. Texto pode ser girado para executar vertical ou diagonalmente, mas a linha de base ainda é uma linha reta.

Há vezes, no entanto, quando deseja que o texto a ser executado ao longo de uma curva. Essa é a finalidade do [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

O texto especificado no primeiro argumento é feito para ser executado ao longo do caminho especificado como o segundo argumento. Você pode começar o texto em um deslocamento do início do caminho com o `hOffset` argumento. Normalmente o caminho de forma a linha de base do texto: ascendentes de texto estão em um lado do caminho e os descendentes de texto são no outro. Mas você pode deslocar a linha de base do texto do caminho com o `vOffset` argumento.

Esse método não tem nenhum recurso para fornecer orientação sobre como o `TextSize` propriedade de `SKPaint` para tornar o texto dimensionado perfeitamente para executados desde o início do caminho até o fim. Às vezes, você pode descobrir esse tamanho de texto por conta própria. Outras vezes, você precisará usar funções de medição de caminho a ser descrito no próximo artigo na [ **informações de caminho e enumeração**](information.md).

O **Circular texto** programa quebra o texto em torno de um círculo. É fácil determinar a circunferência de um círculo, portanto, é fácil dimensionar o texto caiba exatamente. O `PaintSurface` manipulador do [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcula um raio de um círculo com base no tamanho da página. Torna-se de que o círculo `circularPath`:

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

O `TextSize` propriedade de `textPaint` , em seguida, é ajustado para que a largura do texto corresponda a circunferência do círculo:

[![](text-paths-images/circulartext-small.png "Captura de tela da página de texto Circular tripla")](text-paths-images/circulartext-large.png#lightbox "tripla captura de tela da página de texto Circular")

O texto em si foi escolhido para ser um pouco circular também: A palavra "círculo" é o assunto da sentença e o objeto de uma frase prepositional.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
