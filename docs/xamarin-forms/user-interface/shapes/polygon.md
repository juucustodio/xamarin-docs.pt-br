---
title: 'Xamarin.Forms Formas: polígono'
description: A Xamarin.Forms Classe Polygon pode ser usada para desenhar polígonos, que são uma série de linhas conectadas que formam formas fechadas.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2928e3bd32d7dd2722f01ad5a51dbaab127afd77
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585878"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.Forms Formas: polígono

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Polygon` classe deriva da `Shape` classe e pode ser usada para desenhar polígonos, que são uma série de linhas conectadas que formam formas fechadas. Para obter informações sobre as propriedades que a `Polygon` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Polygon` define as propriedades a seguir:

- `Points`, do tipo `PointCollection` , que é uma coleção de [`Point`](xref:Xamarin.Forms.Point) estruturas que descreve os pontos de vértice do polígono.
- `FillRule`, do tipo `FillRule` , que especifica como o preenchimento interior da forma é determinado. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O `PointsCollection` tipo é um `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. A `Point` estrutura define `X` e `Y` Propriedades, do tipo `double` , que representam um par de coordenadas x e y em espaço 2D. Portanto, a `Points` propriedade deve ser definida como uma lista de pares x e coordenada y que descrevem os pontos de vértice do polígono, delimitados por uma única vírgula e/ou um ou mais espaços. Por exemplo, "40, 10 70, 80" e "40 10, 70 80" são válidos.

Para obter mais informações sobre a `FillRule` enumeração, consulte [ Xamarin.Forms formas: regras de preenchimento](fillrules.md).

## <a name="create-a-polygon"></a>Criar um polígono

Para desenhar um polígono, crie um `Polygon` objeto e defina sua `Points` propriedade para os vértices de uma forma. Uma linha é desenhada automaticamente que conecta o primeiro e o último pontos. Para pintar o interior do polígono, defina sua `Fill` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. Para dar um contorno ao polígono, defina sua `Stroke` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. A `StrokeThickness` propriedade especifica a espessura do contorno do polígono. Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

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

Para obter mais informações sobre como desenhar um polígono tracejado, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

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

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Formas: regras de preenchimento](fillrules.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
