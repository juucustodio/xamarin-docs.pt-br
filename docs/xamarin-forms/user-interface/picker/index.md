---
title: Seletor Xamarin. Forms
description: O seletor Xamarin. Forms exibe uma pequena lista de itens, da qual o usuário pode selecionar um item. Este artigo explica como usar a classe de seletor para selecionar um item de texto de uma lista de dados.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695997"
---
# <a name="xamarinforms-picker"></a>Seletor Xamarin. Forms

_A exibição do seletor é um controle para selecionar um item de texto de uma lista de dados._

O [`Picker`](xref:Xamarin.Forms.Picker) Xamarin. Forms exibe uma pequena lista de itens, da qual o usuário pode selecionar um item. `Picker` define as seguintes propriedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title) do tipo `string`, cujo padrão é `null`.
- `TitleColor` do tipo [`Color`](xref:Xamarin.Forms.Color), a cor usada para exibir o texto do `Title`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) do tipo `IList`, a lista fonte de itens a serem exibidos, cujo padrão é `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) do tipo `int`, o índice do item selecionado, cujo padrão é-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) do tipo `object`, o item selecionado, cujo padrão é `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) do tipo [`Color`](xref:Xamarin.Forms.Color), a cor usada para exibir o texto, cujo padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) do tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), cujo padrão é [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) do tipo `string`, cujo padrão é `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) do tipo `double`, que usa como padrão-1,0.
- `CharacterSpacing`, do tipo `double`, é o espaçamento entre os caracteres do item exibido pelo `Picker`.

Todas as propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser estilizadas e as propriedades podem ser destinos de associações de dados. As propriedades [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) e [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) têm um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), o que significa que elas podem ser destinos de associações de dados em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

Um [`Picker`](xref:Xamarin.Forms.Picker) não mostra nenhum dado quando é exibido pela primeira vez. Em vez disso, o valor de sua propriedade [`Title`](xref:Xamarin.Forms.Picker.Title) é mostrado como um espaço reservado nas plataformas Ios e Android:

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Quando o [`Picker`](xref:Xamarin.Forms.Picker) ganha foco, seus dados são exibidos e o usuário pode selecionar um item:

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

O [`Picker`](xref:Xamarin.Forms.Picker) aciona um evento de [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) quando o usuário seleciona um item. Após a seleção, o item selecionado é exibido pelo `Picker`:

![](images/picker-after-selection.png "Picker after Selection")

Há duas técnicas para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados:

- Definindo a propriedade [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) para os dados a serem exibidos. Esta é a técnica recomendada. Para obter mais informações, consulte [definindo a propriedade ItemsSource de um seletor](populating-itemssource.md).
- Adicionando os dados a serem exibidos à coleção de [`Items`](xref:Xamarin.Forms.Picker.Items) . Essa técnica era o processo original para popular um [`Picker`](xref:Xamarin.Forms.Picker) com dados. Para obter mais informações, consulte [adicionando dados à coleção de itens de um seletor](populating-items.md).

## <a name="related-links"></a>Links relacionados

- [Seletor](xref:Xamarin.Forms.Picker)
