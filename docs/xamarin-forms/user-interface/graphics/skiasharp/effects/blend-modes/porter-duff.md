---
Título: "carregador-Duff modos de mesclagem" Descrição: "Use os modos de combinação carregador-Duff para compor cenas com base nas imagens de origem e de destino."
MS. Prod: xamarin MS. Technology: xamarin-skiasharp MS. AssetID: 57F172F8-BA03-43EC-A215-ED6B78696BB5 autor: davidbritch MS. Author: dabritch MS. Date: 08/23/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="porter-duff-blend-modes"></a>Modos de mesclagem carregador-Duff

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Os modos de mesclagem carregador-Duff são nomeados após Thomas carregador e Tom Duff, que desenvolveu uma Algebra de composição enquanto trabalha para Lucasfilm. Suas [_imagens digitais de composição_](https://graphics.pixar.com/library/Compositing/paper.pdf) de papel foram publicadas na edição de julho de 1984 de _gráficos de computador_, páginas 253 a 259. Esses modos de mesclagem são essenciais para composição, que está montando várias imagens em uma cena composta:

![Exemplo de carregador-Duff](porter-duff-images/PorterDuffSample.png "Exemplo de carregador-Duff")

## <a name="porter-duff-concepts"></a>Conceitos de carregador-Duff

Suponha que um retângulo marrom ocupe os dois terços esquerdo e superior da sua superfície de exibição:

![Destino carregador-Duff](porter-duff-images/PorterDuffDst.png "Destino carregador-Duff")

Essa área é chamada de _destino_ ou, às vezes, do _plano de fundo_ ou do _pano_.

Você deseja desenhar o retângulo a seguir, que tem o mesmo tamanho do destino. O retângulo é transparente, exceto por uma área bluish que ocupe a direita e a parte inferior dois terços:

![Origem do carregador-Duff](porter-duff-images/PorterDuffSrc.png "Origem do carregador-Duff")

Isso é chamado de _origem_ ou, às vezes, do _primeiro plano_.

Quando você exibe a origem no destino, aqui está o que você espera:

![Carregador-Duff de origem](porter-duff-images/PorterDuffSrcOver.png "Carregador-Duff de origem")

Os pixels transparentes da fonte permitem que o plano de fundo mostre, enquanto os pixels de origem bluish obscurecem o plano de fundo. Esse é o caso normal e é referenciado em SkiaSharp como `SKBlendMode.SrcOver` . Esse valor é a configuração padrão da `BlendMode` propriedade quando um `SKPaint` objeto é instanciado pela primeira vez.

No entanto, é possível especificar um modo de mesclagem diferente para um efeito diferente. Se você especificar `SKBlendMode.DstOver` , na área em que a origem e o destino se cruzam, o destino aparecerá em vez da fonte:

![Carregador-Duff de destino](porter-duff-images/PorterDuffDstOver.png "Carregador-Duff de destino")

O `SKBlendMode.DstIn` modo de mesclagem exibe apenas a área em que o destino e a origem fazem interseção usando a cor de destino:

![Carregador-Duff de destino](porter-duff-images/PorterDuffDstIn.png "Carregador-Duff de destino")

O modo de mesclagem de `SKBlendMode.Xor` (exclusivo ou) não faz nada para aparecer onde as duas áreas se sobrepõem:

![Carregador-Duff exclusivo ou](porter-duff-images/PorterDuffXor.png "Carregador-Duff exclusivo ou")

O destino colorido e os retângulos de origem dividem efetivamente a superfície de exibição em quatro áreas exclusivas que podem ser coloridas de várias maneiras correspondentes à presença dos retângulos de destino e de origem:

![Carregador-Duff](porter-duff-images/PorterDuff.png "Carregador-Duff")

Os retângulos superior direito e inferior esquerdo estão sempre em branco porque o destino e a origem são transparentes nessas áreas. A cor de destino ocupa a área superior esquerda, para que a área possa ser colorida com a cor de destino ou não. Da mesma forma, a cor de origem ocupa a área inferior direita, para que a área possa ser colorida com a cor de origem ou não. A interseção do destino e da origem no meio pode ser colorida com a cor de destino, a cor de origem ou não.

O número total de combinações é 2 (para os períodos superior esquerdo) 2 (para a parte inferior direita) vezes 3 (para o centro) ou 12. Estes são os 12 modos básicos de composição carregador-Duff.

No final da _composição de imagens digitais_ (página 256), carregador e Duff adicionam um modo 13 chamado _Plus_ (correspondente ao membro SkiaSharp `SKBlendMode.Plus` e o modo _mais claro_ do W3C (que não deve ser confundido com o modo de _clareamento_ do W3C). Esse `Plus` modo adiciona as cores de origem e destino, um processo que será descrito mais detalhadamente em breve.

Skia adiciona um modo 14 chamado `Modulate` que é muito semelhante a `Plus` , exceto que as cores de origem e destino são multiplicadas. Ele pode ser tratado como um modo de mesclagem carregador-Duff adicional.

Aqui estão os 14 modos carregador Duff, conforme definido em SkiaSharp. A tabela mostra como elas colorem cada uma das três áreas que não estão em branco no diagrama acima:

| Mode       | Destino | Interseção | Fonte |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Fonte       | X      |
| `Dst`      | X           | Destino  |        |
| `SrcOver`  | X           | Fonte       | X      |
| `DstOver`  | X           | Destino  | X      |
| `SrcIn`    |             | Fonte       |        |
| `DstIn`    |             | Destino  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Fonte       |        |
| `DstATop`  |             | Destino  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | SUM          | X      |
| `Modulate` |             | Produto      |        | 

Esses modos de mesclagem são simétricos. A origem e o destino podem ser trocados e todos os modos ainda estão disponíveis.

A Convenção de nomenclatura dos modos segue algumas regras simples:

- **Src** ou **DST** por si só significa que apenas os pixels de origem ou de destino estão visíveis.
- O sufixo **over** indica o que está visível na interseção. A origem ou o destino é desenhado "sobre" o outro.
- O sufixo **in** significa que apenas a interseção é colorida. A saída é restrita a apenas a parte da origem ou destino que está "em" a outra.
- O sufixo **out** significa que a interseção não é colorida. A saída é apenas a parte da origem ou destino que está "fora" da interseção.
- O sufixo **acima** é a União de **entrada** e **saída**. Ele inclui a área em que a origem ou o destino está "acima" do outro.

Observe a diferença com os `Plus` `Modulate` modos e. Esses modos estão executando um tipo diferente de cálculo nos pixels de origem e de destino. Eles são descritos mais detalhadamente em breve.

A página de **grade carregador-Duff** mostra todos os 14 modos em uma tela na forma de uma grade. Cada modo é uma instância separada do `SKCanvasView` . Por esse motivo, uma classe é derivada de `SKCanvasView` Named `PorterDuffCanvasView` . O construtor estático cria dois bitmaps do mesmo tamanho, um com um retângulo marrom em sua área superior esquerda e outro com um retângulo bluish:

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

O construtor de instância tem um parâmetro do tipo `SKBlendMode` . Ele salva esse parâmetro em um campo. 

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

A `OnPaintSurface` substituição desenha os dois bitmaps. A primeira é desenhada normalmente:

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

O segundo é desenhado com um `SKPaint` objeto em que a `BlendMode` propriedade foi definida como o argumento do Construtor:

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

O restante da `OnPaintSurface` substituição desenha um retângulo em volta do bitmap para indicar seus tamanhos.

A `PorterDuffGridPage` classe cria quatorze instâncias de `PorterDurffCanvasView` , uma para cada membro da `blendModes` matriz. A ordem dos `SKBlendModes` Membros na matriz é um pouco diferente da tabela, a fim de posicionar modos semelhantes adjacentes entre si. As 14 instâncias do `PorterDuffCanvasView` são organizadas junto com os rótulos em um `Grid` :

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

Você desejará convencer-se de que a transparência é crucial para o funcionamento adequado dos modos de mistura carregador-Duff. A `PorterDuffCanvasView` classe contém um total de três chamadas para o `Canvas.Clear` método. Todos eles usam o método sem parâmetros, que define todos os pixels como transparentes:

```csharp
canvas.Clear();
```

Tente alterar qualquer uma dessas chamadas para que os pixels sejam definidos como branco opaco:

```csharp
canvas.Clear(SKColors.White);
```

Após essa alteração, alguns dos modos de mesclagem parecerão funcionar, mas outros não vão. Se você definir o plano de fundo do bitmap de origem como branco, o `SrcOver` modo não funcionará porque não há nenhum pixel transparente no bitmap de origem para permitir que o destino seja mostrado. Se você definir o plano de fundo do bitmap de destino ou a tela como branco, o `DstOver` não funcionará porque o destino não tem nenhum pixel transparente.

Pode haver uma tentação de substituir os bitmaps na página de **grade carregador-Duff** por chamadas mais simples `DrawRect` . Isso funcionará para o retângulo de destino, mas não para o retângulo de origem. O retângulo de origem deve abranger mais do que apenas a área bluish. O retângulo de origem deve incluir uma área transparente que corresponda à área colorida do destino. Somente então esses modos de mesclagem funcionarão.

## <a name="using-mattes-with-porter-duff"></a>Usando Matts com carregador-Duff

A página de **composição do Brick-Wall** mostra um exemplo de uma tarefa de composição clássica: uma imagem precisa ser montada de várias partes, incluindo um bitmap com um plano de fundo que precisa ser eliminado. Aqui está o bitmap **SeatedMonkey.jpg** com o plano de fundo problemático:

![Macaco sentado](porter-duff-images/SeatedMonkey.jpg "Macaco sentado")

Na preparação para a composição, um _fosco_ correspondente foi criado, que é outro bitmap que é preto onde você deseja que a imagem seja exibida e transparente caso contrário. Esse arquivo é denominado **SeatedMonkeyMatte.png** e está entre os recursos na pasta de **mídia** no exemplo [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Macaco encaixado fosco](porter-duff-images/SeatedMonkeyMatte.png "Macaco encaixado fosco")

Esse _não_ é um fosco criado de especialista. Idealmente, o fosco deve incluir pixels parcialmente transparentes em volta da borda dos pixels pretos, e esse fosco não.

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

O arquivo code-behind carrega os dois bitmaps necessários e manipula o `Clicked` evento do `Button` . Para cada `Button` clique, o `step` campo é incrementado e uma nova `Text` propriedade é definida para o `Button` . Quando `step` atinge 5, ele é definido de volta como 0:

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

Quando o programa é executado pela primeira vez, nada é visível, exceto `Button` :

[![Etapa 0 da composição de mural de tijolo](porter-duff-images/BrickWallCompositing0.png "Etapa 0 da composição de mural de tijolo")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Pressionar as `Button` causas a serem `step` incrementadas para 1 e o `PaintSurface` manipulador agora exibe **SeatedMonkey.jpg**:

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

Não há nenhum `SKPaint` objeto e, portanto, não há nenhum modo de mesclagem. O bitmap aparece na parte inferior da tela:

[![Etapa 1 da composição de parede de Brick](porter-duff-images/BrickWallCompositing1.png "Etapa 1 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Pressione a `Button` novamente e `step` incrementa para 2. Esta é a etapa crucial de exibir o arquivo de **SeatedMonkeyMatte.png** :

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

O modo de mesclagem é `SKBlendMode.DstIn` , o que significa que o destino será preservado em áreas correspondentes às áreas não transparentes da origem. O restante do retângulo de destino correspondente ao bitmap original se torna transparente:

[![Etapa 2 da composição de parede de Brick](porter-duff-images/BrickWallCompositing2.png "Etapa 2 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

O plano de fundo foi removido. 

A próxima etapa é desenhar um retângulo que se assemelha a um calçada em que o macaco está sentado. A aparência desse calçada é baseada em uma composição de dois sombreadores: um sombreador de cor sólido e um sombreador de ruído de Perlm:

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

Como esse calçada deve ficar atrás do macaco, o modo de mesclagem é `DstOver` . O destino aparece apenas onde o plano de fundo é transparente:

[![Etapa 3 da composição de parede de Brick](porter-duff-images/BrickWallCompositing3.png "Etapa 3 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

A etapa final é adicionar uma parede de tijolos. O programa usa o bloco de bitmap de parede de tijolo disponível como a propriedade estática `BrickWallTile` na `AlgorithmicBrickWallPage` classe. Uma transformação de tradução é adicionada à `SKShader.CreateBitmap` chamada para deslocar os blocos para que a linha inferior seja um bloco completo:

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

Para sua conveniência, a `DrawRect` chamada exibe esse sombreador sobre toda a tela, mas o `DstOver` modo limita a saída somente à área da tela que ainda é transparente:

[![Etapa 4 da composição de parede de Brick](porter-duff-images/BrickWallCompositing4.png "Etapa 4 da composição de parede de Brick")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Obviamente, há outras maneiras de compor essa cena. Ele pode ser criado a partir do plano de fundo e progredindo para o primeiro plano. Mas usar os modos de mesclagem proporciona mais flexibilidade. Em particular, o uso do Matte permite que o plano de fundo de um bitmap seja excluído da cena composta.

Como você aprendeu no artigo [recorte com caminhos e regiões](../../curves/clipping.md), a `SKCanvas` classe define três tipos de recorte, correspondentes aos [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) métodos, e [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . Os modos de mesclagem carregador-Duff adicionam outro tipo de recorte, que permite restringir uma imagem a qualquer coisa que você possa desenhar, incluindo bitmaps. O matte usado na **composição de mural de tijolo** basicamente define uma área de recorte.

## <a name="gradient-transparency-and-transitions"></a>Transparência e transições de gradiente

Os exemplos dos modos de mesclagem carregador-Duff mostrados anteriormente neste artigo têm todas as imagens envolvidas que consistiram em pixels opacos e pixels transparentes, mas não em pixels parcialmente transparentes. As funções de modo de combinação também são definidas para esses pixels. A tabela a seguir é uma definição mais formal dos modos de mesclagem carregador-Duff que usa a notação encontrada na referência do skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Como **referência de SkBlendMode** é uma referência de skia, a sintaxe do C++ é usada.)

Conceitualmente, os componentes vermelho, verde, azul e alfa de cada pixel são convertidos de bytes em números de ponto flutuante no intervalo de 0 a 1. Para o canal alfa, 0 é totalmente transparente e 1 é totalmente opaco

A notação na tabela abaixo usa as seguintes abreviações:

- O **da** é o canal alfa de destino
- O **DC** é a cor RGB de destino
- **SA** é o canal alfa de origem
- **SC** é a cor RGB de origem

As cores RGB são previamente multiplicadas pelo valor alfa. Por exemplo, se **SC** representar vermelho puro, mas **SA** for 0X80, a cor RGB será **(0x80, 0, 0)**. Se **SA** for 0, todos os componentes RGB também serão zero.

O resultado é mostrado entre colchetes com o canal alfa e a cor RGB, separados por uma vírgula: **[alfa, cor]**. Para a cor, o cálculo é executado separadamente para os componentes vermelho, verde e azul:

| Mode       | Operação |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [SA, SC]  |
| `Dst`      | [Da, DC]  |
| `SrcOver`  | [SA + da · (1 – SA), SC + DC · (1 – SA) | 
| `DstOver`  | [Da + SA · (1 – da), DC + SC · (1 – da) |
| `SrcIn`    | Administrador Da, SC · Auditoria |
| `DstIn`    | Auditoria SA, DC · Administrador |
| `SrcOut`   | Administrador (1 – da), SC · (1 – da)] |
| `DstOut`   | Auditoria (1 – SA), DC · (1 – SA)] |
| `SrcATop`  | [Da, SC · Da + DC · (1 – SA)] |
| `DstATop`  | [SA, DC · SA + SC · (1 – da)] |
| `Xor`      | [SA + da – 2 · Administrador Da, SC · (1 – da) + DC · (1 – SA)] |
| `Plus`     | [SA + da, SC + DC] |
| `Modulate` | Administrador Da, SC · Origem | 

Essas operações são mais fáceis de analisar quando o **da** e **SA** são 0 ou 1. Por exemplo, para o `SrcOver` modo padrão, se **SA** for 0, **SC** também será 0 e o resultado será **[da, DC]**, o alfa de destino e a cor. Se o **SA** for 1, o resultado será **[SA, SC]**, a fonte alfa e a cor, ou **[1, SC]**.

Os `Plus` `Modulate` modos e são um pouco diferentes dos outros, pois as novas cores podem resultar da combinação da origem e do destino. O `Plus` modo pode ser interpretado com componentes de byte ou componentes de ponto flutuante. Na página da **grade carregador-Duff** mostrada anteriormente, a cor de destino é **(0xC0, 0x80, 0x00)** e a cor de origem é **(0x00, 0x80, 0xC0)**. Cada par de componentes é adicionado, mas a soma é clamped em 0xFF. O resultado é a cor **(0xC0, 0xFF, 0xC0)**. Essa é a cor mostrada na interseção.

Para o `Modulate` modo, os valores RGB devem ser convertidos em ponto flutuante. A cor de destino é **(0,75, 0,5, 0)** e a origem é **(0, 0,5, 0,75)**. Os componentes RGB são cada um multiplicado em conjunto e o resultado é **(0, 0,25, 0)**. Essa é a cor mostrada na interseção na página de **grade carregador-Duff** para esse modo.

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

O arquivo code-behind preenche dois retângulos do mesmo tamanho usando um gradiente linear. O gradiente de destino é do canto superior direito para o inferior esquerdo. É muito bem no canto superior direito, mas, em direção, o centro começa a ficar transparente e transparente no canto inferior esquerdo. 

O retângulo de origem tem um gradiente do canto superior esquerdo para o canto inferior direito. O canto superior esquerdo é bluish, mas novamente esmaece para transparente e é transparente no canto inferior direito. 

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

Este programa demonstra que os modos de combinação carregador-Duff podem ser usados com objetos gráficos diferentes de bitmaps. No entanto, a origem deve incluir uma área transparente. Esse é o caso aqui porque o gradiente preenche o retângulo, mas parte do gradiente é transparente.

Aqui estão três exemplos:

[![Transparência de carregador-Duff](porter-duff-images/PorterDuffTransparency.png "Transparência de carregador-Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

A configuração do destino e da fonte é muito semelhante aos diagramas mostrados na página 255 do papel original de [_imagens digitais de composição_](https://graphics.pixar.com/library/Compositing/paper.pdf) carregador-Duff, mas essa página demonstra que os modos de mesclagem têm um bom desempenho para áreas de transparência parcial.

Você pode usar gradientes transparentes para alguns efeitos diferentes. Uma possibilidade é o mascaramento, que é semelhante à técnica mostrada na seção [**gradientes radiais para mascaramento**](../shaders/circular-gradients.md#radial-gradients-for-masking) da **página gradientes circulares do SkiaSharp**. Grande parte da página **máscara de composição** é semelhante ao programa anterior. Ele carrega um recurso de bitmap e determina um retângulo no qual exibi-lo. Um gradiente radial é criado com base em um centro e um raio predeterminados:

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

A diferença com esse programa é que o gradiente começa com preto no centro e termina com a transparência. Ele é exibido no bitmap com um modo de mesclagem de `DstIn` , que mostra o destino somente nas áreas da origem que não são transparentes.

Após a `DrawRect` chamada, toda a superfície da tela é transparente, exceto pelo círculo definido pelo gradiente radial. É feita uma chamada final:

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Todas as áreas transparentes da tela são cor rosa:

[![Máscara de composição](porter-duff-images/CompositingMask.png "Máscara de composição")](porter-duff-images/CompositingMask-Large.png#lightbox)

Você também pode usar os modos carregador-Duff e gradientes parcialmente transparentes para transições de uma imagem para outra. A página **transições de gradiente** inclui um `Slider` para indicar um nível de progresso na transição de 0 para 1 e um `Picker` para escolher o tipo de transição desejado:

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

O arquivo code-behind carrega dois recursos de bitmap para demonstrar a transição. Essas são as mesmas duas imagens usadas na página de **dissolução de bitmap** anteriormente neste artigo. O código também define uma enumeração com três membros correspondentes a três tipos de gradientes &mdash; linear, radial e Sweep. Esses valores são carregados no `Picker` :

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

O arquivo code-behind cria três `SKPaint` objetos. O `paint0` objeto não usa um modo de mesclagem. Esse objeto de pintura é usado para desenhar um retângulo com um gradiente que vai de preto para transparente, conforme indicado na `colors` matriz. A `positions` matriz é baseada na posição do `Slider` , mas ajustada um pouco. Se o `Slider` for no mínimo ou máximo, os `progress` valores serão 0 ou 1 e um dos dois bitmaps deverá estar totalmente visível. A `positions` matriz deve ser definida adequadamente para esses valores.

Se o `progress` valor for 0, a `positions` matriz conterá os valores-0,1 e 0. SkiaSharp ajustará que o primeiro valor será igual a 0, o que significa que o gradiente é preto somente em 0 e transparente, caso contrário. Quando `progress` é 0,5, a matriz contém os valores 0,45 e 0,55. O gradiente é preto de 0 a 0,45, então faz a transição para transparente e é totalmente transparente de 0,55 para 1. Quando `progress` é 1, a `positions` matriz é 1 e 1,1, o que significa que o gradiente é preto de 0 a 1.

As `colors` `position` matrizes e são usadas nos três métodos de `SKShader` que criam um gradiente. Somente um desses sombreadores é criado com base na `Picker` seleção: 

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

Esse gradiente é exibido no retângulo sem um modo de mesclagem. Depois dessa `DrawRect` chamada, a tela simplesmente contém um gradiente de preto para transparente. A quantidade de aumentos pretos com `Slider` valores mais altos.

Nas quatro instruções finais do `PaintSurface` manipulador, os dois bitmaps são exibidos. O `SrcOut` modo de mesclagem significa que o primeiro bitmap é exibido apenas nas áreas transparentes do plano de fundo. O `DstOver` modo para o segundo bitmap significa que o segundo bitmap é exibido somente nessas áreas em que o primeiro bitmap não é exibido.

As capturas de tela a seguir mostram os três tipos de transições diferentes, cada um na marca 50%:

[![Transições de gradiente](porter-duff-images/GradientTransitions.png "Transições de gradiente")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
