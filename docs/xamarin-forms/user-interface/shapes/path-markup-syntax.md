---
title: 'Xamarin.Forms Formas: sintaxe de marcação de caminho'
description: Xamarin.Forms A sintaxe de marcação de caminho permite especificar de forma compacta geometrias de caminho em XAML.
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 743f6366cc7e798247ea0151720a81e9cbddfeeb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559098"
---
# <a name="no-locxamarinforms-shapes-path-markup-syntax"></a>Xamarin.Forms Formas: sintaxe de marcação de caminho

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Forms a sintaxe de marcação de caminho permite especificar de forma compacta geometrias de caminho em XAML. A sintaxe é especificada como um valor de cadeia de caracteres para a `Path.Data` Propriedade:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

A sintaxe de marcação de caminho é composta por um `FillRule` valor opcional e uma ou mais descrições de figura. Essa sintaxe pode ser expressa como: `<Path Data="` *[fillRule]* *figureDescription* *[figureDescription]* * `" ... />`

Nesta sintaxe:

- *fillRule* é um opcional `Xamarin.Forms.Shapes.FillRule` que especifica se a geometria deve usar `EvenOdd` ou `Nonzero` `FillRule` . `F0` é usado para especificar a `EvenOdd` regra de preenchimento, enquanto `F1` é usado para especificar a `Nonzero` regra de preenchimento. Para obter mais informações sobre regras de preenchimento, consulte [ Xamarin.Forms formas: regras de preenchimento](fillrules.md).
- *figureDescription* representa uma figura composta por um comando de movimentação, comandos de desenho e um comando de fechamento opcional. Um comando move especifica o ponto inicial da figura. Os comandos Draw descrevem o conteúdo da figura e o comando opcional Close fecha a figura.

No exemplo acima, a sintaxe de marcação de caminho Especifica um ponto de partida usando o comando Move ( `M` ), uma série de linhas retas usando o comando line ( `L` ) e fecha o caminho com o comando Close ( `Z` ).

Na sintaxe de marcação de caminho, os espaços não são necessários antes ou depois dos comandos. Além disso, dois números não precisam ser separados por uma vírgula ou espaço em branco, mas isso só pode ser obtido quando a cadeia de caracteres não é ambígua.

> [!TIP]
> A linguagem de marcação de caminho usa uma sintaxe compatível com definições de caminho de imagem SVG (gráficos vetoriais escalonáveis) e, portanto, pode ser útil para portar gráficos do formato SVG.

## <a name="move-command"></a>Comando de movimentação

O comando mover especifica o ponto de partida de uma nova figura. A sintaxe para esse comando é: `M` *StartPoint* ou `m` *StartPoint*.

Nessa sintaxe, *StartPoint* é uma [`Point`](xref:Xamarin.Forms.Point) estrutura que especifica o ponto de partida de uma nova figura. Se você listar vários pontos após o comando mover, uma linha será desenhada para esses pontos.

`M 10,10` é um exemplo de um comando de movimentação válido.

## <a name="draw-commands"></a>Comandos de desenho

Um comando de desenho pode consistir em vários comandos de formas. Os seguintes comandos Draw estão disponíveis:

- Linha ( `L` ou `l` ).
- Linha horizontal ( `H` ou `h` ).
- Linha vertical ( `V` ou `v` ).
- Arco elíptico ( `A` ou `a` ).
- Curva de Bézier cúbica ( `C` ou `c` ).
- Curva de Bézier quadrática ( `Q` ou `q` ).
- Curva de Bézier cubica suave ( `S` ou `s` ).
- Curva de Bézier quadrática suave ( `T` ou `t` ).

Cada comando Draw é especificado com uma letra que não diferencia maiúsculas de minúsculas. Ao inserir sequencialmente mais de um comando do mesmo tipo, você pode omitir a entrada de comando duplicada. Por exemplo, `L 100,200 300,400` é equivalente a `L 100,200 L 300,400` .

### <a name="line-command"></a>Comando de linha

O comando linha cria uma linha reta entre o ponto atual e o ponto de extremidade especificado. A sintaxe para esse comando é: `L` *ponto* de extremidade ou `l` *ponto de extremidade*.

Nessa sintaxe, *Endpoint* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto final da linha.

`L 20,30` e `L 20 30` são exemplos de comandos de linha válidos.

