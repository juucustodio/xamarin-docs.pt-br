---
title: Xamarin.FormsSeletor
description: O Xamarin.Forms seletor exibe uma lista curta de itens, da qual o usuário pode selecionar um item. Este artigo explica como usar a classe de seletor para selecionar um item de texto de uma lista de dados.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 30f5aefe5fcb327a7c3333bee8e8b553e2630f57
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918007"
---
# <a name="no-locxamarinforms-picker"></a>Xamarin.FormsSeletor

_A exibição do seletor é um controle para selecionar um item de texto de uma lista de dados._

O Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) exibe uma lista curta de itens, da qual o usuário pode selecionar um item. `Picker` define as propriedades a seguir:

- [`CharacterSpacing`](xref:Xamarin.Forms.Picker.CharacterSpacing), do tipo `double` , é o espaçamento entre os caracteres do item exibido pelo `Picker` .
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , que usa como padrão [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)do tipo `string` , que usa como padrão `null` .
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)do tipo `double` , que usa como padrão-1,0.
- `HorizontalTextAlignment`, do tipo [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) , é o alinhamento horizontal do texto exibido pelo `Picker` .
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)do tipo `IList` , a lista fonte de itens a serem exibidos, cujo padrão é `null` .
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)do tipo `int` , o índice do item selecionado, cujo padrão é-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)do tipo `object` , o item selecionado, cujo padrão é `null` .
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir o texto, cujo padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`Title`](xref:Xamarin.Forms.Picker.Title)do tipo `string` , que usa como padrão `null` .
- [`TitleColor`](xref:Xamarin.Forms.Picker.TitleColor)do tipo [`Color`](xref:Xamarin.Forms.Color) , a cor usada para exibir o `Title` texto.
- `VerticalTextAlignment`, do tipo [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) , é o alinhamento vertical do texto exibido pelo `Picker` .

Todas as propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. As [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Propriedades e têm um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , o que significa que elas podem ser destinos de associações de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

Um [`Picker`](xref:Xamarin.Forms.Picker) não mostra dados quando é exibido pela primeira vez. Em vez disso, o valor de sua [`Title`](xref:Xamarin.Forms.Picker.Title) propriedade é mostrado como um espaço reservado nas plataformas Ios e Android:

[![Exibição do seletor inicial](images/picker-initial.png)](images/picker-initial-large.png#lightbox "Exibição do seletor inicial")

Quando os [`Picker`](xref:Xamarin.Forms.Picker) ganhos se concentrarem, seus dados serão exibidos e o usuário poderá selecionar um item:

[![Seletor selecionando um item](images/picker-selection.png)](images/picker-selection-large.png#lightbox "Seletor selecionando um item")

O [`Picker`](xref:Xamarin.Forms.Picker) dispara um [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando o usuário seleciona um item. Após a seleção, o item selecionado é exibido pelo `Picker` :

![Seletor após seleção](images/picker-after-selection.png)

Há duas técnicas para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados:

- Definindo a [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriedade para os dados a serem exibidos. Essa é a técnica recomendada. Para obter mais informações, consulte [definindo a propriedade ItemsSource de um seletor](populating-itemssource.md).
- Adicionando os dados a serem exibidos à [`Items`](xref:Xamarin.Forms.Picker.Items) coleção. Essa técnica era o processo original para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados. Para obter mais informações, consulte [adicionando dados à coleção de itens de um seletor](populating-items.md).

## <a name="related-links"></a>Links relacionados

- [Seletor](xref:Xamarin.Forms.Picker)
