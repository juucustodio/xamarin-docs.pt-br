---
title: 'Xamarin.FormsFormas: elipse'
description: A Xamarin.Forms classe Ellipse pode ser usada para desenhar reticências e círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0796ba2a3b1ab28370fd8280e12df273223b9f4d
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917738"
---
# <a name="no-locxamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Ellipse` classe deriva da `Shape` classe e pode ser usada para desenhar reticências e círculos. Para obter informações sobre as propriedades que a `Ellipse` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

A `Ellipse` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` . Para obter mais informações sobre a `Aspect` propriedade, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Criar uma elipse

Para desenhar uma elipse, crie um `Ellipse` objeto e defina suas `WidthRequest` `HeightRequest` Propriedades e. Para pintar o interior da elipse, defina sua `Fill` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . Para dar à elipse um contorno, defina sua `Stroke` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . A `StrokeThickness` propriedade especifica a espessura da estrutura de tópicos da elipse.

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

![Circle](ellipse-images/circle.png "Circle")

Para obter informações sobre como desenhar uma elipse tracejada, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsExistentes](index.md)
