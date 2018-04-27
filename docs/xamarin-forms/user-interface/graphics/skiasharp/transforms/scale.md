---
title: A transformação de escala
description: Descobrir a transformação de escala SkiaSharp para objetos para vários tamanhos de dimensionamento
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: b4a36e15bd5db72ef113748282175c6d31a95966
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="the-scale-transform"></a>A transformação de escala

_Descobrir a transformação de escala SkiaSharp para objetos para vários tamanhos de dimensionamento_

Como você viu no [traduzir a transformar](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md) artigo, a transformação de conversão pode mover um objeto de gráfico de um local para outro. Por outro lado, a transformação de escala altera o tamanho do objeto de gráfico:

![](scale-images/scaleexample.png "Uma palavra alta escala de tamanho")

A transformação de escala também geralmente faz com que as coordenadas de gráficos mover conforme eles ficam maiores.

Anteriormente, você viu duas fórmulas de transformação que descrevem os efeitos de fatores de conversão de `dx` e `dy`:

x' = x + dx

y' = y + dy

Dimensionar fatores de `sx` e `sy` são multiplicação em vez de suplementares:

x' = sx · x

y' = sy · y

Os valores padrão dos fatores de conversão são 0; os valores padrão dos fatores de escala são 1.

O `SKCanvas` classe define quatro `Scale` métodos. A primeira [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/) método é para casos em que você deseja que a mesma escala horizontal e vertical fatores:

```csharp
public void Scale (Single s)
```

Isso é conhecido como *isotropic* dimensionamento &mdash; escala que é o mesmo em ambas as direções. Dimensionamento Isotropic preserva a taxa de proporção do objeto.

