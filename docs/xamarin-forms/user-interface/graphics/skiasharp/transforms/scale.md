---
title: A transformação de escala
description: Thhis artigo explora a transformação de escala de SkiaSharp para dimensionar objetos para vários tamanhos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292252"
---
# <a name="the-scale-transform"></a>A transformação de escala

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descobrir a transformação de escala de SkiaSharp para dimensionar objetos para vários tamanhos_

Como você viu no artigo [**converter transformação**](translate.md) , a transformação Converter pode mover um objeto gráfico de um local para outro. Por outro lado, a transformação de escala altera o tamanho do objeto gráfico:

![](scale-images/scaleexample.png "A tall word scaled in size")

A transformação de escala também geralmente faz com que as coordenadas de gráficos mover conforme elas são feitas maiores.

Anteriormente, você viu duas fórmulas de transformação que descrevem os efeitos dos fatores de tradução de `dx` e `dy`:

x' = x + dx

y' = y + dy

Fatores de escala de `sx` e `sy` são multiplicativa em vez de aditivo:

x' = sx · x

y' = sy reforçado y

Os valores padrão dos fatores traduzir são 0; os valores padrão dos fatores de dimensionamento são 1.

A classe `SKCanvas` define quatro métodos `Scale`. O primeiro método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) é para casos em que você deseja o mesmo fator de escala horizontal e vertical:

```csharp
public void Scale (Single s)
```

Isso é conhecido como dimensionamento de *isotropic* &mdash; dimensionamento que é o mesmo em ambas as direções. Dimensionamento Isotropic preserva a taxa de proporção do objeto.

O segundo método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) permite especificar valores diferentes para o dimensionamento horizontal e vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Isso resulta em dimensionamento de *anisotropic* .
O terceiro método de [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) combina os dois fatores de dimensionamento em um único valor de `SKPoint`:

```csharp
public void Scale (SKPoint size)
```

O quarto método de `Scale` será descrito em breve.

