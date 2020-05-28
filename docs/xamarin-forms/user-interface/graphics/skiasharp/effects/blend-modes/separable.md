---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c1939c30cbefdbf8d6546761a8c6ac7199bfff62
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139679"
---
# <a name="the-separable-blend-modes"></a>Os modos de mesclagem separáveis

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como você viu no artigo [**SkiaSharp carregador-Duff Blend Modes**](porter-duff.md), os modos de combinação carregador-Duff geralmente executam operações de recorte. Os modos de mesclagem separáveis são diferentes. Os modos separáveis alteram os componentes individuais de cor de vermelho, verde e azul de uma imagem. Os modos de mesclagem separáveis podem misturar cores para demonstrar que a combinação de vermelho, verde e azul é realmente branca:

![Cores primárias](separable-images/SeparableSample.png "Cores primárias")

## <a name="lighten-and-darken-two-ways"></a>Clarear e escurecer duas maneiras 

É comum ter um bitmap que seja um pouco escuro ou muito claro. Você pode usar modos de mistura separáveis para clarear ou escurecer o imabe.  Na verdade, dois dos modos de mesclagem separáveis na [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) enumeração são nomeados `Lighten` e `Darken` . 

Esses dois modos são demonstrados na página **clarear e escurecer** . O arquivo XAML instancia dois `SKCanvasView` objetos e duas `Slider` exibições:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

O primeiro `SKCanvasView` e `Slider` o demonstram `SKBlendMode.Lighten` e o segundo par demonstra `SKBlendMode.Darken` . As duas `Slider` exibições compartilham o mesmo `ValueChanged` manipulador e as duas `SKCanvasView` compartilham o mesmo `PaintSurface` manipulador. Ambos os manipuladores de eventos verificam qual objeto está acionando o evento:

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

O `PaintSurface` manipulador calcula um retângulo adequado para o bitmap. O manipulador exibe esse bitmap e, em seguida, exibe um retângulo sobre o bitmap usando um `SKPaint` objeto com sua `BlendMode` propriedade definida como `SKBlendMode.Lighten` ou `SKBlendMode.Darken` . A `Color` propriedade é uma sombra cinza com base no `Slider` . Para o `Lighten` modo, a cor varia de preto para branco, mas, para o `Darken` modo, ele varia de branco para preto.

As capturas de tela da esquerda para a direita mostram valores cada vez maiores `Slider` , pois a imagem superior fica mais clara e a imagem inferior fica mais escura:

