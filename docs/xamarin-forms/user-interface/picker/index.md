---
title: Seletor de xamarin. Forms
description: O seletor de xamarin. Forms exibe uma lista curta de itens, na qual o usuário pode selecionar um item. Este artigo explica como usar a classe do seletor para selecionar um item de texto em uma lista de dados.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 82ae36a7be139e2a93d0e5c43c4bad355c49f217
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245033"
---
# <a name="xamarinforms-picker"></a>Seletor de xamarin. Forms

_O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados._

O xamarin. Forms [ `Picker` ](xref:Xamarin.Forms.Picker) exibe uma lista curta de itens, na qual o usuário pode selecionar um item. `Picker` define oito propriedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title) tipo `string`, que assume como padrão `null`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) tipo `IList`, a lista de origem de itens a serem exibidos, cujo padrão é `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) tipo `int`, o índice do item selecionado, cujo padrão é -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) tipo `object`, o item selecionado, cujo padrão é `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) tipo [ `Color` ](xref:Xamarin.Forms.Color), a cor usada para exibir o texto, cujo padrão é [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), que assume como padrão [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) tipo `string`, que assume como padrão `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) tipo `double`, que assume como padrão -1,0.

Todas as propriedades de oito contam com [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser denominados, e as propriedades podem ser alvos de associações de dados. O [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) e [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedades têm um modo de associação padrão de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), que significa que eles podem ser destinos das associações de dados em um aplicativo que usa o [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura. Para obter informações sobre propriedades de fonte de configuração, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) não mostra todos os dados quando ela é exibida pela primeira vez. Em vez disso, o valor de seu [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propriedade é mostrada como um espaço reservado nas plataformas Android e iOS:

[![](images/picker-initial.png "Seletor de exibição de inicial")](images/picker-initial-large.png#lightbox "inicial seletor de exibição")

Quando o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) ganhos foco, seus dados é exibido e o usuário pode selecionar um item:

[![](images/picker-selection.png "Selecionando um Item de seletor")](images/picker-selection-large.png#lightbox "seletor para selecionar um Item")

O [ `Picker` ](xref:Xamarin.Forms.Picker) dispara uma [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando o usuário seleciona um item. Após a seleção, o item selecionado é exibido pelo `Picker`:

![](images/picker-after-selection.png "Seletor de após a seleção")

Existem duas técnicas para popular um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) com dados:

- Definindo o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade aos dados a serem exibidos. Essa é a técnica recomendada, que foi introduzida no xamarin. Forms 2.3.4. Para obter mais informações, consulte [definindo a propriedade de ItemsSource do seletor](populating-itemssource.md).
- Adicionando dados a ser exibida para o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção. Essa técnica foi o processo original para popular um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) com dados. Para obter mais informações, consulte [adicionando dados à coleção de itens do seletor](populating-items.md).

## <a name="related-links"></a>Links relacionados

- [Seletor](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
