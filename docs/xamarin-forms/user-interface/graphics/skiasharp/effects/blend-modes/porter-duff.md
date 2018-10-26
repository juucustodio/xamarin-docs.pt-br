---
title: Modos de mesclagem Porter Duff
description: Use os modos do blend Porter Duff para compor cenas com base em imagens de origem e destino.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: ebd4db28b2c20bd2b9e1d93e03dd101ebc5da663
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130868"
---
# <a name="porter-duff-blend-modes"></a>Modos de mesclagem Porter Duff

Os modos do blend Porter Duff são nomeados depois Thomas Porter e Tom Duff, que desenvolveu uma álgebra de composição enquanto trabalhava Lucasfilm. Seu papel [ _imagens digitais de composição_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) foi publicado na edição de julho de 1984 _gráficos de computador_, páginas de 253 para 259. Esses modos de mesclagem são essenciais para a composição, que é montando diversas imagens em uma cena de composição:

![Exemplo de Porter Duff](porter-duff-images/PorterDuffSample.png "Duff Porter exemplo")

## <a name="porter-duff-concepts"></a>Conceitos de Porter Duff

Suponha que um retângulo brownish ocupa a esquerda e superior dois terços de sua superfície de exibição:

![Destino de Porter Duff](porter-duff-images/PorterDuffDst.png "Duff Porter destino")

Essa área é chamada a _destino_ ou, às vezes, o _plano de fundo_ ou _pano de fundo_.

Você deseja desenhar o retângulo a seguir, que é o mesmo tamanho do destino. O retângulo está transparente, exceto para uma área azulada que ocupa à direita e abaixo dois terços:

![Origem de Porter Duff](porter-duff-images/PorterDuffSrc.png "Duff Porter origem")

Isso é chamado de _fonte_ ou, às vezes, o _em primeiro plano_.

Quando você exibe o código-fonte no destino, aqui está o que você espera:

![Fonte a Duff Porter](porter-duff-images/PorterDuffSrcOver.png "fonte a Porter Duff")

Os pixels transparentes da fonte de permitir que o plano de fundo apareça, enquanto os pixels de origem azulada obscurecer o plano de fundo. Esse é o caso normal e ele é chamado no SkiaSharp como `SKBlendMode.SrcOver`. Que valor é a configuração padrão do `BlendMode` propriedade quando um `SKPaint` objeto é instanciado pela primeira vez.

No entanto, é possível especificar um modo de mesclagem diferente para um efeito diferente. Se você especificar `SKBlendMode.DstOver`, em seguida, na área de interseção entre a origem e destino, o destino é exibido em vez do código-fonte:

![Destino Porter Duff pela](porter-duff-images/PorterDuffDstOver.png "Duff Porter destino sobre")

O `SKBlendMode.DstIn` modo blend exibe apenas a área em que o código-fonte e o destino se cruzam usando a cor de destino:

![Destino de Porter Duff na](porter-duff-images/PorterDuffDstIn.png "Duff Porter destino na")

O modo de mesclagem do `SKBlendMode.Xor` (OR exclusivo) faz com que nada aparecer onde sobrepõem as duas áreas:

![Duff Porter exclusivo ou](porter-duff-images/PorterDuffXor.png "Duff Porter exclusivo ou")

Os retângulos coloridos de origem e destino efetivamente dividem a superfície de exibição em quatro áreas exclusivas que podem ser coloridas de várias maneiras correspondente para a presença dos retângulos de origem e destino:

![Duff Porter](porter-duff-images/PorterDuff.png "Duff Porter")

Os retângulos do canto superior direito e inferior esquerdo estão sempre em branco porque o destino e origem são transparentes nessas áreas. A cor de destino ocupa a área do canto superior esquerdo, para que a área ou pode ser colorida com a cor de destino ou não. Da mesma forma, a cor de fonte ocupa a área de inferior direito, para que a área pode ser colorida com a cor de origem ou de forma alguma. A interseção do destino e origem no meio pode ser colorida com a cor de destino, a cor da fonte, ou não de forma alguma.

