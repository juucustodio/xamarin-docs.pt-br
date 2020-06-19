---
title: 'Xamarin.FormsFormas: elipse'
description: A Xamarin.Forms classe Ellipse pode ser usada para desenhar reticências e círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 142deccbcd29548e2d72b7144a01322f9909d98f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101283"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Ellipse` classe deriva da `Shape` classe e pode ser usada para desenhar reticências e círculos. Para obter informações sobre as propriedades que a `Ellipse` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

A `Ellipse` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` .

## <a name="create-an-ellipse"></a>Criar uma elipse

O exemplo de XAML a seguir mostra como desenhar uma elipse preenchida:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

O exemplo de XAML a seguir mostra como desenhar um círculo:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
