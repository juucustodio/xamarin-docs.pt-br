---
title: A transformação de escala
description: Thhis artigo explora a transformação de escala de SkiaSharp para dimensionar objetos para vários tamanhos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 94105cbb83e4c6eb3558ca3fc55e505ab41f28fe
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615597"
---
# <a name="the-scale-transform"></a>A transformação de escala

_Descobrir a transformação de escala para dimensionar objetos para diversos tamanhos de SkiaSharp_

Como você viu [traduzir a transformar](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md) artigo, a transformação de conversão pode mover um objeto gráfico de um local para outro. Por outro lado, a transformação de escala altera o tamanho do objeto gráfico:

![](scale-images/scaleexample.png "Uma palavra altura dimensionada no tamanho")

A transformação de escala também geralmente faz com que as coordenadas de gráficos mover conforme elas são feitas maiores.

Vimos anteriormente duas fórmulas de transformação que descrevem os efeitos de fatores de conversão de `dx` e `dy`:

x' = x + dx

y' = y + dy

Fatores de dimensionamento `sx` e `sy` são multiplicação em vez de aditivo:

x' = sx · x

y' = sy reforçado y

Os valores padrão dos fatores traduzir são 0; os valores padrão dos fatores de dimensionamento são 1.

O `SKCanvas` classe define quatro `Scale` métodos. A primeira [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/) método é para casos em que você deseja que a mesma escala horizontal e vertical fatores:

```csharp
public void Scale (Single s)
```

Isso é conhecido como *isotropic* dimensionamento &mdash; dimensionamento que é o mesmo em ambas as direções. Dimensionamento Isotropic preserva a taxa de proporção do objeto.

