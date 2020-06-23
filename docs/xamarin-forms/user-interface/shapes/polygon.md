---
title: 'Xamarin.FormsFormas: polígono'
description: A Xamarin.Forms Classe Polygon pode ser usada para desenhar polígonos, que são uma série de linhas conectadas que formam formas fechadas.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c3164fbd1d7fb3bc3de15558cdd41f4791515eb
ms.sourcegitcommit: 22aca3e7e24038d6f83d68dbfc7435080223b1ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133742"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsFormas: polígono

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Polygon` classe deriva da `Shape` classe e pode ser usada para desenhar polígonos, que são uma série de linhas conectadas que formam formas fechadas. Para obter informações sobre as propriedades que a `Polygon` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Polygon` define as propriedades a seguir:

- `Points`, do tipo `PointCollection` , que é uma coleção de [`Point`](xref:Xamarin.Forms.Point) estruturas que descreve os pontos de vértice do polígono.
- `FillRule`, do tipo `FillRule` , que especifica como o preenchimento interior da forma é determinado. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O `PointsCollection` tipo é um `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. A `Point` estrutura define `X` e `Y` Propriedades, do tipo `double` , que representam um par de coordenadas x e y em espaço 2D. Portanto, a `Points` propriedade deve ser definida como uma lista de pares x e coordenada y que descrevem os pontos de vértice do polígono, delimitadas por uma única vírgula e/ou um ou mais espaços. Por exemplo, "40, 10 70, 80" e "40 10, 70 80" são válidos.

A enumeração `FillRule` define os seguintes membros:

- `EvenOdd`representa uma regra que determina se um ponto está na região de preenchimento do polígono. Ele desenha um raio do ponto para o infinito em qualquer direção e conta o número de segmentos de caminho dentro da forma que o raio cruza. Se esse número for ímpar, o ponto estará dentro. Se esse número for par, o ponto estará fora do.
- `Nonzero`representa uma regra que determina se um ponto está na região de preenchimento do polígono. Ele desenha um raio do ponto para o infinito em qualquer direção e, em seguida, examina os locais em que um segmento da forma cruza o raio. Começando com uma contagem de zero, adicione uma a cada vez que um segmento cruza o raio da esquerda para a direita e subtrai um a cada vez que um segmento cruza o raio da direita para a esquerda. Após a contagem de cruzamentos, se o resultado for zero, o ponto estará fora do caminho. Caso contrário, ele estará dentro de.

## <a name="create-a-polygon"></a>Criar um polígono

Para desenhar um polígono, crie um `Polygon` objeto e defina sua `Points` propriedade para os vértices de uma forma. Uma linha é desenhada automaticamente que conecta o primeiro e o último pontos. Para pintar o interior do polígono, defina sua `Fill` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . Para dar um contorno ao polígono, defina sua `Stroke` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . A `StrokeThickness` propriedade especifica a espessura do contorno do polígono.

O exemplo de XAML a seguir mostra como desenhar um polígono preenchido:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

Neste exemplo, um polígono preenchido que representa um triângulo é desenhado:

![Polígono preenchido](polygon-images/filled.png "Polígono preenchido")

O exemplo de XAML a seguir mostra como desenhar um polígono tracejado:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

Neste exemplo, a estrutura de tópicos do polígono é tracejada:

![Polígono tracejado](polygon-images/dashed.png "Polígono tracejado")

Para obter mais informações sobre como desenhar um polígono tracejado, consulte [formas tracejadas](index.md#dashed-shapes).

O exemplo de XAML a seguir mostra um polígono que usa a regra de preenchimento padrão:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

Neste exemplo, o comportamento de preenchimento de cada polígono é determinado usando a `EvenOdd` regra de preenchimento.

![Polígono EvenOdd](polygon-images/evenodd.png "Polígono EvenOdd")

O exemplo de XAML a seguir mostra um polígono que usa a `Nonzero` regra de preenchimento:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![Polígono diferente de zero](polygon-images/nonzero.png "Polígono diferente de zero")

Neste exemplo, o comportamento de preenchimento de cada polígono é determinado usando a `Nonzero` regra de preenchimento.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsExistentes](index.md)