[![Clarear e escurecer](separable-images/LightenAndDarken.png "Clarear e escurecer")](separable-images/LightenAndDarken-Large.png#lightbox)

Este programa demonstra a maneira normal em que os modos de mesclagem separáveis são usados: o destino é uma imagem de algum tipo, com muita frequência um bitmap. A origem é um retângulo exibido usando um `SKPaint` objeto com sua `BlendMode` propriedade definida como um modo de mistura separáveis. O retângulo pode ser uma cor sólida (como está aqui) ou um gradiente. A transparência _não_ é geralmente usada com os modos de mistura separáveis.

Ao experimentar esse programa, você descobrirá que esses dois modos de mesclagem não claream e escurecem a imagem uniformemente. Em vez disso, o `Slider` parece definir um limite de algum tipo. Por exemplo, à medida que você aumenta o `Slider` para o `Lighten` modo, as áreas mais escuras da imagem recebem luz primeiro, enquanto as áreas mais claras permanecem as mesmas.

Para o `Lighten` modo, se o pixel de destino for o valor de cor RGB (Dr, DG, DB) e o pixel de origem for a cor (Sr, SG, SB), a saída será (ou, Ogon, OB) calculada da seguinte maneira:

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Para vermelho, verde e azul separadamente, o resultado é o maior do destino e da origem. Isso produz o efeito de clarear as áreas escuras do destino primeiro.

O `Darken` modo é semelhante, exceto pelo fato de que o resultado é o menor do destino e da fonte:

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

Os componentes vermelho, verde e azul são tratados separadamente, motivo pelo qual esses modos de mesclagem são chamados de modos de mistura _separáveis_ . Por esse motivo, as abreviações **DC** e **SC** podem ser usadas para as cores de destino e origem, e é entendida que os cálculos se aplicam a cada um dos componentes vermelho, verde e azul separadamente.

A tabela a seguir mostra todos os modos de combinação de separáveis com explicações breves sobre o que eles fazem. A segunda coluna mostra a cor de origem que não produz nenhuma alteração:

| Modo de mesclagem   | Nenhuma alteração | Operação |
| ---
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------ | ---Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- | ---Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: Descrição: MS. Prod: MS. Technology: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- | | `Plus`       | Preto | Clareia adicionando cores: SC + DC | | `Modulate`   | Branco | Escurece multiplicando as cores: SC · DC | | `Screen`     | Preto | Complementa o produto de Complementos: SC + DC &ndash; SC · DC | | `Overlay`    | Cinza | Inverso de `HardLight` | | `Darken`     | Branco | Mínimo de cores: mín. (SC, DC) | | `Lighten`    | Preto | Máximo de cores: máx. (SC, DC) | | `ColorDodge` | Preto | Claree o destino com base na origem | | `ColorBurn`  | Branco | Escurece o destino com base na origem | | `HardLight`  | Cinza | Semelhante ao efeito de refletor de destaque "| | `SoftLight`  | Cinza | Semelhante ao efeito do Spotlight reversível | | `Difference` | Preto | Subtrai o mais escuro do mais claro: ABS (DC &ndash; SC) | | `Exclusion`  | Preto | Semelhante a `Difference` mas menor contraste | | `Multiply`   | Branco | Escurece multiplicando as cores: SC · DC |

Algoritmos mais detalhados podem ser encontrados na especificação W3C de [**composição e mesclagem de nível 1**](https://www.w3.org/TR/compositing-1/) e na referência de skia [**SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference), embora a notação nessas duas fontes não seja a mesma. Tenha em mente que `Plus` normalmente é considerado um modo de mesclagem carregador-Duff e `Modulate` não faz parte da especificação W3C.

Se a origem for transparente, para todos os modos de mistura separáveis, exceto `Modulate` , o modo de mesclagem não terá nenhum efeito. Como vimos anteriormente, o modo de `Modulate` mesclagem incorpora o canal alfa na multiplicação. Caso contrário, `Modulate` terá o mesmo efeito que `Multiply` . 

Observe os dois modos chamados `ColorDodge` e `ColorBurn` . As palavras _subexposição_ e _gravação_ originadas em práticas darkrooms fotográficas. Um ampliador faz uma impressão fotográfica com uma luz brilhante por um negativo. Sem luz, a impressão é branca. A impressão fica mais escura, pois mais luz cai na impressão por um período de tempo maior. Os facilitadores de impressão costumavam usar um objeto de mão ou pequeno para impedir que parte da luz caia em uma determinada parte da impressão, tornando essa área mais clara. Isso é conhecido como _dodging_. Por outro lado, um material opaco com uma brecha (ou bloqueio de mãos na maior parte da luz) poderia ser usado para direcionar mais luz em um ponto específico para escurecer, chamado de _gravação_.

O programa de **subexposição e gravação** é muito semelhante a **clarear e escurecer**. O arquivo XAML é estruturado da mesma forma, mas com nomes de elementos diferentes, e o arquivo code-behind é muito semelhante, mas o efeito desses dois modos de mesclagem é bastante diferente:

[![Subexposição e gravação](separable-images/DodgeAndBurn.png "Subexposição e gravação")](separable-images/DodgeAndBurn-Large.png#lightbox)

Para `Slider` valores pequenos, o `Lighten` modo clareia as áreas escuras primeiro e, ao mesmo tempo, `ColorDodge` clareia mais uniformemente.

Os programas de aplicativo de processamento de imagens geralmente permitem que o dodging e a gravação sejam restritos a áreas específicas, assim como em um Darkroom. Isso pode ser feito por gradientes ou por um bitmap com tons variados de cinza.

## <a name="exploring-the-separable-blend-modes"></a>Explorando os modos de mistura separáveis

A página **modos de mesclagem do separáveis** permite que você examine todos os modos de mesclagem separáveis. Ele exibe um destino de bitmap e uma fonte de retângulo colorido usando um dos modos de mesclagem. 

O arquivo XAML define um `Picker` (para selecionar o modo de mesclagem) e quatro controles deslizantes. Os três primeiros controles deslizantes permitem que você defina os componentes vermelho, verde e azul da origem. O quarto controle deslizante destina-se a substituir esses valores definindo uma tonalidade cinza. Os controles deslizantes individuais não são identificados, mas as cores indicam sua função:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega um dos recursos de bitmap e o desenha duas vezes, uma vez na metade superior da tela e novamente na metade inferior da tela:

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Na direção da parte inferior do `PaintSurface` manipulador, um retângulo é desenhado sobre o segundo bitmap com o modo de mesclagem selecionado e a cor selecionada. Você pode comparar o bitmap modificado na parte inferior com o bitmap original na parte superior:

[![Modos do Blend separáveis](separable-images/SeparableBlendModes.png "Modos do Blend separáveis")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Cores primárias aditivas e subtraídas

A página **cores primárias** desenha três círculos sobrepostos de vermelho, verde e azul:

[![Cores primárias aditivas](separable-images/PrimaryColors-Additive.png "Cores primárias aditivas")](separable-images/PrimaryColors-Additive.png#lightbox)

Essas são as cores primárias aditivas. As combinações de dois produzem ciano, magenta e amarelo, e uma combinação de todos os três é branca.

Esses três círculos são desenhados com o `SKBlendMode.Plus` modo, mas você também pode usar `Screen` , `Lighten` ou `Difference` para o mesmo efeito. Este é o programa:

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

O programa inclui um `TabGestureRecognizer` . Quando você toca ou clica na tela, o programa usa `SKBlendMode.Multiply` para exibir os três primários subtraídos:

[![Cores primárias subtraíis](separable-images/PrimaryColors-Subtractive.png "Cores primárias subtraíis")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

O `Darken` modo também funciona para esse mesmo efeito.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
