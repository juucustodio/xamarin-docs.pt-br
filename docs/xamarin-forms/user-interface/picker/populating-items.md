---
title: Adicionar dados à coleção de itens do seletor
description: O modo de exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, adicionando-o à coleção de itens e como responder a seleção de item pelo usuário.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 5eb3348df39b8931d8cdd89d8c6f54b8bb3024a0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057425"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Adicionar dados à coleção de itens do seletor

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)

_O modo de exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, adicionando-o à coleção de itens e como responder a seleção de item pelo usuário._

## <a name="populating-a-picker-with-data"></a>Populando um seletor de Data

Antes de xamarin. Forms 2.3.4, o processo de preenchimento de uma [ `Picker` ](xref:Xamarin.Forms.Picker) com dados era adicionar os dados a ser exibida para somente leitura [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleção, que é do tipo `IList<string>`. Cada item na coleção deve ser do tipo `string`. Itens podem ser adicionados no XAML, inicializando o `Items` propriedade com uma lista de `x:String` itens:

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

O código c# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Além de adicionar dados usando o `Items.Add` método, dados também podem ser inseridos na coleção usando o `Items.Insert` método.

## <a name="responding-to-item-selection"></a>Respondendo a seleção de Item

Um [ `Picker` ](xref:Xamarin.Forms.Picker) dá suporte à seleção de um item por vez. Quando um usuário seleciona um item, o [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado e o [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é atualizada em um inteiro que representa o índice do item selecionado na lista. O `SelectedIndex` propriedade é um número com base em zero que indica o item selecionado pelo usuário. Se nenhum item for selecionado, que é o caso quando o `Picker` é criado e inicializado, primeiramente `SelectedIndex` será -1.

> [!NOTE]
> Item de comportamento de seleção em um [ `Picker` ](xref:Xamarin.Forms.Picker) podem ser personalizadas no iOS com uma plataforma específica. Para obter mais informações, consulte [seleção de Item de seletor controlando](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

O seguinte exemplo de código mostra a `OnPickerSelectedIndexChanged` método de manipulador de eventos, que é executado quando o [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Esse método obtém o [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propriedade e usa o valor para recuperar o item selecionado dos [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleção. Porque cada item na `Items` coleção é um `string`, eles podem ser exibidos por uma [ `Label` ](xref:Xamarin.Forms.Label) sem a necessidade de uma conversão.

> [!NOTE]
> Um [ `Picker` ](xref:Xamarin.Forms.Picker) pode ser inicializado para exibir um item específico, configurando as [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade. No entanto, o `SelectedIndex` propriedade deve ser definida depois de inicializar o [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleção.

## <a name="summary"></a>Resumo

O [ `Picker` ](xref:Xamarin.Forms.Picker) exibição é um controle para selecionar um item de texto em uma lista de dados. Este artigo explicou como preencher uma `Picker` com os dados, adicionando-o para o [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleta e como responder a seleção de item pelo usuário. Isso foi o processo para usar um `Picker` antes do xamarin. Forms 2.3.4.


## <a name="related-links"></a>Links relacionados

- [Demonstração de seletor (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Seletor](xref:Xamarin.Forms.Picker)
