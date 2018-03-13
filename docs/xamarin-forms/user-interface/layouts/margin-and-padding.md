---
title: Margem e preenchimento
description: "A margem e preenchimento propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las."
ms.topic: article
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 461430ba27b5d6008338019e5feaebed7b09d4cb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="margin-and-padding"></a>Margem e preenchimento

_A margem e preenchimento propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las._

## <a name="overview"></a>Visão geral

Margem e preenchimento são conceitos de layout relacionadas:

- O [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propriedade representa a distância entre um elemento e seus elementos adjacentes e é usada para controlar a posição de renderização do elemento e a posição de renderização de seus vizinhos. `Margin` os valores podem ser especificados em [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [exibição](~/xamarin-forms/user-interface/controls/views.md) classes.
- O [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedade representa a distância entre um elemento e seus elementos filho e é usada para separar o controle de seu próprio conteúdo. `Padding` os valores podem ser especificados em [layout](~/xamarin-forms/user-interface/controls/layouts.md) classes.

O diagrama a seguir ilustra dois conceitos:

[![](margin-and-padding-images/margins-and-padding-sml.png "Margens e preenchimento conceitos")](margin-and-padding-images/margins-and-padding.png#lightbox "margens e preenchimento conceitos")

Observe que [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) os valores são aditivas. Portanto, se dois elementos adjacentes especificam uma margem de 20 pixels, a distância entre os elementos serão 40 pixels. Além disso, a margem e preenchimento são aditivas quando ambos são aplicadas, em que a distância entre um elemento e qualquer conteúdo será a margem e preenchimento.

## <a name="specifying-a-thickness"></a>Especifica uma espessura

O [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) e [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedades forem do tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/). Existem três possibilidades ao criar um `Thickness` estrutura:

- Criar um [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estrutura definida por um único valor uniforme. O valor único é aplicado à esquerda, superior, direita e lados da parte inferior do elemento.
- Criar um [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estrutura definido por valores horizontal e vertical. O valor horizontal simetricamente é aplicado à esquerda e à direita do elemento, com o valor vertical simetricamente sendo aplicado para os lados superior e inferior do elemento.
- Criar um [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estrutura definida por quatro valores distintos que são aplicados para a esquerda, superior, direita e lados da parte inferior do elemento.

O exemplo de código XAML a seguir mostra todos os três possibilidades:

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` os valores podem ser negativos, que normalmente recorta ou overdraws o conteúdo.

## <a name="summary"></a>Resumo

Este artigo demonstrou a diferença entre o [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) e [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedades e como defini-las. As propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário.


## <a name="related-links"></a>Links relacionados

- [Margin](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)
- [Padding](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)
- [Espessura](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)
