---
title: Margem e preenchimento
description: As propriedades Margin e Padding controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 66ac81631466131cf1ef44dde39aa768d31b65a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772485"
---
# <a name="margin-and-padding"></a>Margem e preenchimento

_As propriedades Margin e Padding controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las._

## <a name="overview"></a>Visão geral

A margem e o preenchimento são conceitos de layout relacionados:

- A [`Margin`](xref:Xamarin.Forms.View.Margin) propriedade representa a distância entre um elemento e seus elementos adjacentes e é usada para controlar a posição de renderização do elemento e a posição de renderização de seus vizinhos. `Margin`os valores podem ser especificados em classes de [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [exibição](~/xamarin-forms/user-interface/controls/views.md) .
- A [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriedade representa a distância entre um elemento e seus elementos filho e é usada para separar o controle de seu próprio conteúdo. `Padding`os valores podem ser especificados em classes de [layout](~/xamarin-forms/user-interface/controls/layouts.md) .

O diagrama a seguir ilustra os dois conceitos:

[Conceitos de margens e preenchimento ![(margin-and-padding-images/margins-and-padding-sml.png " ")]] (margin-and-padding-images/margins-and-padding.png#lightbox "Conceitos de margens e preenchimento")

Observe que [`Margin`](xref:Xamarin.Forms.View.Margin) os valores são aditivos. Portanto, se dois elementos adjacentes especificarem uma margem de 20 pixels, a distância entre os elementos será de 40 pixels. Além disso, a margem e o preenchimento são aditivos quando ambos são aplicados, pois a distância entre um elemento e qualquer conteúdo será a margem mais preenchimento.

## <a name="specifying-a-thickness"></a>Especificando uma espessura

As [`Margin`](xref:Xamarin.Forms.View.Margin) propriedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) e são do tipo [`Thickness`](xref:Xamarin.Forms.Thickness). Há três possibilidades ao criar uma `Thickness` estrutura:

- Crie uma [`Thickness`](xref:Xamarin.Forms.Thickness) estrutura definida por um único valor uniforme. O valor único é aplicado aos lados esquerdo, superior, direito e inferior do elemento.
- Crie uma [`Thickness`](xref:Xamarin.Forms.Thickness) estrutura definida por valores horizontais e verticais. O valor horizontal é aplicado simetricamente aos lados esquerdo e direito do elemento, com o valor vertical sendo aplicado simetricamente aos lados superior e inferior do elemento.
- Crie uma [`Thickness`](xref:Xamarin.Forms.Thickness) estrutura definida por quatro valores distintos que são aplicados aos lados esquerdo, superior, direito e inferior do elemento.

O exemplo de código XAML a seguir mostra todas as três possibilidades:

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
> `Thickness`os valores podem ser negativos, o que normalmente corta ou desenha o conteúdo.

## <a name="summary"></a>Resumo

Este artigo demonstrou a diferença entre as [`Margin`](xref:Xamarin.Forms.View.Margin) propriedades [`Padding`](xref:Xamarin.Forms.Layout.Padding) e e como defini-las. O comportamento do layout de controle de propriedades quando um elemento é renderizado na interface do usuário.

## <a name="related-links"></a>Links relacionados

- [Margem](xref:Xamarin.Forms.View.Margin)
- [Margem](xref:Xamarin.Forms.Layout.Padding)
- [Espessura](xref:Xamarin.Forms.Thickness)
