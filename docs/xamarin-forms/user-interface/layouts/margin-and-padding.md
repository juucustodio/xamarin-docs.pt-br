---
title: Margem e preenchimento
description: A margem e preenchimento propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996610"
---
# <a name="margin-and-padding"></a>Margem e preenchimento

_A margem e preenchimento propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário. Este artigo demonstra a diferença entre as duas propriedades e como defini-las._

## <a name="overview"></a>Visão geral

Margem e preenchimento são conceitos relacionados de layout:

- O [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriedade representa a distância entre um elemento e seus elementos adjacentes e é usada para controlar a posição de renderização do elemento e a posição de renderização de seus vizinhos. `Margin` valores podem ser especificados em [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [exibição](~/xamarin-forms/user-interface/controls/views.md) classes.
- O [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriedade representa a distância entre um elemento e seus elementos filho e é usada para separar o controle de seu próprio conteúdo. `Padding` valores podem ser especificados em [layout](~/xamarin-forms/user-interface/controls/layouts.md) classes.

O diagrama a seguir ilustra os dois conceitos:

[![](margin-and-padding-images/margins-and-padding-sml.png "Conceitos de preenchimento e margens")](margin-and-padding-images/margins-and-padding.png#lightbox "conceitos de preenchimento e margens")

Observe que [ `Margin` ](xref:Xamarin.Forms.View.Margin) valores são aditivas. Portanto, se dois elementos adjacentes especificam uma margem de 20 pixels, a distância entre os elementos será 40 pixels. Além disso, margem e preenchimento são aditivos, quando ambos são aplicadas, em que a distância entre um elemento e qualquer conteúdo será a margem e preenchimento.

## <a name="specifying-a-thickness"></a>Especifica uma espessura

O [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriedades forem do tipo [ `Thickness` ](xref:Xamarin.Forms.Thickness). Existem três possibilidades durante a criação de um `Thickness` estrutura:

- Criar uma [ `Thickness` ](xref:Xamarin.Forms.Thickness) estrutura definida por um único valor uniforme. O valor único é aplicado para a esquerda, superior, direita e lados da parte inferior do elemento.
- Criar uma [ `Thickness` ](xref:Xamarin.Forms.Thickness) estrutura definida pelos valores horizontais e verticais. O valor horizontal é aplicado simetricamente para os lados esquerdo e direito do elemento, com o valor vertical que está sendo aplicado simetricamente para os lados superior e inferior do elemento.
- Criar uma [ `Thickness` ](xref:Xamarin.Forms.Thickness) estrutura definida por quatro valores distintos que são aplicados para a esquerda, superior, direita e lados da parte inferior do elemento.

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
> `Thickness` valores podem ser negativos, que normalmente recorta ou overdraws o conteúdo.

## <a name="summary"></a>Resumo

Este artigo demonstrou a diferença entre o [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriedades e como defini-las. As propriedades controlam o comportamento de layout quando um elemento é renderizado na interface do usuário.


## <a name="related-links"></a>Links relacionados

- [Margem](xref:Xamarin.Forms.View.Margin)
- [Preenchimento](xref:Xamarin.Forms.Layout.Padding)
- [Espessura](xref:Xamarin.Forms.Thickness)
