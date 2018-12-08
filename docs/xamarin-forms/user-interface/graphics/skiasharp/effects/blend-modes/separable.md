---
title: Os modos do blend separáveis
description: Use os modos do blend separáveis para alterar as cores vermelhas, verdes e azuis.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 594e98230d4f4bd8aca27f92f4544f8c59b5f0a2
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061449"
---
# <a name="the-separable-blend-modes"></a>Os modos do blend separáveis

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Como você viu neste artigo [ **modos de mesclagem do SkiaSharp Porter-Duff**](porter-duff.md), os modos do blend Porter Duff geralmente executam operações de recorte. Os modos do blend separáveis são diferentes. Os modos de separáveis alteram os componentes de cor vermelho, verde e azul individual de uma imagem. Modos de mesclagem separáveis podem misturar a cor para demonstrar que a combinação de vermelho, verde e azul é, de fato, branca:

![Cores primárias](separable-images/SeparableSample.png "cores primárias")

## <a name="lighten-and-darken-two-ways"></a>Clarear e escurecer duas maneiras 

É comum ter um bitmap que é um pouco muito claro ou escuro muito. Você pode usar os modos de mesclagem separáveis para clarear ou escurecer o imabe.  Na verdade, dois dos modos no blend separáveis a [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) enumeração são nomeados `Lighten` e `Darken`. 

Esses dois modos são demonstrados a **clarear e escurecer** página. O arquivo XAML instancia dois `SKCanvasView` objetos e dois `Slider` modos de exibição:

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

A primeira `SKCanvasView` e `Slider` demonstram `SKBlendMode.Lighten` e demonstra o segundo par `SKBlendMode.Darken`. Os dois `Slider` modos de exibição compartilham o mesmo `ValueChanged` manipulador e os dois `SKCanvasView` compartilham o mesmo `PaintSurface` manipulador. Ambas as verificação de manipuladores de eventos qual objeto está acionando o evento:

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

O `PaintSurface` manipulador calcula um retângulo adequado para o bitmap. O manipulador exibe esse bitmap e, em seguida, exibe um retângulo ao longo de bitmap usando um `SKPaint` do objeto com seus `BlendMode` propriedade definida como `SKBlendMode.Lighten` ou `SKBlendMode.Darken`. O `Color` propriedade é uma tonalidade cinza com base no `Slider`. Para o `Lighten` modo, os intervalos de cores de preto para branco, mas para o `Darken` modo ele varia de branco para preto.

As capturas de tela da esquerda para direita mostram cada vez maiores `Slider` valores como a parte superior da imagem obtém mais leve e a imagem inferior obtém mais escura:

