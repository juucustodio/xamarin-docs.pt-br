---
title: Dados do caminho SVG em SkiaSharp
description: Este artigo explica como definir caminhos SkiaSharp usando cadeias de caracteres de texto no formato gráfico de vetor escalonável e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12af5d9be025fb2113d70a93a364619aff75598d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370059"
---
# <a name="svg-path-data-in-skiasharp"></a>Dados do caminho SVG em SkiaSharp

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Definir caminhos usando cadeias de caracteres de texto no formato gráfico de vetor escalonável_

A [`SKPath`](xref:SkiaSharp.SKPath) classe oferece suporte à definição de objetos de caminho inteiros a partir de cadeias de caracteres de texto em um formato estabelecido pela especificação SVG (gráficos de vetor escalonáveis). Você verá mais adiante neste artigo como você pode representar um caminho inteiro como este em uma cadeia de texto:

![Um caminho de exemplo definido com dados de caminho SVG](path-data-images/pathdatasample.png)

O SVG é uma linguagem de programação gráfica baseada em XML para páginas da Web. Como o SVG deve permitir que os caminhos sejam definidos na marcação, em vez de uma série de chamadas de função, o padrão SVG inclui uma maneira extremamente concisa de especificar um caminho gráfico inteiro como uma cadeia de texto.

