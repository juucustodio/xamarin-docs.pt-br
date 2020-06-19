---
title: 'Xamarin.FormsFormas: retângulo'
description: A Xamarin.Forms classe Rectangle pode ser usada para desenhar retângulos.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da9649a4abb2cb65930d98576eda81739b711886
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101369"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.FormsFormas: retângulo

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Rectangle` classe deriva da `Shape` classe e pode ser usada para desenhar retângulos. Para obter informações sobre as propriedades que a `Rectangle` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Rectangle` define as propriedades a seguir:

- `RadiusX`, do tipo `double` , que é o raio do eixo x que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.
- `RadiusY`, do tipo `double` , que é o raio do eixo y que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `Rectangle` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` .

## <a name="create-a-rectangle"></a>Criar um retângulo

O exemplo de XAML a seguir mostra como desenhar um retângulo preenchido, com cantos arredondados:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           RadiusX="12"
           RadiusY="24"           
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
