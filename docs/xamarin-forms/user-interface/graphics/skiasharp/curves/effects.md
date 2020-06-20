---
title: Efeitos de caminho em SkiaSharp
description: Este artigo explica os vários efeitos de caminho SkiaSharp que permitem que os caminhos sejam usados para traçar e preencher e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3a5a581ffb4ca2acf1d4209b8b7a744f0daa5eb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128033"
---
# <a name="path-effects-in-skiasharp"></a>Efeitos de caminho em SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descobrir os vários efeitos de caminho que permitem que os caminhos sejam usados para traçar e preencher_

Um *efeito de caminho* é uma instância da [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe que é criada com um dos oito métodos de criação estáticos definidos pela classe. O `SKPathEffect` objeto é então definido como a [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propriedade de um [`SKPaint`](xref:SkiaSharp.SKPaint) objeto para uma variedade de efeitos interessantes, por exemplo, traçando uma linha com um pequeno caminho replicado:

![O exemplo de cadeia vinculada](effects-images/patheffectsample.png)

Os efeitos de caminho permitem que você:

- Traçar uma linha com pontos e traços
- Traçar uma linha com qualquer caminho preenchido
- Preencher uma área com linhas hachuradas
- Preencher uma área com um demarcador de lado
- Fazer cantos nítidos arredondados
- Adicionar "tremulação" aleatória a linhas e curvas

Além disso, você pode combinar dois ou mais efeitos de caminho.

Este artigo também demonstra como usar o [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) método de `SKPaint` para converter um caminho em outro caminho aplicando Propriedades de `SKPaint` , incluindo `StrokeWidth` e `PathEffect` . Isso resulta em algumas técnicas interessantes, como a obtenção de um caminho que seja um contorno de outro caminho. `GetFillPath`também é útil em conexão com efeitos de caminho.

## <a name="dots-and-dashes"></a>Pontos e traços

O uso do [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método foi descrito nos [**pontos e traços**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)do artigo. O primeiro argumento do método é uma matriz que contém um número par de dois ou mais valores, alternando entre comprimentos de traços e comprimentos de lacunas entre os traços:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Esses valores *não* são relativos à largura do traço. Por exemplo, se a largura do traço for 10 e você quiser uma linha composta por traços quadrados e lacunas quadradas, defina a `intervals` matriz como {10, 10}. O `phase` argumento indica onde, dentro do padrão de traço, a linha começa. Neste exemplo, se você quiser que a linha comece com a lacuna quadrada, defina `phase` como 10.

As extremidades dos traços são afetadas pela `StrokeCap` propriedade de `SKPaint` . Para larguras de traço largos, é muito comum definir essa propriedade como `SKStrokeCap.Round` para arredondar as extremidades dos traços. Nesse caso, os valores na `intervals` matriz *não* incluem o comprimento extra resultante do arredondamento. Esse fato significa que um ponto circular requer a especificação de uma largura de zero. Para uma largura de traço de 10, para criar uma linha com pontos circulares e lacunas entre os pontos do mesmo diâmetro, use uma `intervals` matriz de {0, 20}.

A página de **texto pontilhada animada** é semelhante à página de **texto contornada** descrita no artigo [**integrando texto e elementos gráficos**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) , pois ele exibe caracteres de texto contornados definindo a `Style` Propriedade do `SKPaint` objeto como `SKPaintStyle.Stroke` . Além disso, o **texto pontilhado animado** usa `SKPathEffect.CreateDash` para dar a esse esboço uma aparência pontilhada, e o programa também anima o `phase` argumento do `SKPathEffect.CreateDash` método para que os pontos pareçam percorrer os caracteres de texto. Aqui está a página no modo paisagem:

[![Captura de tela tripla da página de texto pontilhada animada](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

A [`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe começa definindo algumas constantes e também substitui os `OnAppearing` `OnDisappearing` métodos e para a animação:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

O `PaintSurface` manipulador começa criando um `SKPaint` objeto para exibir o texto. A `TextSize` propriedade é ajustada com base na largura da tela:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

Em direção ao final do método, o `SKPathEffect.CreateDash` método é chamado usando o `dashArray` que é definido como um campo e o valor animado `phase` . A `SKPathEffect` instância é definida como a `PathEffect` Propriedade do `SKPaint` objeto para exibir o texto.

Como alternativa, você pode definir o `SKPathEffect` objeto para o `SKPaint` objeto antes de medir o texto e centralizá-lo na página. Nesse caso, no entanto, os pontos e traços animados causam alguma variação no tamanho do texto renderizado, e o texto tende a vibrar um pouco. (Experimente!)

Você também observará que, como os pontos animados circulam os caracteres de texto, há um determinado ponto em cada curva fechada onde os pontos parecem ficar inativas e fora de existência. É aí que o caminho que define a estrutura de tópicos de caractere começa e termina. Se o comprimento do caminho não for um múltiplo integral do comprimento do padrão Dash (nesse caso, 20 pixels), somente parte desse padrão poderá se ajustar ao final do caminho.

É possível ajustar o comprimento do padrão de tracejado para se ajustar ao comprimento do caminho, mas isso requer a determinação do comprimento do caminho, uma técnica que é abordada nas [**informações de caminho e enumeração**](information.md)do artigo.

O programa **transformar ponto/traço** anima o padrão tracejado em si para que os traços pareçam dividir em pontos, que se combinam com traços de formulário novamente:

[![Captura de tela tripla da página do ponto traço Morph](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

A [`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe substitui os `OnAppearing` métodos e da `OnDisappearing` mesma forma que o programa anterior, mas a classe define o `SKPaint` objeto como um campo:

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

O `PaintSurface` manipulador cria um caminho elíptico com base no tamanho da página e executa uma seção longa de código que define as `dashArray` `phase` variáveis e. Como a variável animada `t` varia de 0 a 1, os `if` blocos dividem esse tempo em quatro trimestres e, em cada um desses trimestres, `tsub` também variam de 0 a 1. No final, o programa cria o e o `SKPathEffect` define como o `SKPaint` objeto para desenhar.

## <a name="from-path-to-path"></a>De caminho para caminho

O [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) método de `SKPaint` transforma um caminho em outro com base nas configurações do `SKPaint` objeto. Para ver como isso funciona, substitua a `canvas.DrawPath` chamada no programa anterior pelo seguinte código:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

Nesse novo código, a `GetFillPath` chamada converte o `ellipsePath` (que é apenas uma elipse) em `newPath` , que é exibido com `newPaint` . O `newPaint` objeto é criado com todas as configurações de propriedade padrão, exceto que a `Style` propriedade é definida com base no valor de retorno booliano de `GetFillPath` .

Os visuais são idênticos, exceto pela cor, que é definida em `ellipsePaint` , mas não `newPaint` . Em vez da elipse simples definida em `ellipsePath` , `newPath` contém vários contornos de caminho que definem a série de pontos e traços. Esse é o resultado da aplicação de várias propriedades de `ellipsePaint` (especificamente, `StrokeWidth` , `StrokeCap` e `PathEffect` ) ao `ellipsePath` e à colocação do caminho resultante no `newPath` . O `GetFillPath` método retorna um valor booliano que indica se o caminho de destino deve ser preenchido ou não; neste exemplo, o valor de retorno é `true` para preencher o caminho.

Tente alterar a `Style` configuração em `newPaint` para `SKPaintStyle.Stroke` e você verá os contornos de caminho individuais contornados com uma linha de largura de um pixel.

## <a name="stroking-with-a-path"></a>Traçado com um caminho

O [`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) método é conceitualmente semelhante a `SKPathEffect.CreateDash` , exceto que você especifica um caminho, em vez de um padrão de traços e lacunas. Esse caminho é replicado várias vezes para traçar a linha ou curva.

A sintaxe do é:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

Em geral, o caminho que você passa para `Create1DPath` será pequeno e centralizado em volta do ponto (0,0). O `advance` parâmetro indica a distância entre os centros do caminho, pois o caminho é replicado na linha. Normalmente, você define esse argumento como a largura aproximada do caminho. O `phase` argumento reproduz a mesma função aqui como no `CreateDash` método.

O [`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle) tem três membros:

- `Translate`
- `Rotate`
- `Morph`

O `Translate` membro faz com que o caminho permaneça na mesma orientação que é replicado ao longo de uma linha ou curva. Para `Rotate` , o caminho é girado com base em uma tangente para a curva. O caminho tem sua orientação normal para linhas horizontais. `Morph`é semelhante a `Rotate` , exceto que o próprio caminho também é curvo para corresponder à curvatura da linha que está sendo cortada.

A página de **efeito de caminho 1D** demonstra essas três opções. O arquivo [**OneDimensionalPathEffectPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) define um seletor que contém três itens correspondentes aos três membros da enumeração:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

O arquivo code-behind [**OneDimensionalPathEffectPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) define três `SKPathEffect` objetos como campos. Todos eles são criados usando `SKPathEffect.Create1DPath` com `SKPath` objetos criados usando o `SKPath.ParseSvgPathData` . A primeira é uma caixa simples, a segunda é uma forma de losango e a terceira é um retângulo. Eles são usados para demonstrar os três estilos de efeito:

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

O `PaintSurface` manipulador cria uma curva de Bézier que faz um loop em si mesmo e acessa o seletor para determinar qual `PathEffect` deve ser usado para Stroke-lo. As três opções — `Translate` , `Rotate` e `Morph` — são mostradas da esquerda para a direita:

[![Captura de tela tripla da página de efeito do caminho 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

O caminho especificado no `SKPathEffect.Create1DPath` método é sempre preenchido. O caminho especificado no `DrawPath` método sempre será traçado se o `SKPaint` objeto tiver sua `PathEffect` propriedade definida como um efeito de caminho 1D. Observe que o `pathPaint` objeto não tem nenhuma `Style` configuração, que normalmente usa como padrão `Fill` , mas o caminho é traçado independentemente.

A caixa usada no `Translate` exemplo é de 20 pixels de quadrado e o `advance` argumento é definido como 24. Essa diferença causa uma lacuna entre as caixas quando a linha é aproximadamente horizontal ou vertical, mas as caixas se sobrepõem um pouco quando a linha é diagonal, pois a diagonal da caixa é de 28,3 pixels.

A forma de losango no `Rotate` exemplo também tem 20 pixels de largura. O `advance` é definido como 20 para que os pontos continuem a tocar enquanto o losango é girado junto com a curvatura da linha.

A forma de retângulo no `Morph` exemplo tem 50 pixels de largura com uma `advance` configuração de 55 para fazer uma pequena lacuna entre os retângulos à medida que eles são tortos em volta da curva Bézier.

Se o `advance` argumento for menor que o tamanho do caminho, os caminhos replicados poderão se sobrepor. Isso pode resultar em alguns efeitos interessantes. A página **cadeia vinculada** exibe uma série de círculos sobrepostos que parecem ser semelhantes a uma cadeia vinculada, que trava na forma distinta de um Catenary:

[![Captura de tela tripla da página cadeia vinculada](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Olhe muito próximo e você verá que esses não são realmente círculos. Cada link na cadeia é dois arcos, dimensionados e posicionados para que pareçam conectar-se a links adjacentes.

Uma cadeia ou cabo de distribuição de peso uniforme trava na forma de um Catenary. Um Arch criado na forma de um Catenary invertido beneficia-se de uma distribuição igual de pressão a partir do peso de um Arch. O Catenary tem uma descrição matemática aparentemente simples:

`y = a · cosh(x / a)`

O *cosh* é a função hiperbólica do cosseno. Para *x* igual a 0, *cosh* é zero e *y* é igual *a a*. Esse é o centro do Catenary. Assim como a função *cosseno* , *cosh* é considerado *mesmo*, o que significa que *cosh (– x)* é igual a *cosh (x)* e aumenta os valores para aumentar os argumentos positivos ou negativos. Esses valores descrevem as curvas que formam os lados do Catenary.

Encontrar o valor adequado de *um* para ajustar o Catenary às dimensões da página do telefone não é um cálculo direto. Se *w* e *h* forem a largura e a altura de um retângulo, o valor ideal de *um* atenderá à seguinte equação:

`cosh(w / 2 / a) = 1 + h / a`

O método a seguir na [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora essa igualdade referindo-se às duas expressões à esquerda e à direita do sinal de igual como `left` e `right` . Para valores pequenos de *a*, `left` é maior que `right` ; para valores grandes de *a*, `left` é menor que `right` . O `while` loop é limitado em um valor ideal de *um*:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

O `SKPath` objeto para os links é criado no construtor da classe e o `SKPathEffect` objeto resultante é definido como a `PathEffect` Propriedade do `SKPaint` objeto que é armazenado como um campo:

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

O trabalho principal do `PaintSurface` manipulador é criar um caminho para o próprio Catenary. Depois de determinar o ideal *e* armazená-lo na `optA` variável, ele também precisará calcular um deslocamento da parte superior da janela. Em seguida, ele pode acumular uma coleção de `SKPoint` valores para o Catenary, transformá-lo em um caminho e desenhar o caminho com o objeto criado anteriormente `SKPaint` :

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Este programa define o caminho usado no `Create1DPath` para ter seu ponto (0, 0) no centro. Isso parece razoável porque o ponto (0, 0) do caminho está alinhado com a linha ou curva que ele está adornando. No entanto, você pode usar um ponto não centralizado (0, 0) para alguns efeitos especiais.

A página da **correia da transportadora** cria um caminho que se assemelha a uma correia de transportador Oblong com uma parte superior e inferior curva dimensionada para as dimensões da janela. Esse caminho é traçado com um objeto simples de `SKPaint` 20 pixels de largura e cinza colorido e, em seguida, traçado novamente com outro `SKPaint` objeto com um `SKPathEffect` objeto que faz referência a um caminho semelhante a um pequeno Bucket:

[![Captura de tela tripla da página da correia do esteira](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

O ponto (0,0) do caminho do Bucket é o identificador, portanto, quando o `phase` argumento é animado, os buckets parecem girar em volta da correia do transportador, talvez scoopingndo a água na parte inferior e despejando-a na parte superior.

A [`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implementa a animação com substituições `OnAppearing` dos `OnDisappearing` métodos e. O caminho para o Bucket é definido no construtor da página:

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

O código de criação de buckets é concluído com duas transformações que tornam o Bucket um pouco maior e os viram para os lados. A aplicação dessas transformações foi mais fácil do que ajustar todas as coordenadas no código anterior.

O `PaintSurface` manipulador começa definindo um caminho para a correia da transportadora em si. Isso é simplesmente um par de linhas e um par de círculos semicirculares que são desenhados com uma linha cinza de 20 pixels de largura escura:

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

A lógica para desenhar a correia do transportador não funciona no modo paisagem.

Os buckets devem ser espaçados cerca de 200 pixels de distância na correia do transportador. No entanto, a correia do transportador provavelmente não é um múltiplo de 200 pixels de comprimento, o que significa que, como o `phase` argumento de `SKPathEffect.Create1DPath` é animado, os buckets ficarão dentro e fora da existência.

Por esse motivo, o programa primeiro calcula um valor chamado `length` que é o comprimento da correia do transportador. Como a correia do transportador consiste em linhas retas e semicírculos, esse é um cálculo simples. Em seguida, o número de buckets é calculado dividindo `length` por 200. Isso é arredondado para o número inteiro mais próximo e esse número é então dividido em `length` . O resultado é um espaçamento para um número integral de buckets. O `phase` argumento é simplesmente uma fração disso.

## <a name="from-path-to-path-again"></a>De caminho para caminho novamente

Na parte inferior do `DrawSurface` manipulador na **correia do transportador**, comente a `canvas.DrawPath` chamada e substitua-a pelo código a seguir:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Assim como no exemplo anterior do `GetFillPath` , você verá que os resultados são os mesmos, exceto a cor. Após `GetFillPath` a execução, o `newPath` objeto contém várias cópias do caminho de Bucket, cada uma posicionada no mesmo ponto em que a animação as posicionou no momento da chamada.

## <a name="hatching-an-area"></a>Hachurando uma área

O [`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) método preenche uma área com linhas paralelas, geralmente chamadas de *linhas hachuradas*. O método tem a seguinte sintaxe:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

O `width` argumento especifica a largura do traço das linhas de hachura. O `matrix` parâmetro é uma combinação de dimensionamento e rotação opcional. O fator de dimensionamento indica o incremento de pixel que o skia usa para espaçar as linhas de hachura. A separação entre as linhas é o fator de dimensionamento menos o `width` argumento. Se o fator de dimensionamento for menor ou igual ao `width` valor, não haverá espaço entre as linhas de hachura e a área parecerá estar preenchida. Especifique o mesmo valor para dimensionamento horizontal e vertical.

Por padrão, as linhas de hachura são horizontais. Se o `matrix` parâmetro contiver rotação, as linhas de hachura serão giradas no sentido horário.

A página **preenchimento de hachura** demonstra esse efeito de caminho. A [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe define três efeitos de caminho como campos, o primeiro para linhas de hachura horizontal com uma largura de 3 pixels com um fator de dimensionamento que indica que eles têm 6 pixels de distância. A separação entre as linhas é, portanto, três pixels. O segundo efeito de caminho é para linhas de hachura vertical com uma largura de seis pixels com espaçamento de 24 pixels de distância (por isso, a separação é 18 pixels) e a terceira é para linhas hachuradas diagonais 12 pixels de largura com 36 pixels de distância.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Observe o método de matriz `Multiply` . Como os fatores de dimensionamento horizontal e vertical são os mesmos, a ordem na qual as matrizes de dimensionamento e rotação são multiplicadas não importa.

O `PaintSurface` manipulador usa esses três efeitos de caminho com três cores diferentes em combinação com `fillPaint` para preencher um retângulo arredondado dimensionado de acordo com a página. A `Style` propriedade definida em `fillPaint` é ignorada; quando o `SKPaint` objeto inclui um efeito de caminho criado a partir de `SKPathEffect.Create2DLine` , a área é preenchida independentemente:

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Se olhar atentamente os resultados, você verá que as linhas de hachura vermelha e azul não são confinadas precisamente no retângulo arredondado. (Isso é aparentemente uma característica do código skia subjacente.) Se isso não for satisfatório, uma abordagem alternativa será mostrada para as linhas de hachura diagonal em verde: o retângulo arredondado é usado como um caminho de recorte e as linhas de hachura são desenhadas na página inteira.

O `PaintSurface` manipulador termina com uma chamada para simplesmente traçar o retângulo arredondado, para que você possa ver a discrepância com as linhas de hachura vermelha e azul:

[![Captura de tela tripla da página preenchimento de hachura](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

A tela do Android não tem a seguinte aparência: a escala da captura de telas fez com que as linhas vermelhas finas e os espaços finos sejam consolidados em linhas vermelhas mais largas e espaços mais largos.

## <a name="filling-with-a-path"></a>Preenchendo com um caminho

O [`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) permite que você preencha uma área com um caminho que é replicado horizontal e verticalmente, em vigor ao lado da área:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Os `SKMatrix` fatores de dimensionamento indicam o espaçamento horizontal e vertical do caminho replicado. Mas você não pode girar o caminho usando esse `matrix` argumento; se desejar que o caminho girado, gire o próprio caminho usando o `Transform` método definido por `SKPath` .

O caminho replicado normalmente é alinhado às bordas esquerda e superior da tela, em vez da área que está sendo preenchida. Você pode substituir esse comportamento fornecendo fatores de tradução entre 0 e os fatores de dimensionamento para especificar deslocamentos horizontais e verticais dos lados esquerdo e superior.

A página **preenchimento do bloco de caminho** demonstra esse efeito de caminho. O caminho usado para sublado a área é definido como um campo na [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. As coordenadas horizontal e vertical variam de – 40 a 40, o que significa que esse caminho é de 80 pixels quadrado:

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

No `PaintSurface` manipulador, as `SKPathEffect.Create2DPath` chamadas definem o espaçamento horizontal e vertical como 64 para fazer com que os blocos quadrados de 80 pixels se sobreponham. Felizmente, o caminho é semelhante a uma peça de quebra-cabeça, com a malha com blocos adjacentes:

[![Captura de tela tripla da página de preenchimento do bloco caminho](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

O dimensionamento da captura de tela original causa alguma distorção, especialmente na tela do Android.

Observe que esses blocos sempre aparecem inteiros e nunca são truncados. Nas duas primeiras capturas de tela, não é mesmo evidente que a área que está sendo preenchida é um retângulo arredondado. Se você quiser truncar esses blocos para uma área específica, use um caminho de recorte.

Tente definir a `Style` Propriedade do `SKPaint` objeto como `Stroke` , e você verá os blocos individuais descritos em vez de preenchidos.

Também é possível preencher uma área com um bitmap em ladrilho, conforme mostrado no artigo [**SkiaSharp bitmap de blocos**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Arredondando cantos nítidos

O programa **heptagon arredondado** apresentado nas [**três maneiras de desenhar um artigo Arc**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) usava um arco tangente para curvar os pontos de uma figura de sete lados. A **outra página heptagon arredondada** mostra uma abordagem muito mais fácil que usa um efeito de caminho criado a partir do [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Embora o argumento único seja nomeado `radius` , você deve defini-lo como metade do raio de canto desejado. (Essa é uma característica do código skia subjacente.)

Aqui está o `PaintSurface` manipulador na [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

Você pode usar esse efeito com traçado ou preenchimento com base na `Style` Propriedade do `SKPaint` objeto. Aqui está executando:

[![Captura de tela tripla da outra página de heptagon arredondada](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Você verá que esse heptagon arredondado é idêntico ao programa anterior. Se você precisar de mais convencer que o raio de canto é verdadeiramente 100 em vez de 50 especificado na `SKPathEffect.CreateCorner` chamada, você pode remover o comentário da instrução final no programa e ver um círculo 100-RADIUS sobreposto no canto.

## <a name="random-jitter"></a>Tremulação aleatória

Às vezes, as linhas retas sem falhas de gráficos de computador não são exatamente o que você quer, e uma pequena aleatoriedade é desejada. Nesse caso, você desejará tentar o [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Você pode usar esse efeito de caminho para o traçado ou preenchimento. As linhas são separadas em segmentos conectados — o comprimento aproximado especificado por `segLength` — e se estendem em direções diferentes. A extensão do desvio da linha original é especificada por `deviation` .

O argumento final é uma semente usada para gerar a sequência pseudo aleatória usada para o efeito. O efeito de tremulação parecerá um pouco diferente para propagações diferentes. O argumento tem um valor padrão igual a zero, o que significa que o efeito é o mesmo sempre que você executa o programa. Se você quiser uma variação diferente sempre que a tela for repintada, poderá definir a semente para a `Millisecond` propriedade de um `DataTime.Now` valor (por exemplo,).

A página **experimento de tremulação** permite que você experimente valores diferentes no traçado de um retângulo:

[![Captura de tela tripla da página JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

O programa é simples. O arquivo [**JitterExperimentPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) instancia dois `Slider` elementos e um `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

O `PaintSurface` manipulador no arquivo de código-behind [**JitterExperimentPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) é chamado sempre que um `Slider` valor é alterado. Ele chama `SKPathEffect.CreateDiscrete` o uso de dois `Slider` valores e o usa para traçar um retângulo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Você também pode usar esse efeito para preencher, caso em que o contorno da área preenchida está sujeito a esses desvios aleatórios. A página de **texto de tremulação** demonstra como usar esse efeito de caminho para exibir texto. A maior parte do código no `PaintSurface` manipulador da [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe é dedicada ao dimensionamento e à centralização do texto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Aqui, ele está sendo executado no modo paisagem:

[![Captura de tela tripla da página JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Estrutura de tópicos do caminho

Você já viu dois pequenos exemplos do [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) método do `SKPaint` , que existem duas versões:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Somente os dois primeiros argumentos são necessários. O método acessa o caminho referenciado pelo `src` argumento, modifica os dados de caminho com base nas propriedades de traço no `SKPaint` objeto (incluindo a `PathEffect` Propriedade) e, em seguida, grava os resultados no `dst` caminho. O `resScale` parâmetro permite reduzir a precisão para criar um caminho de destino menor e o `cullRect` argumento pode eliminar contornos fora de um retângulo.

Um uso básico desse método não envolve efeitos de caminho: se o `SKPaint` objeto tiver sua `Style` propriedade definida como `SKPaintStyle.Stroke` e *não* tiver seu `PathEffect` conjunto, o `GetFillPath` criará um caminho que representa um *contorno* do caminho de origem como se tivesse sido traçado pelas propriedades de pintura.

Por exemplo, se o `src` caminho for um círculo simples de raio 500 e o `SKPaint` objeto especificar uma largura de traço de 100, o `dst` caminho se tornará dois círculos concêntricos, um com um raio de 450 e o outro com um raio de 550. O método é chamado `GetFillPath` porque o preenchimento desse `dst` caminho é o mesmo que traçar o `src` caminho. Mas você também pode traçar o `dst` caminho para ver os contornos do caminho.

O **toque para descrever o caminho** demonstra isso. O `SKCanvasView` e `TapGestureRecognizer` são instanciados no arquivo [**TapToOutlineThePathPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) . O arquivo code-behind [**TapToOutlineThePathPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) define três `SKPaint` objetos como campos, dois para o traçado com larguras de traço de 100 e 20 e o terceiro para preencher:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Se a tela não tiver sido tocada, o `PaintSurface` manipulador usará os `blueFill` `redThickStroke` objetos e pintará para renderizar um caminho circular:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

O círculo é preenchido e traçado como esperado:

[![Captura de tela tripla do toque normal para descrever a página do caminho](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Quando você toca na tela, `outlineThePath` é definido como `true` , e o `PaintSurface` manipulador cria um novo `SKPath` objeto e o usa como o caminho de destino em uma chamada para `GetFillPath` no `redThickStroke` objeto Paint. Esse caminho de destino é então preenchido e traçado `redThinStroke` , resultando no seguinte:

[![Captura de tela tripla do toque para descrever a página do caminho](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Os dois círculos vermelhos indicam claramente que o caminho circular original foi convertido em dois contornos circulares.

Esse método pode ser muito útil no desenvolvimento de caminhos a serem usados para o `SKPathEffect.Create1DPath` método. Os caminhos que você especificar nesses métodos sempre serão preenchidos quando os caminhos forem replicados. Se você não quiser que o caminho inteiro seja preenchido, você deve definir cuidadosamente os contornos.

Por exemplo, no exemplo de **cadeia vinculada** , os links foram definidos com uma série de quatro arcos, cada par com base em dois raios para descrever a área do caminho a ser preenchido. É possível substituir o código na `LinkedChainPage` classe para fazê-lo um pouco diferente.

Primeiro, você desejará redefinir a `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`Agora, o é apenas dois arcos com base no único raio, com os ângulos de partida e os ângulos de limpeza desejados:

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

O `outlinePath` objeto é, então, o destinatário da estrutura de tópicos de `linkPath` quando ele é traçado com as propriedades especificadas em `strokePaint` .

Outro exemplo que usa essa técnica é chegar em seguida para o caminho usado em um método.

## <a name="combining-path-effects"></a>Combinando efeitos de caminho

Os dois métodos de criação estática final do `SKPathEffect` são [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) e [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) :

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ambos os métodos combinam dois efeitos de caminho para criar um efeito de caminho composto. O `CreateSum` método cria um efeito de caminho semelhante aos dois efeitos de caminho aplicados separadamente, enquanto `CreateCompose` aplica um efeito de caminho (o `inner` ) e, em seguida, aplica o `outer` a ele.

Você já viu como o `GetFillPath` método de `SKPaint` pode converter um caminho para outro caminho com base nas `SKPaint` Propriedades (incluindo `PathEffect` ), portanto, não deve ser *muito* misterioso como um `SKPaint` objeto pode executar essa operação duas vezes com os dois efeitos de caminho especificados nos `CreateSum` `CreateCompose` métodos ou.

Um uso óbvio do `CreateSum` é definir um `SKPaint` objeto que preenche um caminho com um efeito de caminho e traça o caminho com outro efeito de caminho. Isso é demonstrado na amostra de **gatos no quadro** , que exibe uma matriz de gatos dentro de um quadro com bordas enquadradas:

[![Captura de tela tripla da página de gatos no quadro](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

A [`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe começa definindo vários campos. Você pode reconhecer o primeiro campo da [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe a partir do artigo [**dados do caminho SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) . O segundo caminho se baseia em uma linha e um arco para o padrão de Vieira do quadro:

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

O `catPath` pode ser usado no `SKPathEffect.Create2DPath` método se a `SKPaint` Propriedade do objeto `Style` for definida como `Stroke` . No entanto, se o `catPath` for usado diretamente neste programa, toda a cabeça do gato será preenchida e as caixas estreitas nem mesmo estarão visíveis. (Experimente!) É necessário obter o contorno desse caminho e usar essa estrutura de tópicos no `SKPathEffect.Create2DPath` método.

O construtor faz esse trabalho. Primeiro, ele aplica duas transformações ao `catPath` para mover o ponto (0, 0) para o centro e dimensioná-lo em tamanho. `GetFillPath`Obtém todos os contornos dos contornos no `outlinedCatPath` , e esse objeto é usado na `SKPathEffect.Create2DPath` chamada. Os fatores de dimensionamento no `SKMatrix` valor são ligeiramente maiores do que o tamanho horizontal e vertical do gato para fornecer um pequeno buffer entre os blocos, enquanto os fatores de conversão foram derivados de certa forma empiricamente para que um gato completo fique visível no canto superior esquerdo do quadro:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

O Construtor então chama `SKPathEffect.Create1DPath` o quadro separado. Observe que a largura do caminho é de 100 pixels, mas o avanço é 75 pixels para que o caminho replicado seja sobreposto em todo o quadro. A instrução final do construtor chama `SKPathEffect.CreateSum` para combinar os dois efeitos de caminho e define o resultado para o `SKPaint` objeto.

Todo esse trabalho permite que o `PaintSurface` manipulador seja bem simples. Ele só precisa definir um retângulo e desenhá-lo usando `framePaint` :

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Os algoritmos por trás dos efeitos de caminho sempre fazem com que o caminho inteiro usado para traçar ou preencher seja exibido, o que pode fazer com que alguns elementos visuais apareçam fora do retângulo. A `ClipRect` chamada antes da `DrawRect` chamada permite que os visuais sejam consideravelmente mais limpos. (Experimente sem recortar!)

É comum usar o `SKPathEffect.CreateCompose` para adicionar alguma variação a outro efeito de caminho. Você certamente pode experimentar por conta própria, mas aqui está um exemplo um pouco diferente:

As **linhas de hachura tracejadas** ocupam uma elipse com linhas de hachura tracejadas. A maior parte do trabalho na [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe é executada diretamente nas definições de campo. Esses campos definem um efeito de traço e um efeito de hachura. Eles são definidos como `static` porque eles são referenciados em uma `SKPathEffect.CreateCompose` chamada na `SKPaint` definição:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

O `PaintSurface` manipulador precisa conter apenas a sobrecarga padrão, mais uma chamada para `DrawOval` :

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Como você já descobriu, as linhas de hachura não são precisamente restritas ao interior da área e, neste exemplo, elas sempre começam à esquerda com um traço inteiro:

[![Captura de tela tripla da página linhas de hachura tracejada](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Agora que você já viu os efeitos de caminho que variam de pontos simples e traços a combinações estranhas, use sua imaginação e veja o que você pode criar.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