O número total de combinações é 2 (para o canto superior esquerdo) vezes 2 (para o canto inferior direito) vezes 3 (para o centro) ou 12. Esses são os modos de composição básicos Porter Duff 12.

No final da _imagens digitais de composição_ (página 256), Porter e Duff adicionar um modo de 13 chamado _plus_ (correspondente a SkiaSharp `SKBlendMode.Plus` membro e o W3C _mais leve_  modo (que não é deve ser confundido com o W3C _clarear_ modo.) Isso `Plus` modo adiciona as cores de origem e destino, um processo que será descrito mais detalhadamente em breve.

Skia adiciona um modo 14 chamado `Modulate` que é muito semelhante ao `Plus` , exceto que as cores de origem e destino são multiplicadas. Ele pode ser tratado como um modo de mesclagem Porter Duff adicional.

Aqui estão os modos de Porter Duff 14 conforme definido em SkiaSharp. A tabela mostra como cor cada uma das três áreas não em branco no diagrama acima:

| Modo       | Destino | Interseção | Origem |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Origem       | X      |
| `Dst`      | X           | Destino  |        |
| `SrcOver`  | X           | Origem       | X      |
| `DstOver`  | X           | Destino  | X      |
| `SrcIn`    |             | Origem       |        |
| `DstIn`    |             | Destino  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Origem       |        |
| `DstATop`  |             | Destino  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Produto      |        | 

Esses modos de mesclagem são simétricos. A origem e destino podem ser trocadas e todos os modos ainda estão disponíveis.

A convenção de nomenclatura dos modos segue algumas regras simples:

- **Src** ou **Dst** por si só, significa que somente os pixels de origem ou destino estão visíveis.
- O **sobre** sufixo indica o que é visível na interseção. A origem ou o destino é desenhado "ou" outro.
- O **em** sufixo significa que apenas a interseção é colorida. A saída é restrita a apenas a parte de origem ou destino é "in" a outra.
- O **Out** sufixo significa que a interseção não é colorida. A saída é apenas a parte de origem ou destino é "out" da interseção.
- O **acima** sufixo é a união entre **na** e **Out**. Ele inclui a área em que a origem ou destino é "acima" dos outros.

Observe a diferença com o `Plus` e `Modulate` modos. Esses modos estão executando um tipo diferente de cálculo nos pixels de origem e de destino. Eles são descritos mais detalhadamente em breve.

O **Duff Porter grade** página mostra todos os 14 modos em uma única tela, na forma de uma grade. Cada modo é uma instância separada do `SKCanvasView`. Por esse motivo, uma classe é derivada de `SKCanvasView` chamado `PorterDuffCanvasView`. O construtor estático cria dois bitmaps do mesmo tamanho, um com um retângulo brownish em sua área do canto superior esquerdo e outro com um retângulo azulado:

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

O construtor de instância tem um parâmetro de tipo `SKBlendMode`. Ele salva esse parâmetro em um campo. 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

O `OnPaintSurface` substituição desenha dois bitmaps. A primeira é desenhada normalmente:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

A segunda é desenhada com uma `SKPaint` objeto no qual o `BlendMode` propriedade foi definida para o argumento do construtor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

O restante do `OnPaintSurface` substituição desenha um retângulo em torno de bitmap para indicar a seus tamanhos.

O `PorterDuffGridPage` classe cria quatorze instâncias `PorterDurffCanvasView`, uma para cada membro do `blendModes` matriz. A ordem dos `SKBlendModes` membros da matriz é um pouco diferente da tabela para posicionar os modos semelhantes adjacentes uns aos outros. As 14 instâncias do `PorterDuffCanvasView` são organizadas juntamente com os rótulos em um `Grid`:

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

Aqui está o resultado:

[![Grade de Porter Duff](porter-duff-images/PorterDuffGrid.png "Duff Porter grade")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Você vai querer convencido de que a transparência é crucial para o funcionamento adequado dos modos Porter Duff blend. O `PorterDuffCanvasView` classe contém um total de três chamadas para o `Canvas.Clear` método. Todos eles usam o método sem parâmetros, o que define todos os pixels transparentes:

```csharp
canvas.Clear();
```

Tente alterar qualquer uma dessas chamadas para que os pixels são definidos para branco opaco:

```csharp
canvas.Clear(SKColors.White);
```

Após essa alteração, alguns dos modos de mesclagem parecerá funcionar, mas outros não. Se você definir o plano de fundo do bitmap de origem em branco, então o `SrcOver` modo não funciona porque não há nenhum pixels transparentes no bitmap para permitir que o destino apareçam através de código-fonte. Se você definir o plano de fundo de bitmap de destino ou a tela para branco, em seguida, `DstOver` não funciona porque o destino não tem os pixels transparentes.

Pode haver uma tentação de substituir os bitmaps na **grade Porter Duff** página com mais simples `DrawRect` chamadas. Isso funciona para o retângulo de destino, mas não para o retângulo de origem. O retângulo de origem deve abranger mais do que apenas a área azulada coloridas. O retângulo de origem deve incluir uma área transparente que corresponde à área colorida do destino. Somente, em seguida, eles serão blend modos de trabalho.

## <a name="using-mattes-with-porter-duff"></a>Usando foscos com Porter Duff

O **composição de parede de tijolos** página mostra um exemplo de uma tarefa de composição clássico: uma imagem precisa ser montada a partir de várias partes, incluindo um bitmap com um plano de fundo que precisa ser eliminada. Aqui está o **SeatedMonkey.jpg** bitmap com o plano de fundo de um problema:

![Encaixado Monkey](porter-duff-images/SeatedMonkey.jpg "encaixado Monkey")

Em preparação para a composição, um correspondente _fosco_ foi criado, que é outro bitmap é preto, em que você deseja que a imagem apareça e transparente, caso contrário. Esse arquivo é denominado **SeatedMonkeyMatte.png** e está entre os recursos na **mídia** pasta no [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo :

![Encaixado Monkey fosco](porter-duff-images/SeatedMonkeyMatte.png "encaixado Monkey fosco")

Isso é _não_ fosco um forma especializada criado. De maneira ideal, fosco deve incluir pixels parcialmente transparentes em torno da borda dos pixels pretos, e esse fosco não.

O arquivo XAML para o **composição de parede de tijolos** página instancia um `SKCanvasView` e um `Button` que orienta o usuário pelo processo de compor a imagem final:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega dois bitmaps em que ele precisa e lida com o `Clicked` eventos do `Button`. Para cada `Button` clicar, o `step` campo é incrementado e uma nova `Text` propriedade é definida para o `Button`. Quando `step` atinge 5, ela será redefinida como 0:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

Quando o programa é executado pela primeira vez, nada é visível, exceto o `Button`:

[![Composição de parede de tijolos etapa 0](porter-duff-images/BrickWallCompositing0.png "etapa de composição de parede de tijolos 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Pressionar o `Button` faz com que uma vez `step` incrementar como 1 e o `PaintSurface` manipulador agora exibe **SeatedMonkey.jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

Não há nenhum `SKPaint` objeto e, portanto, nenhum modo de mesclagem. O bitmap aparece na parte inferior da tela:

[![Composição de parede de tijolos etapa 1](porter-duff-images/BrickWallCompositing1.png "parede de tijolos composição etapa 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Pressione a `Button` novamente e `step` incrementos para 2. Esta é a etapa crucial de exibir o **SeatedMonkeyMatte.png** arquivo:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

O modo de mesclagem é `SKBlendMode.DstIn`, o que significa que o destino será preservado nas áreas correspondente às áreas não transparente da origem. O restante do retângulo de destino correspondente no bitmap original se torna transparente:

[![Etapa de composição de parede de tijolos 2](porter-duff-images/BrickWallCompositing2.png "parede de tijolos composição etapa 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

O plano de fundo foi removido. 

A próxima etapa é desenhar um retângulo que se parece com uma calçada que monkey está situada sobre. A aparência desse calçada baseia-se em uma composição de dois sombreadores: um sombreador de cor sólida e um sombreador de ruído de Perlin:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

Porque este calçada deve passar por trás do monkey, o modo de mesclagem é `DstOver`. O destino será exibida somente quando a tela de fundo é transparente:

[![Composição de parede de tijolos etapa 3](porter-duff-images/BrickWallCompositing3.png "parede de tijolos composição etapa 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

A etapa final é adicionar uma parede de tijolos. O programa usa o bloco de bitmap de parede de tijolos disponível como a propriedade estática `BrickWallTile` no `AlgorithmicBrickWallPage` classe. A transformação de conversão é adicionada para o `SKShader.CreateBitmap` chamada mudar os blocos para que a linha inferior é um bloco completo:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Para sua conveniência, o `DrawRect` chamada exibe esse sombreador sobre a tela inteira, mas o `DstOver` modo limita a saída para apenas a área da tela ainda transparente:

[![Composição de parede de tijolos etapa 4](porter-duff-images/BrickWallCompositing4.png "parede de tijolos composição etapa 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, existem outras maneiras para compor essa cena. Backup começando em segundo plano e está em andamento para o primeiro plano possa ser criado. Mas, usar os modos de mesclagem oferece mais flexibilidade. Em particular, o uso de fosco permite que o plano de fundo de um bitmap a ser excluído da cena composta.

Como você aprendeu neste artigo [recorte com caminhos de regiões](../../curves/clipping.md), o `SKCanvas` classe define três tipos de recorte, correspondente a [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*), [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*), e [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*) métodos. Os modos do blend Porter Duff adicionar outro tipo de recorte, o que permite restringir uma imagem para qualquer coisa que você possa desenhar, incluindo os bitmaps. O fosco usado na **composição de parede de tijolos** essencialmente define uma área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Transições e transparência de gradiente

Os exemplos de Porter-Duff blend modos mostrados anteriormente neste artigo tem todos os envolvidos imagens que consistia em pixels opacos e pixels transparentes, mas não parcialmente transparentes pixels. As funções do modo de mesclagem são definidas para esses pixels também. A tabela a seguir é uma definição mais formal de Porter Duff dos modos de mesclagem que usa a notação encontrada no Skia [ **referência SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Porque **SkBlendMode referência** é uma referência Skia, sintaxe de C++ é usada.)

Conceitualmente, os componentes vermelhos, verdes, azuis e alfabéticos de cada pixel são convertidos de bytes para números de ponto flutuante no intervalo de 0 a 1. Para o canal alfa, 0 é completamente transparente e 1 é completamente opaco

A notação na tabela a seguir usa as seguintes abreviações:

- **Acelerador de desenvolvimento** é o canal alfa do destino
- **Controlador de domínio** é o cor RGB de destino
- **SA** é o canal alfa de origem
- **SC** é a cor RGB de origem

As cores RGB já forem multiplicadas pelo valor alfa. Por exemplo, se **Sc** representa vermelho puro, mas **Sa** é 0x80, em seguida, é a cor RGB **(0x80, 0, 0)**. Se **Sa** é 0 e, em seguida, todos os componentes RGB também são zero.

O resultado é mostrado entre colchetes com o canal alfa e a cor RGB separados por vírgula: **[cor alfa,]**. Para a cor, o cálculo é executado separadamente para os componentes vermelhos, verdes e azuis:

| Modo       | Operação |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa-Sc]  |
| `Dst`      | [Acelerador de desenvolvimento, controlador de domínio]  |
| `SrcOver`  | [Sa + Da· (1 – Sa), Sc + Dc· (1 – Sa) | 
| `DstOver`  | [Acelerador de desenvolvimento + Sa· (1 – Da), controlador de domínio + Sc· (1 – Da) |
| `SrcIn`    | [Sa· Acelerador de desenvolvimento, Sc· Acelerador de desenvolvimento] |
| `DstIn`    | [Da· SA, Dc· SA] |
| `SrcOut`   | [Sa· (1 – Da), Sc· (1 – Da]) |
| `DstOut`   | [Da· (1 – Sa), Dc· (1 – Sa)] |
| `SrcATop`  | [Acelerador de desenvolvimento, Sc· Acelerador de desenvolvimento + Dc· (1 – Sa)] |
| `DstATop`  | [Sa, Dc· SA + Sc· (1 – Da]) |
| `Xor`      | [Sa + acelerador de desenvolvimento – 2· SA· Acelerador de desenvolvimento, Sc· (1 – Da) + Dc· (1 – Sa)] |
| `Plus`     | [Sa + acelerador de desenvolvimento, Sc + Dc] |
| `Modulate` | [Sa· Acelerador de desenvolvimento, Sc· O controlador de domínio] | 

Essas operações são mais fáceis de analisar quando **da{1:0000** e **Sa** são 0 ou 1. Por exemplo, para o padrão `SrcOver` modo, se **Sa** for 0, então **Sc** é também 0 e o resultado é **[acelerador de desenvolvimento, o controlador de domínio]**, o alfa do destino e a cor. Se **Sa** é 1, o resultado será **[Sa, Sc]**, o alfa do código-fonte e cor, ou **[1, Sc]**.

O `Plus` e `Modulate` modos são um pouco diferentes dos outros em que novas cores podem resultar da combinação de origem e o destino. O `Plus` modo pode ser interpretado com componentes de byte ou componentes de ponto flutuantes. No **grade Porter Duff** página mostrada anteriormente, a cor de destino é **(0xC0, 0x80, 0x00)** e a cor da fonte é **(0x00, 0x80, 0xC0)**. Cada par de componentes é adicionada, mas a soma é fixada em 0xFF. O resultado é a cor **(0xC0, 0xFF, 0xC0)**. Que é a cor que mostra a interseção.

Para o `Modulate` modo, os valores RGB devem ser convertidos para ponto flutuante. É a cor de destino **(0,75, 0,5, 0)** e a fonte for **(0, 0,5, 0,75)**. Os componentes RGB são cada multiplicados juntos e o resultado será **(0, 0,25, 0)**. Que é a cor que mostra a interseção na **Duff Porter grade** página nesse modo.

O **Duff Porter transparência** página permite que você examine como os modos do blend Porter Duff operam em objetos gráficos que são parcialmente transparentes. O arquivo XAML inclui um `Picker` com os modos de Porter Duff:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

O arquivo code-behind preenche dois retângulos do mesmo tamanho usando um gradiente linear. O gradiente de destino é do canto superior direito para o canto inferior esquerdo. Ele é brownish no canto superior direito, mas, em seguida, em direção ao centro começa fica transparente e é transparente no canto inferior esquerdo. 

O retângulo de origem tem uma gradação da parte superior esquerda no canto inferior direito. O canto superior esquerdo é azulado mas novamente fica transparente e é transparente no canto inferior direito. 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Este programa demonstra que os modos do blend Porter Duff podem ser usados com objetos gráficos que não seja de bitmaps. No entanto, a origem deve incluir uma área transparente. Esse é o caso aqui porque o gradiente preenche o retângulo, mas parte do gradiente é transparente.

Aqui estão três exemplos:

[![Transparência de Porter Duff](porter-duff-images/PorterDuffTransparency.png "Duff Porter transparência")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

A configuração da origem e destino é muito semelhante aos diagramas mostrados na página 255 do original Porter-Duff [ _imagens digitais de composição_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) papel, mas esta página demonstra que o modos de mesclagem são bem comportados para áreas de transparência parcial.

Você pode usar gradientes transparentes para alguns efeitos diferentes. Uma possibilidade é mascarada, que é semelhante à técnica mostrada na [ **gradientes radiais para o mascaramento** ](../shaders/circular-gradients.md#radial-gradients-for-masking) seção o **página de gradientes circular SkiaSharp**. Quase o **máscara de composição** página é semelhante a esse programa anterior. Ele carrega um recurso de bitmap e determina um retângulo no qual para exibi-lo. Um gradiente radial é criado com base em um centro predeterminado e radius:

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

A diferença com este programa é que o gradiente começa com preta no centro e termina com transparência. Ele é exibido o bitmap com um modo de mesclagem do `DstIn`, que mostra o destino apenas nas áreas da origem que não são transparentes.

Após o `DrawRect` chamada, toda a superfície da tela é transparente, exceto para o círculo definido por um gradiente radial. É feita uma chamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas as áreas transparentes da tela são coloridas em rosas:

[![Máscara de composição](porter-duff-images/CompositingMask.png "máscara de composição")](porter-duff-images/CompositingMask-Large.png#lightbox)

Você também pode usar modos de Porter Duff e gradientes parcialmente transparentes para transições de uma imagem para outra. O **transições de gradiente** página inclui uma `Slider` para indicar um nível de progresso na transição de 0 a 1 e um `Picker` para escolher o tipo de transição desejado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega dois recursos de bitmap para demonstrar a transição. Essas são as mesmas dois imagens usadas na **Bitmap se dissolvem** página no início deste artigo. O código também define uma enumeração com três membros que correspondem aos três tipos de gradientes &mdash; linear, radial e limpeza. Esses valores são carregados no `Picker`:

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

O arquivo code-behind cria três `SKPaint` objetos. O `paint0` objeto não usa o modo de mesclagem. Esse objeto de pintura é usado para desenhar um retângulo com um gradiente que vai do preto transparente conforme indicado no `colors` matriz. O `positions` matriz se baseia na posição do `Slider`, mas ajustado um pouco. Se o `Slider` está no seu mínimo ou máximo, o `progress` valores são 0 ou 1, e um dos dois bitmaps deve estar totalmente visível. O `positions` matriz deve ser definida adequadamente para obter esses valores.

Se o `progress` valor é 0, então o `positions` matriz contém os valores-0.1 e 0. Caso contrário, o SkiaSharp ajustará esse valor primeiro para ser igual a 0, o que significa que o gradiente é preto apenas em 0 e transparente. Quando `progress` é 0,5 e, em seguida, a matriz contém os valores 0,45 e 0,55. O gradiente é preto de 0 para 0,45, e em seguida, faz a transição para transparente e é totalmente transparente do 0,55 como 1. Quando `progress` for 1, o `positions` matriz é 1 e 1.1, o que significa que o gradiente é preto de 0 a 1.

O `colors` e `position` matrizes são usadas nos três métodos de `SKShader` que criar um gradiente. Somente um desses sombreadores é criado com base no `Picker` seleção: 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

Esse gradiente é exibido no retângulo sem o modo de mesclagem. Depois disso `DrawRect` chamada, a tela contém simplesmente um gradiente de preto para transparente. Aumenta a quantidade de preto com superior `Slider` valores.

No finais quatro instruções da `PaintSurface` manipulador, dois bitmaps são exibidos. O `SrcOut` modo blend significa que o bitmap primeiro é exibido somente nas áreas da tela de fundo transparentes. O `DstOver` modo para o bitmap de segundo significa que o bitmap de segundo é exibido apenas nessas áreas em que o bitmap a primeira não é exibido.

As capturas de tela a seguir mostram os três tipos de transições diferentes, cada um na marca de 50%:

[![Transições de gradiente](porter-duff-images/GradientTransitions.png "transições de gradiente")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)