Para obter informações sobre como criar uma linha reta como um `PathGeometry` objeto, consulte [criar um LineSegment](geometries.md#create-a-linesegment).

### <a name="horizontal-line-command"></a>Comando de linha horizontal

O comando de linha horizontal cria uma linha horizontal entre o ponto atual e a coordenada x especificada. A sintaxe para esse comando é: `H` *x* ou `h` *x*.

Nessa sintaxe, *x* é um `double` que representa a coordenada x do ponto de extremidade da linha.

`H 90` é um exemplo de um comando de linha horizontal válido.

### <a name="vertical-line-command"></a>Comando de linha vertical

O comando de linha vertical cria uma linha vertical entre o ponto atual e a coordenada y especificada. A sintaxe para esse comando é: `V` *y* ou `v` *y*.

Nessa sintaxe, *y* é um `double` que representa a coordenada y do ponto de extremidade da linha.

`V 90` é um exemplo de um comando de linha vertical válido.

### <a name="elliptical-arc-command"></a>Comando de arco elíptico

O comando de arco elíptico cria um arco elíptico entre o ponto atual e o ponto de extremidade especificado. A sintaxe para esse comando é: `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *Endpoint* ou `a` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *Endpoint*.

Nesta sintaxe:

- `size` é um [`Size`](xref:Xamarin.Forms.Size) que representa o raio x e y do arco.
- `rotationAngle` é um `double` que representa a rotação da elipse, em graus.
- `isLargeArcFlag` deve ser definido como 1 se o ângulo do arco deve ser 180 graus ou maior, caso contrário, defina-o como 0.
- `sweepDirectionFlag` deve ser definido como 1 se o arco for desenhado em uma direção de ângulo positivo; caso contrário, defina-o como 0.
- `endPoint` é um [`Point`](xref:Xamarin.Forms.Point) para o qual o arco é desenhado.

`A 150,150 0 1,0 150,-150` é um exemplo de um comando de arco elíptico válido.

Para obter informações sobre como criar um arco elíptico como um `PathGeometry` objeto, consulte [criar um ArcSegment](geometries.md#create-an-arcsegment).

### <a name="cubic-bezier-curve-command"></a>Comando de curva de Bézier cúbica

O comando de curva de Bézier cúbica cria uma curva de Bézier cúbica entre o ponto atual e o ponto de extremidade especificado usando o ponto de controle especificado dois. A sintaxe para esse comando é: `C` *ControlPoint1* *ControlPoint2* *Endpoint* ou `c` *ControlPoint1* *ControlPoint2* *Endpoint*.

Nesta sintaxe:

- *ControlPoint1* é um [`Point`](xref:Xamarin.Forms.Point) que representa o primeiro ponto de controle da curva, que determina a tangente inicial da curva.
- *ControlPoint2* é um [`Point`](xref:Xamarin.Forms.Point) que representa o segundo ponto de controle da curva, que determina a tangente final da curva.
- o *ponto de extremidade* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto para o qual a curva é desenhada.

`C 100,200 200,400 300,200` é um exemplo de um comando de curva de Bézier cúbica válido.

Para obter informações sobre como criar uma curva de Bézier cúbica como um `PathGeometry` objeto, consulte [criar um BezierSegment](geometries.md#create-a-beziersegment).

### <a name="quadratic-bezier-curve-command"></a>Comando de curva de Bézier quadrática

O comando de curva de Bézier quadrática cria uma curva de Bezier quadrática entre o ponto atual e o ponto de extremidade especificado usando o ponto de controle especificado. A sintaxe para este comando é: `Q` *ControlPoint* *Endpoint* ou `q` *ControlPoint* *Endpoint*.

Nesta sintaxe:

- o *ControlPoint* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto de controle da curva, que determina as tangentes de início e término da curva.
- o *ponto de extremidade* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto para o qual a curva é desenhada.

`Q 100,200 300,200` é um exemplo de um comando de curva de Bezier quadrática válido.

Para obter informações sobre como criar uma curva de Bézier quadrática como um `PathGeometry` objeto, consulte [criar um QuadraticBezierSegment](geometries.md#create-a-quadraticbeziersegment).

### <a name="smooth-cubic-bezier-curve-command"></a>Comando de curva de Bézier cubica suave

O comando de curva de Bézier cubica suave cria uma curva de Bézier cúbica entre o ponto atual e o ponto de extremidade especificado usando o ponto de controle especificado. A sintaxe para esse comando é: `S` *controlPoint2* *ponto de extremidade* ControlPoint2 ou ponto de `s` *controlPoint2* *extremidade*ControlPoint2.  

Nesta sintaxe:

- *ControlPoint2* é um [`Point`](xref:Xamarin.Forms.Point) que representa o segundo ponto de controle da curva, que determina a tangente final da curva.
- o *ponto de extremidade* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto para o qual a curva é desenhada.

Assume-se que o primeiro ponto de controle seja a reflexão do segundo ponto de controle do comando anterior, em relação ao ponto atual. Se não houver nenhum comando anterior, ou o comando anterior não era um comando de curva de Bézier cúbica ou um comando de curva de Bézier cúbico suave, o primeiro ponto de controle será considerado coincidente com o ponto atual.

`S 100,200 200,300` é um exemplo de um comando de curva de Bézier cúbica suave válido.

### <a name="smooth-quadratic-bezier-curve-command"></a>Comando de curva de Bézier quadrática suave

O comando de curva de Bézier quadrática suave cria uma curva de Bezier quadrática entre o ponto atual e o ponto de extremidade especificado usando um ponto de controle. A sintaxe para esse comando é: `T` *ponto* de extremidade ou `t` *ponto de extremidade*.

Nessa sintaxe, *Endpoint* é um [`Point`](xref:Xamarin.Forms.Point) que representa o ponto para o qual a curva é desenhada.

O ponto de controle é assumido ser a reflexão do ponto de controle do comando anterior relativo ao ponto atual. Se não houver nenhum comando anterior ou se o comando anterior não fosse uma curva de Bézier quadrática ou um comando Smooth de curva de Bézier quadrática, o ponto de controle será considerado como coincidente com o ponto atual.

`T 100,30` é um exemplo de um comando de curva de Bezier cubica quadrática válido.

## <a name="close-command"></a>Comando de fechamento

O comando fechar encerra a figura atual e cria uma linha que conecta o ponto atual ao ponto inicial da figura. Portanto, esse comando cria uma junção de linha entre o último segmento e o primeiro segmento da figura.

A sintaxe para o comando fechar é: `Z` ou `z` .

## <a name="additional-values"></a>Valores adicionais

Em vez de um valor numérico padrão, você também pode usar os seguintes valores especiais que diferenciam maiúsculas de minúsculas:

- `Infinity` representa `double.PositiveInfinity` .
- `-Infinity`representa `double.NegativeInfinity` .
- `NaN` representa `double.NaN` .

Além disso, você também pode usar notação científica sem diferenciação de maiúsculas e minúsculas. Portanto, `+1.e17` é um valor válido.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formas: geometrias](geometries.md)
- [Xamarin.Forms Formas: regras de preenchimento](fillrules.md)