Em SkiaSharp, esse formato é conhecido como "SVG Path-Data." O formato também tem suporte em ambientes de programação baseados em XAML do Windows, incluindo o Windows Presentation Foundation e o Plataforma Universal do Windows, onde é conhecido como a [sintaxe de marcação de caminho](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) ou a sintaxe de [comandos move e Draw](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Ele também pode servir como um formato de troca para imagens gráficas vetoriais, especialmente em arquivos baseados em texto, como XML.

A [`SKPath`](xref:SkiaSharp.SKPath) classe define dois métodos com as palavras `SvgPathData` em seus nomes:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

O [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) método estático converte uma cadeia de caracteres em um `SKPath` objeto, enquanto [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) converte um `SKPath` objeto em uma cadeia de caracteres.

Aqui está uma cadeia de caracteres SVG para uma estrela de cinco pontas centralizada no ponto (0, 0) com um raio de 100:

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

As letras são comandos que criam um `SKPath` objeto: `M` indica uma `MoveTo` chamada, `L` é `LineTo` e `Z` é `Close` fechar uma delimitação. Cada par de números fornece uma coordenada X e Y de um ponto. Observe que o `L` comando é seguido por vários pontos separados por vírgulas. Em uma série de coordenadas e pontos, vírgulas e espaços em branco são tratados de forma idêntica. Alguns programadores preferem colocar vírgulas entre as coordenadas X e Y em vez de entre os pontos, mas as vírgulas ou os espaços são necessários apenas para evitar ambigüidade. Isso é perfeitamente legal:

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

A sintaxe dos dados do caminho SVG está formalmente documentada na [seção 8,3 da especificação SVG](https://www.w3.org/TR/SVG11/paths.html#PathData). Aqui está um resumo:

## <a name="moveto"></a>**Mover**

```
M x y
```

Isso inicia uma nova delimitação no caminho definindo a posição atual. Os dados de caminho devem sempre começar com um `M` comando.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Esse comando adiciona uma linha reta (ou linhas) ao caminho e define a nova posição atual para o final da última linha. Você pode seguir o `L` comando com vários pares de coordenadas *x* e *y* .

## <a name="horizontal-lineto"></a>**Linha horizontalto**

```
H x ...
```

Esse comando adiciona uma linha horizontal ao caminho e define a nova posição atual para o fim da linha. Você pode seguir o `H` comando com várias coordenadas *x* , mas isso não faz muito sentido.

## <a name="vertical-line"></a>**Linha vertical**

```
V y ...
```

Esse comando adiciona uma linha vertical ao caminho e define a nova posição atual para o fim da linha.

## <a name="close"></a>**Fechar**

```
Z
```

O `C` comando fecha a delimitação adicionando uma linha reta da posição atual até o início da delimitação.

## <a name="arcto"></a>**ArcTo**

O comando para adicionar um arco elíptico à delimitação é, de longe, o comando mais complexo em toda a especificação de dados de caminho SVG. É o único comando no qual os números podem representar algo diferente dos valores de coordenadas:

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Os parâmetros *RX* e *entar* são os raios horizontais e verticais da elipse. O *ângulo de rotação* é o sentido horário em graus.

Defina o *sinalizador de arco grande* para 1 para o arco grande ou para 0 para o arco pequeno.

Defina o *sinalizador de varredura* como 1 para o sentido horário e 0 para o sentido anti-horário.

O arco é desenhado para o ponto ( *x* , *y* ), que se torna a nova posição atual.

## <a name="cubicto"></a>**Cúbicoto**

```
C x1 y1 x2 y2 x3 y3 ...
```

Esse comando adiciona uma curva Bézier cúbica da posição atual para ( *X3* , *y3* ), que se torna a nova posição atual. Os pontos ( *X1* , *Y1* ) e ( *X2* , *Y2* ) são pontos de controle.

Várias curvas Bézier podem ser especificadas por um único `C` comando. O número de pontos deve ser um múltiplo de 3.

Também há um comando de curva Bézier "suave":

```
S x2 y2 x3 y3 ...
```

Esse comando deve seguir um comando de Bézier regular (embora isso não seja estritamente necessário). O comando Smooth Bézier calcula o primeiro ponto de controle para que ele seja um reflexo do segundo ponto de controle do Bézier anterior em relação ao seu ponto mútuo. Portanto, esses três pontos são colineares e a conexão entre as duas curvas Bézier é suave.

## <a name="quadto"></a>**Quádruplo**

```
Q x1 y1 x2 y2 ...
```

Para curvas Bézier quadráticas, o número de pontos deve ser um múltiplo de 2. O ponto de controle é ( *X1* , *Y1* ) e o ponto de extremidade (e a nova posição atual) é ( *X2* , *Y2* )

Há também um comando de curva quadrática suave:

```
T x2 y2 ...
```

O ponto de controle é calculado com base no ponto de controle da curva quadrática anterior.

Todos esses comandos também estão disponíveis em versões "relativas", em que os pontos de coordenadas são relativos à posição atual. Esses comandos relativos começam com letras minúsculas, por exemplo, `c` em vez de `C` para a versão relativa do comando Bézier cúbico.

Essa é a extensão da definição de dados de caminho SVG. Não há nenhum recurso para repetir grupos de comandos ou para executar qualquer tipo de cálculo. Comandos para `ConicTo` ou outros tipos de especificações ARC não estão disponíveis.

O [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) método estático espera uma cadeia de caracteres válida de comandos SVG. Se qualquer erro de sintaxe for detectado, o método retornará `null` . Essa é a única indicação de erro.

O [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) método é útil para obter dados de caminho SVG de um `SKPath` objeto existente para transferir para outro programa ou para armazenar em um formato de arquivo baseado em texto, como XML. (O `ToSvgPathData` método não é demonstrado no código de exemplo neste artigo.) *Não* Espere `ToSvgPathData` retornar uma cadeia de caracteres correspondente exatamente às chamadas de método que criou o caminho. Em particular, você descobrirá que os arcos são convertidos em vários `QuadTo` comandos, e é assim que eles aparecem nos dados de caminho retornados de `ToSvgPathData` .

A página de **saudação de dados de caminho** soletra a palavra "Olá" usando dados de caminho SVG. Os `SKPath` objetos e `SKPaint` são definidos como campos na [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

O caminho que define a cadeia de texto começa no canto superior esquerdo no ponto (0, 0). Cada letra tem 50 unidades de largura e 100 unidades de altura e as letras são separadas por outras 25 unidades, o que significa que o caminho inteiro tem 350 unidades de largura.

O ' H ' de "Olá" é composto de contornos de linha de 3 1, enquanto ' E ' são duas curvas Bézier cúbicas conectadas. Observe que o `C` comando é seguido por seis pontos, e dois dos pontos de controle têm coordenadas y de – 10 e 110, o que os coloca fora do intervalo das coordenadas y das outras letras. O ' L' é duas linhas conectadas, enquanto o ' O ' é uma elipse que é renderizada com um `A` comando.

Observe que o `M` comando que começa a última delimitação define a posição para o ponto (350, 50), que é o centro vertical do lado esquerdo do ' o '. Conforme indicado pelos primeiros números que seguem o `A` comando, a elipse tem um raio horizontal de 25 e um raio vertical de 50. O ponto de extremidade é indicado pelo último par de números no `A` comando, que representa o ponto (300, 49,9). Isso é deliberadamente um pouco diferente do ponto de partida. Se o ponto de extremidade for definido como igual ao início, o arco não será renderizado. Para desenhar uma elipse completa, você deve definir o ponto de extremidade próximo a (mas não igual a) o início, ou deve usar dois ou mais `A` comandos, cada um para parte da elipse completa.

Talvez você queira adicionar a instrução a seguir ao construtor da página e, em seguida, definir um ponto de interrupção para examinar a cadeia de caracteres resultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Você descobrirá que o arco foi substituído por uma longa série de `Q` comandos para uma aproximação com partes do arco usando curvas Bézier quadráticas.

O `PaintSurface` manipulador obtém os limites rígidos do caminho, que não inclui os pontos de controle das curvas ' e ' e ' O '. As três transformações movem o centro do caminho até o ponto (0, 0), dimensionam o caminho para o tamanho da tela (mas também ocupam a largura do traço em conta) e, em seguida, movem o centro do caminho para o centro da tela:

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

O caminho preenche a tela, o que parece mais razoável quando exibido no modo paisagem:

[![Captura de tela tripla da página de saudação de dados de caminho](path-data-images/pathdatahello-small.png)](path-data-images/pathdatahello-large.png#lightbox "Captura de tela tripla da página de saudação de dados de caminho")

A página **Cat de dados de caminho** é semelhante. Os objetos Path e Paint são definidos como campos na [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

O cabeçalho de um gato é um círculo, e aqui ele é renderizado com dois `A` comandos, cada um deles com um semicírculo. Os dois `A` comandos para a cabeça definem raios horizontais e verticais de 100. O primeiro arco começa em (240, 100) e termina em (240, 300), que se torna o ponto de partida para o segundo arco que termina em (240, 100).

Os dois olhos também são renderizados com dois `A` comandos e, assim como acontece com a cabeça do gato, o segundo `A` comando termina no mesmo ponto que o início do primeiro `A` comando. No entanto, esses pares de `A` comandos não definem uma elipse. O com de cada arco é de 40 unidades e o raio também é 40 unidades, o que significa que esses arcos não são em semicírculos totais.

O `PaintSurface` manipulador executa transformações semelhantes como o exemplo anterior, mas define um único `Scale` fator para manter a taxa de proporção e fornecer uma pequena margem para que as caixas estreitas do gato não se preocupem com os lados da tela:

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Este é o programa em execução:

[![Captura de tela tripla da página Cat de dados do caminho](path-data-images/pathdatacat-small.png)](path-data-images/pathdatacat-large.png#lightbox "Captura de tela tripla da página Cat de dados do caminho")

Normalmente, quando um `SKPath` objeto é definido como um campo, os contornos do caminho devem ser definidos no construtor ou em outro método. No entanto, ao usar dados de caminho SVG, você viu que o caminho pode ser totalmente especificado na definição de campo.

O exemplo de **relógio analógico feio** anterior no artigo [**de transformação girar**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) exibiu as mãos do relógio como linhas simples. O programa de **relógio muito analógico** abaixo substitui essas linhas por `SKPath` objetos definidos como campos na [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe junto com `SKPaint` objetos:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

As mãos de hora e minuto agora têm áreas fechadas. Para tornar essas mãos distintas umas das outras, elas são desenhadas com um contorno preto e um preenchimento cinza usando os `handStrokePaint` `handFillPaint` objetos e.

No exemplo de **relógio analógico feio** anterior, os pequenos círculos que marcavam as horas e os minutos foram desenhados em um loop. Nesse exemplo de **relógio muito analógico** , uma abordagem totalmente diferente é usada: as marcas de hora e de minuto são linhas pontilhadas desenhadas com os `minuteMarkPaint` `hourMarkPaint` objetos e:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

O artigo [**pontos e traços**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) abordou como você pode usar o [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) método para criar uma linha tracejada. O primeiro argumento é uma `float` matriz que geralmente tem dois elementos: o primeiro elemento é o comprimento dos traços e o segundo elemento é a lacuna entre os traços. Quando a `StrokeCap` propriedade é definida como `SKStrokeCap.Round` , as extremidades arredondadas do traço aumentam efetivamente o comprimento do traço na largura do traço em ambos os lados do traço. Portanto, definir o primeiro elemento de matriz como 0 cria uma linha pontilhada.

A distância entre esses pontos é regida pelo segundo elemento da matriz. Como você verá em breve, esses dois `SKPaint` objetos são usados para desenhar círculos com um raio de 90 unidades. A circunferência desse círculo é, portanto, 180 π, o que significa que as marcas de 60 minutos devem aparecer todas as unidades 3π, que é o segundo valor na `float` matriz no `minuteMarkPaint` . As 12 horas devem aparecer em todas as unidades 15π, que é o valor na segunda `float` matriz.

A `PrettyAnalogClockPage` classe define um temporizador para invalidar a superfície a cada 16 milissegundos e o `PaintSurface` manipulador é chamado com essa taxa. As definições anteriores dos `SKPath` objetos e `SKPaint` permitem um código de desenho muito claro:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

No entanto, algo especial é feito com a segunda mão. Como o relógio é atualizado a cada 16 milissegundos, a `Millisecond` Propriedade do `DateTime` valor pode potencialmente ser usada para animar uma varredura de segunda mão, em vez de uma que se move em saltos discretos de segundo para segundo. Mas esse código não permite que a movimentação seja suave. Em vez disso, ele usa as Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) funções de atenuação de animação e para um tipo diferente de movimento. Essas funções de atenuação fazem com que a segunda mão se mova de um jerkier de forma que &mdash; retorne um pouco antes de se mover e, em seguida, migre um pouco o seu destino, um efeito que infelizmente não pode ser reproduzido nessas capturas de tela estáticas:

[![Captura de tela tripla da página de relógio muito analógica](path-data-images/prettyanalogclock-small.png)](path-data-images/prettyanalogclock-large.png#lightbox "Captura de tela tripla da página de relógio muito analógica")

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)