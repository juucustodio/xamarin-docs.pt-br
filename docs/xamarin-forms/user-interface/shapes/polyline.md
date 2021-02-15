---
title: 'Xamarin.Forms Formas: polilinha'
description: A Xamarin.Forms classe Polyline pode ser usada para desenhar uma série de linhas retas conectadas.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 95b60c4ab28200dd2bc2434396df0832532bdd7a
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585865"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.Forms Formas: polilinha

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Polyline` classe deriva da `Shape` classe e pode ser usada para desenhar uma série de linhas retas conectadas. Uma polilinha é semelhante a um polígono, exceto que o último ponto em uma polilinha não está conectado ao primeiro ponto. Para obter informações sobre as propriedades que a `Polyline` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Polyline` define as propriedades a seguir:

- `Points`, do tipo `PointCollection` , que é uma coleção de `Point` estruturas que descreve os pontos de vértice da polilinha.
- `FillRule`, do tipo `FillRule` , que especifica como as áreas de interseção na polilinha são combinadas. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O `PointsCollection` tipo é um `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. A `Point` estrutura define `X` e `Y` Propriedades, do tipo `double` , que representam um par de coordenadas x e y em espaço 2D. Portanto, a `Points` propriedade deve ser definida como uma lista de pares x e coordenada y que descrevem os pontos de vértice da polilinha, delimitadas por uma única vírgula e/ou um ou mais espaços. Por exemplo, "40, 10 70, 80" e "40 10, 70 80" são válidos.

Para obter mais informações sobre a `FillRule` enumeração, consulte [ Xamarin.Forms formas: regras de preenchimento](fillrules.md).

## <a name="create-a-polyline"></a>Criar uma polilinha

Para desenhar uma polilinha, crie um `Polyline` objeto e defina sua `Points` propriedade para os vértices de uma forma. Para dar um contorno à polilinha, defina sua `Stroke` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. A `StrokeThickness` propriedade especifica a espessura da estrutura de tópicos da polilinha. Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

> [!IMPORTANT]
> Se você definir a `Fill` propriedade de um `Polyline` para um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado, o espaço interior da polilinha será pintado, mesmo que o ponto inicial e o ponto de extremidade não se interseccionam.

O exemplo de XAML a seguir mostra como desenhar uma polilinha:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red" />
```

Neste exemplo, uma polilinha vermelha é desenhada:

![Linha poligonal](polyline-images/stroke.png "Linha poligonal")

O exemplo de XAML a seguir mostra como desenhar uma polilinha tracejada:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

Neste exemplo, a polilinha é tracejada:

![Polilinha tracejada](polyline-images/dashed.png "Polilinha tracejada")

Para obter mais informações sobre como desenhar uma polilinha tracejada, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

O exemplo de XAML a seguir mostra uma polilinha que usa a regra de preenchimento padrão:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

Neste exemplo, o comportamento de preenchimento da polilinha é determinado usando a `EvenOdd` regra de preenchimento.

![Polilinha EvenOdd](polyline-images/evenodd.png "EvenOdd polyine")

O exemplo de XAML a seguir mostra uma polilinha que usa a `Nonzero` regra de preenchimento:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![Polilinha diferente de zero](polyline-images/nonzero.png "Polilinha diferente de zero")

Neste exemplo, o comportamento de preenchimento da polilinha é determinado usando a `Nonzero` regra de preenchimento.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Formas: regras de preenchimento](fillrules.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
