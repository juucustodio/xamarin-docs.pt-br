---
title: Efeitos de caminho
description: Descobrir vários efeitos de caminho que permitem que os caminhos a ser usada para traçar e preenchimento
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 76192f48bedebb183c64c83e34c3908cc85d591c
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="path-effects"></a>Efeitos de caminho

_Descobrir vários efeitos de caminho que permitem que os caminhos a ser usada para traçar e preenchimento_

Um *efeito* é uma instância do [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe que é criado com um dos oito estático `Create` métodos. O `SKPathEffect` objeto é definido como o [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) propriedade de um `SKPaint` objeto para uma variedade de efeitos interessantes, por exemplo, traça uma linha com um pequeno caminho replicado:

![](effects-images/patheffectsample.png "O exemplo de cadeia vinculado")

Efeitos de caminho permitem que você:

- Traçar uma linha com pontos e traços
- Uma linha com qualquer caminho preenchido de traço
- Preencher uma área com linhas de hachura
- Preencher uma área com um caminho lado a lado
- Verifique a nitidez cantos arredondados
- Adicionar aleatório "tremulação", para linhas e curvas

Além disso, você pode combinar duas ou mais efeitos de caminho.

Este artigo também demonstra como usar o `GetFillPath` método `SKPaint` para converter um caminho em outro caminho, aplicando as propriedades de `SKPaint`, incluindo `StrokeWidth` e `PathEffect`. Isso resulta em algumas técnicas interessantes, como obter um caminho que é uma estrutura de tópicos do outro caminho. `GetFillPath` também é útil em conjunto com efeitos de caminho.

## <a name="dots-and-dashes"></a>Pontos e traços

O uso do [ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) método foi descrito no artigo [ **pontos e hífens**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). O primeiro argumento do método é uma matriz que contém um número par de dois ou mais valores, alternando entre tamanhos de traços e comprimentos de lacunas entre os traços:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Esses valores são *não* relativo à largura do traço. Por exemplo, se a largura do traço é 10, e você deseja que uma linha composto de quadrado traços e lacunas quadradas, defina o `intervals` de matriz para {10, 10}. O `phase` argumento indica onde dentro do padrão de traço a linha começa. Neste exemplo, se quiser que a linha comece com a diferença de quadrados, defina `phase` como 10.

As extremidades de traços são afetadas pelo `StrokeCap` propriedade `SKPaint`. Para larguras de traço ampla, é muito comum para definir essa propriedade como `SKStrokeCap.Round` para arredondar as extremidades dos traços. Nesse caso, os valores a `intervals` matriz faça *não* incluem o tamanho extra resultante de arredondamento, que significa que um ponto circular requer a especificação de largura de zero. Para uma largura do traço de 10, para criar uma linha com pontos circulares e lacunas entre os pontos do diâmetro do mesmo, use um `intervals` matriz de {0, 20}.

O **animado texto pontilhado** página é semelhante ao **texto descritas** página descrita no artigo [ **integração texto e elementos gráficos** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) em que exibe descritas caracteres de texto, definindo o `Style` propriedade o `SKPaint` do objeto para `SKPaintStyle.Stroke`. Além disso, **animado texto pontilhado** usa `SKPathEffect.CreateDash` para dar uma aparência pontilhada essa estrutura de tópicos, e o programa também anima a `phase` argumento do `SKPathEffect.CreateDash` método para fazer com que os pontos parecem ser transportados ao redor do texto caracteres. Aqui está a página no modo paisagem:

[![](effects-images/animateddottedtext-small.png "Tripla captura de tela da página de animação texto pontilhado")](effects-images/animateddottedtext-large.png#lightbox "tripla captura de tela da página de animação texto pontilhado")

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

No final do método, o `SKPathEffect.CreateDash` método é chamado usando o `dashArray` que é definido como um campo e a animação `phase` valor. O `SKPathEffect` instância é definida como o `PathEffect` propriedade o `SKPaint` objeto para exibir o texto.

Como alternativa, você pode definir o `SKPathEffect` o objeto para o `SKPaint` objeto antes medindo o texto e centralizá-lo na página. Nesse caso, no entanto, animados pontos e traços causam algumas variações no tamanho do texto renderizado e o texto tende a Vibrar um pouco. (Experimente!)

Você observará que como o círculo animado pontos ao redor de caracteres de texto, há um determinado ponto em cada curva fechada onde os pontos parecem pop dentro e fora de existência. Isso é onde o caminho que define a estrutura de tópicos de caracteres começa e termina. Se o comprimento do caminho não for um múltiplo inteiro do tamanho de padrão de traço (nesse caso, 20 pixels), a única parte desse padrão pode ser ajustadas no final do caminho.

É possível ajustar o comprimento de padrão de traço para ajustar o comprimento do caminho, mas que requer determinar o comprimento do caminho, uma técnica que ser abordadas em um artigo futuro.

O **Dot / traço Morph** programa anima padrão de traço em si para que os traços parecem dividir em pontos, que são combinados para traços de formulário novamente:

[![](effects-images/dotdashmorph-small.png "Tripla captura de tela da página ponto traço Morph")](effects-images/dotdashmorph-large.png#lightbox "tripla captura de tela da página ponto traço Morph")

O [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) substituições de classe a `OnAppearing` e `OnDisappearing` métodos apenas o programa anterior, mas a classe define o `SKPaint` objeto como um campo:

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

O `PaintSurface` manipulador cria um caminho elíptico com base no tamanho da página e executa uma longa seção de código que define o `dashArray` e `phase` variáveis. Como a variável animada `t` varia de 0 a 1, o `if` blocos dividir o momento em quatro trimestres e em cada um desses trimestres `tsub` também varia de 0 a 1. No final, o programa cria o `SKPathEffect` e o define como o `SKPaint` objeto para o desenho.

## <a name="from-path-to-path"></a>Caminho de caminho

O [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) método `SKPaint` transforma um caminho em outro com base nas configurações de `SKPaint` objeto. Para ver como isso funciona, substitua o `canvas.DrawPath` chamar no programa anterior com o código a seguir:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

Este novo código, o `GetFillPath` chamar converte o `ellipsePath` (que é apenas uma elipse) em `newPath`, que é exibido com `newPaint`. O `newPaint` objeto é criado com todas as configurações de propriedade padrão exceto que o `Style` propriedade é definida com base em booliano retornar o valor de `GetFillPath`.

Os elementos visuais são idênticos, exceto a cor, o que é definida no `ellipsePaint` mas não `newPaint`. Em vez de elipse simple definida em `ellipsePath`, `newPath` contém vários contornos do caminho que definem a série de pontos e hífens. Este é o resultado da aplicação de várias propriedades de `ellipsePaint` — `StrokeWidth`, `StrokeCap`, e `PathEffect` — para `ellipsePath` e colocando o caminho resultante `newPath`. O `GetFillPath` método retorna um valor booliano que indica se o caminho de destino é preenchido ou não; neste exemplo, o valor de retorno é `true` para preencher o caminho.

Tente alterar o `Style` definindo em `newPaint` para `SKPaintStyle.Stroke` e você verá o contorno de caminho individuais delimitado por uma linha de um de pixels de largura.

## <a name="stroking-with-a-path"></a>Traça com um caminho

O [ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/) método é conceitualmente semelhante a `SKPathEffect.CreateDash` exceto que você especificar um caminho em vez de um padrão de traços e lacunas. Esse caminho é replicado várias vezes para a linha de traço ou curva.

A sintaxe é:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> Fique atento: há uma sobrecarga de `Create1DPath` que é definida com um argumento de enumeração de tipo `SkPath1DPathEffect` com em letras minúsculas 'k'. Esse nome é um erro e, consequentemente, que enumeração e método foram preteridos, mas é muito fácil para o método preterido para se tornar parte do seu código e é difícil ver exatamente o que há de errado.

Em geral, o caminho que você passa para `Create1DPath` será pequeno e centralizado em torno do ponto (0, 0). O `advance` parâmetro indica a distância entre os centros de caminho, como o caminho é replicado na linha. Normalmente, você definir esse argumento para a largura aproximada do caminho. O `phase` desempenha argumento a mesma função aqui, como ele faz o `CreateDash` método.

O [ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/) tem três membros:

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

O `Translate` membro faz com que o caminho para permanecer na mesma posição que ele seja replicado ao longo de uma linha ou curva. Para `Rotate`, o caminho for girado com base em uma tangente para a curva. O caminho tem sua orientação normal para as linhas horizontais. `Morph` é semelhante ao `Rotate` exceto pelo fato do demarcador em si também está curvado para corresponder a curvatura da linha que está sendo traçada.

O **1 D efeito** página demonstra essas três opções. O [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) arquivo define um seletor que contém três itens correspondentes aos três membros da enumeração:

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
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
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

O [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) arquivo code-behind define três `SKPathEffect` objetos como campos. Eles são criados usando `SKPathEffect.Create1DPath` com `SKPath` os objetos criados com `SKPath.ParseSvgPathData`. A primeira é uma caixa simple, o segundo é uma forma de losango e o terceiro é um retângulo. Eles são usados para demonstrar os estilos de três efeito:

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

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
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

O `PaintSurface` manipulador cria uma curva de Bézier que executa um loop em torno de si mesmo e acessa o seletor para determinar qual `PathEffect` deve ser usada para traçar a ele. As três opções — `Translate`, `Rotate`, e `Morph` — são mostrados da esquerda para a direita:

[![](effects-images/1dpatheffect-small.png "Captura de tela da página 1-D caminho efeito tripla")](effects-images/1dpatheffect-large.png#lightbox "tripla captura de tela da página 1-D caminho efeito")

O caminho especificado no `SKPathEffect.Create1DPath` método sempre é preenchido. O caminho especificado no `DrawPath` método sempre é traçado se o `SKPaint` objeto tem seu `PathEffect` propriedade definida como um efeito de caminho 1D. Observe que o `pathPaint` objeto não tiver nenhuma `Style` configuração, que normalmente usa como padrão `Fill`, mas o caminho é traçado independentemente.

A caixa de usado no `Translate` exemplo é 20 pixels quadrados e o `advance` argumento é definido como 24. Essa diferença faz com que um intervalo entre as caixas quando a linha é aproximadamente vertical ou horizontal, mas as caixas sobreponham um pouco quando a linha é diagonal porque diagonal da caixa é 28.3 pixels. 

A forma de losango no `Rotate` exemplo também é 20 pixels de largura. O `advance` é definido como 20 para que os pontos de continuam para toque, pois o losango for girado juntamente com a curvatura da linha.

A forma de retângulo no `Morph` exemplo é 50 pixels de largura com um `advance` configuração de 55 para fazer um pequeno intervalo entre os retângulos como eles são torto em torno da curva de Bézier.

Se o `advance` argumento for menor que o tamanho do caminho, em seguida, os caminhos duplicados podem se sobrepor. Isso pode resultar em alguns efeitos interessantes. O **cadeia vinculado** página exibe uma série de círculos que parecem ser semelhante a uma cadeia de vinculado, paradas de forma diferente de um catenary de sobreposição:

[![](effects-images/linkedchain-small.png "Tripla captura de tela da página cadeia vinculado")](effects-images/linkedchain-large.png#lightbox "tripla captura de tela da página cadeia vinculado")

Procure muito próxima e você verá que esses não são realmente círculos. Cada link na cadeia é dois arcos dimensionado e posicionado para que eles parecem se conectar com links adjacente.

Uma cadeia ou o cabo da distribuição uniforme de peso trava na forma de um catenary. Um arch criado na forma de um catenary invertido se beneficia de uma distribuição uniforme de pressão de peso de um arco. O catenary tem uma descrição de matemática aparentemente simple:

y = um · COSH(x / a)

O *cosh* é a função cosseno hiperbólico. Para *x* igual a 0, *cosh* é igual a zero e *y* é igual a *um*. Que é o centro da catenary. Como o *cosseno* função *cosh* será considerada *mesmo*, o que significa que *cosh(–x)* é igual a *cosh(x)*, e aumentam os valores para aumentar os argumentos positivos ou negativos. Esses valores descrevem as curvas que formam os lados do catenary.

Localizando o valor correto de *um* ajustar o catenary para as dimensões de página do telefone não é um cálculo direto. Se *w* e *h* a largura e altura de um retângulo, o valor ideal de *um* satisfaz a seguinte equação:

COSH (w/2/a) = 1 + h / a

O método a seguir no [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora que Igualdade consultando as duas expressões à esquerda e à direita do sinal de igual como `left` e `right`. Para valores pequenos de *um*, `left` é maior do que `right`; para valores grandes de *um*, `left` é menor que `right`. O `while` loop restringe em um valor ideal de *um*:

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

O `SKPath` para os links é criado no construtor da classe e os resultantes do objeto `SKPathEffect` objeto é definido como o `PathEffect` propriedade o `SKPaint` objeto que é armazenado como um campo:

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

O trabalho principal do `PaintSurface` manipulador é criar um caminho para o catenary em si. Depois de determinar o ideal *um* e armazená-lo no `optA` variável, ele também precisa calcular um deslocamento da parte superior da janela. Em seguida, ele pode acumular uma coleção de `SKPoint` valores catenary, fazer isso em um caminho e desenhar o caminho com criado anteriormente `SKPaint` objeto:

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

Este programa define o caminho usado no `Create1DPath` ter suas (0, 0) no centro do ponto. Isso parece razoável porque o (0, 0) ponto do caminho está alinhado com a linha ou curva é adorno. No entanto, você pode usar não centralizada (0, 0) de pontos de efeitos especiais.

O **Esteira** página cria um caminho semelhante a uma faixa de transportadora alongada com uma curva superior e inferior, esse valor é dimensionado para as dimensões da janela. Esse caminho é traçado com um simples `SKPaint` objeto 20 pixels de largura e cinza colorida e traçados novamente com outra `SKPaint` do objeto com um `SKPathEffect` objeto fazendo referência a um caminho semelhante a um bucket pouco:

[![](effects-images/conveyorbelt-small.png "Captura de tela da página Esteira tripla")](effects-images/conveyorbelt-large.png#lightbox "tripla captura de tela da página Esteira")

A (0, 0) de ponto de caminho o bucket é o identificador, portanto, quando o `phase` argumento é animado, os buckets parecem giram em torno de Esteira talvez escopo backup água na parte inferior e descarte-o na parte superior.

O [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implementa animação com substituições do `OnAppearing` e `OnDisappearing` métodos. O caminho para o bucket é definido no construtor da página:

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

O código de criação de bucket é concluído com duas transformações que tornam o bucket um pouco maior e ativá-la para os lados. Aplicar essas transformações foi mais fácil do que o ajuste de todas as coordenadas no código anterior. 

O `PaintSurface` manipulador começa com a definição de um caminho para a faixa de transportadora em si. Isso é simplesmente um par de linhas e um par de ponto-e-círculos desenhadas com uma linha cinza escuro de 20 pixels de largura:

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

Os buckets devem ser espaçados de 200 pixels de distância em que a faixa da transportadora. No entanto, a faixa de transportadora provavelmente não é um múltiplo de 200 pixels longos, que significa que, como o `phase` argumento de `SKPathEffect.Create1DPath` é animada, buckets serão exibida dentro e fora de existência. 

Por esse motivo, o programa primeiro calcula um valor chamado `length` que é o comprimento da faixa de transportadora. Como a Esteira consiste em linhas retas e ponto-e-círculos, isso é um cálculo simple. Em seguida, o número de buckets é calculado pela divisão `length` por 200. Isso é arredondado para o inteiro mais próximo e que número é dividido em `length`. O resultado é um espaçamento para um número integral de buckets. O `phase` é simplesmente uma fração do que.

## <a name="from-path-to-path-again"></a>Caminho para o caminho novamente

Na parte inferior da `DrawSurface` manipulador no **Esteira**, comente a `canvas.DrawPath` chamar e substituí-lo com o código a seguir:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Assim como acontece com o exemplo anterior de `GetFillPath`, você verá que os resultados são os mesmos, exceto a cor. Depois de executar `GetFillPath`, o `newPath` objeto contiver várias cópias do caminho bucket, cada posicionado na mesma especiais que a animação posicionado no momento da chamada.

## <a name="hatching-an-area"></a>Uma área de hachura

O [ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/) método preenche uma área com linhas paralelas, geralmente chamado *hachura linhas*. O método tem a seguinte sintaxe:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

O `width` argumento especifica a largura do traço das linhas de hachura. O `matrix` parâmetro é uma combinação de rotação de dimensionamento e opcional. O fator de escala indica o incremento de pixel Skia usa para as linhas de hachura de espaço. A separação entre as linhas é o fator de escala menos o `width` argumento. Se o fator de escala é menor ou igual a `width` valor, não haverá nenhum espaço entre as linhas de hachura e a área será exibida a ser preenchida. Especifique o mesmo valor para a expansão horizontal e vertical. 

Por padrão, as linhas de hachura são horizontais. Se o `matrix` parâmetro contém rotação, as linhas de hachura são giradas no sentido horário.

O **hachura de preenchimento** página demonstra o efeito de caminho. O [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe define três efeitos de caminho como campos, o primeiro para as linhas de hachura horizontal com uma largura de 3 pixels com um fator escala indicando que eles são espaçados 6 pixels de distância. A separação entre as linhas, portanto, tem 3 pixels. O efeito de caminho segundo é para linhas de hachura vertical com uma largura de 6 pixels espaçados 24 pixels de distância (portanto a separação é 18 pixels), e a terceira é para linhas de Hachura diagonal 12 pixels largura espaçadas 36 pixels de distância. 

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

Observe a matriz `Multiply` método. Como os fatores de escala horizontais e verticais são os mesmos, não importa a ordem em que as matrizes de escala e rotação são multiplicadas.

O `PaintSurface` manipulador usa esses efeitos de três caminhos com três cores diferentes em combinação com `fillPaint` para preencher um retângulo arredondado dimensionado para caber na página. O `Style` propriedade definida em `fillPaint` é ignorado; quando o `SKPaint` objeto inclui um efeito de caminho criado a partir de `SKPathEffect.Create2DLine`, a área é preenchida independentemente:

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

Se você examinar atentamente os resultados, você verá que as linhas de hachura azul e vermelho não restrita com precisão para o retângulo arredondado. (Isso aparentemente é uma característica do código subjacente Skia.) Se isso não for satisfatória, uma abordagem alternativa é mostrada para as linhas de Hachura diagonal verde: O retângulo arredondado é usado como um demarcador de recorte e as linhas de hachura são desenhadas em toda a página.

O `PaintSurface` manipulador é concluído com uma chamada para traçar simplesmente retângulo arredondado, para que você possa ver a discrepância com as linhas de hachura azul e vermelho:

[![](effects-images/hatchfill-small.png "Captura de tela da página de hachura de preenchimento tripla")](effects-images/hatchfill-large.png#lightbox "tripla captura de tela da página de hachura de preenchimento")

A tela Android realmente não parece assim: O dimensionamento da captura de tela causou linhas finas em vermelho e thin espaços para consolidar em linhas vermelhas aparentemente mais ampla e mais amplo.

## <a name="filling-with-a-path"></a>Preenchendo com um caminho

O [ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/) em vigor permite preencher uma área com um caminho que é replicado horizontalmente e verticalmente, lado a lado da área de:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

O `SKMatrix` fatores de dimensionamento indicam o espaçamento horizontal e vertical do caminho replicado. Mas você não pode girar o caminho usando esse `matrix` argumento; se você quiser que o caminho girado, girar o demarcador em si usando o `Transform` método definido pelo `SKPath`. 

O caminho replicado é alinhado com as bordas esquerda e superiores da tela, em vez da área que está sendo preenchido. Você pode substituir esse comportamento, fornecendo os fatores de conversão entre 0 e os fatores de dimensionamento para especificar deslocamentos horizontal e vertical dos lados esquerdos e superiores.

O **caminho bloco preencher** página demonstra o efeito de caminho. O caminho usado para a área de lado a lado é definido como um campo de [ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. A coordenadas horizontal e vertical variam de –40 a 40, que significa que esse caminho é 80 pixels quadrados: 

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

No `PaintSurface` manipulador, o `SKPathEffect.Create2DPath` chamadas define o espaçamento horizontal e vertical para 64 para fazer com que os blocos de quadrado de pixel de 80 a sobreposição. Felizmente, o caminho é semelhante a uma peça do quebra-cabeça, meshing bem com adjacentes lado a lado:

[![](effects-images/pathtilefill-small.png "Tripla captura de tela da página de preenchimento do caminho bloco")](effects-images/pathtilefill-large.png#lightbox "tripla captura de tela da página de preenchimento do caminho lado a lado")

O dimensionamento da captura de tela original faz com que alguns distorção, particularmente na tela do Android.

Observe que esses blocos sempre aparecem inteiros e nunca são truncados. Sobre as duas primeiras capturas de tela, não é mesmo evidente a área de preenchimento é um retângulo arredondado. Se você deseja truncar esses blocos para uma área específica, use um caminho de recorte.

Tente definir a `Style` propriedade o `SKPaint` do objeto para `Stroke`, e você verá os blocos individuais descritos em vez de ser preenchido.

## <a name="rounding-sharp-corners"></a>Os vértices de arredondamento

O **Heptágono arredondado** programa apresentados a [ **três maneiras para desenhar um arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) artigo usado um arco tangente a curva os pontos de uma figura de face de sete. O **Heptágono outro arredondado** página mostra uma abordagem muito mais fácil que usa um efeito de caminho criado a partir de [ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/) método:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Embora o único argumento é denominado `radius` você deve configurá-lo para metade o raio de canto desejado. (Isso é uma característica do código Skia subjacente).

Aqui está o `PaintSurface` manipulador no [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

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

Você pode usar esse efeito com traçado ou preenchimento com base no `Style` propriedade o `SKPaint` objeto. Aqui está em todas as três plataformas:

[![](effects-images/anotherroundedheptagon-small.png "Tripla captura da página do outro Heptágono arredondado")](effects-images/anotherroundedheptagon-large.png#lightbox "tripla captura da página do outro Heptágono arredondado")

Você verá que este Heptágono arredondado é idêntico ao programa anterior. Se você precisar de mais convencer que o raio de canto é realmente 100 em vez da 50 especificado no `SKPathEffect.CreateCorner` chamada, você pode remover o comentário a instrução final no programa e consulte um círculo de raio de 100 sobreposto no canto.

## <a name="random-jitter"></a>Variação aleatória

Às vezes, as linhas retas sem falhas de elementos gráficos do computador não são exatamente o que você deseja e um pouco aleatoriedade é desejada. Nesse caso, você desejará tente o [ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/) método:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Você pode usar esse efeito de caminho para traçar ou preenchimento. Linhas são separadas em segmentos conectados – o tamanho aproximado que é especificado pelo `segLength` — e estender em direções diferentes. A extensão do desvio da linha original é especificada pelo `deviation`. 

O argumento final é uma semente usada para gerar a sequência pseudo-aleatório usada para o efeito. O efeito de variação parecerá um pouco diferente para propagações diferentes. O argumento tem um valor padrão de zero, o que significa que o efeito é o mesmo sempre que você executar o programa. Se você quiser tremulação diferente sempre que a tela é redesenhada, você pode definir a propagação para o `Millisecond` propriedade de uma `DataTime.Now` valor (por exemplo).


O **tremulação experimentar** página permite fazer experiências com valores diferentes nas traça um retângulo:

[![](effects-images/jitterexperiment-small.png "Captura de tela da página de teste de variação de tripla")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

O programa é straightfoward. O [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) arquivo cria dois `Slider` elementos e um `SKCanvasView`:

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

O `PaintSurface` manipulador no [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) arquivo code-behind é chamado sempre que uma `Slider` o valor é alterado. Ele chama `SKPathEffect.CreateDiscrete` usando os dois `Slider` valores e a usa para traçar um retângulo:

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

Você pode usar esse efeito de preenchimento, caso em que o contorno da área preenchida está sujeito a esses desvios aleatórios. O **texto tremulação** página demonstra como usar esse efeito de caminho para exibir o texto. A maior parte do código no `PaintSurface` manipulador do [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe dedicada ao dimensionamento e centralizar o texto:

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

Aqui, ele é executado no modo paisagem em todas as plataformas de três:

[![](effects-images/jittertext-small.png "Captura de tela da página de texto variação de tripla")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>Caminho de estrutura de tópicos

Você já viu dois exemplos pouco do [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) método `SKPaint`, que também existe em um [sobrecarga](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

Apenas os primeiros dois argumentos são necessários. O método acessa o caminho referenciado pelo `src` argumento, modifica os dados de caminho com base nas propriedades de traço no `SKPaint` objeto (incluindo o `PathEffect` propriedade) e, em seguida, grava os resultados a `dst` caminho. O `resScale` parâmetro permite reduzir a precisão para criar um caminho de destino menor e o `cullRect` argumento pode eliminar delimitações fora de um retângulo.

Um uso básico desse método não abrange os efeitos de caminho. Se o `SKPaint` objeto tem seu `Style` propriedade definida como `SKPaintStyle.Stroke`e *não* tem seu `PathEffect` definida, então `GetFillPath` cria um caminho que representa um *contorno*do caminho de origem como se ele tivesse sido feito pelas propriedades de pintura.

Por exemplo, se o `src` caminho é um círculo simple do radius 500 e o `SKPaint` objeto Especifica a largura do traço de 100, o `dst` caminho se torne dois círculos concêntricos, uma com um raio de 450 e o outro com um raio de 550. O método é chamado `GetFillPath` porque preenchendo isso `dst` caminho é o mesmo que traça o `src` caminho. Mas você também pode traçar o `dst` caminho para ver os contornos do caminho.

O **toque, a estrutura de tópicos, o caminho** demonstra isso. O `SKCanvasView` e `TapGestureRecognizer` são instanciados no [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) arquivo. O [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) arquivo code-behind define três `SKPaint` objetos como campos, duas para traçar com larguras de 100 e 20 e o terceiro para preenchimento de traços:

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

Se a tela não foram tocada, o `PaintSurface` manipulador utiliza o `blueFill` e `redThickStroke` pintar objetos para processar um caminho circular:

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

[![](effects-images/taptooutlinethepathnormal-small.png "Tripla captura de tela da página de toque para estrutura de tópicos o caminho normal")](effects-images/taptooutlinethepathnormal-large.png#lightbox "tripla captura de tela da página de toque para estrutura de tópicos o caminho normal")

Quando você tocar em tela, `outlineThePath` é definido como `true`e o `PaintSurface` manipulador cria uma nova `SKPath` do objeto e a usará como o caminho de destino em uma chamada para `GetFillPath` no `redThickStroke` objeto de pintura. Esse caminho de destino é preenchido e traçado com `redThinStroke`, resultando no seguinte:

[![](effects-images/taptooutlinethepathoutlined-small.png "Tripla captura da página de toque para estrutura de tópicos o caminho do contornada")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "tripla captura da página de toque para estrutura de tópicos o caminho do contornada")

Os dois círculos vermelhos indicam claramente que o caminho circular original foi convertido em dois contornos circulares.

Esse método pode ser muito útil para desenvolvimento de caminhos a serem usados para o `SKPathEffect.Create1DPath` método. Os caminhos que você especificar em que esses métodos são preenchidos sempre quando os caminhos são replicados. Se você não quiser todo o caminho a ser preenchida, você deve definir cuidadosamente as estruturas de tópicos.

Por exemplo, no **cadeia vinculado** exemplo, os links foram definidos com uma série de quatro arcos, cada par de que foram baseadas em dois raios descrever a área do caminho a ser preenchida. É possível substituir o código de `LinkedChainPage` classe isso um pouco diferente.

Primeiro, você desejará redefinir o `linkRadius` constante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

O `linkPath` é agora apenas dois arcos com base no que único radius, com os detalhes desejados iniciar ângulos e ângulos de varredura:

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

O `outlinePath` objeto, em seguida, é o destinatário da estrutura de tópicos do `linkPath` quando ele é traçado com as propriedades especificadas na `strokePaint`. 

Outro exemplo que usa essa técnica é próximos para o caminho usado em um `SKPathEffect.Create2DPath` métodos. 

## <a name="combining-path-effects"></a>Combinando os efeitos de caminho

Os dois métodos de criação estáticos final de `SKPathEffect` são [ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/) e [ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Esses dois métodos combinam dois efeitos de caminho para criar um efeito de caminho composto. O `CreateSum` método cria um efeito de caminho que é semelhante aos efeitos de duas caminho aplicados separadamente, enquanto `CreateCompose` aplica um efeito de caminho (o `inner`) e, em seguida, aplica o `outer` ao.

Você viu como o `GetFillPath` método de `SKPaint` pode converter um caminho para outro caminho com base em `SKPaint` propriedades (incluindo `PathEffect`) para que ele não deve ser *muito* misterioso como um `SKPaint`objeto pode executar essa operação duas vezes com os efeitos de dois caminho especificados no `CreateSum` ou `CreateCompose` métodos.

Um uso óbvio de `CreateSum` é definir um `SKPaint` objeto que preenche um caminho com efeito de um caminho e traçados o caminho com outro efeito de caminho. Isso é demonstrado no **gatos no quadro** sample, que exibe uma matriz de gatos dentro de um quadro com bordas-recorte:

[![](effects-images/catsinframe-small.png "Captura de tela da página gatos no quadro tripla")](effects-images/catsinframe-large.png#lightbox "tripla captura de tela da página gatos no quadro")

O [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe começa com a definição de vários campos. Você pode reconhecer o primeiro campo do [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe o [ **SVG caminho dados** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) artigo. O segundo caminho baseia-se em uma linha e um arco padrão Guirlanda do quadro de:

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

O `catPath` pode ser usado no `SKPathEffect.Create2DPath` método se a `SKPaint` objeto `Style` está definida como `Stroke`. No entanto, se o `catPath` é usado diretamente deste programa, em seguida, o cabeçalho inteiro do gato será preenchido e caixas estreitas ainda não ficarão visíveis. (Experimente!) É necessário obter a estrutura de tópicos do caminho e usar essa estrutura de tópicos no `SKPathEffect.Create2DPath` método.

O construtor faz esse trabalho. Primeiro, ele aplica duas transformações para `catPath` para mover a (0, 0) apontar para o centro e reduzir em tamanho. `GetFillPath` obtém todos os contornos dos contornos em `outlinedCatPath`, e esse objeto é usado no `SKPathEffect.Create2DPath` chamar. O dimensionamento fatores `SKMatrix` valor são um pouco maiores do que a horizontal e vertical tamanho do gato para fornecer um buffer pequeno entre os blocos, enquanto os fatores de conversão foram derivadas um pouco empiricamente para que um gato completo está visível no canto superior esquerdo do quadro:

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

Em seguida, chama o construtor `SKPathEffect.Create1DPath` do quadro-recorte. Observe que a largura do caminho é 100 pixels, mas o avanço é de 75 pixels para que o caminho replicado é sobreposto ao redor do quadro. A instrução final das chamadas de construtor `SKPathEffect.CreateSum` para combinar os efeitos de duas caminho e definir o resultado para o `SKPaint` objeto.

Todo esse trabalho permite o `PaintSurface` manipulador ser bastante simples. Ele só precisa definir um retângulo e desenhe usando `framePaint`:

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

Os algoritmos atrás os efeitos de caminho sempre causam o caminho inteiro usado para traçar ou preenchendo a serem exibidos, que pode fazer com que alguns elementos visuais sejam exibidos fora do retângulo. O `ClipRect` chamada anterior para o `DrawRect` chamada permite que os elementos visuais ser consideravelmente mais limpo. (Experimente sem recorte!)

É comum usar `SKPathEffect.CreateCompose` para adicionar alguns variação para outro efeito de caminho. Certamente, você pode experimentar por conta própria, mas aqui está um exemplo um pouco diferentes:

O **hachura tracejadas** preenche uma elipse com linhas de hachura que são tracejadas. A maior parte do trabalho no [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe é executada imediatamente as definições de campo. Esses campos definem um efeito de traço e um efeito de hachura. Eles são definidos como `static` porque eles são referenciados em um `SKPathEffect.CreateCompose` chamar no `SKPaint` definição:

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

O `PaintSurface` manipulador precisam conter apenas a sobrecarga padrão além de uma chamada para `DrawOval`:

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

Como você já tenha descoberto, as linhas de hachura não são precisamente restritas para o interior da área e, neste exemplo, eles sempre comecem à esquerda com um traço inteiro:

[![](effects-images/dashedhatchlines-small.png "Tripla captura de tela da página tracejado hachura linhas")](effects-images/dashedhatchlines-large.png#lightbox "tripla captura de tela da página tracejado hachura linhas")

Agora que você viu efeitos de caminho que variam de simples pontos e hífens para combinações estranhas, use a imaginação e veja o que você pode criar.



## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