A segunda [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) método permite que você especifique valores diferentes para o dimensionamento horizontal e vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Isso resulta em *anisotrópica* dimensionamento.
O terceiro [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/) método combina os dois fatores de dimensionamento em uma única `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

O quarto `Scale` método será descrito em breve.

O **de dimensionamento básico** página demonstra o `Scale` método. O [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) arquivo XAML contém dois `Slider` elementos que permitem que você selecione os fatores de dimensionamento horizontal e vertical entre 0 e 10. O [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) arquivo code-behind usa esses valores para chamar `Scale` antes de exibir um retângulo arredondado traçados com uma linha tracejada e dimensionados de acordo com algum texto no canto superior esquerdo canto da tela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Você talvez esteja se perguntando: como os fatores de dimensionamento afetam o valor retornado de `MeasureText` método de `SKPaint`? A resposta é: nada. `Scale` é um método de `SKCanvas`. Ele não afeta qualquer coisa que você pode fazer com um `SKPaint` objeto até que você usa esse objeto para renderizar algo na tela.

Como você pode ver, tudo o que desenhado após o `Scale` chamar aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Captura de tela da página de dimensionamento básico tripla")](scale-images/basicscale-large.png#lightbox "tripla captura de tela da página de dimensionamento básico")

O texto, a largura da linha tracejada, o comprimento dos traços na linha, o arredondamento de cantos e a margem de 10 pixels, entre as bordas esquerdas e superior da tela e o retângulo arredondado são todos sujeito aos mesmos fatores de dimensionamento.

> [!IMPORTANT]
> A plataforma Universal do Windows não renderizar texto anisotropicly dimensionado corretamente.

Anisotrópica dimensionamento faz com que a largura do traço para se tornar diferente para linhas alinhada com os eixos horizontais e verticais. (Isso também é evidente na primeira imagem nesta página.) Se você não quiser que a largura do traço seja afetado pelos fatores de dimensionamento, defina-o como 0 e sempre será um pixel de largura independentemente do `Scale` configuração.

O dimensionamento é relativa ao canto superior esquerdo da tela. Isso pode ser exatamente o que você deseja, mas pode não ser. Suponha que você deseja posicionar o texto e o retângulo em algum lugar na tela e você deseja dimensioná-lo em relação ao seu centro. Nesse caso, você pode usar a quarta versão dos [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/) método, que inclui dois parâmetros adicionais para especificar o centro do dimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

O `px` e `py` parâmetros definem um ponto que às vezes é chamado de *dimensionamento center* , mas no SkiaSharp documentação é conhecida como um *ponto dinâmico*. Esse é um ponto em relação ao canto superior esquerdo da tela de que não é afetado pela colocação em escala. Todo dimensionamento ocorre relativas a esse centro.

O [ **escala centralizado** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) página mostra como isso funciona. O `PaintSurface` manipulador é semelhante de **dimensionamento básico** do programa, exceto que o `margin` valor é calculado para centralizar o texto horizontalmente, que significa que o programa funciona melhor em modo retrato:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

O canto superior esquerdo do retângulo arredondado é posicionado `margin` pixels do lado esquerdo da tela e `margin` pixels da parte superior. Os dois últimos argumentos para o `Scale` método são definidos como esses valores e a largura e altura do texto, que também é a largura e altura do retângulo arredondado. Isso significa que todo dimensionamento é relativo ao centro do retângulo:

[![](scale-images/centeredscale-small.png "Tripla captura de tela da página de escala centralizado")](scale-images/centeredscale-large.png#lightbox "tripla captura de tela da página de escala centralizado")

O `Slider` elementos neste programa tem um intervalo de &ndash;10 a 10. Como você pode ver, valores negativos de dimensionamento (por exemplo, no Android, tela no centro) vertical fazem os objetos girar em torno do eixo horizontal que passa pelo centro do dimensionamento. Valores negativos de (como a tela UWP à direita) o dimensionamento horizontal fazem os objetos girar em torno do eixo vertical que passa pelo centro do dimensionamento.

Esta versão quarto do `Scale` método é, na verdade, um atalho. Talvez você queira ver como isso funciona, substituindo o `Scale` método neste código pelo seguinte:

```csharp
canvas.Translate(-px, -py);
```

Esses são os negativos das coordenadas de ponto dinâmico.

Agora, execute o programa novamente. Você verá que o retângulo e texto são deslocados para que o centro está no canto superior esquerdo da tela. Você pode vê-lo mal. Os controles deslizantes não funcionam, é claro, porque agora o programa não pode ser escalonada em todos os.

Agora, adicione o basic `Scale` chamar (sem um centro de colocação em escala) *antes de* que `Translate` chamar:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se você estiver familiarizado com este exercício em outros que sistemas de programação de gráficos, você pode pensar que há de errado, mas ele não é. Skia manipula chamadas sucessivas transformação um pouco diferente do qual você pode estar familiarizado com o.

Com o sucessivas `Scale` e `Translate` chamadas, o centro do retângulo arredondado é ainda no canto superior esquerdo, mas agora você pode dimensioná-lo em relação ao canto superior esquerdo da tela, que também é o centro do retângulo arredondado.

Agora, antes que `Scale` chamada adicionar outro `Translate` chamada com os valores de centralização:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Isso move o resultado em escala de volta para a posição original. Esses três chamadas são equivalentes às:

```csharp
canvas.Scale(sx, sy, px, py);
```

As transformações individuais são compostas para que a fórmula de transformação total é:

 x' = sx reforçado (x – px) + px

 y' = sy reforçado (y – py) + py

Tenha em mente que os valores padrão das `sx` e `sy` são 1. É fácil convencido de que o ponto dinâmico (px, Aj) não é transformado por essas fórmulas. Ele permanece no mesmo local em relação à tela.

Ao combinar `Translate` e `Scale` chamadas, a ordem é importante. Se o `Translate` vem após o `Scale`, os fatores de conversão são dimensionados com eficiência com os fatores de dimensionamento. Se o `Translate` vem antes do `Scale`, os fatores de conversão não são dimensionados. Esse processo se torna um pouco mais claro (embora mais matemática) quando o assunto de matrizes de transformação é apresentado.

O `SKPath` classe define um somente leitura [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) propriedade que retorna um `SKRect` definindo a extensão das coordenadas no caminho. Por exemplo, quando o `Bounds` propriedade é obtida do caminho hendecagram criado anteriormente, o `Left` e `Top` propriedades do retângulo são aproximadamente -100, o `Right` e `Bottom` são propriedades aproximadamente 100 e o `Width` e `Height` propriedades são aproximadamente 200. (A maioria dos valores reais é um pouco menor porque os pontos das estrelas são definidos por um círculo com um raio de 100, mas apenas o ponto superior é paralelo com os eixos horizontais ou verticais.)

A disponibilidade dessas informações significa que deve ser possível derivar de escala e traduzir fatores adequados para o dimensionamento de um caminho para o tamanho da tela. O [ **dimensionamento Anisotrópica** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) página demonstra isso com a estrela 11-lo. Uma *anisotrópica* escala significa que ele é diferente nas direções horizontais e verticais, o que significa que a estrela não manterá sua taxa de proporção original. Aqui está o código relevante no `PaintSurface` manipulador:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

O `pathBounds` retângulo é obtido na parte superior desse código e, em seguida, usado mais tarde com a largura e altura da tela no `Scale` chamar. Chamada por si só dimensionará as coordenadas do caminho quando ele é renderizado pelo `DrawPath` chamada, mas a estrela será centralizada no canto superior direito da tela. Ele precisa ser deslocados para baixo e para a esquerda. Esse é o trabalho da `Translate` chamar. Essas duas propriedades de `pathBounds` são aproximadamente -100, portanto, os fatores de conversão são cerca de 100. Porque o `Translate` chamada é após o `Scale` chamar, esses valores são dimensionados com eficiência pelos fatores de dimensionamento, que se movimentam o centro da estrela para o centro da tela:

[![](scale-images/anisotropicscaling-small.png "Captura de tela da página dimensionamento Anisotrópica tripla")](scale-images/anisotropicscaling-large.png#lightbox "tripla captura de tela da página dimensionamento Anisotrópica")

Outra maneira, você pode pensar sobre o `Scale` e `Translate` chamadas é determinar o efeito na sequência inversa: O `Translate` chamada desloca o caminho, por isso é totalmente visível, mas orientado no canto superior esquerdo da tela. O `Scale` método, em seguida, torna esse estrela maior em relação ao canto superior esquerdo.

Na verdade, parece que o star é um pouco maior do que a tela. O problema é a largura do traço. O `Bounds` propriedade de `SKPath` indica as dimensões das coordenadas codificado no caminho, e isso é o que o programa usa para dimensioná-lo. Quando o caminho é renderizado com uma largura de traço específica, o caminho renderizado é maior que a tela.

Para corrigir esse problema, você precisa compensar para fazer isso. Uma abordagem mais fácil para este programa é adicionar o direito de instrução seguir antes do `Scale` chamar:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Isso aumenta a `pathBounds` retângulo pelas unidades de 1,5 nos quatro lados. Isso é uma solução razoável somente quando a junção de traço é arredondada. Uma junção de Malhete pode ser mais longo e é difícil calcular.

Você também pode usar uma técnica semelhante com texto, como o **Anisotrópica texto** página demonstra. Aqui está a parte relevante do `PaintSurface` manipulador do [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

É uma lógica semelhante, e o texto se expande para o tamanho da página com base no retângulo de limites de texto retornado de `MeasureText` (que é um pouco maior do que o texto real):

[![](scale-images/anisotropictext-small.png "Tripla captura de tela da página de teste Anisotrópica")](scale-images/anisotropictext-large.png#lightbox "tripla captura de tela da página de teste Anisotrópica")

Se você precisar preservar a taxa de proporção de objetos gráficos, você desejará usar o dimensionamento isotropic. O **dimensionamento Isotropic** página demonstra isso para a estrela 11-lo. Conceitualmente, as etapas para exibir um objeto gráfico no centro da página com o dimensionamento isotropic são:

- Traduza o centro do objeto gráfico para o canto superior esquerdo.
- Dimensione o objeto com base nos valores mínimo das dimensões de página horizontais e verticais, dividido pelas dimensões do objeto gráfico.
- Traduza o centro do objeto em escala para o centro da página.

O [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) executa estas etapas na ordem inversa, antes de exibir a estrela:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

O código também exibe a estrela dez vezes mais, sempre diminuindo a colocação em escala fatorar 10% e progressivamente alterando a cor de vermelho para azul:

[![](scale-images/isotropicscaling-small.png "Captura de tela da página dimensionamento Isotropic tripla")](scale-images/isotropicscaling-large.png#lightbox "tripla captura de tela da página dimensionamento Isotropic")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
