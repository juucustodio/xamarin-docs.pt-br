---
title: A transformação de escala
description: O artigo Thhis explora a transformação de escala SkiaSharp para dimensionar objetos em vários tamanhos e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5cb43bfe572b98a6530dfeb8d923ac71b5b633a7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932035"
---
# <a name="the-scale-transform"></a>A transformação de escala

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Descobrir a transformação de escala de SkiaSharp para dimensionar objetos para vários tamanhos_

Como você viu no artigo [**converter transformação**](translate.md) , a transformação Converter pode mover um objeto gráfico de um local para outro. Por outro lado, a transformação escala altera o tamanho do objeto gráfico:

![Uma palavra alta dimensionada em tamanho](scale-images/scaleexample.png)

A transformação de escala também faz com que as coordenadas de elementos gráficos sejam movidas à medida que são feitas maiores.

Anteriormente, você viu duas fórmulas de transformação que descrevem os efeitos dos fatores de tradução de `dx` e `dy` :

x ' = x + DX

y ' = y + DY

Fatores de escala de `sx` e `sy` são multiplicativa em vez de aditivo:

x ' = SX · w.x.y.

y ' = Sy · Iar

Os valores padrão dos fatores de conversão são 0; os valores padrão dos fatores de escala são 1.

A `SKCanvas` classe define quatro `Scale` métodos. O primeiro [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) método é para casos em que você deseja o mesmo fator de escala horizontal e vertical:

```csharp
public void Scale (Single s)
```

Isso é conhecido como dimensionamento de escala *isotropic* &mdash; que é o mesmo em ambas as direções. O dimensionamento de isotropic preserva a taxa de proporção do objeto.

O segundo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) método permite especificar valores diferentes para o dimensionamento horizontal e vertical:

```csharp
public void Scale (Single sx, Single sy)
```

Isso resulta em dimensionamento de *anisotropic* .
O terceiro [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) método combina os dois fatores de dimensionamento em um único `SKPoint` valor:

```csharp
public void Scale (SKPoint size)
```

O quarto `Scale` método será descrito em breve.

