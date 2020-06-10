---
Título: "caminhos e texto em SkiaSharp" Descrição: "Este artigo explora a interseção de caminhos e texto do SkiaSharp e demonstra isso com o código de exemplo".
MS. Prod: xamarin MS. AssetID: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B MS. Technology: xamarin-skiasharp autor: davidbritch MS. Author: dabritch MS. Date: 08/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="paths-and-text-in-skiasharp"></a>Caminhos e texto em SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explore a interseção de caminhos e texto_

Em sistemas gráficos modernos, as fontes de texto são coleções de contornos de caracteres, geralmente definidas por curvas Bézier quadráticas. Consequentemente, muitos sistemas gráficos modernos incluem uma instalação para converter caracteres de texto em um caminho gráfico.

Você já viu que pode traçar os contornos de caracteres de texto, bem como preenchê-los. Isso permite que você exiba esses contornos de caractere com uma largura de traço específica e até mesmo um efeito de caminho, conforme descrito no artigo de [**efeitos de caminho**](effects.md) . Mas também é possível converter uma cadeia de caracteres em um `SKPath` objeto. Isso significa que os contornos de texto podem ser usados para recorte com técnicas que foram descritas no artigo [**recorte com caminhos e regiões**](clipping.md) .

Além de usar um efeito de caminho para traçar um contorno de caractere, você também pode criar efeitos de caminho que se baseiam em um caminho derivado de uma cadeia de caracteres, e você pode até mesmo combinar os dois efeitos:

![](text-paths-images/pathsandtextsample.png "Text Path Effect")

No artigo anterior sobre os [**efeitos de caminho**](effects.md), você viu como o [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) método de `SKPaint` pode obter um contorno de um caminho traçado. Você também pode usar esse método com caminhos derivados de contornos de caractere.

