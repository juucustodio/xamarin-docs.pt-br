---
title: 'Xamarin.Forms Formas: elipse'
description: A Xamarin.Forms classe Ellipse pode ser usada para desenhar reticências e círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3130fd4cb054799ca9e0a61d13cacb2629320b7
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585826"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.Forms Formas: elipse

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Ellipse` classe deriva da `Shape` classe e pode ser usada para desenhar reticências e círculos. Para obter informações sobre as propriedades que a `Ellipse` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

A `Ellipse` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` . Para obter mais informações sobre a `Aspect` propriedade, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Criar uma elipse

Para desenhar uma elipse, crie um `Ellipse` objeto e defina suas `WidthRequest` `HeightRequest` Propriedades e. Para pintar o interior da elipse, defina sua `Fill` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. Para dar à elipse um contorno, defina sua `Stroke` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. A `StrokeThickness` propriedade especifica a espessura da estrutura de tópicos da elipse. Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

Para desenhar um círculo, torne as `WidthRequest` `HeightRequest` Propriedades e do `Ellipse` objeto igual.

O exemplo de XAML a seguir mostra como desenhar uma elipse preenchida:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

Neste exemplo, uma elipse vermelha preenchida com dimensões 150x50 (unidades independentes de dispositivo) é desenhada:

![Elipse preenchida](ellipse-images/filled.png "Elipse preenchida")

O exemplo de XAML a seguir mostra como desenhar um círculo:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

Neste exemplo, um círculo vermelho com dimensões 150x150 (unidades independentes de dispositivo) é desenhado:

![Círculo não preenchido](ellipse-images/circle.png "Circle")

Para obter informações sobre como desenhar uma elipse tracejada, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