A página **escala básica** demonstra o `Scale` método. O arquivo [**BasicScalePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contém dois `Slider` elementos que permitem selecionar fatores de dimensionamento horizontal e vertical entre 0 e 10. O arquivo code-behind [**BasicScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa esses valores para chamar `Scale` antes de exibir um retângulo arredondado traçado com uma linha tracejada e dimensionado para ajustar um texto no canto superior esquerdo da tela:

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

Você pode estar imaginando: como os fatores de dimensionamento afetam o valor retornado do `MeasureText` método de `SKPaint` ? A resposta é: não. `Scale`é um método de `SKCanvas` . Ele não afeta nada que você faça com um `SKPaint` objeto até que você use esse objeto para renderizar algo na tela.

Como você pode ver, tudo desenhado após a `Scale` chamada aumenta proporcionalmente:

[![Captura de tela tripla da página de escala básica](scale-images/basicscale-small.png)](scale-images/basicscale-large.png#lightbox "Captura de tela tripla da página de escala básica")

O texto, a largura da linha tracejada, o comprimento dos traços nessa linha, o arredondamento dos cantos e a margem de 10 pixels entre as bordas esquerda e superior da tela e o retângulo arredondado estão todos sujeitos aos mesmos fatores de dimensionamento.

> [!IMPORTANT]
> O Plataforma Universal do Windows não renderiza corretamente o texto escalado anisotropicly.

O dimensionamento de anisotropic faz com que a largura do traço se torne diferente para linhas alinhadas com os eixos horizontal e vertical. (Isso também é evidente na primeira imagem desta página.) Se você não quiser que a largura do traço seja afetada pelos fatores de dimensionamento, defina-a como 0 e ela sempre terá um pixel de largura, independentemente da `Scale` configuração.

O dimensionamento é relativo ao canto superior esquerdo da tela. Isso pode ser exatamente o que você deseja, mas talvez não seja. Suponha que você deseja posicionar o texto e o retângulo em outro lugar na tela e deseja dimensioná-lo em relação ao seu centro. Nesse caso, você pode usar a quarta versão do [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método, que inclui dois parâmetros adicionais para especificar o centro do dimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Os `px` `py` parâmetros e definem um ponto que às vezes é chamado de *centro de dimensionamento* , mas na documentação do SkiaSharp é chamado de *ponto dinâmico*. Esse é um ponto relativo ao canto superior esquerdo da tela que não é afetado pelo dimensionamento. Todo o dimensionamento ocorre em relação a esse centro.

A página [**escala centralizada**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) mostra como isso funciona. O `PaintSurface` manipulador é semelhante ao programa de **escala básica** , exceto pelo fato de que o `margin` valor é calculado para centralizar o texto horizontalmente, o que significa que o programa funciona melhor no modo retrato:

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

O canto superior esquerdo do retângulo arredondado é posicionado `margin` em pixels à esquerda da tela e `margin` pixels da parte superior. Os dois últimos argumentos para o `Scale` método são definidos para esses valores mais a largura e a altura do texto, que também é a largura e a altura do retângulo arredondado. Isso significa que todo o dimensionamento é relativo ao centro desse retângulo:

[![Captura de tela tripla da página de escala centralizada](scale-images/centeredscale-small.png)](scale-images/centeredscale-large.png#lightbox "Captura de tela tripla da página de escala centralizada")

Os `Slider` elementos neste programa têm um intervalo de &ndash; 10 a 10. Como você pode ver, valores negativos de dimensionamento vertical (como na tela do Android no centro) fazem com que os objetos virem em volta do eixo horizontal que passa pelo centro do dimensionamento. Valores negativos de escala horizontal (como na tela UWP à direita) fazem com que os objetos virem em volta do eixo vertical que passa pelo centro do dimensionamento.

A versão do [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) método com pontos dinâmicos é um atalho para uma série de três `Translate` `Scale` chamadas. Talvez você queira ver como isso funciona substituindo o `Scale` método na página **escala centralizada** pelo seguinte:

```csharp
canvas.Translate(-px, -py);
```

Esses são os negativos das coordenadas de ponto dinâmico.

Agora, execute o programa novamente. Você verá que o retângulo e o texto são deslocados para que o centro esteja no canto superior esquerdo da tela. Você pode mal vê-lo. Os controles deslizantes não funcionam naturalmente, porque agora o programa não é dimensionado.

Agora, adicione a `Scale` chamada básica (sem um centro de dimensionamento) *antes* dessa `Translate` chamada:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se você estiver familiarizado com este exercício em outros sistemas de programação de gráficos, talvez ache que isso está errado, mas não é. O skia lida com as chamadas de transformação sucessivas de um pouco diferente do que você pode estar familiarizado.

Com as `Scale` chamadas e sucessivas `Translate` , o centro do retângulo arredondado ainda está no canto superior esquerdo, mas agora você pode dimensioná-lo em relação ao canto superior esquerdo da tela, que também é o centro do retângulo arredondado.

Agora, antes dessa `Scale` chamada, adicione outra `Translate` chamada com os valores de centralização:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Isso move o resultado escalado de volta para a posição original. Essas três chamadas são equivalentes a:

```csharp
canvas.Scale(sx, sy, px, py);
```

As transformações individuais são compostas para que a fórmula de transformação total seja:

 x ' = SX · (x – px) + PX

 y ' = Sy · (y – py) + py

Tenha em mente que os valores padrão de `sx` e `sy` são 1. É fácil convencer-se de que o ponto dinâmico (px, PY) não é transformado por essas fórmulas. Ele permanece no mesmo local em relação à tela.

Quando você combina `Translate` e `Scale` chama, o pedido é importante. Se o `Translate` vier após o `Scale` , os fatores de conversão serão efetivamente dimensionados pelos fatores de dimensionamento. Se o `Translate` vier antes do `Scale` , os fatores de conversão não serão dimensionados. Esse processo se torna um pouco mais claro (embora mais matemático) quando o assunto das matrizes de transformação é introduzido.

A `SKPath` classe define uma propriedade somente leitura [`Bounds`](xref:SkiaSharp.SKPath.Bounds) que retorna um `SKRect` definindo a extensão das coordenadas no caminho. Por exemplo, quando a `Bounds` propriedade é obtida do caminho hendecagram criado anteriormente, as `Left` Propriedades e `Top` do retângulo são aproximadamente – 100, as `Right` `Bottom` Propriedades e são aproximadamente 100, e as `Width` `Height` Propriedades e são aproximadamente 200. (A maioria dos valores reais é um pouco menor porque os pontos das estrelas são definidos por um círculo com um raio de 100, mas apenas o ponto superior é paralelo com os eixos horizontal ou vertical.)

A disponibilidade dessas informações implica que deve ser possível derivar os fatores de escala e de conversão adequados para dimensionar um caminho para o tamanho da tela. A página de [**dimensionamento anisotropic**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) demonstra isso com a estrela de 11 pontas. Uma escala *anisotropic* significa que ela não é igual nas direções horizontal e vertical, o que significa que a estrela não manterá sua taxa de proporção original. Este é o código relevante no `PaintSurface` manipulador:

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

O `pathBounds` retângulo é obtido próximo à parte superior desse código e, em seguida, usado posteriormente com a largura e a altura da tela na `Scale` chamada. Essa chamada por si só dimensionará as coordenadas do caminho quando ela for renderizada pela `DrawPath` chamada, mas a estrela será centralizada no canto superior direito da tela. Ele precisa ser deslocado para baixo e para a esquerda. Esse é o trabalho da `Translate` chamada. Essas duas propriedades de `pathBounds` são aproximadamente – 100, portanto, os fatores de conversão são cerca de 100. Como a `Translate` chamada é após a `Scale` chamada, esses valores são efetivamente dimensionados pelos fatores de dimensionamento, de modo que movem o centro da estrela para o centro da tela:

[![Captura de tela tripla da página de dimensionamento de anisotropic](scale-images/anisotropicscaling-small.png)](scale-images/anisotropicscaling-large.png#lightbox "Captura de tela tripla da página de dimensionamento de anisotropic")

Outra maneira de pensar sobre as `Scale` chamadas e `Translate` é determinar o efeito na sequência inversa: a `Translate` chamada muda o caminho para que ele se torne totalmente visível, mas orientado no canto superior esquerdo da tela. `Scale`Em seguida, o método torna essa estrela maior em relação ao canto superior esquerdo.

Na verdade, parece que a estrela é um pouco maior do que a tela. O problema é a largura do traço. A `Bounds` propriedade de `SKPath` indica as dimensões das coordenadas codificadas no caminho, e é isso o que o programa usa para dimensioná-lo. Quando o caminho é renderizado com uma largura de traço específica, o caminho renderizado é maior do que a tela.

Para corrigir esse problema, você precisa compensar isso. Uma abordagem fácil nesse programa é adicionar a seguinte instrução logo antes da `Scale` chamada:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Isso aumenta o `pathBounds` retângulo de 1,5 unidades em todos os quatro lados. Essa é uma solução razoável somente quando a junção do traço é arredondada. Uma junção de mitra pode ser mais longa e difícil de calcular.

Você também pode usar uma técnica semelhante com texto, como demonstra a página de **texto anisotropic** . Aqui está a parte relevante do `PaintSurface` manipulador da [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

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

É uma lógica semelhante, e o texto se expande para o tamanho da página com base no retângulo de limites de texto retornado `MeasureText` (que é um pouco maior do que o texto real):

[![Captura de tela tripla da página de teste do anisotropic](scale-images/anisotropictext-small.png)](scale-images/anisotropictext-large.png#lightbox "Captura de tela tripla da página de teste do anisotropic")

Se você precisar preservar a taxa de proporção dos objetos gráficos, convém usar o dimensionamento de isotropic. A página de **dimensionamento isotropic** demonstra isso para a estrela de 11 pontas. Conceitualmente, as etapas para exibir um objeto gráfico no centro da página com o dimensionamento de isotropic são:

- Traduza o centro do objeto gráfico para o canto superior esquerdo.
- Dimensione o objeto com base no mínimo das dimensões de página horizontal e vertical dividida pelas dimensões do objeto gráfico.
- Traduza o centro do objeto dimensionado para o centro da página.

O [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) executa estas etapas na ordem inversa antes de exibir a estrela:

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

O código também exibe a estrela 10 mais vezes, cada vez que diminui o fator de dimensionamento em 10% e altera progressivamente a cor de vermelho para azul:

[![Captura de tela tripla da página de dimensionamento de isotropic](scale-images/isotropicscaling-small.png)](scale-images/isotropicscaling-large.png#lightbox "Captura de tela tripla da página de dimensionamento de isotropic")

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