Por fim, este artigo demonstra outra interseção de caminhos e texto: o [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método de `SKCanvas` permite que você exiba uma cadeia de caracteres de texto para que a linha de base do texto siga um caminho curvo.

## <a name="text-to-path-conversion"></a>Conversão de texto em caminho

O [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) método de `SKPaint` converte uma cadeia de caracteres em um `SKPath` objeto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Os `x` `y` argumentos e indicam o ponto de partida da linha de base do lado esquerdo do texto. Eles desempenham a mesma função aqui como no `DrawText` método de `SKCanvas` . Dentro do caminho, a linha de base do lado esquerdo do texto terá as coordenadas (x, y).

O `GetTextPath` método é um exagero se você simplesmente deseja preencher ou traçar o caminho resultante. O `DrawText` método normal permite que você faça isso. O `GetTextPath` método é mais útil para outras tarefas que envolvem caminhos.

Uma dessas tarefas é o recorte. A página de **texto de recorte** cria um caminho de recorte com base nos contornos de caractere da palavra "código". Esse caminho é ampliado para o tamanho da página para recortar um bitmap que contém uma imagem do código-fonte do **texto de recorte** :

[![](text-paths-images/clippingtext-small.png "Triple screenshot of the Clipping Text page")](text-paths-images/clippingtext-large.png#lightbox "Triple screenshot of the Clipping Text page")

O [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) Construtor de classe carrega o bitmap que é armazenado como um recurso incorporado na pasta de **mídia** da solução:

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

O `PaintSurface` manipulador começa criando um `SKPaint` objeto adequado para texto. A `Typeface` propriedade é definida, bem como a `TextSize` , embora para esse aplicativo específico a `TextSize` propriedade seja puramente arbitrária. Observe também que não há nenhuma `Style` configuração.

As `TextSize` `Style` configurações de propriedade e não são necessárias porque esse `SKPaint` objeto é usado exclusivamente para a `GetTextPath` chamada usando a cadeia de texto "código". Em seguida, o manipulador mede o `SKPath` objeto resultante e aplica três transformações para centralizá-lo e dimensioná-lo para o tamanho da página. O caminho pode ser definido como o caminho de recorte:

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

Depois que o caminho de recorte for definido, o bitmap poderá ser exibido e ele será recortado para os contornos de caractere. Observe o uso do [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) método do `SKRect` que calcula um retângulo para preencher a página enquanto preserva a taxa de proporção.

A página de **efeito de caminho de texto** converte um único caractere de e comercial em um caminho para criar um efeito de caminho 1D. Um objeto de pintura com esse efeito de caminho é usado para traçar o contorno de uma versão maior do mesmo caractere:

[![](text-paths-images/textpatheffect-small.png "Triple screenshot of the Text Path Effect page")](text-paths-images/textpatheffect-large.png#lightbox "Triple screenshot of the Text Path Effect page")

Grande parte do trabalho na [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe ocorre nos campos e no construtor. Os dois `SKPaint` objetos definidos como campos são usados para duas finalidades diferentes: o primeiro (chamado `textPathPaint` ) é usado para converter o e comercial com um `TextSize` de 50 para um caminho para o efeito de caminho 1D. O segundo ( `textPaint` ) é usado para exibir a versão maior do e comercial com esse efeito de caminho. Por esse motivo, o `Style` desse segundo objeto Paint é definido como `Stroke` , mas a `StrokeWidth` propriedade não é definida porque essa propriedade não é necessária ao usar um efeito de caminho 1D:

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

O construtor usa primeiro o `textPathPaint` objeto para medir o e comercial com um `TextSize` de 50. Os negativos das coordenadas centrais desse retângulo são passados para o `GetTextPath` método para converter o texto em um caminho. O caminho resultante tem o ponto (0, 0) no centro do caractere, que é ideal para um efeito de caminho 1D.

Você pode imaginar que o `SKPathEffect` objeto criado no final do Construtor poderia ser definido como a `PathEffect` propriedade de `textPaint` em vez de ser salvo como um campo. Mas isso não funciona muito bem porque distorceva os resultados da `MeasureText` chamada no `PaintSurface` manipulador:

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

Essa `MeasureText` chamada é usada para centralizar o caractere na página. Para evitar problemas, a `PathEffect` propriedade é definida como o objeto de pintura depois que o texto é medido, mas antes de ser exibido.

## <a name="outlines-of-character-outlines"></a>Contornos de contornos de caracteres

Normalmente [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) , o método de `SKPaint` converte um caminho para outro aplicando Propriedades de pintura, mais notavelmente a largura do traço e o efeito do caminho. Quando usado sem efeitos de caminho, `GetFillPath` o cria efetivamente um caminho que descreve outro caminho. Isso foi demonstrado na página **tocar para descrever o caminho** no artigo de [**efeitos de caminho**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) .

Você também pode chamar `GetFillPath` no caminho retornado `GetTextPath` , mas, a princípio, você pode não estar totalmente certo de como seria essa aparência.

A página **contornos de caracteres da estrutura de tópicos** demonstra a técnica. Todo o código relevante está no `PaintSurface` manipulador da [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

O Construtor começa criando um `SKPaint` objeto chamado `textPaint` com uma `TextSize` propriedade com base no tamanho da página. Isso é convertido em um caminho usando o `GetTextPath` método. Os argumentos de coordenadas para `GetTextPath` centralizar efetivamente o caminho na tela:

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

`PaintSurface`Em seguida, o manipulador cria um novo caminho chamado `outlinePath` . Isso se torna o caminho de destino na chamada para `GetFillPath` . A `StrokeWidth` propriedade de 25 faz com que você `outlinePath` Descreva o contorno de um caminho de 25 pixels, traçando os caracteres de texto. Esse caminho é exibido em vermelho com uma largura de traço de 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Triple screenshot of the Character Outline Outlines page")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple screenshot of the Character Outline Outlines page")

Examine com mais detalhes e você verá sobreposições onde o esboço do caminho faz um canto nítido. Esses são artefatos normais desse processo.

## <a name="text-along-a-path"></a>Texto ao longo de um caminho

Normalmente, o texto é exibido em uma linha de base horizontal. O texto pode ser girado para ser executado vertical ou diagonalmente, mas a linha de base ainda é uma linha reta.

No entanto, há ocasiões em que você deseja que o texto seja executado ao longo de uma curva. Essa é a finalidade do [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) método de `SKCanvas` :

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

O texto especificado no primeiro argumento é criado para ser executado ao longo do caminho especificado como o segundo argumento. Você pode iniciar o texto em um deslocamento desde o início do caminho com o `hOffset` argumento. Normalmente, o caminho forma a linha de base do texto: os ascendentes de texto estão em um lado do caminho e os descendentes de texto estão no outro. Mas você pode deslocar a linha de base de texto do caminho com o `vOffset` argumento.

Esse método não tem nenhum recurso para fornecer orientação sobre como definir a `TextSize` propriedade de `SKPaint` para tornar o texto dimensionado perfeitamente para ser executado desde o início do caminho até o fim. Às vezes, você pode descobrir esse tamanho de texto por conta própria. Outras vezes, você precisará usar funções de medição de caminho a serem descritas no próximo artigo sobre [**informações de caminho e enumeração**](information.md).

O programa de **texto circular** encapsula o texto em um círculo. É fácil determinar a circunferência de um círculo, portanto, é fácil dimensionar o texto para se ajustar exatamente. O `PaintSurface` manipulador da [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcula um raio de um círculo com base no tamanho da página. Esse círculo se torna `circularPath` :

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

A `TextSize` propriedade de `textPaint` é então ajustada para que a largura do texto corresponda à circunferência do círculo:

[![](text-paths-images/circulartext-small.png "Triple screenshot of the Circular Text page")](text-paths-images/circulartext-large.png#lightbox "Triple screenshot of the Circular Text page")

O texto em si foi escolhido como um pouco circular também: a palavra "Circle" é o assunto da frase e o objeto de uma frase preposicional.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
