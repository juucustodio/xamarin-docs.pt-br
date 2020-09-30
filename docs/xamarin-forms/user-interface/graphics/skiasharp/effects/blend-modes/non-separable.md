---
title: Os modos de mesclagem não separáveis
description: Use os modos de combinação não separáveis para alterar matiz, saturação ou luminosidade.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a77ebb07a09c1bbd2df482c81040f271cdf8f56e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556341"
---
# <a name="the-non-separable-blend-modes"></a>Os modos de mesclagem não separáveis

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como vimos no artigo [**SkiaSharp separáveis Blend Modes**](separable.md), os modos de mesclagem separáveis executam operações nos canais vermelho, verde e azul separadamente. Os modos de combinação não separáveis não são. Ao operar nos níveis de matiz, saturação e luminosidade de cor, os modos de combinação não separáveis podem alterar as cores de maneiras interessantes:

![Exemplo não separáveis](non-separable-images/NonSeparableSample.png "Exemplo não separáveis")

## <a name="the-hue-saturation-luminosity-model"></a>O modelo matiz-saturação-luminosidade

Para entender os modos de combinação não separáveis, é necessário tratar os pixels de destino e de origem como cores no modelo matiz-saturação-luminosidade. (A luminosidade também é conhecida como claridade).

O modelo de cores HSL foi discutido no artigo [**integração com Xamarin.Forms **](../../basics/integration.md) o e um programa de exemplo nesse artigo permite a experimentação com cores HSL. Você pode criar um `SKColor` valor usando valores de matiz, saturação e luminosidade com o método estático [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) .

O matiz representa o comprimento de onda dominante da cor. Os valores de matiz variam de 0 a 360 e percorre os primários aditivos e subtraídos: vermelho é o valor 0, amarelo é 60, verde é 120, ciano é 180, azul é 240, magenta é 300 e o ciclo volta para o vermelho às 360.

Se não houver nenhuma cor dominante &mdash; , por exemplo, a cor será branca ou preta ou uma sombra cinza &mdash; , o matiz será indefinido e, normalmente, definido como 0. 

Os valores de saturação podem variar de 0 a 100 e indicam a pureza da cor. Um valor de saturação de 100 é a cor mais pura, enquanto os valores inferiores a 100 fazem com que a cor se torne mais grayish. Um valor de saturação de 0 resulta em um tom de cinza.

O valor de luminosidade (ou claridade) indica quão claro a cor é. Um valor de luminosidade 0 é preto, independentemente das outras configurações. Da mesma forma, um valor de luminosidade de 100 é branco. 

O valor HSL (0, 100, 50) é o valor RGB (FF, 00, 00), que é vermelho puro. O valor HSL (180, 100, 50) é o valor RGB (00, FF, FF), ciano puro. À medida que a saturação é reduzida, o componente de cor dominante diminui e os outros componentes são aumentados. Em um nível de saturação igual a 0, todos os componentes são os mesmos e a cor é uma sombra cinza. Diminuir a luminosidade para ir para preto; Aumente a luminosidade para ir para branco.

## <a name="the-blend-modes-in-detail"></a>Os modos de mesclagem em detalhes

Como os outros modos de mesclagem, os quatro modos de combinação não separáveis envolvem um destino (que geralmente é uma imagem de bitmap) e uma fonte, que geralmente é uma única cor ou um gradiente. Os modos de mesclagem combinam valores de matiz, saturação e luminosidade do destino e da origem:

| Modo de mesclagem   | Componentes da origem | Componentes do destino |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Matiz                    | Saturação e luminosidade   |
| `Saturation` | Saturação             | Matiz e luminosidade          |
| `Color`      | Matiz e saturação     | Luminosidade                  | 
| `Luminosity` | Luminosidade             | Matiz e saturação          | 