A segunda [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) método permite que você especifique valores diferentes para a expansão horizontal e vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Isso resulta em *anisotrópico* dimensionamento.
O terceiro [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/) método combina os dois fatores de dimensionamento em um único `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

O quarto `Scale` método será descrito em breve.

O **básica de escala** página demonstra o `Scale` método. O [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) arquivo XAML contém duas `Slider` elementos que permitem que você selecione os fatores de escala horizontais e verticais entre 0 e 10. O [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) arquivo code-behind usa esses valores para chamar `Scale` antes de exibir um retângulo arredondado traçados com uma linha tracejada e dimensionado para ajustar o texto no canto superior esquerdo canto da tela:

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

Você talvez esteja se perguntando: como os fatores de dimensionamento afetam o valor retornado do `MeasureText` método `SKPaint`? A resposta é: nenhum. `Scale` é um método de `SKCanvas`. Ele não afeta qualquer coisa que você faria com um `SKPaint` objeto até que você usar esse objeto para renderizar algo na tela.

Como você pode ver, tudo desenhado após o `Scale` chamar aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Tripla captura de tela da página de escala básico")](scale-images/basicscale-large.png#lightbox "tripla captura de tela da página de escala básico")

O texto, a largura da linha tracejada, o comprimento de traços nessa linha, o arredondamento de cantos e a margem de 10 pixels entre as bordas esquerda e superiores da tela e o retângulo arredondado estão todos os sujeitos os mesmos fatores de dimensionamento.

> [!IMPORTANT]
> A plataforma Universal do Windows não processa texto anisotropicly dimensionado corretamente.

Anisotrópico dimensionamento faz com que a largura do traço fiquem diferentes para linhas alinhados com os eixos horizontais e verticais. (Isso também é evidente a partir da primeira imagem nesta página.) Se não quiser que a largura do traço ser afetados por fatores de dimensionamento, defina-o como 0 e sempre será um pixel de largura, independentemente do `Scale` configuração.

O dimensionamento é relativo ao canto superior esquerdo da tela. Isso pode ser exatamente o que você deseja, mas talvez não seja. Suponha que você deseja posicionar o texto e um retângulo em outro lugar na tela e você deseja dimensioná-lo em relação ao centro. Nesse caso, você pode usar a quarta versão do [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/) método, que inclui dois parâmetros adicionais para especificar o centro do dimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

O `px` e `py` parâmetros definem um ponto que às vezes é chamado o *dimensionamento center* , mas no SkiaSharp documentação é conhecida como um *dinamizar ponto*. Esse é um ponto em relação ao canto superior esquerdo da tela que não é afetada pelo dimensionamento. Todo dimensionamento ocorre em relação a esse centro.

O [ **centralizada escala** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) página mostra como isso funciona. O `PaintSurface` manipulador é semelhante do **básica de escala** programa exceto que o `margin` valor é calculado para centralizar o texto horizontalmente, que significa que o programa funciona melhor no modo retrato:

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

O canto superior esquerdo do retângulo arredondado é posicionado `margin` pixels do lado esquerdo da tela e `margin` pixels da parte superior. Os dois últimos argumentos para o `Scale` método são definidas para os valores mais a largura e altura do texto, que também é a largura e altura do retângulo arredondado. Isso significa que todo dimensionamento é relativa ao centro do retângulo:

[![](scale-images/centeredscale-small.png "Tripla captura de tela da página de escala centralizada")](scale-images/centeredscale-large.png#lightbox "tripla captura de tela da página de escala centralizada")

O `Slider` elementos deste programa tem um intervalo de &ndash;10 a 10. Como você pode ver, valores negativos de vertical escala (como no Android tela no centro) causam objetos Inverter em torno do eixo horizontal que passa através do centro da escala. Valores negativos de horizontal para escala (como a tela UWP à direita) causam objetos Inverter em torno do eixo vertical que passa através do centro da escala.

Esta versão quarto do `Scale` método é realmente um atalho. Talvez você queira ver como isso funciona, substituindo o `Scale` método nesse código com o seguinte:

```csharp
canvas.Translate(-px, -py);
```

Esses são negativos das coordenadas de ponto de pivot.

Agora, execute o programa novamente. Você verá que o retângulo e texto são deslocados para que o centro está no canto superior esquerdo da tela. Você pode ver mal-lo. Os controles deslizantes não funcionam claro porque agora o programa não pode ser dimensionado em todos os.

Agora adicione básica `Scale` chamar (sem um centro de escala) *antes de* que `Translate` chamar:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se você estiver familiarizado com esse exercício em outros que sistemas de programação de gráficos, você pode pensar que está errado, mas ele não é. Skia controla chamadas sucessivas transformação um pouco diferente do que você talvez esteja familiarizado com.

Com o sucessivas `Scale` e `Translate` chamadas, o centro do retângulo arredondado ainda está no canto superior esquerdo, mas agora você poderá dimensioná-lo em relação ao canto superior esquerdo da tela, também é o centro do retângulo arredondado.

Agora, antes que `Scale` chamada adicionar outro `Translate` chamada com os valores de centralização:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Isso move o resultado em escala de volta à posição original. Esses três chamadas são equivalentes a:

```csharp
canvas.Scale(sx, sy, px, py);
```

As transformações individuais são compostas de forma que a fórmula de transformação total é:

 x' = sx · (x – px) + px

 y' = sy · (y – py) + piar

Lembre-se de que os valores padrão de `sx` e `sy` são 1. É fácil para convencê-mesmo que o ponto pivô (px, piar) não é transformado por essas fórmulas. Ele permanece no mesmo local em relação a tela.

Quando você combina `Translate` e `Scale` a ordem de chamadas, é importante. Se o `Translate` vem após o `Scale`, os fatores de conversão são dimensionados efetivamente com os fatores de dimensionamento. Se o `Translate` antecede o `Scale`, os fatores de conversão não são dimensionados. Esse processo se torna um pouco mais claro (embora mais matemática) quando o assunto das matrizes de transformação é apresentado.

O `SKPath` classe define um somente leitura [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) propriedade que retorna um `SKRect` definindo a extensão das coordenadas no caminho. Por exemplo, quando o `Bounds` propriedade é obtida do caminho hendecagram criado anteriormente, o `Left` e `Top` propriedades do retângulo são aproximadamente -100, o `Right` e `Bottom` são propriedades aproximadamente 100 e o `Width` e `Height` propriedades são aproximadamente 200. (A maioria dos valores reais é um pouco menor porque os pontos de estrelas são definidos por um círculo com raio de 100, mas apenas o ponto superior é paralelo com os eixos horizontais ou verticais.)

A disponibilidade dessas informações implica que deve ser possível derivar de escala e converter fatores adequados para dimensionar um caminho para o tamanho da tela. O [ **dimensionamento anisotrópico** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) página demonstra isso com a estrela apontada 11. Um *anisotrópico* escala significa que ela é diferente nas direções horizontais e verticais, o que significa que o asterisco não manterá sua taxa de proporção original. Aqui está o código relevante `PaintSurface` manipulador:

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

O `pathBounds` retângulo é obtido na parte superior do código e usado posteriormente com a largura e altura da tela no `Scale` chamar. Chamada por si só dimensionará as coordenadas do caminho quando ele for renderizado o `DrawPath` chamada mas estrela será centralizada no canto superior direito da tela. Ele precisa ser alterado para baixo e para a esquerda. Esse é o trabalho do `Translate` chamar. Essas duas propriedades de `pathBounds` são aproximadamente -100, portanto, os fatores de conversão são aproximadamente 100. Porque o `Translate` é chamada após o `Scale` chamar, esses valores são efetivamente dimensionadas com os fatores de dimensionamento para o centro da estrela terem sido movidos para o centro da tela:

[![](scale-images/anisotropicscaling-small.png "Tripla captura de tela da página dimensionamento anisotrópico")](scale-images/anisotropicscaling-large.png#lightbox "tripla captura de tela da página dimensionamento anisotrópico")

Outra maneira, você pode pensar sobre o `Scale` e `Translate` chamadas é determinar o efeito em sequência inversa: O `Translate` chamada desloca o caminho de forma que seja totalmente visível, mas orientado por no canto superior esquerdo da tela. O `Scale` método torna esse estrela maior em relação ao canto superior esquerdo.

Na verdade, parece que estrela é um pouco maior do que a tela. O problema é a largura do traço. O `Bounds` propriedade `SKPath` indica as dimensões das coordenadas codificado no caminho, e que é o que o programa usa para dimensioná-lo. Quando o caminho é renderizado com uma largura do traço específico, o caminho renderizado é maior que a tela.

Para corrigir esse problema, você precisa compensar que. Uma abordagem simples para este programa é adicionar o direito de instrução seguinte antes do `Scale` chamar:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Isso aumenta a `pathBounds` retângulo 1,5 unidades todos os quatro lados. Esta é uma solução de razoável somente quando a junção de traço é arredondada. Uma associação de esquadria pode ser maior e é difícil de ser calculado.

Você também pode usar uma técnica semelhante com texto, como o **texto anisotrópico** demonstra da página. Aqui está a parte relevante do `PaintSurface` manipulador do [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

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

Lógica semelhante é e o texto se expande para o tamanho da página com base no retângulo de limites de texto retornado de `MeasureText` (que é um pouco maior do que o texto real):

[![](scale-images/anisotropictext-small.png "Captura de tela da página de teste anisotrópico tripla")](scale-images/anisotropictext-large.png#lightbox "tripla captura de tela da página de teste anisotrópico")

Se você precisar preservar a proporção de objetos gráficos, você desejará usar o dimensionamento isotropic. O **dimensionamento Isotropic** página demonstra isso para estrela apontada 11. Conceitualmente, as etapas para exibir um objeto gráfico no centro da página com o dimensionamento isotropic são:

- Converte o centro do objeto gráfico para o canto superior esquerdo.
- Dimensione o objeto com base nos valores mínimo das dimensões de página horizontais e verticais dividido pelas dimensões objeto gráfico.
- Converte o centro do objeto em escala para o centro da página.

O [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) executa estas etapas na ordem inversa antes de exibir a estrela:

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

O código também exibirá estrela dez vezes mais, cada vez que o dimensionamento de diminuição fatores 10% e progressivamente alterando a cor de vermelho para azul:

[![](scale-images/isotropicscaling-small.png "Captura de tela da página dimensionamento Isotropic tripla")](scale-images/isotropicscaling-large.png#lightbox "tripla captura de tela da página dimensionamento Isotropic")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
