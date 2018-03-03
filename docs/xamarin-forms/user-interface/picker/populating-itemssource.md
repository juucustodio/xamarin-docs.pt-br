---
title: Definir propriedade de ItemsSource do seletor
description: "O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, definindo a propriedade ItemsSource e como responder a seleção de item pelo usuário."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 5e3d20ad213df9fd9331c71c84003c7738bd5a29
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="setting-a-pickers-itemssource-property"></a>Definir propriedade de ItemsSource do seletor

_O modo de exibição de seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, definindo a propriedade ItemsSource e como responder a seleção de item pelo usuário._

Xamarin. Forms 2.3.4 aprimorou o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) exibição adicionando a capacidade para preenchê-la com dados definindo sua [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade e para recuperar o item selecionado do [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) propriedade. Além disso, a cor do texto do item selecionado pode ser alterada definindo a [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.TextColor/) propriedade para um [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/).

## <a name="populating-a-picker-with-data"></a>Preenchendo um seletor de dados

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) podem ser preenchidos com dados definindo sua [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade para um `IList` coleção. Cada item na coleção deve ser de, ou derivado, `object`. Itens podem ser adicionados em XAML, inicializando o `ItemsSource` propriedade de uma matriz de itens:

```xaml
<Picker x:Name="picker" Title="Select a monkey">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Observe que o `x:Array` elemento requer uma `Type` atributo que indica o tipo dos itens na matriz.

O código c# equivalente é mostrado abaixo:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Respondendo a seleção de Item

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) dá suporte à seleção de um item de cada vez. Quando um usuário seleciona um item, o [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento ser acionado, o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propriedade é atualizada para um inteiro que representa o índice do item selecionado na lista e o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) propriedade é atualizada para o `object` que representa o item selecionado. O [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propriedade é um número com base em zero que indica o item que o usuário selecionado. Se nenhum item for selecionado, que é o caso quando o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) primeiro é criado e inicializado, `SelectedIndex` será -1.

> [!NOTE]
> Item o comportamento de seleção em uma [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) podem ser personalizadas no iOS com uma plataforma específica. Para obter mais informações, consulte [controlando a seleção de Item do seletor](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

O exemplo de código a seguir mostra como recuperar o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) valor de propriedade de [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) em XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

O código c# equivalente é mostrado abaixo:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Além disso, um manipulador de eventos pode ser executado quando o [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) aciona eventos:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Esse método obtém o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) valor da propriedade e usa o valor para recuperar o item selecionado do [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) coleção. Isso é funcionalmente equivalente ao recuperar o item selecionado do [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) propriedade. Observe que cada item de `ItemsSource` coleção é do tipo `object`e, portanto, deve ser convertido em um `string` para exibição.

> [!NOTE]
> Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) pode ser inicializado para exibir um item específico, definindo o [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) ou [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) propriedades. No entanto, essas propriedades devem ser definidas após inicializar o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) coleção.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Preenchendo um seletor de dados usando a associação de dados

Um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) pode ser também preenchido com dados usando associação de dados para associar seu [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade para um `IList` coleção. Em XAML, isso é feito com o [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) extensão de marcação:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

O código c# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

O [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) associa dados de propriedade para o `Monkeys` propriedade do modelo de exibição conectado, que retorna um `IList<Monkey>` coleção. O seguinte exemplo de código mostra o `Monkey` classe, que contém quatro propriedades:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Ao associar a uma lista de objetos, o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) deve ser informado cuja propriedade para exibir de cada objeto. Isso é feito definindo o [ `ItemDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemDisplayBinding/) propriedade para a propriedade de cada objeto. Nos exemplos de código acima, o `Picker` estiver definido para exibir cada `Monkey.Name` valor da propriedade.

### <a name="responding-to-item-selection"></a>Respondendo a seleção de Item

Associação de dados pode ser usada para definir um objeto com o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) valor da propriedade quando ele é alterado:

```xaml
<Picker Title="Select a monkey"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

O código c# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

O [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) associa dados de propriedade para o `SelectedMonkey` propriedade do modelo de exibição conectado, que é do tipo `Monkey`. Portanto, quando o usuário seleciona um item no [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), o `SelectedMonkey` propriedade será definida para selecionado `Monkey` objeto. O `SelectedMonkey` dados de objeto são exibidos na interface do usuário por [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) modos de exibição:

![](populating-itemssource-images/monkeys.png "Seleção de Item do seletor")

> [!NOTE]
> Observe que o [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) e [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) ambas as propriedades oferecem suporte a associações bidirecionais por padrão.

## <a name="summary"></a>Resumo

O [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) exibição é um controle para selecionar um item de texto de uma lista de dados. Este artigo explicou como preencher uma `Picker` com dados definindo o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propriedade e como responder a seleção de item pelo usuário. Essa abordagem, o que foi introduzida no xamarin. Forms 2.3.4, é a abordagem recomendada para interagir com um `Picker`.


## <a name="related-links"></a>Links relacionados

- [Demonstração de seletor (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Macaco aplicativo (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Seletor de associável (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Seletor](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
