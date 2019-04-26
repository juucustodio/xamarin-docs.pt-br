---
title: Seletor de xamarin. Forms
description: O seletor de xamarin. Forms exibe uma lista curta de itens, do qual o usuário pode selecionar um item. Este artigo explica como usar a classe de seletor para selecionar um item de texto em uma lista de dados.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: dc39fd9c129fb63fa4a3a73b15aea4204a38cdbd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230994"
---
# <a name="xamarinforms-picker"></a>Seletor de xamarin. Forms

_O modo de exibição do seletor é um controle para selecionar um item de texto de uma lista de dados._

O xamarin. Forms [ `Picker` ](xref:Xamarin.Forms.Picker) exibe uma lista curta de itens, do qual o usuário pode selecionar um item. `Picker` Define as propriedades a seguir:

- [`Title`](xref:Xamarin.Forms.Picker.Title) do tipo `string`, cujo padrão é `null`.
- `TitleColor` do tipo [ `Color` ](xref:Xamarin.Forms.Color), a cor usada para exibir o `Title` texto.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) do tipo `IList`, a lista de origem de itens a serem exibidos, cujo padrão é `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) do tipo `int`, o índice do item selecionado, cujo padrão é -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) do tipo `object`, o item selecionado, cujo padrão é `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) do tipo [ `Color` ](xref:Xamarin.Forms.Color), a cor usada para exibir o texto, cujo padrão é [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) do tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), cujo padrão é [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) do tipo `string`, cujo padrão é `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) do tipo `double`, cujo padrão é de -1,0.

Todas as propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser estilizados e as propriedades podem ser alvos de vinculações de dados. O [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) e [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedades têm um modo de associação padrão do [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), que significa que elas possam ser destinos de vinculações de dados em um aplicativo que usa o [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura. Para obter informações sobre como definir propriedades de fonte, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

Um [ `Picker` ](xref:Xamarin.Forms.Picker) não mostra todos os dados quando ele é exibido pela primeira vez. Em vez disso, o valor de sua [ `Title` ](xref:Xamarin.Forms.Picker.Title) propriedade é mostrada como um espaço reservado nas plataformas Android e iOS:

[![](images/picker-initial.png "Inicial seletor de exibição")](images/picker-initial-large.png#lightbox "inicial seletor de exibição")

Quando o [ `Picker` ](xref:Xamarin.Forms.Picker) ganhos foco, seus dados é exibido e o usuário pode selecionar um item:

[![](images/picker-selection.png "Selecionar um Item de seletor")](images/picker-selection-large.png#lightbox "seletor para selecionar um Item")

O [ `Picker` ](xref:Xamarin.Forms.Picker) dispara uma [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento quando o usuário seleciona um item. Após a seleção, o item selecionado é exibido pelo `Picker`:

![](images/picker-after-selection.png "Seletor de após a seleção")

Existem duas técnicas para preencher uma [ `Picker` ](xref:Xamarin.Forms.Picker) com dados:

- Definindo o [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade aos dados a serem exibidos. Esta é a técnica recomendada. Para obter mais informações, consulte [definindo a propriedade ItemsSource de um seletor](populating-itemssource.md).
- Adicionando dados a ser exibida para o [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleção. Essa técnica foi o processo original para popular uma [ `Picker` ](xref:Xamarin.Forms.Picker) com os dados. Para obter mais informações, consulte [adicionando dados à coleção de itens do seletor](populating-items.md).

## <a name="related-links"></a>Links relacionados

- [Seletor](xref:Xamarin.Forms.Picker)