Consulte a especificação W3C de [**composição e mesclagem nível 1**](https://www.w3.org/TR/compositing-1/) para os algoritmos.

A página **modos de mesclagem não separáveis** contém um `Picker` para selecionar um desses modos de mesclagem e três `Slider` exibições para selecionar uma cor HSL:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Para economizar espaço, as três `Slider` exibições não são identificadas na interface do usuário do programa. Você precisará se lembrar de que a ordem é matiz, saturação e luminosidade. Duas `Label` exibições na parte inferior da página mostram os valores de cor HSL e RGB.

O arquivo code-behind carrega um dos recursos de bitmap, exibe o máximo possível na tela e, em seguida, aborda a tela com um retângulo. A cor do retângulo é baseada nas três `Slider` exibições e o modo de mesclagem é aquele selecionado no `Picker` :

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Observe que o programa não exibe o valor de cor HSL conforme selecionado pelos três controles deslizantes. Em vez disso, ele cria um valor de cor desses controles deslizantes e, em seguida, usa o [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) método para obter os valores de matiz, saturação e luminosidade. Isso ocorre porque o `FromHsl` método converte uma cor HSL em uma cor RGB, que é armazenada internamente na `SKColor` estrutura. O `ToHsl` método converte de RGB em HSL, mas o resultado nem sempre será o valor original. 

Por exemplo, `FromHsl` converte o valor HSL (180, 50, 0) na cor RGB (0, 0, 0) porque o `Luminosity` é zero. O `ToHsl` método converte a cor RGB (0,0) para a cor HSL (0, 0, 0) porque os valores de matiz e de saturação são irrelevantes. Ao usar esse programa, é melhor que você veja a representação da cor HSL que o programa está usando, e não aquele que você especificou com os controles deslizantes.

O `SKBlendModes.Hue` modo de mesclagem usa o nível de matiz da origem, mantendo os níveis de saturação e luminosidade do destino. Quando você testa esse modo de mesclagem, os controles deslizantes de saturação e luminosidade devem ser definidos com algo diferente de 0 ou 100 porque, nesses casos, o matiz não é definido exclusivamente.

[![Modos de combinação não separáveis-matiz](non-separable-images/NonSeparableBlendModes-Hue.png "Modos de combinação não separáveis-matiz")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Quando você usa definir o controle deslizante como 0 (como com a captura de tela do iOS à esquerda), tudo fica reddish. Mas isso não significa que a imagem está totalmente ausente em verde e azul. Obviamente, ainda há tonalidades cinzas presentes no resultado. Por exemplo, a cor RGB (40, 40, C0) é equivalente à cor HSL (240, 50, 50). O matiz é azul, mas o valor de saturação de 50 indica que há componentes vermelhos e verdes também. Se o matiz for definido como 0 com `SKBlendModes.Hue` , a cor HSL será (0, 50, 50), que é a cor RGB (C0, 40, 40). Ainda há componentes azuis e verdes, mas agora o componente dominante é vermelho.

O `SKBlendModes.Saturation` modo de mesclagem combina o nível de saturação da origem com o matiz e a luminosidade do destino. Como o matiz, a saturação não será bem definida se a luminosidade for 0 ou 100. Teoricamente, qualquer configuração de luminosidade entre esses dois extremos deve funcionar. No entanto, a configuração de luminosidade parece afetar o resultado mais do que deveria. Defina a luminosidade como 50 e você poderá ver como é possível definir o nível de saturação da imagem:

[![Modos de combinação não separáveis-saturação](non-separable-images/NonSeparableBlendModes-Saturation.png "Modos de combinação não separáveis-saturação")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Você pode usar esse modo de mesclagem para aumentar a saturação de cor de uma imagem sem graça, ou pode diminuir a saturação para zero (como na captura de tela do iOS à esquerda) para uma imagem resultante composta de apenas tons de cinza.

O `SKBlendModes.Color` modo de mesclagem mantém a luminosidade do destino, mas usa o matiz e a saturação da origem. Novamente, isso implica que qualquer configuração do controle deslizante luminosidade em algum lugar entre os extremos deve funcionar. 

[![Modos de mistura não separáveis-cor](non-separable-images/NonSeparableBlendModes-Color.png "Modos de mistura não separáveis-cor")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Você verá um aplicativo desse modo de mesclagem em breve.

Por fim, o `SKBlendModes.Luminosity` modo de mesclagem é o oposto de `SKBlendModes.Color` . Ele retém o matiz e a saturação do destino, mas usa a luminosidade da origem. O `Luminosity` modo de mesclagem é o mais misterioso do lote: os controles deslizantes de matiz e saturação afetam a imagem, mas até mesmo a luminosidade média, a imagem não é distinta:

[![Modos de combinação não separáveis-luminosidade](non-separable-images/NonSeparableBlendModes-Luminosity.png "Modos de combinação não separáveis-luminosidade")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

Teoricamente, aumentar ou diminuir a luminosidade de uma imagem deve torná-la mais clara ou mais escura. Curiosamente, o [exemplo de luminosidade na referência de **SkBlendMode** skia](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) é bastante semelhante.

Geralmente, não é o caso de você querer usar um dos modos de combinação não separáveis com uma fonte que consiste em uma única cor aplicada à imagem de destino inteira. O efeito é muito ótimo. Você desejará restringir o efeito a uma parte da imagem. Nesse caso, a origem provavelmente incorporará transparancy, ou talvez a fonte seja limitada a um elemento gráfico menor.

## <a name="a-matte-for-a-separable-mode"></a>Um fosco para um modo separáveis

Aqui está um dos bitmaps incluídos como um recurso no exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . O nome do arquivo é **Banana.jpg**:

![Macaco banana](non-separable-images/Banana.jpg "Macaco banana")

É possível criar um fosco que abrange apenas o banana. Esse também é um recurso no exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . O nome do arquivo é **BananaMatte.png**:

![Banana fosco](non-separable-images/BananaMatte.png "Banana fosco")

Além da forma banana preta, o restante do bitmap é transparente.

A página **azul banana** usa esse fosco para alterar o matiz e a saturação do banana que o macaco está segurando, mas não alterar nada mais na imagem. 

Na classe a seguir `BlueBananaPage` , o bitmap **Banana.jpg** é carregado como um campo. O Construtor carrega o bitmap **BananaMatte.png** como o `matteBitmap` objeto, mas não retém esse objeto além do construtor. Em vez disso, um terceiro bitmap chamado `blueBananaBitmap` é criado. O `matteBitmap` é desenhado em `blueBananaBitmap` seguido por um `SKPaint` com seu `Color` definido como azul e seu `BlendMode` definido como `SKBlendMode.SrcIn` . O `blueBananaBitmap` permanece mais transparente, mas com uma imagem azul pura sólida do banana:

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

O `PaintSurface` manipulador desenha o bitmap com o macaco mantendo o banana. Esse código é seguido pela exibição de `blueBananaBitmap` com `SKBlendMode.Color` . Na superfície do banana, o matiz e a saturação de cada pixel são substituídos pelo azul sólido, que corresponde a um valor de matiz de 240 e um valor de saturação de 100. No entanto, a luminosidade permanece a mesma, o que significa que o banana continua tendo uma textura realista apesar da nova cor:

[![Banana azul](non-separable-images/BlueBanana.png "Banana azul")](non-separable-images/BlueBanana-Large.png#lightbox)

Tente alterar o modo de mesclagem para `SKBlendMode.Saturation` . O banana permanece amarelo, mas é um amarelo mais intenso. Em um aplicativo da vida real, isso pode ser um efeito mais desejável do que transformar o banana azul.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)