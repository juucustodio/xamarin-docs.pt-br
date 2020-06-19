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
ms.openlocfilehash: 2d67a6a4bbea6a4be27f0c0440d9c28ffd5a188f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101304"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsFormas: polígono

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Polygon` classe deriva da `Shape` classe e pode ser usada para desenhar polígonos, que são uma série de linhas conectadas que formam formas fechadas. Para obter informações sobre as propriedades que a `Polygon` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Polygon` define as propriedades a seguir:

- `Points`, do tipo `PointCollection` , que é uma coleção de `Point` estruturas que descreve os pontos de vértice do polígono.
- `FillRule`, do tipo `FillRule` , que especifica como o preenchimento interior da forma é determinado. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

## <a name="create-a-polygon"></a>Criar um polígono

O exemplo de XAML a seguir mostra como desenhar um polígono:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         HeightRequest="100"
         WidthRequest="200" />
```

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
