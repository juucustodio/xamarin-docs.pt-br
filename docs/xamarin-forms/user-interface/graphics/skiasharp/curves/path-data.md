---
title: Dados de caminho SVG em SkiaSharp
description: Este artigo explica como definir caminhos de SkiaSharp usando cadeias de caracteres de texto no formato de elementos gráficos vetoriais escaláveis e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: f3c06198ae9e677c667c9216b3ace8784a6056b2
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615321"
---
# <a name="svg-path-data-in-skiasharp"></a>Dados de caminho SVG em SkiaSharp

_Definir caminhos usando cadeias de caracteres de texto no formato de elementos gráficos vetoriais escaláveis_

O `SKPath` classe oferece suporte à definição de objetos de todo o caminho de cadeias de caracteres de texto em um formato estabelecido pela especificação de elementos gráficos vetoriais escaláveis (SVG). Você verá neste artigo como você pode representar um caminho inteiro como esta em uma cadeia de caracteres de texto:

![](path-data-images/pathdatasample.png "Um exemplo de caminho definido com dados de caminho SVG")

O SVG é um linguagem para páginas da web de programação de gráficos baseados em XML. Porque o SVG deve permitir que os caminhos a serem definidos na marcação, em vez de uma série de chamadas de função, o SVG standard inclui uma maneira extremamente concisa de especificar um caminho gráfico inteiro como uma cadeia de caracteres de texto.

