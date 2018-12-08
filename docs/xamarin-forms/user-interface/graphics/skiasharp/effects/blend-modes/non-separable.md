---
title: Os modos de mesclagem não separáveis
description: Use os modos de mesclagem não separáveis para alterar o matiz, saturação ou luminosidade.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9b94db14a197ca31be42e8712c2170fd66b86579
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055005"
---
# <a name="the-non-separable-blend-modes"></a>Os modos de mesclagem não separáveis

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Como você viu neste artigo [ **SkiaSharp separável modos do blend**](separable.md), os modos do blend separáveis executam operações nos canais de vermelho, verdes e azuis separadamente. Os modos de mesclagem não separáveis não. Por funcionando com os níveis de matiz, saturação e luminosidade da cor, os modos de mesclagem não separáveis podem alterar as cores de maneiras interessantes:

![O exemplo não separáveis](non-separable-images/NonSeparableSample.png "exemplo não separáveis")

## <a name="the-hue-saturation-luminosity-model"></a>O modelo de matiz-saturação-luminosidade

Para entender os modos de mesclagem não separáveis, é necessário tratar os pixels de origem e destino como as cores no modelo de matiz-saturação-luminosidade. (Luminosidade também é chamada para de luminosidade.)

O modelo de cor HSL foi discutido no artigo [ **a integração com o xamarin. Forms** ](../../basics/integration.md) e um programa de exemplo neste artigo permite experimentação com cores HSL. Você pode criar uma `SKColor` valor com os valores de matiz, saturação e luminosidade com estático [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*) método.

O matiz representa o comprimento de onda dominante da cor. Valores de Matiz variam de 0 a 360 e percorrer os primários aditivos e subtrativas: vermelho é o valor 0, amarelo é 60, verde é 120, ciano é 180, azul é 240, magenta é de 300 e o ciclo de volta para vermelho no 360.

Se não houver nenhuma cor dominante &mdash; por exemplo, a cor é em branco ou preto ou um tom de cinza &mdash; em seguida, o matiz é indefinido e geralmente é definido como 0. 

Os valores de saturação podem variar de 0 a 100 e indicam a pureza da cor. Um valor de saturação de 100 é a cor puro, enquanto valores inferiores a 100 fazem com que a cor se torne mais grayish. Um valor de saturação de 0 resulta em um tom de cinza.

O valor de luminosidade (ou luminosidade) indica a cor brilhante como é. Um valor de luminosidade de 0 é preto, independentemente de outras configurações. Da mesma forma, um valor de luminosidade de 100 é branco. 

O valor HSL (0, 100, 50) é o valor RGB (FF, 00 00), que é vermelho puro. O valor HSL (180, 100, 50) é o valor RGB (00, FF, FF), ciano puro. Como a saturação é reduzida, o componente de cor dominante é reduzido, e os outros componentes são aumentados. Em um nível de saturação de 0, todos os componentes são os mesmos e a cor é um tom de cinza. Diminuir a luminosidade para ir para preto; Aumente a luminosidade para ir para branco.

## <a name="the-blend-modes-in-detail"></a>Os modos do blend em detalhes

Como os outros modos de mesclagem, os quatro modos de mesclagem não separáveis envolvem um destino (que geralmente é uma imagem de bitmap) e uma fonte, que é normalmente uma única cor ou um gradiente. Os modos do blend combinam valores de matiz, saturação e luminosidade de destino e de fonte:

| Modo de mesclagem   | Componentes de fonte | Componentes de destino |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Matiz                    | Saturação e luminosidade   |
| `Saturation` | saturação             | Matiz e luminosidade          |
| `Color`      | Matiz e a saturação     | Luminosidade                  | 
| `Luminosity` | Luminosidade             | Matiz e a saturação          | 

