---
title: Modos de mesclagem Porter Duff
description: Use os modos do blend Porter Duff para compor cenas com base em imagens de origem e destino.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916400"
---
# <a name="porter-duff-blend-modes"></a>Modos de mesclagem Porter Duff

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Os modos do blend Porter Duff são nomeados depois Thomas Porter e Tom Duff, que desenvolveu uma álgebra de composição enquanto trabalhava Lucasfilm. Suas [_imagens digitais de composição_](https://graphics.pixar.com/library/Compositing/paper.pdf) de papel foram publicadas na edição de julho de 1984 de _gráficos de computador_, páginas 253 a 259. Esses modos de mesclagem são essenciais para a composição, que é montando diversas imagens em uma cena de composição:

![Exemplo de carregador-Duff](porter-duff-images/PorterDuffSample.png "Exemplo de carregador-Duff")

## <a name="porter-duff-concepts"></a>Conceitos de Porter Duff

Suponha que um retângulo brownish ocupa a esquerda e superior dois terços de sua superfície de exibição:

![Destino carregador-Duff](porter-duff-images/PorterDuffDst.png "Destino carregador-Duff")

Essa área é chamada de _destino_ ou, às vezes, do _plano de fundo_ ou do _pano_.

Você deseja desenhar o retângulo a seguir, que é o mesmo tamanho do destino. O retângulo está transparente, exceto para uma área azulada que ocupa à direita e abaixo dois terços:

![Origem do carregador-Duff](porter-duff-images/PorterDuffSrc.png "Origem do carregador-Duff")

Isso é chamado de _origem_ ou, às vezes, do _primeiro plano_.

Quando você exibe o código-fonte no destino, aqui está o que você espera:

![Carregador-Duff de origem](porter-duff-images/PorterDuffSrcOver.png "Carregador-Duff de origem")

Os pixels transparentes da fonte de permitir que o plano de fundo apareça, enquanto os pixels de origem azulada obscurecer o plano de fundo. Esse é o caso normal e é referenciado em SkiaSharp como `SKBlendMode.SrcOver`. Esse valor é a configuração padrão da propriedade `BlendMode` quando um objeto `SKPaint` é instanciado pela primeira vez.

No entanto, é possível especificar um modo de mesclagem diferente para um efeito diferente. Se você especificar `SKBlendMode.DstOver`, na área em que a origem e o destino se interseccionam, o destino aparecerá em vez da fonte:

![Carregador-Duff de destino](porter-duff-images/PorterDuffDstOver.png "Carregador-Duff de destino")

O modo de mesclagem `SKBlendMode.DstIn` exibe apenas a área onde o destino e a origem fazem interseção usando a cor de destino:

![Carregador-Duff de destino](porter-duff-images/PorterDuffDstIn.png "Carregador-Duff de destino")

O modo de mesclagem de `SKBlendMode.Xor` (Exclusive ou) não faz nada para aparecer onde as duas áreas se sobrepõem:

![Carregador-Duff exclusivo ou](porter-duff-images/PorterDuffXor.png "Carregador-Duff exclusivo ou")

Os retângulos coloridos de origem e destino efetivamente dividem a superfície de exibição em quatro áreas exclusivas que podem ser coloridas de várias maneiras correspondente para a presença dos retângulos de origem e destino:

![Carregador-Duff](porter-duff-images/PorterDuff.png "Porter Duff")

Os retângulos do canto superior direito e inferior esquerdo estão sempre em branco porque o destino e origem são transparentes nessas áreas. A cor de destino ocupa a área do canto superior esquerdo, para que a área ou pode ser colorida com a cor de destino ou não. Da mesma forma, a cor de fonte ocupa a área de inferior direito, para que a área pode ser colorida com a cor de origem ou de forma alguma. A interseção do destino e origem no meio pode ser colorida com a cor de destino, a cor da fonte, ou não de forma alguma.

O número total de combinações é 2 (para o canto superior esquerdo) vezes 2 (para o canto inferior direito) vezes 3 (para o centro) ou 12. Esses são os modos de composição básicos Porter Duff 12.

No final da _composição de imagens digitais_ (página 256), carregador e Duff adicionam um modo 13 chamado _Plus_ (correspondente ao membro SkiaSharp `SKBlendMode.Plus` e o modo _mais claro_ do W3C (que não deve ser confundido com o modo de _clareamento_ do W3C). Esse modo de `Plus` adiciona as cores de origem e destino, um processo que será descrito em mais detalhes em breve.

Skia adiciona um modo 14 chamado `Modulate` que é muito semelhante a `Plus`, exceto que as cores de destino e origem são multiplicadas. Ele pode ser tratado como um modo de mesclagem Porter Duff adicional.

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

- **Src** ou **DST** por si só significa que apenas os pixels de origem ou de destino estão visíveis.
- O sufixo **over** indica o que está visível na interseção. A origem ou o destino é desenhado "ou" outro.
- O sufixo **in** significa que apenas a interseção é colorida. A saída é restrita a apenas a parte de origem ou destino é "in" a outra.
- O sufixo **out** significa que a interseção não é colorida. A saída é apenas a parte de origem ou destino é "out" da interseção.
- O sufixo **acima** é a União de **entrada** e **saída**. Ele inclui a área em que a origem ou o destino está "acima" do outro.

Observe a diferença com os modos de `Plus` e `Modulate`. Esses modos estão executando um tipo diferente de cálculo nos pixels de origem e de destino. Eles são descritos mais detalhadamente em breve.

A página de **grade carregador-Duff** mostra todos os 14 modos em uma tela na forma de uma grade. Cada modo é uma instância separada do `SKCanvasView`. Por esse motivo, uma classe é derivada de `SKCanvasView` chamada `PorterDuffCanvasView`. O construtor estático cria dois bitmaps do mesmo tamanho, um com um retângulo brownish em sua área do canto superior esquerdo e outro com um retângulo azulado:

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

O construtor de instância tem um parâmetro do tipo `SKBlendMode`. Ele salva esse parâmetro em um campo. 

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

A substituição de `OnPaintSurface` desenha os dois bitmaps. A primeira é desenhada normalmente:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

O segundo é desenhado com um objeto `SKPaint` em que a propriedade `BlendMode` foi definida como o argumento do Construtor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

O restante da substituição de `OnPaintSurface` desenha um retângulo em volta do bitmap para indicar seus tamanhos.

A classe `PorterDuffGridPage` cria quatorze instâncias de `PorterDurffCanvasView`, uma para cada membro da matriz de `blendModes`. A ordem dos membros do `SKBlendModes` na matriz é um pouco diferente da tabela para posicionar modos semelhantes adjacentes entre si. As 14 instâncias de `PorterDuffCanvasView` são organizadas junto com rótulos em uma `Grid`:

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

Eis o resultado:

[![Grade carregador-Duff](porter-duff-images/PorterDuffGrid.png "Grade carregador-Duff")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Você vai querer convencido de que a transparência é crucial para o funcionamento adequado dos modos Porter Duff blend. A classe `PorterDuffCanvasView` contém um total de três chamadas para o método `Canvas.Clear`. Todos eles usam o método sem parâmetros, o que define todos os pixels transparentes:

```csharp
canvas.Clear();
```

Tente alterar qualquer uma dessas chamadas para que os pixels são definidos para branco opaco:

```csharp
canvas.Clear(SKColors.White);
```

Após essa alteração, alguns dos modos de mesclagem parecerá funcionar, mas outros não. Se você definir o plano de fundo do bitmap de origem como branco, o modo de `SrcOver` não funcionará porque não há nenhum pixel transparente no bitmap de origem para permitir que o destino seja mostrado. Se você definir o plano de fundo do bitmap de destino ou da tela como branco, `DstOver` não funcionará porque o destino não tem nenhum pixel transparente.

Pode haver uma tentação de substituir os bitmaps na página de **grade carregador-Duff** por chamadas de `DrawRect` mais simples. Isso funciona para o retângulo de destino, mas não para o retângulo de origem. O retângulo de origem deve abranger mais do que apenas a área azulada coloridas. O retângulo de origem deve incluir uma área transparente que corresponde à área colorida do destino. Somente, em seguida, eles serão blend modos de trabalho.

## <a name="using-mattes-with-porter-duff"></a>Usando foscos com Porter Duff

A página de **composição do Brick-Wall** mostra um exemplo de uma tarefa de composição clássica: uma imagem precisa ser montada de várias partes, incluindo um bitmap com um plano de fundo que precisa ser eliminado. Aqui está o bitmap **SeatedMonkey. jpg** com o plano de fundo problemático:

![Macaco sentado](porter-duff-images/SeatedMonkey.jpg "Macaco sentado")

Na preparação para a composição, um _fosco_ correspondente foi criado, que é outro bitmap que é preto onde você deseja que a imagem seja exibida e transparente caso contrário. Esse arquivo é denominado **SeatedMonkeyMatte. png** e está entre os recursos na pasta de **mídia** no exemplo de [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Macaco encaixado fosco](porter-duff-images/SeatedMonkeyMatte.png "Macaco encaixado fosco")

Esse _não_ é um fosco criado de especialista. De maneira ideal, fosco deve incluir pixels parcialmente transparentes em torno da borda dos pixels pretos, e esse fosco não.

O arquivo XAML para a página de **composição do Brick-Wall** instancia um `SKCanvasView` e um `Button` que orienta o usuário pelo processo de composição da imagem final:

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

O arquivo code-behind carrega os dois bitmaps necessários e manipula o evento `Clicked` do `Button`. Para cada `Button` clique, o campo `step` é incrementado e uma nova propriedade `Text` é definida para o `Button`. Quando `step` chega a 5, ele é definido de volta como 0:

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

Quando o programa é executado pela primeira vez, nada fica visível, exceto o `Button`:

[![Etapa 0 da composição de mural de tijolo](porter-duff-images/BrickWallCompositing0.png "Etapa 0 da composição de mural de tijolo")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Pressionar a `Button` uma vez faz com que `step` seja incrementado para 1, e o manipulador de `PaintSurface` agora exibe **SeatedMonkey. jpg**:

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

Não há nenhum objeto `SKPaint` e, portanto, não há nenhum modo de mesclagem. O bitmap aparece na parte inferior da tela:

[![Etapa 1 da composição de parede de Brick](porter-duff-images/BrickWallCompositing1.png "Etapa 1 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Pressione a `Button` novamente e `step` incrementos para 2. Essa é a etapa crucial de exibir o arquivo **SeatedMonkeyMatte. png** :

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

O modo de mesclagem é `SKBlendMode.DstIn`, o que significa que o destino será preservado em áreas correspondentes às áreas não transparentes da origem. O restante do retângulo de destino correspondente no bitmap original se torna transparente:

[![Etapa 2 da composição de parede de Brick](porter-duff-images/BrickWallCompositing2.png "Etapa 2 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

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

Como esse calçada deve ficar atrás do macaco, o modo de mesclagem é `DstOver`. O destino será exibida somente quando a tela de fundo é transparente:

[![Etapa 3 da composição de parede de Brick](porter-duff-images/BrickWallCompositing3.png "Etapa 3 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

A etapa final é adicionar uma parede de tijolos. O programa usa o bloco de bitmap de parede de tijolo disponível como a propriedade estática `BrickWallTile` na classe `AlgorithmicBrickWallPage`. Uma transformação de tradução é adicionada à chamada de `SKShader.CreateBitmap` para deslocar os blocos para que a linha inferior seja um bloco completo:

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

Para sua conveniência, a chamada de `DrawRect` exibe esse sombreador sobre toda a tela, mas o modo de `DstOver` limita a saída somente à área da tela que ainda é transparente:

[![Etapa 4 da composição de parede de Brick](porter-duff-images/BrickWallCompositing4.png "Etapa 4 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, existem outras maneiras para compor essa cena. Backup começando em segundo plano e está em andamento para o primeiro plano possa ser criado. Mas, usar os modos de mesclagem oferece mais flexibilidade. Em particular, o uso de fosco permite que o plano de fundo de um bitmap a ser excluído da cena composta.

Como você aprendeu no artigo [recorte com caminhos e regiões](../../curves/clipping.md), a classe `SKCanvas` define três tipos de recorte, correspondentes aos métodos [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*), [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*)e [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . Os modos do blend Porter Duff adicionar outro tipo de recorte, o que permite restringir uma imagem para qualquer coisa que você possa desenhar, incluindo os bitmaps. O matte usado na **composição de mural de tijolo** basicamente define uma área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Transições e transparência de gradiente

Os exemplos de Porter-Duff blend modos mostrados anteriormente neste artigo tem todos os envolvidos imagens que consistia em pixels opacos e pixels transparentes, mas não parcialmente transparentes pixels. As funções do modo de mesclagem são definidas para esses pixels também. A tabela a seguir é uma definição mais formal dos modos de mesclagem carregador-Duff que usa a notação encontrada na referência do skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Como **referência de SkBlendMode** é uma referência de C++ skia, a sintaxe é usada.)

Conceitualmente, os componentes vermelhos, verdes, azuis e alfabéticos de cada pixel são convertidos de bytes para números de ponto flutuante no intervalo de 0 a 1. Para o canal alfa, 0 é completamente transparente e 1 é completamente opaco

A notação na tabela a seguir usa as seguintes abreviações:

- O **da** é o canal alfa de destino
- O **DC** é a cor RGB de destino
- **SA** é o canal alfa de origem
- **SC** é a cor RGB de origem

As cores RGB já forem multiplicadas pelo valor alfa. Por exemplo, se **SC** representar vermelho puro, mas **SA** for 0X80, a cor RGB será **(0x80, 0, 0)** . Se **SA** for 0, todos os componentes RGB também serão zero.

O resultado é mostrado entre colchetes com o canal alfa e a cor RGB, separados por uma vírgula: **[alfa, cor]** . Para a cor, o cálculo é executado separadamente para os componentes vermelhos, verdes e azuis:

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

Essas operações são mais fáceis de analisar quando o **da** e **SA** são 0 ou 1. Por exemplo, para o modo de `SrcOver` padrão, se **SA** for 0, **SC** também será 0 e o resultado será **[da, DC]** , o alfa de destino e a cor. Se o **SA** for 1, o resultado será **[SA, SC]** , a fonte alfa e a cor, ou **[1, SC]** .

Os modos de `Plus` e `Modulate` são um pouco diferentes dos outros, pois as novas cores podem resultar da combinação da origem e do destino. O modo de `Plus` pode ser interpretado com componentes de byte ou componentes de ponto flutuante. Na página da **grade carregador-Duff** mostrada anteriormente, a cor de destino é **(0xC0, 0x80, 0x00)** e a cor de origem é **(0x00, 0x80, 0xC0)** . Cada par de componentes é adicionada, mas a soma é fixada em 0xFF. O resultado é a cor **(0xC0, 0xFF, 0xC0)** . Que é a cor que mostra a interseção.

Para o modo de `Modulate`, os valores RGB devem ser convertidos em ponto flutuante. A cor de destino é **(0,75, 0,5, 0)** e a origem é **(0, 0,5, 0,75)** . Os componentes RGB são cada um multiplicado em conjunto e o resultado é **(0, 0,25, 0)** . Essa é a cor mostrada na interseção na página de **grade carregador-Duff** para esse modo.

A página **transparência carregador-Duff** permite que você examine como os modos de mesclagem carregador-Duff operam em objetos gráficos que são parcialmente transparentes. O arquivo XAML inclui um `Picker` com os modos carregador-Duff:

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

[![Transparência de carregador-Duff](porter-duff-images/PorterDuffTransparency.png "Transparência de carregador-Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

A configuração do destino e da fonte é muito semelhante aos diagramas mostrados na página 255 do papel original de [_imagens digitais de composição_](https://graphics.pixar.com/library/Compositing/paper.pdf) carregador-Duff, mas essa página demonstra que os modos de mesclagem têm um bom desempenho para áreas de transparência parcial.

Você pode usar gradientes transparentes para alguns efeitos diferentes. Uma possibilidade é o mascaramento, que é semelhante à técnica mostrada na seção [**gradientes radiais para mascaramento**](../shaders/circular-gradients.md#radial-gradients-for-masking) da **página gradientes circulares do SkiaSharp**. Grande parte da página **máscara de composição** é semelhante ao programa anterior. Ele carrega um recurso de bitmap e determina um retângulo no qual para exibi-lo. Um gradiente radial é criado com base em um centro predeterminado e radius:

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

A diferença com este programa é que o gradiente começa com preta no centro e termina com transparência. Ele é exibido no bitmap com um modo de mesclagem de `DstIn`, que mostra o destino somente nas áreas da origem que não são transparentes.

Após a chamada de `DrawRect`, toda a superfície da tela é transparente, exceto pelo círculo definido pelo gradiente radial. É feita uma chamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas as áreas transparentes da tela são coloridas em rosas:

[![Máscara de composição](porter-duff-images/CompositingMask.png "Máscara de composição")](porter-duff-images/CompositingMask-Large.png#lightbox)

Você também pode usar modos de Porter Duff e gradientes parcialmente transparentes para transições de uma imagem para outra. A página **transições de gradiente** inclui uma `Slider` para indicar um nível de progresso na transição de 0 para 1 e uma `Picker` para escolher o tipo de transição desejado:

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

O arquivo code-behind carrega dois recursos de bitmap para demonstrar a transição. Essas são as mesmas duas imagens usadas na página de **dissolução de bitmap** anteriormente neste artigo. O código também define uma enumeração com três membros correspondentes a três tipos de gradientes &mdash; linear, radial e Sweep. Esses valores são carregados no `Picker`:

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

O arquivo code-behind cria três objetos `SKPaint`. O objeto `paint0` não usa um modo de mesclagem. Esse objeto de pintura é usado para desenhar um retângulo com um gradiente que vai de preto para transparente, conforme indicado na matriz de `colors`. A matriz de `positions` é baseada na posição do `Slider`, mas ajustada um pouco. Se o `Slider` for no mínimo ou máximo, os valores de `progress` serão 0 ou 1, e um dos dois bitmaps deverá estar totalmente visível. A matriz de `positions` deve ser definida adequadamente para esses valores.

Se o valor de `progress` for 0, a matriz `positions` conterá os valores-0,1 e 0. Caso contrário, o SkiaSharp ajustará esse valor primeiro para ser igual a 0, o que significa que o gradiente é preto apenas em 0 e transparente. Quando `progress` for 0,5, a matriz conterá os valores 0,45 e 0,55. O gradiente é preto de 0 para 0,45, e em seguida, faz a transição para transparente e é totalmente transparente do 0,55 como 1. Quando `progress` é 1, a matriz de `positions` é 1 e 1,1, o que significa que o gradiente é preto de 0 a 1.

As matrizes `colors` e `position` são usadas nos três métodos de `SKShader` que criam um gradiente. Somente um desses sombreadores é criado com base na seleção de `Picker`: 

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

Esse gradiente é exibido no retângulo sem o modo de mesclagem. Depois que `DrawRect` chamada, a tela simplesmente contém um gradiente de preto para transparente. A quantidade de aumentos pretos com mais de `Slider` valores.

Nas quatro instruções finais do manipulador de `PaintSurface`, os dois bitmaps são exibidos. O modo de mesclagem `SrcOut` significa que o primeiro bitmap é exibido apenas nas áreas transparentes do plano de fundo. O modo de `DstOver` para o segundo bitmap significa que o segundo bitmap é exibido somente nessas áreas em que o primeiro bitmap não é exibido.

As capturas de tela a seguir mostram os três tipos de transições diferentes, cada um na marca de 50%:

[![Transições de gradiente](porter-duff-images/GradientTransitions.png "Transições de gradiente")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
