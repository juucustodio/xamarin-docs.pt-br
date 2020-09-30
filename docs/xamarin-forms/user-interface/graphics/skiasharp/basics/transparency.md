---
title: Transparência de SkiaSharp
description: Use a transparência para combinar vários objetos em uma única cena.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af1747a19af6c715d1594122393e500d1f73c79b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562516"
---
# <a name="skiasharp-transparency"></a>Transparência de SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como você viu, a [`SKPaint`](xref:SkiaSharp.SKPaint) classe inclui uma [`Color`](xref:SkiaSharp.SKPaint.Color) Propriedade do tipo [`SKColor`](xref:SkiaSharp.SKColor) . `SKColor` inclui um canal alfa, de modo que qualquer coisa que você colorir com um `SKColor` valor pode ser parcialmente transparente. 

Alguma transparência foi demonstrada na [**Animação básica no artigo SkiaSharp**](animation.md) . Este artigo vai um pouco mais fundo na transparência para combinar vários objetos em uma única cena, uma técnica, às vezes conhecida como _mesclagem_. Técnicas de mesclagem mais avançadas são discutidas nos artigos na seção [**sombreadores SkiaSharp**](../effects/shaders/index.md) .

Você pode definir o nível de transparência ao criar uma cor pela primeira vez usando o construtor de quatro parâmetros [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) :

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Um valor alfa de 0 é totalmente transparente e um valor alfa de 0xFF é totalmente opaco. Os valores entre esses dois extremos criam cores que são parcialmente transparentes.

Além disso, `SKColor` o define um [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) método útil que cria uma nova cor a partir de uma cor existente, mas com o nível alfa especificado:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

O uso de texto parcialmente transparente é demonstrado na página código **mais de código** no exemplo [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Essa página esmaece duas cadeias de caracteres de texto para dentro e para fora, incorporando transparência nos `SKColor` valores:

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

O `transparency` campo é animado para variar de 0 a 1 e de volta em um ritmo sinusoidal. A primeira cadeia de texto é exibida com um valor alfa calculado subtraindo o `transparency` valor de 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

O [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) método define o componente alfa em uma cor existente, que é aqui `SKColors.Blue` . A segunda cadeia de texto usa um valor alfa calculado a partir do `transparency` próprio valor:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

A animação alterna entre as duas palavras, urging o usuário a "codificar mais" (ou talvez solicitando "mais código"):

[![Codificar mais código](transparency-images/CodeMoreCode.png "Codificar mais código")](transparency-images/CodeMoreCode-Large.png#lightbox)

No artigo anterior sobre [**noções básicas de bitmap no SkiaSharp**](bitmaps.md), você viu como exibir bitmaps usando um dos [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) métodos do `SKCanvas` . Todos os `DrawBitmap` métodos incluem um `SKPaint` objeto como o último parâmetro. Por padrão, esse parâmetro é definido como `null` e você pode ignorá-lo. 

Como alternativa, você pode definir a `Color` propriedade desse `SKPaint` objeto para exibir um bitmap com algum nível de transparência. A definição de um nível de transparência na `Color` propriedade de permite que você os `SKPaint` bitmaps para dentro e para fora, ou para dissolver um bitmap em outro. 

A transparência do bitmap é demonstrada na página de **dissolução de bitmap** . O arquivo XAML instancia um `SKCanvasView` e um `Slider` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind carrega dois recursos de bitmap. Esses bitmaps não têm o mesmo tamanho, mas têm a mesma taxa de proporção:

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

A `Color` Propriedade do `SKPaint` objeto é definida como dois níveis alfa complementares para os dois bitmaps. Ao usar `SKPaint` com bitmaps, não importa qual é o resto do `Color` valor. Tudo o que importa é o canal alfa. O código aqui simplesmente chama o `WithAlpha` método no valor padrão da `Color` propriedade.

Mover as `Slider` dessoluções entre um bitmap e o outro:

[![Dissolução de bitmap](transparency-images/BitmapDissolve.png "Dissolução de bitmap")](transparency-images/BitmapDissolve-Large.png#lightbox)

Nos últimos artigos, você viu como usar o SkiaSharp para desenhar texto, círculos, elipses, retângulos arredondados e bitmaps. A próxima etapa é [SkiaSharp linhas e caminhos](../paths/index.md) nos quais você aprenderá como desenhar linhas conectadas em um caminho gráfico.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)