[![Clarear e escurecer](separable-images/LightenAndDarken.png "clarear e escurecer")](separable-images/LightenAndDarken-Large.png#lightbox)

Este programa demonstra o vetor perpendicular à forma em que os modos do blend separáveis são usados: O destino é uma imagem de algum tipo, com muita frequência um bitmap. A fonte é um retângulo exibido usando um `SKPaint` do objeto com seu `BlendMode` propriedade definida como modo de mesclagem separáveis. O retângulo pode ser uma cor sólida (como a seguir) ou um gradiente. A transparência é _não_ geralmente usada com os modos do blend separáveis.

Ao testar com este programa, você descobrirá que esses modos de mesclagem de dois não clarear e escurecer a imagem de maneira uniforme. Em vez disso, o `Slider` parece definir um limite de algum tipo. Por exemplo, como você aumentar o `Slider` para o `Lighten` modo, as áreas mais escuras da imagem receber luz primeiro enquanto as áreas mais claras permanecem os mesmos.

Para o `Lighten` modo, se o pixel de destino é o valor de cor RGB (recuperação de desastre, gd, banco de dados) e o pixel da fonte é a cor da (Sr, Sg, Sb), em seguida, a saída é (ou, Og, Ob) calculado da seguinte maneira:

 Ou = Máx (recuperação de desastre, Sr) Og = máx (gd, Sg) Ob = máx (banco de dados, Sb)

Para vermelho, verde e azul separadamente, o resultado for maior do código-fonte e destino. Isso produz o efeito de ritmo muito escuras áreas de destino pela primeira vez.

O `Darken` modo é semelhante, exceto que o resultado é inferior a origem e destino:

 Ou = min (recuperação de desastre, Sr) Og = min (gd, Sg) Ob = min (banco de dados, Sb)

Os componentes vermelhos, verdes e azuis são cada tratados separadamente, por isso, esses modos de mesclagem são conhecidos como o _separáveis_ modos do blend. Por esse motivo, as abreviações **Dc** e **Sc** pode ser usado para as cores de origem e destino, e deve ser entendido que se aplicam os cálculos para cada um dos componentes vermelhos, verdes e azuis separadamente.

A tabela a seguir mostra todos os modos do blend separáveis com breves explicações sobre o que eles fazem. A segunda coluna mostra a cor de fonte que não produz nenhuma alteração:

| Modo de mesclagem   | Nenhuma alteração | Operação |
| ------------ | --------- | --------- |
| `Plus`       | Preto     | Clareia adicionando cores: Sc + controlador de domínio |
| `Modulate`   | Branco     | Escurece multiplicando cores: Sc· Controlador de domínio | 
| `Screen`     | Preto     | Complementa o produto de complementa: Sc + Dc &ndash; Sc· Controlador de domínio |
| `Overlay`    | Cinza      | Inverso da `HardLight` |
| `Darken`     | Branco     | Mínimo de cores: min (Sc, o controlador de domínio) |
| `Lighten`    | Preto     | Máximo de cores: max (Sc, o controlador de domínio) |
| `ColorDodge` | Preto     | Clareia com base na fonte de destino |
| `ColorBurn`  | Branco     | Escurece com base na fonte de destino | 
| `HardLight`  | Cinza      | Semelhante ao efeito de refletor de luz |
| `SoftLight`  | Cinza      | Semelhante ao efeito de destaque reversível | 
| `Difference` | Preto     | Subtrai a mais escura mais claras: Abs (controlador de domínio &ndash; Sc) | 
| `Exclusion`  | Preto     | Semelhante ao `Difference` contraste mais baixo, mas |
| `Multiply`   | Branco     | Escurece multiplicando cores: Sc· Controlador de domínio |

Os algoritmos mais detalhados podem ser encontrados no W3C [ **composição e a combinação do nível 1** ](https://www.w3.org/TR/compositing-1/) especificação e o Skia [ **SkBlendMode referência** ](https://skia.org/user/api/SkBlendMode_Reference), embora a notação nessas duas origens não é o mesmo. Tenha em mente que `Plus` geralmente é considerado como um modo de mesclagem Duff Porter, e `Modulate` não é parte da especificação do W3C.

Se a fonte for transparente, em seguida, para todos os separáveis modos do blend, exceto `Modulate`, o modo de mesclagem não tem nenhum efeito. Como você viu anteriormente, o `Modulate` modo blend incorpora o canal alfa na multiplicação. Caso contrário, `Modulate` tem o mesmo efeito que `Multiply`. 

Observe os dois modos denominados `ColorDodge` e `ColorBurn`. As palavras _dodge_ e _gravar_ proveniente de práticas recomendadas do quarto escuro fotográfica. Um ampliador torna uma impressão fotográfica, iluminar com luz por meio de um negativo. Com nenhuma luz, a impressão é branca. A impressão obtém mais escura à medida que mais uma luz cai no servidor de impressão para um período de tempo maior. Criadores de impressão usado geralmente uma mão ou objetos pequenos para bloquear algumas da luz de cair em uma determinada parte de imprimir, deixa essa área mais clara. Isso é conhecido como _evitando_. Por outro lado, material opaco com um buraco no (ou mais da luz de bloqueio de mãos) poderia ser usado para direcionar o mais claro em um local específico para escurecê-lo, denominado _gravação_.

O **Dodge e Burn** programa é muito semelhante à **clarear e escurecer**. O arquivo XAML é estruturado o mesmo, mas com nomes de elementos diferentes e o arquivo code-behind é bastante semelhante, mas o efeito desses dois dos modos de mesclagem é bem diferente:

[![Dodge e Burn](separable-images/DodgeAndBurn.png "Dodge e Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Para pequenas `Slider` valores, o `Lighten` modo clareia áreas escuras em primeiro lugar, embora `ColorDodge` clareia mais uniformemente.

Programas de aplicativo de processamento de imagens permitem que muitas vezes evitando e a gravação seja restrito a áreas específicas, assim como em um quarto escuro. Isso pode ser feito por gradientes ou por um bitmap com tons diferentes de cinza.

## <a name="exploring-the-separable-blend-modes"></a>Explorando os modos do blend separáveis

O **separáveis modos do Blend** página permite que você examine todos os modos do blend separáveis. Ele exibe um destino de bitmap e uma fonte de retângulo colorido usando um dos modos de mesclagem. 

O arquivo XAML define um `Picker` (para selecionar o modo de mesclagem) e quatro controles deslizantes. Os controles deslizantes de três primeiros permitem que você definir os componentes vermelhos, verdes e azuis da origem. O controle deslizante de quarto destina-se a substituir esses valores, definindo um tom de cinza. Os controles deslizantes individuais não são identificados, mas as cores indicam sua função:

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

O arquivo code-behind carrega um dos recursos de bitmap e desenha duas vezes, uma vez na metade superior da tela e, novamente, na parte inferior metade da tela:

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

Na parte inferior do `PaintSurface` manipulador, um retângulo é desenhado na segunda bitmap com o modo de mesclagem selecionada e a cor selecionada. Você pode comparar o bitmap modificado na parte inferior, com o bitmap original na parte superior:

[![Modos de mesclagem separáveis](separable-images/SeparableBlendModes.png "modos de mesclagem separáveis")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Cores primárias de aditivas e subtrativas

O **cores primárias** página desenha três círculos sobrepostos de vermelho, verde e azul:

[![Cores aditivas primárias](separable-images/PrimaryColors-Additive.png "Cores aditivas primárias")](separable-images/PrimaryColors-Additive.png#lightbox)

Essas são as cores primárias aditivas. Combinações de quaisquer dois produzem ciano, magenta e amarelo, e uma combinação de todos os três é branca.

Esses três círculos são desenhados com a `SKBlendMode.Plus` modo, mas você também pode usar `Screen`, `Lighten`, ou `Difference` para o mesmo efeito. Aqui está o programa:

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

O programa inclui um `TabGestureRecognizer`. Quando você toque ou clique na tela, o programa usa `SKBlendMode.Multiply` para exibir as três primárias subtrativas:

[![Cores de primárias subtrativa](separable-images/PrimaryColors-Subtractive.png "subtrativa cores de primárias")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

O `Darken` modo também funciona para o mesmo efeito.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