Dentro de SkiaSharp, esse formato é conhecido como "-dados de caminho SVG." Também é compatível com o formato XAML do Windows com base em ambientes de programação, incluindo o Windows Presentation Foundation e a plataforma Universal do Windows, onde ele é conhecido como o [sintaxe de marcação de caminho](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx) ou o [mover e desenhe a sintaxe de comandos](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Ele também pode servir como um formato de troca para imagens de gráficos vetoriais, especialmente em arquivos com base em texto, como XML.

SkiaSharp define dois métodos com as palavras `SvgPathData` em seus nomes:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Estático [ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método converte uma cadeia de caracteres para um `SKPath` objeto, enquanto [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) converte um `SKPath` objeto a ser uma cadeia de caracteres.

Aqui está uma cadeia de caracteres SVG para uma estrela de cinco pontas centralizada no ponto (0, 0) com um raio de 100:

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

As letras são comandos que criam um `SKPath` objeto. `M` indica um `MoveTo` chamar, `L` é `LineTo`, e `Z` é `Close` para fechar um contorno. Cada par de números fornece uma coordenada X e Y de um ponto. Observe que o `L` comando é seguido por vários pontos, separados por vírgulas. Em uma série de coordenadas, pontos, vírgulas e espaço em branco são tratados idêntico. Alguns programadores preferem coloque vírgulas entre as coordenadas X e Y, em vez de entre os pontos, mas vírgulas ou espaços são necessárias apenas para evitar ambiguidade. Isso é perfeitamente aceitável:

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

A sintaxe de dados de caminho SVG formalmente documentada no [8.3 da seção da especificação do SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Aqui está um resumo:

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

Isso inicia uma nova delimitação no caminho, definindo a posição atual. Dados de caminho sempre devem começar com um `M` comando.

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

Este comando adiciona uma linha reta (ou linhas) para o caminho e define a nova posição atual até o final da última linha. Você pode seguir a `L` com vários pares de *x* e *y* coordenadas.

## <a name="horizontal-lineto"></a>**Horizontal LineTo**

```csharp
H x ...
```

Este comando adiciona uma linha horizontal para o caminho e define a nova posição atual até o final da linha. Você pode seguir a `H` comando com vários *x* coordenadas, mas ele não faz muito sentido.

## <a name="vertical-line"></a>**Linha vertical**

```csharp
V y ...
```

Este comando adiciona uma linha vertical para o caminho e define a nova posição atual até o final da linha.

## <a name="close"></a>**Fechar**

```csharp
Z
```

O `C` comando fecha a delimitação adicionando uma linha reta da posição atual para o início da delimitação.

## <a name="arcto"></a>**ArcTo**

O comando para adicionar um arco elíptico para a delimitação, sem dúvida é o comando mais complexo na especificação dos dados de caminho SVG inteira. É o único comando em que números podem representar algo diferente de valores de coordenadas:

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

O *rx* e *entar* parâmetros são os raios horizontais e verticais da elipse. O *ângulo de rotação* é no sentido horário em graus.

Defina a *sinalizador grande arco* para 1 para o arco grande ou 0 para o arco pequeno.

Defina as *sinalizador de varredura* como 1 para no sentido horário e como 0 para no sentido anti-horário.

O arco é desenhado para o ponto (*x*, *y*), que se torna a nova posição atual.

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

Este comando adiciona uma curva de Bézier cúbica da posição atual para (*x3*, *y3*), que se torna a nova posição atual. Os pontos (*x1*, *y1*) e (*x2*, *y2*) são pontos de controle.

Várias curvas de Bézier podem ser especificadas por uma única `C` comando. O número de pontos deve ser um múltiplo de 3.

Também é um comando de curva de Bézier "suave":

```csharp
S x2 y2 x3 y3 ...
```

Esse comando deve seguir um comando de Bézier regular (embora não seja estritamente o que é necessário). O comando de Bézier smooth calcula o primeiro ponto de controle para que ele seja um reflexo do segundo ponto de controle de Bézier anterior em torno de seu ponto mútuo. Esses três pontos, portanto, são colinear, e a conexão entre as duas curvas de Bézier é suave.

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

Para curvas de Bézier quadráticas, o número de pontos deve ser um múltiplo de 2. É o ponto de controle (*x1*, *y1*) e o ponto de extremidade (e a nova posição atual) é (*x2*, *y2*)

Também há um comando de curva quadrática suave:

```csharp
T x2 y2 ...
```

O ponto de controle é calculado com base no ponto de controle da curva quadrática anterior.

Todos esses comandos também estão disponíveis nas versões "relativas", em que os pontos de coordenadas são em relação à posição atual. Esses comandos relativos começam com letras minúsculas, por exemplo `c` em vez de `C` para a versão relativa do comando de Bézier cúbica.

Esta é a extensão da definição de dados de caminho SVG. Há um recurso para grupos de comandos de repetição ou para realizar qualquer tipo de cálculo. Comandos para `ConicTo` ou outros tipos de especificações de arco não estão disponíveis.

Estático [ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método espera uma cadeia de caracteres válida de comandos SVG. Se qualquer erro de sintaxe for detectado, o método retorna `null`. Que é a indicação de erro somente.

O [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) método é útil para a obtenção de dados de caminho SVG de uma já existente `SKPath` objeto transferir para outro programa ou para armazenar em um formato de arquivo de texto como XML. (O `ToSvgPathData` método não é demonstrado no código de exemplo neste artigo.) Fazer *não* esperar `ToSvgPathData` para retornar uma cadeia de caracteres correspondente exatamente as chamadas de método que criou o caminho. Em particular, você descobrirá que os arcos são convertidos para vários `QuadTo` comandos, e isso é como eles aparecem nos dados de caminho retornados do `ToSvgPathData`.

O **caminho de dados Hello** página mágicas a palavra "HELLO" usando dados de caminho SVG. Tanto a `SKPath` e `SKPaint` objetos são definidos como campos na [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

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

O caminho que define a cadeia de caracteres de texto começa no canto superior esquerdo no ponto (0, 0). Cada letra é 50 unidades de largura e altura de 100 unidades e letras são separadas por outro 25 unidades, que significa que todo o caminho é 350 unidades amplas.

O 'H' "Olá" é composto de três dos contornos de uma linha, enquanto o 'E' está duas curvas de Bézier cúbicas conectadas. Observe que o `C` comando é seguido por seis pontos e dois dos pontos de controle têm coordenadas Y de – 10 e 110, o que coloca-os fora do intervalo das coordenadas Y das outras letras. O "L" é duas linhas conectadas, enquanto a ' l'é uma elipse que é renderizada com um `A` comando.

Observe que o `M` que começa a delimitação do último comando define a posição para o ponto (350, 50), que é o centro vertical da esquerda lado do ' l'. Conforme indicado pelos seguintes números primeiro o `A` de comando, a elipse tem um raio horizontal de 25 e um raio vertical de 50. O ponto de extremidade é indicado pelo último par de números no `A` comando, que representa o ponto (300, 49.9). Que é deliberadamente apenas um pouco diferente do ponto de início. Se o ponto de extremidade é definido igual ao ponto de início, o arco não será renderizado. Para desenhar uma elipse completa, você deve definir o ponto de extremidade próximo a (mas não é igual a) o ponto de partida, ou você deve usar dois ou mais `A` comandos, cada parte da elipse completa.

Talvez você queira adicionar a seguinte instrução ao construtor da página e, em seguida, defina um ponto de interrupção para examinar a cadeia de caracteres resultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Você descobrirá que o arco foi substituído por uma série de tempo de `Q` comandos para uma restauração por etapas aproximação do arco usando curvas de Bézier quadráticas.

O `PaintSurface` manipulador obtém os limites rígidos do caminho, que não inclui os pontos de controle para o 'E' e ' l' curvas. As três transformações mover o centro do caminho para o ponto (0, 0), dimensionar o caminho para o tamanho da tela (mas também considerando a largura do traço) e, em seguida, mover o centro do caminho para o centro da tela:

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

O caminho preenche a tela, que se parece mais razoável quando exibido no modo paisagem:

[![](path-data-images/pathdatahello-small.png "Caminho dados Hello da página de captura de tela tripla")](path-data-images/pathdatahello-large.png#lightbox "tripla captura de tela de caminho dados Hello página")

O **caminho de dados Cat** página é semelhante. Os objetos de caminho e paint são definidos como campos na [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

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

O cabeçalho de um gato é um círculo, e aqui, ele é renderizado com dois `A` comandos, cada um deles desenha um semicírculo. Ambos `A` os comandos para o cabeçalho define os raios horizontais e verticais de 100. Primeiro arco começa em (240, 100) e termina em (240, 300), que se torna o ponto inicial para o segundo arco que termina no (240, 100).

Os dois olhos também são processados com dois `A` comandos e assim como acontece com head do gato, a segunda `A` comando termina no mesmo ponto como o início do primeiro `A` comando. No entanto, esses pares de `A` comandos não definem uma elipse. O com de cada arco é 40 unidades e o raio também 40 unidades, que significa que esses arcos não são semicircles completos.

O `PaintSurface` manipulador executa transformações semelhantes, como o exemplo anterior, mas define um único `Scale` fator para manter a taxa de proporção e fornecer uma pequena margem, de modo que caixas estreitas do gato sem tocar os lados da tela:

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

Aqui está o programa em execução em todas as três plataformas:

[![](path-data-images/pathdatacat-small.png "Tripla captura de tela da página do caminho dados Cat")](path-data-images/pathdatacat-large.png#lightbox "tripla captura de tela da página do caminho dados Cat")

Normalmente, quando um `SKPath` objeto é definido como um campo, os contornos do caminho devem ser definidos no construtor ou outro método. Ao usar dados de caminho SVG, no entanto, você viu que o caminho pode ser especificado inteiramente na definição de campo.

Anterior **feio relógio de analógico** amostra na [ **girar a transformar** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) artigo exibido os ponteiros do relógio como linhas simples. O **bastante o relógio analógico** programa abaixo substitui essas linhas com `SKPath` objetos definidos como campos na [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe juntamente com `SKPaint` objetos:

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

Os ponteiros de hora e minuto agora tem colocado áreas, portanto, para tornar esses mãos diferentes entre si, eles são desenhados com um contorno preto e preenchimento cinza usando o `handStrokePaint` e `handFillPaint` objetos.

Na anterior **feio relógio de analógico** exemplo, o pouco círculos que marcados como as horas e minutos foram desenhados em um loop. Neste **bastante o relógio analógico** exemplo, uma abordagem totalmente diferente é usada: as marcas de hora e minuto são linhas pontilhadas desenhadas com a `minuteMarkPaint` e `hourMarkPaint` objetos:

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

O [ **por pontos e traços** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) guia abordou como você pode usar o `SKPathEffect.CreateDash` método para criar uma linha tracejada. O primeiro argumento é um `float` matriz que geralmente tem dois elementos: O primeiro elemento é o comprimento dos traços e o segundo elemento é a lacuna entre os traços. Quando o `StrokeCap` estiver definida como `SKStrokeCap.Round`, em seguida, as extremidades arredondadas do traço efetivamente aumentam o comprimento do traço usando a largura do traço em ambos os lados do traço. Assim, definindo o primeiro elemento da matriz como 0 cria uma linha pontilhada.

A distância entre esses pontos é regida por segundo elemento de matriz. Como você verá em breve, esses dois `SKPaint` objetos são usados para desenhar círculos com um raio de 90 unidades. A circunferência deste círculo, portanto, é 180π, o que significa que as marcas de 60 minutos devem aparecer cada unidades 3 π, que é o valor de segundo na `float` matriz em `minuteMarkPaint`. As marcas de hora doze devem aparecer cada unidades 15π, que é o valor no segundo `float` matriz.

O `PrettyAnalogClockPage` classe define um temporizador para invalidar a superfície de cada 16 milissegundos e o `PaintSurface` manipulador é chamado em que a taxa. As definições anteriores a `SKPath` e `SKPaint` objetos permitem bem simples código de desenho:

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

Algo especial é feito com a mão de segundo, no entanto. Porque o relógio é atualizado a cada 16 milissegundos, o `Millisecond` propriedade do `DateTime` valor potencialmente pode ser usado para animar uma varredura de segundo manualmente em vez de um que se move em pequenos saltos do segundo a segundo. Mas esse código não permite o movimento suave. Em vez disso, ele usa o xamarin. Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) e [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) animação funções para um tipo diferente de movimentação de easing. Essas funções de easing fazer com que a mão de segundo mover de uma maneira mais irregular &mdash; extraindo voltar um pouco antes de ele move e, em seguida, um pouco excesso de solução seu destino, um efeito que Infelizmente não pode ser reproduzido nessas capturas de tela estáticas:

[![](path-data-images/prettyanalogclock-small.png "Captura de tela da página bastante o relógio analógico tripla")](path-data-images/prettyanalogclock-large.png#lightbox "tripla captura de tela da página bastante o relógio analógico")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
