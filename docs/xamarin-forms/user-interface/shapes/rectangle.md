---
title: 'Xamarin.Forms Formas: retângulo'
description: A Xamarin.Forms classe Rectangle pode ser usada para desenhar retângulos.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 48d8d61633d09212e445d37f6bd282677ef6b1b1
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558837"
---
# <a name="no-locxamarinforms-shapes-rectangle"></a>Xamarin.Forms Formas: retângulo

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Rectangle` classe deriva da `Shape` classe e pode ser usada para desenhar retângulos e quadrados. Para obter informações sobre as propriedades que a `Rectangle` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Rectangle` define as propriedades a seguir:

- `RadiusX`, do tipo `double` , que é o raio do eixo x que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.
- `RadiusY`, do tipo `double` , que é o raio do eixo y que é usado para arredondar os cantos do retângulo. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `Rectangle` classe define a `Aspect` propriedade, herdada da `Shape` classe, para `Stretch.Fill` . Para obter mais informações sobre a `Aspect` propriedade, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-a-rectangle"></a>Criar um retângulo

Para desenhar um retângulo, crie um `Rectangle` objeto e defina suas `WidthRequest` `HeightRequest` Propriedades e. Para pintar o interior do retângulo, defina sua `Fill` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . Para dar um esboço ao retângulo, defina sua `Stroke` propriedade como a [`Color`](xref:Xamarin.Forms.Color) . A `StrokeThickness` propriedade especifica a espessura da estrutura de tópicos do retângulo.

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