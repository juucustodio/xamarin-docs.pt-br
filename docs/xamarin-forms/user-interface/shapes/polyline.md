---
title: 'Xamarin.FormsFormas: polilinha'
description: A Xamarin.Forms classe Polyline pode ser usada para desenhar uma série de linhas retas conectadas.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6c884caa7bfb301f949f353f3c6c3445704aa89
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101353"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.FormsFormas: polilinha

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Polyline` classe deriva da `Shape` classe e pode ser usada para desenhar uma série de linhas retas conectadas. Para obter informações sobre as propriedades que a `Polyline` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Polyline` define as propriedades a seguir:

- `Points`, do tipo `PointCollection` , que é uma coleção de `Point` estruturas que descreve os pontos de vértice da polilinha.
- `FillRule`, do tipo `FillRule` , que especifica como as áreas de interseção na polilinha são combinadas. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

## <a name="create-a-polyline"></a>Criar uma polilinha

O exemplo de XAML a seguir mostra como desenhar um polígono:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          HeightRequest="100"
          WidthRequest="500" />
```

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
