---
title: Dados de caminho SVG
description: "Definir caminhos usando cadeias de caracteres de texto no formato de gráficos vetoriais escalonáveis"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: feb4c5f4c7e7ad3fc5f762786001be9aa57ae718
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="svg-path-data"></a>Dados de caminho SVG

_Definir caminhos usando cadeias de caracteres de texto no formato de gráficos vetoriais escalonáveis_

O `SKPath` classe oferece suporte à definição de objetos de todo o caminho de cadeias de caracteres de texto em um formato estabelecido pela especificação de elementos gráficos de vetor escalável (SVG). Você verá neste artigo como você pode representar um caminho completo, como este em uma cadeia de caracteres de texto:

![](path-data-images/pathdatasample.png "Um exemplo de caminho definido com dados de caminho SVG")

SVG é um linguagem para páginas da web de programação de gráficos baseados em XML. Como SVG deve permitir que os caminhos a serem definidos na marcação em vez de uma série de chamadas de função, o padrão de SVG inclui uma maneira concisa extremamente de especificar um caminho de gráfico inteiro como uma cadeia de caracteres de texto.

Em SkiaSharp, esse formato é conhecido como "SVG caminho de dados." Também é compatível com o formato XAML do Windows com base em ambientes de programação, incluindo o Windows Presentation Foundation e a plataforma Universal do Windows, onde ele é conhecido como o [sintaxe de marcação de caminho](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx) ou [mover e desenhe a sintaxe de comandos](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Ele também pode servir como um formato de troca de imagens de gráficos vetoriais, principalmente em arquivos de texto, como XML.

SkiaSharp define dois métodos com as palavras `SvgPathData` em seus nomes:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Estático [ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método converte uma cadeia de caracteres para um `SKPath` objeto, enquanto [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) converte uma `SKPath` objeto como uma cadeia de caracteres.

Aqui está uma cadeia de caracteres para uma estrela de cinco pontas centralizada no ponto (0, 0) com um raio de 100 SVG:

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

As letras são comandos que criam um `SKPath` objeto. `M` indica um `MoveTo` chamar, `L` é `LineTo`, e `Z` é `Close` para fechar um contorno. Cada par de números fornece uma coordenada de X e Y de um ponto. Observe que o `L` comando é seguido por vários pontos, separados por vírgulas. Em uma série de coordenadas, pontos, vírgulas e espaço em branco são tratadas da mesma forma. Alguns programadores preferem coloque vírgulas entre as coordenadas X e Y e não entre os pontos, mas vírgulas ou espaços são necessárias somente para evitar ambiguidade. Isso é permitido:

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

A sintaxe dos dados de caminho SVG formalmente está documentada em [seção 8.3 da especificação SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Aqui está um resumo:

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

Isso inicia uma nova delimitação no caminho definindo a posição atual. Dados de caminho sempre devem começar com um `M` comando.

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

Este comando adiciona uma linha reta (ou linhas) para o caminho e define a nova posição atual até o final da última linha. Você pode seguir o `L` com vários pares de *x* e *y* coordenadas.

## <a name="horizontal-lineto"></a>**Horizontal LineTo**

```csharp
H x ...
```

Este comando adiciona uma linha horizontal para o caminho e define a nova posição atual ao final da linha. Você pode seguir o `H` comando com vários *x* coordenadas, mas ele não faz muito sentido.

## <a name="vertical-line"></a>**Linha vertical**

```csharp
V y ...
```

Este comando adiciona uma linha vertical para o caminho e define a nova posição atual ao final da linha.

## <a name="close"></a>**Fechar**

```csharp
Z
```

O `C` comando fecha o contorno, adicionando uma linha reta da posição atual para o início do contorno.

## <a name="arcto"></a>**ArcTo**

O comando para adicionar um arco elíptico para o contorno de longe é o comando mais complexo na especificação do caminho de dados inteiro do SVG. É o único comando em que números podem representar algo diferente de valores de coordenada:

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

O *rx* e *rápida* parâmetros são os raios horizontais e verticais da elipse. O *ângulo de rotação* é no sentido horário em graus.

Definir o *sinalizador grande arco* para 1 para o arco grande ou 0 para o arco pequeno.

Definir o *varredura sinalizador* 1 para no sentido horário e como 0 para no sentido anti-horário.

O arco é desenhado no ponto (*x*, *y*), que se torna a nova posição atual.

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

Este comando adiciona uma curva cúbica de Bézier da posição atual para (*x3*, *y3*), que se torna a nova posição atual. Os pontos (*x1*, *y1*) e (*x2*, *y2*) são pontos de controle.

Várias curvas de Bézier podem ser especificadas por um único `C` comando. O número de pontos deve ser um múltiplo de 3.

Também é um comando de curva de Bézier "suave":

```csharp
S x2 y2 x3 y3 ...
```

Este comando deve seguir um comando de Bézier regular (embora não seja estritamente obrigatório). O comando Bézier smooth calcula o primeiro ponto de controle para que ele é um reflexo do segundo ponto de controle de Bézier anterior ao redor de seu ponto mútuo. Esses três pontos são, portanto, colinear, e a conexão entre duas curvas de Bézier é suave.

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

Para curvas de Bézier quadráticas, o número de pontos deve ser um múltiplo de 2. É o ponto de controle (*x1*, *y1*) e o ponto de extremidade (e a nova posição atual) é (*x2*, *y2*)

Também é um comando de curva quadrática suave:

```csharp
T x2 y2 ...
```

O ponto de controle é calculado com base no ponto de controle da curva quadrática anterior.

Todos esses comandos também estão disponíveis em versões "relativas", onde os pontos de coordenadas são em relação à posição atual. Esses comandos relativos começam com letras minúsculas, por exemplo `c` em vez de `C` para a versão relativa do comando Bézier cúbica.

Esta é a extensão da definição de dados do caminho de SVG. Não há nenhum recurso de grupos de comandos de repetição ou para executar qualquer tipo de cálculo. Comandos para `ConicTo` ou outros tipos de especificações de arco não estão disponíveis.

Estático [ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método espera uma cadeia de caracteres válida de comandos SVG. Se for detectado um erro de sintaxe, o método retornará `null`. Que é a indicação de erro somente.

O [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) método é útil para a obtenção de dados de caminho SVG de uma já existente `SKPath` objeto transferir para outro programa ou para armazenar em um formato de arquivo de texto como XML. (O `ToSvgPathData` método não será demonstrado no código de exemplo neste artigo.) Fazer *não* esperar `ToSvgPathData` para retornar uma cadeia de caracteres correspondente exatamente as chamadas de método que criou o caminho. Em particular, você descobrirá que arcos são convertidos em vários `QuadTo` comandos, e como eles são exibidos nos dados de caminho retornados de `ToSvgPathData`.

O **caminho dados Hello** página spells a palavra "HELLO" usando dados de caminho SVG. Tanto o `SKPath` e `SKPaint` objetos são definidos como campos de [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

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

O caminho que define a cadeia de caracteres de texto começa no canto superior esquerdo no ponto (0, 0). Cada letra é 50 unidades de largura e altura de 100 unidades e letras são separadas por outro unidades 25, que significa que todo o caminho é 350 unidades amplas.

O 'H' "Olá" é composta de três delimitações de uma linha, enquanto a 'E' está duas curvas de Bézier cúbicas conectadas. Observe que o `C` comando é seguido por seis pontos, e dois pontos de controle têm coordenadas Y de – 10 e 110, o que coloca fora do intervalo das coordenadas Y das outras letras. O L é duas linhas conectadas, enquanto a ' l'é uma elipse que é processada com um `A` comando.

Observe que o `M` que começa o contorno do último comando define a posição para o ponto (350, 50), que é o centro vertical da esquerda lado do ' l'. Conforme indicado a seguir números primeiro o `A` de comando, a elipse tem um raio horizontal de 25 e um raio vertical de 50. O ponto de extremidade é indicado pelo último par de números no `A` comando, que representa o ponto (300, 49.9). Que é deliberadamente ligeiramente diferente do ponto de início. Se o ponto de extremidade é definido como o ponto inicial, o arco não será renderizado. Para desenhar uma elipse concluída, você deve definir o ponto de extremidade próximo (mas não igual a) o ponto inicial, ou você deve usar duas ou mais `A` comandos, cada parte da elipse concluída.

Você talvez queira adicionar a seguinte instrução ao construtor da página e, em seguida, defina um ponto de interrupção para examinar a cadeia de caracteres resultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Você descobrirá que o arco foi substituído por uma série de tempo de `Q` comandos para uma restauração por etapas aproximação do arco usando curvas de Bézier quadráticas.

O `PaintSurface` manipulador obtém os limites rígidos do caminho, que não inclui os pontos de controle para a 'E' e ' l' curvas. As três transformações mover o centro do caminho para o ponto (0, 0), dimensionar o caminho para o tamanho da tela (mas também considerando-se a largura do traço) e, em seguida, mover o centro do caminho para o centro da tela:

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

O caminho preenche a tela, que é mais razoável quando exibido no modo paisagem:

[![](path-data-images/pathdatahello-small.png "Captura de tela tripla do caminho dados Hello página")](path-data-images/pathdatahello-large.png "tripla captura de tela de caminho dados Hello página")

O **caminho dados Cat** página é semelhante. Os objetos de caminho e paint são definidos como campos de [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

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

O cabeçalho de um gato é um círculo e aqui é renderizado com dois `A` comandos, cada um deles desenha um semicírculo. Ambos `A` comandos de cabeçalho define raios horizontais e verticais de 100. O arco primeiro começa em (240, 100) e termina no (240, 300), que se torna o ponto de início para o segundo arco que termina no (240, 100).

Os dois olhos também são processados com dois `A` comandos e assim como acontece com head do atendimento ao cliente, a segunda `A` comando termina no mesmo ponto como o início do primeiro `A` comando. No entanto, esses pares de `A` comandos não definem uma elipse. O com de cada arco é 40 unidades e o raio também 40 unidades, que significa que esses arcos não semicircles completos.

O `PaintSurface` manipulador executa transformações semelhantes do exemplo anterior, mas define um único `Scale` fator para manter a taxa de proporção e fornecer uma pequena margem para caixas estreitas de seu sem tocar os lados da tela:

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

[![](path-data-images/pathdatacat-small.png "Tripla captura da página do caminho dados Cat")](path-data-images/pathdatacat-large.png "tripla captura da página do caminho dados Cat")

Normalmente, quando um `SKPath` objeto é definido como um campo, o contorno do caminho deve ser definido no construtor ou outro método. Ao usar dados de caminho SVG, no entanto, você viu que o caminho pode ser especificado inteiramente na definição do campo.

O anteriores **feio relógio de analógico** exemplo no [ **a girar transformar** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) artigo exibido os ponteiros do relógio como linhas simples. O **bastante o relógio analógico** programa abaixo substitui essas linhas com `SKPath` objetos definidos como campos no [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe junto com `SKPaint` objetos:

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

Os ponteiros de hora e minuto agora anexando áreas para tornar esses ponteiros diferentes entre si, eles são desenhados com um contorno preto e preenchimento cinza usando o `handStrokePaint` e `handFillPaint` objetos.

No anteriores **feio relógio de analógico** exemplo, o pouco círculos que marcado as horas e minutos foram desenhados em um loop. Neste **bastante o relógio analógico** exemplo, uma abordagem inteiramente diferente é usada: as marcas de hora e minuto são linhas pontilhadas desenhadas com a `minuteMarkPaint` e `hourMarkPaint` objetos:

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

O [ **pontos e hífens** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) guia discutidos como você pode usar o `SKPathEffect.CreateDash` método para criar uma linha tracejada. O primeiro argumento é uma `float` matriz que geralmente tem dois elementos: O primeiro elemento é o comprimento dos traços, e o segundo elemento é a diferença entre os traços. Quando o `StrokeCap` está definida como `SKStrokeCap.Round`, em seguida, as extremidades arredondadas do traço efetivamente aumentam o comprimento do traço usando a largura do traço em ambos os lados de um traço. Assim, o primeiro elemento da matriz a definição como 0 cria uma linha pontilhada.

A distância entre esses pontos é regida por segundo elemento de matriz. Como você verá em breve, esses dois `SKPaint` objetos são usados para desenhar círculos com um raio de 90 unidades. A circunferência deste círculo, portanto, é 180π, o que significa que as marcas de 60 minutos devem aparecer todas as unidades de 3 π, que é o segundo valor no `float` de matriz em `minuteMarkPaint`. As marcas de 12 horas devem aparecer todas as unidades de 15π, que é o valor no segundo `float` matriz.

O `PrettyAnalogClockPage` classe define um timer para invalidar a superfície de cada 16 milissegundos e o `PaintSurface` manipulador é chamado com essa taxa. As definições anteriores a `SKPath` e `SKPaint` objetos permitem bem simples de código de desenho:

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

Algo especial é feito com o ponteiro de segundo, no entanto. Porque o relógio é atualizado a cada 16 milissegundos, o `Millisecond` propriedade do `DateTime` valor potencialmente pode ser usado para animar uma varredura de segundo manualmente em vez de um que move na discretos saltos do segundo a segundo. Mas esse código não permite a movimentação suave. Em vez disso, ele usa o xamarin. Forms [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) e [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) animação atenuação funções para um tipo diferente de movimento. Essas funções de atenuação fazer com que a mão de segundo mover uma maneira de mais irregular & #x 2014; remoção de um pouco antes de movê-la e, em seguida, um pouco excesso acertar seu destino, um efeito que Infelizmente não pode ser reproduzido nessas capturas de tela estáticas:

[![](path-data-images/prettyanalogclock-small.png "Captura de tela de tripla da página bastante o relógio analógico")](path-data-images/prettyanalogclock-large.png "tripla captura de tela da página bastante o relógio analógico")


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
