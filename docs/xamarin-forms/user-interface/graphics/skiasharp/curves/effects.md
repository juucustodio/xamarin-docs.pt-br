---
title: Efeitos de caminho em SkiaSharp
description: Este artigo explica os diversos efeitos de caminho de SkiaSharp que permitem que os caminhos a ser usado para traçar e o preenchimento e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: f43c4dac1811a54ee0ceeb70e2b2b1835a5ca030
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228248"
---
# <a name="path-effects-in-skiasharp"></a>Efeitos de caminho em SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descobrir os diversos efeitos de caminho que permitem que os caminhos a ser usado para traçar e o preenchimento_

Um *efeito de caminho* é uma instância das [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) classe que é criado com um dos oito métodos de criação estáticos definidos pela classe. O `SKPathEffect` objeto é definido como o [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propriedade de um [ `SKPaint` ](xref:SkiaSharp.SKPaint) objeto para uma variedade de efeitos interessantes, por exemplo, traçar uma linha com um pequeno caminho replicado :

![O exemplo de cadeia vinculada](effects-images/patheffectsample.png)

Efeitos de caminho permitem que você:

- Uma linha com pontos e traços de traço
- Uma linha com qualquer caminho preenchido de traço
- Preencher uma área com linhas de hachura
- Preencher uma área com um caminho de lado a lado
- Tornar nítidas cantos arredondados
- Adicionar aleatório "variação", para linhas e curvas

Além disso, você pode combinar duas ou mais efeitos de caminho.

Este artigo também demonstra como usar o [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) método `SKPaint` para converter um caminho em outro caminho, aplicando as propriedades de `SKPaint`, incluindo `StrokeWidth` e `PathEffect`. Isso resulta em algumas técnicas interessantes, como a obtenção de um caminho que é uma estrutura de tópicos de outro caminho. `GetFillPath` também é útil em conjunto com os efeitos de caminho.

## <a name="dots-and-dashes"></a>Pontos e traços

O uso do [ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) método foi descrito no artigo [ **por pontos e traços**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). O primeiro argumento do método é uma matriz que contém um número par de dois ou mais valores, alternando entre os comprimentos dos traços e os comprimentos das lacunas entre os traços:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Esses valores são *não* em relação a largura do traço. Por exemplo, se a largura do traço é 10, e você deseja que uma linha composta de quadrados traços e lacunas de quadrados, defina o `intervals` de matriz para {10, 10}. O `phase` argumento indica onde no padrão de traço a linha começa. Neste exemplo, se você deseja que a linha comece com a diferença de quadrados, defina `phase` como 10.

As extremidades dos traços são afetadas pela `StrokeCap` propriedade de `SKPaint`. Para larguras de traço ampla, é muito comum para definir essa propriedade como `SKStrokeCap.Round` para as extremidades dos traços de ida e volta. Nesse caso, os valores a `intervals` matriz faça *não* incluem o tamanho extra resultante de arredondamento. Esse fato significa que um ponto circular exige a especificação de largura de zero. Para uma largura de traço de 10, para criar uma linha com pontos circulares e lacunas entre os pontos do diâmetro do mesmo, use um `intervals` matriz de {0, 20}.

O **animada texto pontilhado** página é semelhante ao **texto contornado** página descrita no artigo [ **a integração do texto e elementos gráficos** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) em que ele exibe descritas caracteres de texto, definindo o `Style` propriedade do `SKPaint` do objeto para `SKPaintStyle.Stroke`. Além disso, **animada texto pontilhado** usa `SKPathEffect.CreateDash` para dar uma aparência pontilhada essa estrutura de tópicos e o programa também anima a `phase` argumento do `SKPathEffect.CreateDash` método para fazer com que os pontos parecem viagem ao redor do texto caracteres. Aqui está a página no modo paisagem:

[![Captura de tela tripla da página de texto pontilhada animada](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

O [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe começa definindo algumas constantes e também substitui o `OnAppearing` e `OnDisappearing` métodos para a animação:

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

O `PaintSurface` manipulador começa criando um `SKPaint` objeto para exibir o texto. O `TextSize` propriedade é ajustada com base na largura da tela:

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

No final do método, o `SKPathEffect.CreateDash` método for chamado usando o `dashArray` que é definida como um campo e o animado `phase` valor. O `SKPathEffect` instância é definida como o `PathEffect` propriedade do `SKPaint` objeto para exibir o texto.

Como alternativa, você pode definir as `SKPathEffect` do objeto para o `SKPaint` objeto antes de medir o texto e centralizando-lo na página. Nesse caso, no entanto, animados pontos e traços causam algumas variações no tamanho do texto renderizado e o texto tende a vibração um pouco. (Experimente!)

Você também observará que como o círculo pontos animado em torno de caracteres de texto, há um determinado ponto em cada curva fechada onde os pontos parecem entrar e sair de existência. Isso é onde o caminho que define o contorno de caracteres começa e termina. Se o comprimento do caminho não for um múltiplo inteiro do comprimento do padrão de traço (nesse caso, 20 pixels), apenas parte desse padrão pode se encaixar no final do caminho.

É possível ajustar o comprimento do padrão de traço para ajustar o comprimento do caminho, mas que exija determinando o comprimento do caminho, uma técnica que é abordado neste artigo [ **informações de caminho e enumeração** ](information.md).

O **Dot / traço Morph** programa anima padrão de tracejado em si, de modo que os traços parecem se dividir em pontos, que são combinados para traços de formulário novamente:

[![Captura de tela tripla da página do ponto traço Morph](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

O [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) substituições de classe a `OnAppearing` e `OnDisappearing` métodos exatamente como fez o programa anterior, mas a classe define o `SKPaint` objeto como um campo:

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

O `PaintSurface` manipulador cria uma trajetória elíptica com base no tamanho da página e executa uma longo seção de código que define o `dashArray` e `phase` variáveis. Como a variável animada `t` varia de 0 a 1, o `if` blocos dividem esse tempo, em quatro trimestres e em cada uma dessas trimestres `tsub` também varia de 0 a 1. No final, o programa cria o `SKPathEffect` e o define como o `SKPaint` objeto para o desenho.

## <a name="from-path-to-path"></a>Do caminho para o caminho

O [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) método de `SKPaint` transforma um caminho em outro com base nas configurações de `SKPaint` objeto. Para ver como isso funciona, substitua o `canvas.DrawPath` chamar no programa anterior com o código a seguir:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

Nesse código novo, o `GetFillPath` chamar converte as `ellipsePath` (que é apenas uma elipse) em `newPath`, que é exibido com `newPaint`. O `newPaint` objeto é criado com todas as configurações de propriedade padrão, exceto que o `Style` propriedade é definida com base no booliano retornar o valor da `GetFillPath`.

Os elementos visuais são idênticos, exceto para a cor, o que é definida no `ellipsePaint` , mas não `newPaint`. Em vez da elipse simple definida em `ellipsePath`, `newPath` contém vários contornos do caminho que definem a série de pontos e traços. Esse é o resultado da aplicação de várias propriedades de `ellipsePaint` (especificamente, `StrokeWidth`, `StrokeCap`, e `PathEffect`) para `ellipsePath` e colocando o caminho resultante `newPath`. O `GetFillPath` método retorna um valor booliano que indica se o caminho de destino deve ser preenchido; neste exemplo, o valor de retorno é `true` para preencher o caminho.

Tente alterar o `Style` definindo no `newPaint` para `SKPaintStyle.Stroke` e você verá os contornos de caminho individuais contornados com uma largura de pixel de uma linha.

## <a name="stroking-with-a-path"></a>Traçar com um caminho

O [ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) método é conceitualmente semelhante a `SKPathEffect.CreateDash` , exceto que você especifica um caminho em vez de um padrão de traços e lacunas. Esse caminho é replicado várias vezes para a linha de traço ou curva.

A sintaxe é:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

Em geral, o caminho que você passa para `Create1DPath` será pequeno e centralizado em torno do ponto (0, 0). O `advance` parâmetro indica a distância entre os centros do caminho como o caminho é replicado na linha. Você geralmente defina este argumento como a largura aproximada do caminho. O `phase` desempenha argumento a mesma função aqui como ele faz o `CreateDash` método.

O [ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle) tem três membros:

- `Translate`
- `Rotate`
- `Morph`

O `Translate` membro faz com que o caminho para permanecer na mesma posição que ela seja replicada ao longo de uma linha ou curva. Para `Rotate`, o caminho é girado com base em uma tangente da curva. O caminho tem sua orientação normal para as linhas horizontais. `Morph` é semelhante ao `Rotate` , exceto que o próprio caminho for curvo também para corresponder a curvatura da linha que está sendo traçada.

O **efeito de caminho D 1** página demonstra essas três opções. O [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) arquivo define um seletor que contém três itens que correspondem aos três membros da enumeração:

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

O [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) arquivo code-behind define três `SKPathEffect` objetos como campos. Essas são todas criadas usando `SKPathEffect.Create1DPath` com `SKPath` objetos criados usando `SKPath.ParseSvgPathData`. A primeira é uma caixa simples, a segunda é uma forma de losango e o terceiro é um retângulo. Eles são usados para demonstrar os estilos de três efeito:

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

O `PaintSurface` manipulador cria uma curva de Bézier que executa um loop em torno de si mesmo e, em seguida, acessa o seletor para determinar qual `PathEffect` deve ser usado para traçar a ele. As três opções — `Translate`, `Rotate`, e `Morph` — são exibidos da esquerda para direita:

[![Captura de tela tripla da página de efeito do caminho 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

O caminho especificado no `SKPathEffect.Create1DPath` método sempre é preenchido. O caminho especificado na `DrawPath` método sempre é traçado se o `SKPaint` objeto tem seu `PathEffect` propriedade definida como um efeito de caminho 1D. Observe que o `pathPaint` objeto não tem nenhum `Style` configuração, que normalmente tem como padrão para `Fill`, mas o caminho é traçado independentemente.

A caixa usada na `Translate` exemplo é de 20 pixels quadrados e o `advance` argumento é definido como 24. Essa diferença provoca uma lacuna entre as caixas de quando a linha é de aproximadamente horizontal ou vertical, mas as caixas se sobrepõem um pouco quando a linha for diagonal porque diagonal da caixa é 28.3 pixels.

A forma de losango no `Rotate` exemplo também é 20 pixels de largura. O `advance` é definido como 20 para que os pontos de continuam a toque, pois o diamante é girado juntamente com a curvatura da linha.

A forma de retângulo na `Morph` exemplo é de 50 pixels de largura com um `advance` configuração de 55 para fazer um pequeno intervalo entre os retângulos, conforme eles estiver torto em torno a curva de Bézier.

Se o `advance` argumento for menor que o tamanho do caminho, em seguida, os caminhos replicados podem se sobrepor. Isso pode resultar em alguns efeitos interessantes. O **vinculado cadeia** página exibe uma série de círculos que parecem ser semelhante a uma cadeia de vinculado, que trava na forma de um catenary diferenciada de sobreposição:

[![Captura de tela tripla da página cadeia vinculada](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Procurar muito perto e você verá que esses não são realmente círculos. Cada link na cadeia é dois arcos, dimensionado e posicionado para que eles parecem se conectar com links adjacente.

Uma cadeia ou um cabo de distribuição de peso uniforme trava na forma de um catenary. Um arch criado na forma de um catenary invertida se beneficia de uma distribuição igual de pressão do peso de um arch. O catenary tem uma descrição de matemática aparentemente simple:

`y = a · cosh(x / a)`

O *cosh* é a função cosseno hiperbólico. Para *x* igual a 0, *cosh* for zero e *y* é igual a *um*. Que é o centro do catenary. Como o *cosseno* função, *cosh* deve ser *mesmo*, o que significa que *cosh(–x)* é igual a *cosh(x)* , e valores aumentam para aumentar os argumentos de positivos ou negativos. Esses valores descrevem as curvas que formam os lados do catenary.

Localizando o valor apropriado de *um* de acordo com o catenary para as dimensões da página do telefone não é um cálculo direto. Se *w* e *h* a largura e altura de um retângulo, o valor ideal de *um* satisfaz a seguinte equação:

`cosh(w / 2 / a) = 1 + h / a`

O seguinte método as [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora essa igualdade referindo-se a duas expressões à esquerda e à direita do sinal de igual como `left` e `right`. Para valores pequenos de *uma*, `left` é maior que `right`; para valores grandes de *um*, `left` é menor que `right`. O `while` loop é restrita em um valor ideal de *um*:

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

O `SKPath` do objeto para os links é criado no construtor da classe e o resultante `SKPathEffect` objeto é definido como o `PathEffect` propriedade do `SKPaint` objeto armazenado como um campo:

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

A principal tarefa do `PaintSurface` manipulador é criar um caminho para o catenary em si. Depois de determinar o ideal *uma* e armazená-lo no `optA` variável, ele também precisa calcular um deslocamento da parte superior da janela. Em seguida, ele pode se acumular uma coleção de `SKPoint` valores para catenary, transformar isso em um caminho e desenhe o caminho com criado anteriormente `SKPaint` objeto:

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

Este programa define o caminho usado no `Create1DPath` ter seus (0, 0) no centro do ponto. Isso parece razoável porque a (0, 0) ponto do caminho é alinhado com a linha ou curva que ele é adornado. No entanto, você pode usar não centralizada (0, 0) aponte para alguns efeitos especiais.

O **Esteira** página cria um caminho que se assemelha a uma esteira alongada com uma curva superior e inferior é dimensionado para as dimensões da janela. Esse caminho é traçado com um simples `SKPaint` 20 pixels de largura e cor cinza de objeto e traçados novamente com outra `SKPaint` do objeto com um `SKPathEffect` objeto fazendo referência a um caminho que se assemelha a um bucket pouco:

[![Captura de tela tripla da página da correia do esteira](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

A (0, 0) ponto do caminho do bucket é o identificador, portanto, quando o `phase` argumento é animado, os buckets parecem giram em torno a Esteira talvez escopo backup água na parte inferior e despejar-out na parte superior.

O [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implementa a animação com substituições dos `OnAppearing` e `OnDisappearing` métodos. O caminho para o bucket é definido no construtor da página:

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

O código de criação de bucket é concluída com duas transformações que tornam o bucket um pouco maior e ativá-lo para os lados. Aplicar essas transformações era mais fácil do que ajustar todas as coordenadas no código anterior.

O `PaintSurface` manipulador começa definindo um caminho para a esteira em si. Isso é simplesmente um par de linhas e um par de vírgulas círculos desenhados com uma linha cinza escuro de 20 pixels de largura:

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

A lógica para desenhar a Esteira não funciona no modo paisagem.

Os buckets devem ser espaçados de 200 pixels de distância sobre a Esteira. No entanto, a Esteira provavelmente não é um múltiplo de 200 pixels longas, que significa que, como o `phase` argumento de `SKPathEffect.Create1DPath` é animada, buckets serão exibida dentro e fora de existência.

Por esse motivo, o programa primeiro calcula um valor chamado `length` que é o comprimento da Esteira. Como a Esteira consiste em linhas retas e círculos ponto e vírgula, isso é um cálculo simples. Em seguida, o número de buckets é calculado dividindo `length` por 200. Isso é arredondado para o inteiro mais próximo, e se o número, em seguida, é dividido em `length`. O resultado é um espaçamento para um número integral de buckets. O `phase` argumento é simplesmente uma fração do que.

## <a name="from-path-to-path-again"></a>Do caminho para o caminho novamente

Na parte inferior a `DrawSurface` manipulador no **Esteira**, comente o `canvas.DrawPath` chamar e substituí-lo com o código a seguir:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Assim como acontece com o exemplo anterior de `GetFillPath`, você verá que os resultados são os mesmos, exceto para a cor. Depois de executar `GetFillPath`, o `newPath` objeto contiver várias cópias do caminho de bucket, cada posicionado no mesmo especiais que a animação posicionado no momento da chamada.

## <a name="hatching-an-area"></a>Uma área de hachura

O [ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) método preenche uma área com linhas paralelas, geralmente chamado *linhas de hachura*. O método tem a seguinte sintaxe:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

O `width` argumento especifica a largura do traço das linhas de hachura. O `matrix` parâmetro é uma combinação de rotação de colocação em escala e opcional. O fator de escala indica o incremento de pixel Skia usa para as linhas de hachura de espaço. A separação entre as linhas é o fator de escala menos o `width` argumento. Se o fator de escala é menor ou igual ao `width` valor, não haverá nenhum espaço entre as linhas de hachura e aparecerá a área a ser preenchido. Especifique o mesmo valor para o dimensionamento horizontal e vertical.

Por padrão, as linhas de hachura são horizontais. Se o `matrix` parâmetro contém rotação, as linhas de hachura são giradas no sentido horário.

O **hachura preenchimento** página demonstra esse efeito de caminho. O [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe define três efeitos de caminho como campos, o primeiro para linhas de hachura horizontal com uma largura de 3 pixels com um fator escala indicando que eles estão espaçados 6 pixels de distância. A separação entre as linhas, portanto, é três pixels. O segundo efeito de caminho é para linhas de hachura vertical com uma largura de seis pixels espaçados 24 pixels de distância (portanto, a separação é 18 pixels), e o terceiro é de 12 pixels largos espaçados 36 pixels de distância de linhas de Hachura diagonal.

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

Observe a matriz `Multiply` método. Como os fatores de dimensionamento horizontais e verticais são os mesmos, não importa a ordem em que as matrizes de escala e rotação são multiplicadas.

O `PaintSurface` manipulador usa esses efeitos de três caminho com três cores diferentes em combinação com `fillPaint` para preencher um retângulo arredondado dimensionado para se ajustar à página. O `Style` propriedade definida em `fillPaint` é ignorado; quando o `SKPaint` um efeito de caminho criado a partir do objeto inclui `SKPathEffect.Create2DLine`, a área é preenchida independentemente:

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

Se você examinar cuidadosamente os resultados, você verá que as linhas de hachura de vermelho e azul não estão restritos precisamente para o retângulo arredondado. (Isso é, aparentemente, uma característica do código subjacente Skia.) Se isso não for satisfatório, uma abordagem alternativa será mostrada para as linhas de hachura diagonal em verde: O retângulo arredondado é usado como um caminho de recorte e as linhas de hachura são desenhadas na página inteira.

O `PaintSurface` manipulador conclui com uma chamada para simplesmente traçar o retângulo arredondado, para que você possa ver a discrepância com as linhas de hachura de vermelho e azul:

[![Captura de tela tripla da página preenchimento de hachura](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

A tela do Android não tem a seguinte aparência: O dimensionamento da captura de tela fez com que as linhas vermelhas finas e os espaços finos essolidem em linhas vermelhas mais largas e espaços mais largos.

## <a name="filling-with-a-path"></a>Preenchendo com um caminho

O [ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) em vigor permite preencher uma área com um caminho que é replicado horizontalmente e verticalmente, lado a lado da área de:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

O `SKMatrix` fatores de dimensionamento indicam o espaçamento horizontal e vertical do caminho replicado. Mas você não pode girar o caminho usando esse `matrix` argumento; se você quiser que o caminho girado, girar o próprio caminho usando o `Transform` método definido pelo `SKPath`.

O caminho replicado normalmente é alinhado com as bordas esquerdas e superior da tela, em vez da área sendo preenchida. Você pode substituir esse comportamento fornecendo fatores de conversão entre 0 e os fatores de dimensionamento para especificar deslocamentos horizontais e verticais dos lados superior e esquerdos.

O **caminho bloco preenche** página demonstra esse efeito de caminho. O caminho usado para a área de lado a lado é definido como um campo de [ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. As coordenadas horizontais e verticais variam desde –40 até 40, que significa que esse caminho é 80 pixels quadrados:

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

No `PaintSurface` manipulador, o `SKPathEffect.Create2DPath` chamadas define o espaçamento horizontal e vertical para 64 para fazer com que os blocos de 80 pixels quadrados se sobreponham. Felizmente, o caminho é semelhante a uma parte do quebra-cabeça, criar malhas muito bem com adjacente blocos:

[![Captura de tela tripla da página de preenchimento do bloco caminho](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

A colocação em escala na captura de tela original faz com que alguns distorção, particularmente na tela do Android.

Observe que esses blocos sempre aparecem todos e nunca são truncados. Nos dois primeiros capturas de tela, não é mesmo evidente que a área sendo preenchida é um retângulo arredondado. Se você deseja truncar desses blocos para uma área específica, use um caminho de recorte.

Tente definir a `Style` propriedade do `SKPaint` do objeto para `Stroke`, e você verá os blocos individuais descritos em vez de ser preenchido.

Também é possível preencher uma área com um bitmap lado a lado, conforme mostrado no artigo [ **lado a lado o bitmap de SkiaSharp**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Arredondando cantos

O **Heptágono arredondado** programa apresentado na [ **três formas para desenhar um arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo usado um arco tangente para os pontos de uma figura de face sete de curva. O **Heptágono outro arredondado** página mostra uma abordagem muito mais fácil que usa um efeito de caminho criado a partir de [ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Embora o único argumento seja denominado `radius`, você deve configurá-lo para metade o raio do canto desejado. (Essa é uma característica do código subjacente Skia.)

Aqui está o `PaintSurface` manipulador na [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

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

Você pode usar esse efeito com traçar ou preencher com base nas `Style` propriedade do `SKPaint` objeto. Aqui ele está em execução:

[![Captura de tela tripla da outra página de heptagon arredondada](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Você verá que esse arredondado Heptágono é idêntico ao programa anterior. Se você precisar convencer mais que o raio do canto é verdadeiramente 100 em vez dos 50 especificado no `SKPathEffect.CreateCorner` chamada, você pode remover o comentário a instrução final no programa e consulte um círculo de raio de 100 sobreposta no canto.

## <a name="random-jitter"></a>Variação aleatória

Às vezes, as linhas retas impecável dos gráficos de computador não são exatamente o que você deseja, e uma pequena aleatoriedade é desejada. Nesse caso, você desejará experimentar as [ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Você pode usar esse efeito de caminho para traçar ou preenchimento. Linhas são separadas em segmentos conectados — o tamanho aproximado dos quais é especificado pelo `segLength` — e estender em direções diferentes. A extensão do desvio da linha original é especificada pelo `deviation`.

O argumento final é uma semente usada para gerar a sequência pseudoaleatória usada para o efeito. O efeito de tremulação terá uma aparência um pouco diferente para diferentes sementes. O argumento tem um valor padrão de zero, o que significa que o efeito é o mesmo sempre que você executar o programa. Se você quiser tremulação diferente sempre que a tela é redesenhada, você pode definir a semente para o `Millisecond` propriedade de um `DataTime.Now` valor (por exemplo).


O **tremulação experimentar** página lhe permite fazer experiências com valores diferentes nas traçar um retângulo:

[![Captura de tela tripla da página JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

O programa é simples. O [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) arquivo instancia dois `Slider` elementos e um `SKCanvasView`:

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

O `PaintSurface` manipulador na [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) arquivo code-behind é chamado sempre que um `Slider` o valor é alterado. Ele chama `SKPathEffect.CreateDiscrete` usando os dois `Slider` valores e usa isso para traçar um retângulo:

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

Você pode usar esse efeito para preencher também, caso em que o contorno da área preenchida está sujeita a esses desvios aleatórios. O **tremulação texto** página demonstra como usar esse efeito de caminho para exibir o texto. A maioria do código a `PaintSurface` manipulador do [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe é dedicado ao dimensionamento e centralizar o texto:

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

Aqui ele está em execução no modo paisagem:

[![Captura de tela tripla da página JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Caminho de estrutura de tópicos

Você já viu dois exemplos pouco a [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) método de `SKPaint`, qual existe a duas versões:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Somente os primeiros dois argumentos são necessários. O método acessa o caminho referenciado pela `src` argumento, modifica os dados de caminho com base nas propriedades de traço na `SKPaint` objeto (incluindo o `PathEffect` propriedade) e, em seguida, grava os resultados no `dst` caminho. O `resScale` parâmetro permite reduzir a precisão para criar um caminho de destino menor e o `cullRect` argumento pode eliminar delimitações fora de um retângulo.

Um uso básico desse método não envolve efeitos de caminho: Se o `SKPaint` objeto tiver sua `Style` propriedade definida como `SKPaintStyle.Stroke` `PathEffect`enão tiver seu conjunto, o criaráumcaminhoquerepresentaumcontornodocaminhodeorigemcomosetivessesidotraçado`GetFillPath` pelo Propriedades de pintura.

Por exemplo, se o `src` caminho é um círculo simples do radius 500 e o `SKPaint` objeto Especifica a largura do traço de 100, então o `dst` caminho se torne dois círculos concêntricos, uma com um raio de 450 e outra com um raio de 550. O método é chamado `GetFillPath` porque preencher esse `dst` caminho é o mesmo que traça o `src` caminho. Mas você também pode traçar o `dst` caminho para ver os contornos do caminho.

O **toque, a estrutura de tópicos, o caminho** demonstra isso. O `SKCanvasView` e `TapGestureRecognizer` são instanciadas na [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) arquivo. O [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) arquivo code-behind define três `SKPaint` objetos como campos, dois para traçar com traçar as larguras de 100 e 20 e o terceiro para preenchimento:

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

Se a tela não tiver sido tocada, o `PaintSurface` manipulador usa o `blueFill` e `redThickStroke` pintar objetos para processar um caminho circular:

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

O círculo é preenchido e traçado conforme o esperado:

[![Captura de tela tripla do toque normal para descrever a página do caminho](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Quando você toca a tela `outlineThePath` é definido como `true`e o `PaintSurface` manipulador cria uma nova `SKPath` do objeto e a usará como o caminho de destino em uma chamada para `GetFillPath` no `redThickStroke` objeto de pintura. Esse caminho de destino, em seguida, é preenchido e traçado com `redThinStroke`, resultando no seguinte:

[![Captura de tela tripla do toque para descrever a página do caminho](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Os dois círculos vermelhos indicam claramente que o caminho circular original foi convertido em dois contornos circulares.

Esse método pode ser muito útil no desenvolvimento de caminhos a serem usados para o `SKPathEffect.Create1DPath` método. Os caminhos que você especifica nesses métodos sempre são preenchidos quando os caminhos são replicados. Se não quiser que todo o caminho a ser preenchido, você deve definir cuidadosamente as estruturas de tópicos.

Por exemplo, nos **vinculado cadeia** exemplo, os links foram definidos com uma série de quatro arcos, cada par dos quais eram baseadas em dois raios descrever a área do caminho a ser preenchido. É possível substituir o código no `LinkedChainPage` classe fazê-lo um pouco diferente.

Primeiro, você desejará redefinir o `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

O `linkPath` é agora apenas dois arcos com base no que radius único, com os detalhes desejados iniciar ângulos e ângulos de varredura:

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

O `outlinePath` objeto, em seguida, é o destinatário do contorno da `linkPath` quando ela é traçada com as propriedades especificadas na `strokePaint`.

Outro exemplo, usando essa técnica está por vir para o caminho usado em um método.

## <a name="combining-path-effects"></a>Combinando os efeitos de caminho

Os dois métodos de criação estáticos final da `SKPathEffect` estão [ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) e [ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ambos os métodos de combinam dois efeitos de caminho para criar um efeito de caminho de composição. O `CreateSum` método cria um efeito de caminho que é semelhante aos efeitos dois caminho aplicados separadamente, enquanto `CreateCompose` aplica-se um efeito de caminho (a `inner`) e, em seguida, aplica o `outer` para que.

Você já viu como o `GetFillPath` método de `SKPaint` pode converter um caminho para outro caminho com base em `SKPaint` propriedades (incluindo `PathEffect`) para que ele não deve ser *muito* misterioso como um `SKPaint`objeto pode executar essa operação duas vezes com os efeitos de dois caminho especificados na `CreateSum` ou `CreateCompose` métodos.

Um uso óbvio `CreateSum` é definir um `SKPaint` objeto que preenche um demarcador com efeito de um caminho e traçados o caminho com outro efeito de caminho. Isso é demonstrado na **gatos no quadro** exemplo, que exibe uma matriz de gatos dentro de um quadro com bordas-recorte:

[![Captura de tela tripla da página de gatos no quadro](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

O [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe começa definindo vários campos. Você pode reconhecer o primeiro campo do [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe os [ **dados de caminho SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) artigo. O segundo caminho se baseia em uma linha e um arco para o padrão de Guirlanda do quadro:

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

O `catPath` poderiam ser usados em de `SKPathEffect.Create2DPath` método se o `SKPaint` objeto `Style` estiver definida como `Stroke`. No entanto, se o `catPath` é usado diretamente neste programa, em seguida, o cabeçalho inteiro do gato será preenchido e caixas estreitas até mesmo não ficarão visíveis. (Experimente!) É necessário obter a estrutura de tópicos do caminho e usar essa estrutura de tópicos no `SKPathEffect.Create2DPath` método.

O construtor faz esse trabalho. Primeiro, ele aplica duas transformações para `catPath` para mover a (0, 0) aponte para o centro e reduzi-lo em tamanho. `GetFillPath` obtém todos os contornos dos contornos em `outlinedCatPath`, e esse objeto é usado no `SKPathEffect.Create2DPath` chamar. O dimensionamento considera o `SKMatrix` valor são um pouco maiores do que a horizontal e o tamanho vertical do gato para fornecer um buffer pequeno entre os blocos, enquanto os fatores de conversão foram derivadas um pouco Empiricamente, para que um gato completo está visível no canto superior esquerdo do quadro:

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

Em seguida, chama o construtor `SKPathEffect.Create1DPath` para o quadro-recorte. Observe que a largura do caminho é de 100 pixels, mas o avanço é 75 pixels, de modo que o caminho replicado é sobreposto ao redor do quadro. A instrução final das chamadas de construtor `SKPathEffect.CreateSum` para combinar os efeitos de dois caminho e definiu o resultado para o `SKPaint` objeto.

Todo esse trabalho permite que o `PaintSurface` manipulador seja bastante simples. Ele só precisa definir um retângulo e desenhá-lo usando `framePaint`:

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

Os algoritmos os efeitos de caminho sempre fazer com que o caminho inteiro usado para traçar ou preenchimento a ser exibido, que pode causar alguns elementos visuais sejam exibidos fora do retângulo. O `ClipRect` chamar antes do `DrawRect` chamada permite que os visuais para ser consideravelmente mais limpo. (Experimente sem recorte!)

É comum usar `SKPathEffect.CreateCompose` adicionar alguma tremulação a outro efeito de caminho. Certamente, você pode experimentar por conta própria, mas aqui está um exemplo um pouco diferente:

O **hachura tracejadas** preenche uma elipse com linhas de hachura são tracejadas. A maior parte do trabalho na [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe é executada diretamente nas definições de campo. Esses campos definem um efeito de traço e um efeito de hachura. Eles são definidos como `static` porque eles são referenciados em um `SKPathEffect.CreateCompose` chamar a `SKPaint` definição:

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

O `PaintSurface` manipulador deve conter apenas a sobrecarga padrão além de uma chamada para `DrawOval`:

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

Como você já descobriu, as linhas de hachura não são precisamente restritas ao interior da área e, neste exemplo, eles sempre começam na parte esquerda com um traço inteiro:

[![Captura de tela tripla da página linhas de hachura tracejada](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Agora que você já viu que variam de simples pontos e traços a combinações estranhas para efeitos de caminho, use sua imaginação e veja o que você pode criar.



## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
