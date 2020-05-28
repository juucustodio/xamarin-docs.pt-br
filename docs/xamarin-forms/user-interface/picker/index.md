---
title: Xamarin.FormsSeletor
description: O Xamarin.Forms seletor exibe uma lista curta de itens, da qual o usuário pode selecionar um item. Este artigo explica como usar a classe de seletor para selecionar um item de texto de uma lista de dados.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 50f605f4ad9839521fd4169531ad46d197f20dbf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139642"
---
# <a name="xamarinforms-picker"></a>Xamarin.FormsSeletor

_A exibição do seletor é um controle para selecionar um item de texto de uma lista de dados._

O Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) exibe uma lista curta de itens, da qual o usuário pode selecionar um item. `Picker` define as propriedades a seguir:

- [`Title`](xref:Xamarin.Forms.Picker.Title)do tipo `string` , que usa como padrão `null` .
- `TitleColor`do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir o `Title` texto.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)do tipo `IList` , a lista fonte de itens a serem exibidos, cujo padrão é `null` .
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)do tipo `int` , o índice do item selecionado, cujo padrão é-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)do tipo `object` , o item selecionado, cujo padrão é `null` .
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir o texto, cujo padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que usa como padrão [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)do tipo `string` , que usa como padrão `null` .
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)do tipo `double` , que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double` , é o espaçamento entre os caracteres do item exibido pelo `Picker` .

Todas as propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. As [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Propriedades e têm um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , o que significa que elas podem ser destinos de associações de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

Um [`Picker`](xref:Xamarin.Forms.Picker) não mostra dados quando é exibido pela primeira vez. Em vez disso, o valor de sua [`Title`](xref:Xamarin.Forms.Picker.Title) propriedade é mostrado como um espaço reservado nas plataformas Ios e Android:

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Quando os [`Picker`](xref:Xamarin.Forms.Picker) ganhos se concentrarem, seus dados serão exibidos e o usuário poderá selecionar um item:

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

O [`Picker`](xref:Xamarin.Forms.Picker) dispara um [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando o usuário seleciona um item. Após a seleção, o item selecionado é exibido pelo `Picker` :

![](images/picker-after-selection.png "Picker after Selection")

Há duas técnicas para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados:

- Definindo a [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriedade para os dados a serem exibidos. Essa é a técnica recomendada. Para obter mais informações, consulte [definindo a propriedade ItemsSource de um seletor](populating-itemssource.md).
- Adicionando os dados a serem exibidos à [`Items`](xref:Xamarin.Forms.Picker.Items) coleção. Essa técnica era o processo original para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados. Para obter mais informações, consulte [adicionando dados à coleção de itens de um seletor](populating-items.md).

## <a name="related-links"></a>Links relacionados

- [Seletor](xref:Xamarin.Forms.Picker)