Consulte o W3C [ **composição e a combinação do nível 1** ](https://www.w3.org/TR/compositing-1/) especificação para os algoritmos.

O **não separáveis modos do Blend** página contém uma `Picker` para selecionar um desses modos e três do blend `Slider` modos de exibição para selecionar uma cor HSL:

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

Para economizar espaço, os três `Slider` exibições não são identificadas na interface do usuário do programa. Você precisará se lembrar de que a ordem é matiz, saturação e luminosidade. Dois `Label` exibições na parte inferior da página mostram os valores de cor HSL e RGB.

O arquivo code-behind carrega um dos recursos de bitmap, exibe que tão grandes quanto possível na tela e, em seguida, aborda a tela com um retângulo. A cor do retângulo é baseada nas três `Slider` modos de exibição e o modo de mesclagem é selecionado no `Picker`:

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

Observe que o programa não exibirá o valor de cor HSL conforme selecionado pelo três controles deslizantes. Em vez disso, ele cria um valor de cor usando esses controles deslizantes e, em seguida, usa o [ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*) método para obter os valores de matiz, saturação e luminosidade. Isso ocorre porque o `FromHsl` método converte uma cor RGB, que é armazenada internamente em uma cor HSL o `SKColor` estrutura. O `ToHsl` método converte de RGB para HSL, mas o resultado não será sempre o valor original. 

Por exemplo, `FromHsl` converte o valor HSL (180, 50, 0) para a cor RGB (0, 0, 0), porque o `Luminosity` é zero. O `ToHsl` método converte a cor RGB (0, 0, 0) para a cor HSL (0, 0, 0), porque os valores de matiz e a saturação são irrelevantes. Ao usar este programa, é melhor que você vê a representação da cor HSL que o programa está usando em vez daquele especificado com os controles deslizantes.

O `SKBlendModes.Hue` modo de mesclagem usa o nível de matiz da origem enquanto mantém os níveis de saturação e luminosidade do destino. Quando você testar este modo de mesclagem, os controles deslizantes de saturação e luminosidade devem ser definidos como algo diferente de 0 ou 100 como nesses casos, o matiz não é definido exclusivamente.

[![Modos de mesclagem não separáveis - matiz](non-separable-images/NonSeparableBlendModes-Hue.png "modos de mesclagem não separáveis - matiz")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Quando você usar definido o controle deslizante como 0 (assim como acontece com a captura de tela do iOS à esquerda), tudo o que ativa o avermelhado. Mas isso não significa que a imagem esteja totalmente ausente de verde e azul. É claro que existem tons de cinza ainda presentes no resultado. Por exemplo, a cor RGB (40, 40, C0) é equivalente à cor HSL (240, 50, 50). O matiz é azul, mas o valor de saturação de 50 indica que há também os componentes vermelhos e verdes. Se o matiz é definido como 0 com `SKBlendModes.Hue`, a cor HSL é (0, 50, 50), que é a cor RGB (C0, 40, 40). Há componentes ainda azuis e verdes, mas agora o componente dominante é vermelho.

O `SKBlendModes.Saturation` modo blend combina o nível de saturação da origem com o matiz e luminosidade do destino. Como o matiz, a saturação não é bem definida se a luminosidade for 0 ou 100. Em teoria, qualquer configuração de luminosidade entre esses dois extremos deve funcionar. No entanto, a configuração de luminosidade parece afetar o resultado mais do que deveria. Defina a luminosidade como 50, e você pode ver como você pode definir o nível de saturação da imagem:

[![Modos de mesclagem não separáveis - saturação](non-separable-images/NonSeparableBlendModes-Saturation.png "modos de mesclagem não separáveis - saturação")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Você pode usar esse modo de mesclagem para aumentar a saturação de uma imagem sem graça de cor, ou você pode diminuir a saturação para zero (como a captura de tela do iOS à esquerda) para ver a imagem resultante composta somente tons de cinza.

O `SKBlendModes.Color` modo blend retém a luminosidade do destino, mas usa o matiz e a saturação da origem. Novamente, isso implica que qualquer configuração do controle deslizante de luminosidade em algum lugar entre os extremos deve funcionar. 

[![Modos de mesclagem não separáveis - colorir](non-separable-images/NonSeparableBlendModes-Color.png "modos de mesclagem não separáveis - cor")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Você verá um aplicativo desse modo blend em breve.

Por fim, o `SKBlendModes.Luminosity` modo de mesclagem é o oposto da `SKBlendModes.Color`. Ele retém o matiz e a saturação do destino, mas usa a luminosidade da fonte de. O `Luminosity` modo de mesclagem é mais misterioso do lote: os controles deslizantes a matiz e a saturação afetam a imagem, mas mesmo a luminosidade médio, a imagem não é diferente:

[![Modos de mesclagem não separáveis - luminosidade](non-separable-images/NonSeparableBlendModes-Luminosity.png "modos de mesclagem não separáveis - luminosidade")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

Em teoria, aumentando ou diminuindo a luminosidade de uma imagem deve tornar mais claros ou mais escura. Curiosamente, o [exemplo de luminosidade no Skia **referência SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) é bastante semelhante.

Ele geralmente não é o caso em que você vai querer usar um dos modos de mesclagem não separáveis com uma fonte que consiste em uma única cor aplicada à imagem inteira de destino. O efeito é muito grande. Você vai querer restringir o efeito em uma parte da imagem. Nesse caso, o código-fonte provavelmente irá incorporar transparancy ou talvez a fonte estará limitada a um elemento de gráfico menor.

## <a name="a-matte-for-a-separable-mode"></a>Um fosco para um modo separável

Aqui está um dos bitmaps de incluído como um recurso nas [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo. O nome do arquivo **Banana.jpg**:

![Banana Monkey](non-separable-images/Banana.jpg "Monkey Banana")

É possível criar um fosco que abrange apenas a banana. Isso também é um recurso nas [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) exemplo. O nome do arquivo **BananaMatte.png**:

![Banana fosco](non-separable-images/BananaMatte.png "fosco Banana")

Além da forma banana preto, o restante do bitmap é transparente.

O **Banana azul** página usa esse fosco para alterar o matiz e a saturação de banana o que está mantendo o monkey, mas alterar nada mais na imagem. 

A seguir `BlueBananaPage` classe, o **Banana.jpg** bitmap é carregado como um campo. As cargas do construtor de **BananaMatte.png** bitmap como o `matteBitmap` objeto, mas ele não mantém esse objeto além do construtor. Em vez disso, um terceiro bitmap chamado `blueBananaBitmap` é criado. O `matteBitmap` é desenhado no `blueBananaBitmap` seguido por um `SKPaint` com seu `Color` definida como azul e sua `BlendMode` definido como `SKBlendMode.SrcIn`. O `blueBananaBitmap` permanece quase transparente, mas com uma imagem de azul pura sólida do banana:

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

O `PaintSurface` manipulador desenha o bitmap com o monkey mantendo o banana. Esse código é seguido pela exibição do `blueBananaBitmap` com `SKBlendMode.Color`. Sobre a superfície do banana, matiz e a saturação de cada pixel é substituído pelo azul sólido, o que corresponde a um valor de matiz de 240 e um valor de saturação de 100. A luminosidade, no entanto, permanece o mesmo, que significa que o banana continua a ter uma textura realista, apesar de sua nova cor:

[![Azul Banana](non-separable-images/BlueBanana.png "azul Banana")](non-separable-images/BlueBanana-Large.png#lightbox)

Tente alterar o modo de mesclagem para `SKBlendMode.Saturation`. O banana permanece amarelo, mas é mais intenso amarelo. Em um aplicativo da vida real, isso pode ser um efeito mais desejável do que a ativação de banana azul.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