A página **escala básica** demonstra o método `Scale`. O arquivo [**BasicScalePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contém dois elementos `Slider` que permitem que você selecione fatores de dimensionamento horizontal e vertical entre 0 e 10. O arquivo code-behind [**BasicScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa esses valores para chamar `Scale` antes de exibir um retângulo arredondado traçado com uma linha tracejada e dimensionado para ajustar texto no canto superior esquerdo da tela:

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

Você pode estar imaginando: como os fatores de dimensionamento afetam o valor retornado do método `MeasureText` de `SKPaint`? A resposta é: nada. `Scale` é um método de `SKCanvas`. Ele não afeta nada que você faça com um objeto `SKPaint` até que você use esse objeto para renderizar algo na tela.

Como você pode ver, tudo desenhado após a `Scale` chamada aumenta proporcionalmente:

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

O texto, a largura da linha tracejada, o comprimento dos traços na linha, o arredondamento de cantos e a margem de 10 pixels, entre as bordas esquerdas e superior da tela e o retângulo arredondado são todos sujeito aos mesmos fatores de dimensionamento.

> [!IMPORTANT]
> A plataforma Universal do Windows não renderizar texto anisotropicly dimensionado corretamente.

Anisotrópica dimensionamento faz com que a largura do traço para se tornar diferente para linhas alinhada com os eixos horizontais e verticais. (Isso também é evidente na primeira imagem desta página.) Se você não quiser que a largura do traço seja afetada pelos fatores de dimensionamento, defina-a como 0 e ela sempre terá um pixel de largura, independentemente da configuração de `Scale`.

O dimensionamento é relativa ao canto superior esquerdo da tela. Isso pode ser exatamente o que você deseja, mas pode não ser. Suponha que você deseja posicionar o texto e o retângulo em algum lugar na tela e você deseja dimensioná-lo em relação ao seu centro. Nesse caso, você pode usar a quarta versão do método [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) , que inclui dois parâmetros adicionais para especificar o centro do dimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Os parâmetros `px` e `py` definem um ponto que, às vezes, é chamado de *centro de dimensionamento* , mas na documentação do SkiaSharp é chamado de *ponto dinâmico*. Esse é um ponto em relação ao canto superior esquerdo da tela de que não é afetado pela colocação em escala. Todo dimensionamento ocorre relativas a esse centro.

A página [**escala centralizada**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) mostra como isso funciona. O manipulador de `PaintSurface` é semelhante ao programa de **escala básica** , exceto pelo fato de que o valor de `margin` é calculado para centralizar o texto horizontalmente, o que significa que o programa funciona melhor no modo retrato:

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

O canto superior esquerdo do retângulo arredondado é posicionado `margin` pixels à esquerda da tela e `margin` pixels da parte superior. Os dois últimos argumentos para o método `Scale` são definidos como esses valores, além da largura e altura do texto, que também é a largura e a altura do retângulo arredondado. Isso significa que todo dimensionamento é relativo ao centro do retângulo:

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Os elementos `Slider` neste programa têm um intervalo de &ndash;10 a 10. Como você pode ver, valores negativos de dimensionamento (por exemplo, no Android, tela no centro) vertical fazem os objetos girar em torno do eixo horizontal que passa pelo centro do dimensionamento. Valores negativos de (como a tela UWP à direita) o dimensionamento horizontal fazem os objetos girar em torno do eixo vertical que passa pelo centro do dimensionamento.

A versão do método de [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) com pontos dinâmicos é um atalho para uma série de três chamadas `Translate` e `Scale`. Talvez você queira ver como isso funciona substituindo o método `Scale` na página **escala centralizada** pelo seguinte:

```csharp
canvas.Translate(-px, -py);
```

Esses são os negativos das coordenadas de ponto dinâmico.

Agora, execute o programa novamente. Você verá que o retângulo e texto são deslocados para que o centro está no canto superior esquerdo da tela. Você pode vê-lo mal. Os controles deslizantes não funcionam, é claro, porque agora o programa não pode ser escalonada em todos os.

Agora, adicione a chamada de `Scale` básica (sem um centro de dimensionamento) *antes* dessa chamada `Translate`:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se você estiver familiarizado com este exercício em outros que sistemas de programação de gráficos, você pode pensar que há de errado, mas ele não é. Skia manipula chamadas sucessivas transformação um pouco diferente do qual você pode estar familiarizado com o.

Com as chamadas sucessivas de `Scale` e `Translate`, o centro do retângulo arredondado ainda está no canto superior esquerdo, mas agora você pode dimensioná-lo em relação ao canto superior esquerdo da tela, que também é o centro do retângulo arredondado.

Agora, antes que `Scale` chamada, adicione outra chamada de `Translate` com os valores de centralização:

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

Tenha em mente que os valores padrão de `sx` e `sy` são 1. É fácil convencido de que o ponto dinâmico (px, Aj) não é transformado por essas fórmulas. Ele permanece no mesmo local em relação à tela.

Quando você combina `Translate` e `Scale` chamadas, a ordem é importante. Se o `Translate` vier após o `Scale`, os fatores de conversão serão efetivamente dimensionados pelos fatores de dimensionamento. Se a `Translate` vier antes da `Scale`, os fatores de conversão não serão dimensionados. Esse processo se torna um pouco mais claro (embora mais matemática) quando o assunto de matrizes de transformação é apresentado.

A classe `SKPath` define uma propriedade de [`Bounds`](xref:SkiaSharp.SKPath.Bounds) somente leitura que retorna um `SKRect` definindo a extensão das coordenadas no caminho. Por exemplo, quando a propriedade `Bounds` é obtida do caminho hendecagram criado anteriormente, as propriedades `Left` e `Top` do retângulo são aproximadamente – 100, as propriedades `Right` e `Bottom` são aproximadamente 100 e as propriedades `Width` e `Height` são aproximadamente 200. (A maioria dos valores reais é um pouco menor porque os pontos das estrelas são definidos por um círculo com um raio de 100, mas apenas o ponto superior é paralelo com os eixos horizontais ou verticais.)

A disponibilidade dessas informações significa que deve ser possível derivar de escala e traduzir fatores adequados para o dimensionamento de um caminho para o tamanho da tela. A página de [**dimensionamento anisotropic**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) demonstra isso com a estrela de 11 pontas. Uma escala *anisotropic* significa que ela não é igual nas direções horizontal e vertical, o que significa que a estrela não manterá sua taxa de proporção original. Este é o código relevante no manipulador de `PaintSurface`:

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

O retângulo `pathBounds` é obtido próximo ao topo desse código e, em seguida, usado posteriormente com a largura e a altura da tela na chamada `Scale`. Essa chamada por si só dimensionará as coordenadas do caminho quando ela for renderizada pela chamada `DrawPath`, mas a estrela será centralizada no canto superior direito da tela. Ele precisa ser deslocados para baixo e para a esquerda. Esse é o trabalho da chamada `Translate`. Essas duas propriedades de `pathBounds` são aproximadamente – 100, portanto, os fatores de conversão são cerca de 100. Como a chamada de `Translate` é após a chamada de `Scale`, esses valores são efetivamente dimensionados pelos fatores de dimensionamento, de modo que movem o centro da estrela para o centro da tela:

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Outra maneira de pensar sobre as chamadas `Scale` e `Translate` é determinar o efeito na sequência inversa: a chamada `Translate` muda o caminho para que fique totalmente visível, mas orientado no canto superior esquerdo da tela. Em seguida, o método `Scale` torna essa estrela maior em relação ao canto superior esquerdo.

Na verdade, parece que o star é um pouco maior do que a tela. O problema é a largura do traço. A propriedade `Bounds` de `SKPath` indica as dimensões das coordenadas codificadas no caminho, e é isso o que o programa usa para dimensioná-lo. Quando o caminho é renderizado com uma largura de traço específica, o caminho renderizado é maior que a tela.

Para corrigir esse problema, você precisa compensar para fazer isso. Uma abordagem fácil nesse programa é adicionar a seguinte instrução logo antes da chamada de `Scale`:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Isso aumenta o `pathBounds` retângulo de 1,5 unidades em todos os quatro lados. Isso é uma solução razoável somente quando a junção de traço é arredondada. Uma junção de Malhete pode ser mais longo e é difícil calcular.

Você também pode usar uma técnica semelhante com texto, como demonstra a página de **texto anisotropic** . Aqui está a parte relevante do manipulador de `PaintSurface` da classe [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) :

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

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Se você precisar preservar a taxa de proporção de objetos gráficos, você desejará usar o dimensionamento isotropic. A página de **dimensionamento isotropic** demonstra isso para a estrela de 11 pontas. Conceitualmente, as etapas para exibir um objeto gráfico no centro da página com o dimensionamento isotropic são:

- Traduza o centro do objeto gráfico para o canto superior esquerdo.
- Dimensione o objeto com base nos valores mínimo das dimensões de página horizontais e verticais, dividido pelas dimensões do objeto gráfico.
- Traduza o centro do objeto em escala para o centro da página.

O [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) executa essas etapas na ordem inversa antes de exibir a estrela:

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

O código também exibe os 10 estrela mais vezes, cada vez que o dimensionamento de diminuição fatorar 10% e progressivamente alterando a cor de vermelho para azul:

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
