---
title: Adicionando dados à coleção de itens do seletor
description: O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados adicionando-o à coleção de itens e como responder a seleção de item pelo usuário.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 63a72861895f79d2d0154297f88610ddb8bb8beb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30792648"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Adicionando dados à coleção de itens do seletor

_O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados adicionando-o à coleção de itens e como responder a seleção de item pelo usuário._

## <a name="populating-a-picker-with-data"></a>Preenchendo um seletor de dados

Antes de xamarin. Forms 2.3.4, o processo para popular um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) com dados foi adicionar os dados a ser exibida para somente leitura [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção, que é do tipo `IList<string>`. Cada item na coleção deve ser do tipo `string`. Itens podem ser adicionados em XAML, inicializando o `Items` propriedade com uma lista de `x:String` itens:

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

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) dá suporte à seleção de um item de cada vez. Quando um usuário seleciona um item, o [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento ser acionado e o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propriedade é atualizada para um inteiro que representa o índice do item selecionado na lista. O `SelectedIndex` propriedade é um número com base em zero que indica o item selecionado pelo usuário. Se nenhum item for selecionado, que é o caso quando o `Picker` primeiro é criado e inicializado, `SelectedIndex` será -1.

> [!NOTE]
> Item o comportamento de seleção em uma [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) podem ser personalizadas no iOS com uma plataforma específica. Para obter mais informações, consulte [controlando a seleção de Item do seletor](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

O seguinte exemplo de código mostra o `OnPickerSelectedIndexChanged` método de manipulador de eventos, que é executado quando o [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) aciona eventos:

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

Esse método obtém o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) valor da propriedade e usa o valor para recuperar o item selecionado do [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção. Porque cada item a `Items` coleção é um `string`, eles podem ser exibidos por um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) sem a necessidade de uma conversão.

> [!NOTE]
> Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) pode ser inicializado para exibir um item específico, definindo o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propriedade. No entanto, o `SelectedIndex` propriedade deve ser definida após inicializar o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção.

## <a name="summary"></a>Resumo

O [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) exibição é um controle para selecionar um item de texto de uma lista de dados. Este artigo explicou como preencher uma `Picker` com dados, adicionando-o para o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleta e como responder a seleção de item pelo usuário. Esse era o processo para usar um `Picker` antes de xamarin. Forms 2.3.4.


## <a name="related-links"></a>Links relacionados

- [Demonstração de seletor (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Seletor](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
