---
title: 'Xamarin.Forms Formas: retângulo'
description: A Xamarin.Forms classe Rectangle pode ser usada para desenhar retângulos.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9080f509f9327f18f0cd66b0a497bdf03ac4975a
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585852"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.Forms Formas: retângulo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Rectangle` classe deriva da `Shape` classe e pode ser usada para desenhar retângulos e quadrados. Para obter informações sobre as propriedades que a `Rectangle` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Rectangle` define as propriedades a seguir:

- `RadiusX`, do tipo `double` , que é o raio do eixo x que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.
- `RadiusY`, do tipo `double` , que é o raio do eixo y que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `Rectangle` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` . Para obter mais informações sobre a `Aspect` propriedade, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-a-rectangle"></a>Criar um retângulo

Para desenhar um retângulo, crie um `Rectangle` objeto e defina suas `WidthRequest` `HeightRequest` Propriedades e. Para pintar o interior do retângulo, defina sua `Fill` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. Para dar um esboço ao retângulo, defina sua `Stroke` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado. A `StrokeThickness` propriedade especifica a espessura da estrutura de tópicos do retângulo. Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

Para dar os cantos arredondados do retângulo, defina suas `RadiusX` `RadiusY` Propriedades e. Essas propriedades definem o raios do eixo x e do eixo y que é usado para arredondar os cantos do retângulo.

Para desenhar um quadrado, torne as `WidthRequest` `HeightRequest` Propriedades e do `Rectangle` objeto igual.

O exemplo de XAML a seguir mostra como desenhar um retângulo preenchido:

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

Neste exemplo, um retângulo vermelho preenchido com dimensões 150x50 (unidades independentes de dispositivo) é desenhado:

![Retângulo preenchido](rectangle-images/filled.png "Retângulo preenchido")

O exemplo de XAML a seguir mostra como desenhar um retângulo preenchido, com cantos arredondados:

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

Neste exemplo, um retângulo de preenchimento azul com cantos arredondados é desenhado:

![Retângulo com cantos arredondados](rectangle-images/rounded.png "Retângulo com cantos arredondados")

Para obter informações sobre como desenhar um retângulo tracejado, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
