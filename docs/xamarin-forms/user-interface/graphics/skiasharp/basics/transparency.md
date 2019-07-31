---
title: Transparência de SkiaSharp
description: Use a transparência para combinar vários objetos em uma única cena.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4e8c909ce7dfd9c8f6dccbf2685ff2658931b6a5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656353"
---
# <a name="skiasharp-transparency"></a>Transparência de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Como você viu, o [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe inclui uma [ `Color` ](xref:SkiaSharp.SKPaint.Color) propriedade do tipo [ `SKColor` ](xref:SkiaSharp.SKColor). `SKColor` inclui um canal alfa, portanto, qualquer coisa que você aplicar uma cor com um `SKColor` valor pode ser parcialmente transparente. 

Alguns transparência foi demonstrada na [ **animação básica no SkiaSharp** ](animation.md) artigo. Este artigo se aprofundar um pouco transparência para combinar vários objetos em uma única cena, uma técnica que às vezes conhecida como _mistura_. Mais avançadas técnicas de mesclagem são discutidas nos artigos a [ **sombreadores de SkiaSharp** ](../effects/shaders/index.md) seção.

Você pode definir o nível de transparência, quando você cria uma cor que usa o parâmetro de quatro [ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) construtor:

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Um valor alfa de 0 é completamente transparente e um valor alfa de 0xFF é totalmente opaco. Valores entre esses dois extremos criam cores parcialmente transparentes.

Além disso, `SKColor` define uma prática [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) método que cria uma nova cor de uma cor existente, mas com o nível de alfa especificado:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

O uso de texto parcialmente transparente é demonstrado na **código mais código** página na [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemplo. Esta página duas cadeias de caracteres de texto aparece e desaparece, incorporando a transparência no `SKColor` valores:

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

O `transparency` campo é animado para variar de 0 a 1 e novamente de volta em um ritmo sinusoidal. A primeira cadeia de caracteres de texto é exibida com um valor alfa calculado subtraindo o `transparency` valor de 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

O [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) método define o componente alfa em uma cor existente, que aqui é `SKColors.Blue`. A segunda cadeia de caracteres de texto usa um valor alfa calculado a partir de `transparency` valor em si:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

A animação alterna entre as duas palavras, incitam o usuário "codificar mais" (ou talvez, solicitar "mais código"):

[![Mais código de código](transparency-images/CodeMoreCode.png "mais código de código")](transparency-images/CodeMoreCode-Large.png#lightbox)


Neste artigo anterior sobre [ **Noções básicas de Bitmap no SkiaSharp**](bitmaps.md), você viu como exibir bitmaps usando um dos [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*) métodos de `SKCanvas`. Todos os `DrawBitmap` métodos incluem uma `SKPaint` objeto como o último parâmetro. Por padrão, esse parâmetro é definido como `null` e você pode ignorá-lo. 

Como alternativa, você pode definir as `Color` propriedade deste `SKPaint` objeto para exibir um bitmap com algum nível de transparência. Configuração de um nível de transparência na `Color` propriedade de `SKPaint` permite bitmaps e desapareçam ou somem um bitmap em outro. 

Transparência de bitmap é demonstrada na **Bitmap se dissolvem** página. O arquivo XAML instancia uma `SKCanvasView` e um `Slider`:

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

O arquivo code-behind carrega dois recursos de bitmap. Esses bitmaps não são do mesmo tamanho, mas eles são a mesma proporção:

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

O `Color` propriedade do `SKPaint` objeto é definido como dois níveis de alfa complementares para dois bitmaps. Ao usar `SKPaint` com bitmaps, não importa qual o restante do `Color` é de valor. Tudo o que importa é o canal alfa. O código aqui simplesmente chama o `WithAlpha` método o valor padrão da `Color` propriedade.

Movendo o `Slider` se dissolva entre um bitmap e a outra:

[![Bitmap dissolução](transparency-images/BitmapDissolve.png "dissolução de Bitmap")](transparency-images/BitmapDissolve-Large.png#lightbox)

Vários artigos anteriores, você viu como usar SkiaSharp para desenhar o texto, círculos, elipses, retângulos arredondados e bitmaps. A próxima etapa é [caminhos e linhas de SkiaSharp](../paths/index.md) no qual você aprenderá como desenhar linhas conectadas em um caminho de gráficos.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
