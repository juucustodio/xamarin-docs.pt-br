---
title: Definindo a propriedade ItemsSource de um seletor
description: O modo de exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, definindo a propriedade ItemsSource e como responder a seleção de item pelo usuário.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 2c7daca80a207d0c060fc3a867b1eda03dd65258
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557071"
---
# <a name="setting-a-pickers-itemssource-property"></a>Definindo a propriedade ItemsSource de um seletor

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)

_O modo de exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor de dados, definindo a propriedade ItemsSource e como responder a seleção de item pelo usuário._

Xamarin. Forms 2.3.4 aprimorou o [ `Picker` ](xref:Xamarin.Forms.Picker) exibição adicionando a capacidade para preenchê-lo com dados, definindo sua [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade e para recuperar o item selecionado do [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedade. Além disso, a cor do texto do item selecionado pode ser alterada definindo a [ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor) propriedade como um [ `Color` ](xref:Xamarin.Forms.Color).

## <a name="populating-a-picker-with-data"></a>Populando um seletor de data

Um [ `Picker` ](xref:Xamarin.Forms.Picker) podem ser populados com dados, definindo sua [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade para um `IList` coleção. Cada item na coleção deve ser de ou derivado de, digite `object`. Itens podem ser adicionados no XAML, inicializando o `ItemsSource` propriedade de uma matriz de itens:

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
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
> Observe que o `x:Array` elemento requer um `Type` atributo que indica o tipo dos itens na matriz.

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

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Respondendo a seleção de item

Um [ `Picker` ](xref:Xamarin.Forms.Picker) dá suporte à seleção de um item por vez. Quando um usuário seleciona um item, o [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado, o [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é atualizada em um inteiro que representa o índice do item selecionado na lista e o [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedade é atualizada para o `object` que representa o item selecionado. O [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é um número com base em zero que indica o item que o usuário selecionado. Se nenhum item for selecionado, que é o caso quando o [ `Picker` ](xref:Xamarin.Forms.Picker) primeiro é criado e inicializado, `SelectedIndex` será -1.

> [!NOTE]
> Item de comportamento de seleção em um [ `Picker` ](xref:Xamarin.Forms.Picker) podem ser personalizadas no iOS com uma plataforma específica. Para obter mais informações, consulte [seleção de Item de seletor controlando](~/xamarin-forms/platform/ios/picker-selection.md).

O exemplo de código a seguir mostra como recuperar o [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valor da propriedade a [ `Picker` ](xref:Xamarin.Forms.Picker) no XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

O código c# equivalente é mostrado abaixo:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Além disso, um manipulador de eventos pode ser executado quando o [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado:

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

Esse método obtém o [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propriedade e usa o valor para recuperar o item selecionado dos [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) coleção. Isso é funcionalmente equivalente ao recuperar o item selecionado dos [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedade. Observe que cada item de `ItemsSource` coleção é do tipo `object`e, portanto, deve ser convertido em um `string` para exibição.

> [!NOTE]
> Um [ `Picker` ](xref:Xamarin.Forms.Picker) pode ser inicializado para exibir um item específico, configurando as [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) ou [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriedades. No entanto, essas propriedades devem ser definidas depois de inicializar o [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) coleção.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Preenchendo um seletor com dados usando a associação de dados

Um [ `Picker` ](xref:Xamarin.Forms.Picker) pode ser também preenchido com dados usando associação de dados para associar seu [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriedade para um `IList` coleção. No XAML, isso é feito com o [ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension) extensão de marcação:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

O código c# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

O [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) associa dados de propriedade para o `Monkeys` propriedade do modelo de exibição conectados, que retorna um `IList<Monkey>` coleção. O seguinte exemplo de código mostra o `Monkey` classe, que contém quatro propriedades:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Ao associar a uma lista de objetos, o [ `Picker` ](xref:Xamarin.Forms.Picker) deve ser informado de qual propriedade a ser exibida de cada objeto. Isso é feito definindo a [ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding) propriedade para a propriedade necessária de cada objeto. Nos exemplos de código acima, o `Picker` estiver definido para exibir cada `Monkey.Name` valor da propriedade.

### <a name="responding-to-item-selection"></a>Respondendo a seleção de item

Associação de dados pode ser usada para definir um objeto com o [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valor da propriedade quando ele é alterado:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
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
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
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

O [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) associa dados de propriedade para o `SelectedMonkey` propriedade do modelo de exibição conectado, o que é do tipo `Monkey`. Portanto, quando o usuário seleciona um item na [ `Picker` ](xref:Xamarin.Forms.Picker), o `SelectedMonkey` propriedade será definida para selecionado `Monkey` objeto. O `SelectedMonkey` dados de objeto são exibidos na interface do usuário por [ `Label` ](xref:Xamarin.Forms.Label) e [ `Image` ](xref:Xamarin.Forms.Image) modos de exibição:

![](populating-itemssource-images/monkeys.png "Seleção de Item do seletor")

> [!NOTE]
> Observe que o [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) e [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) ambas as propriedades dá suporte a associações bidirecionais por padrão.

## <a name="related-links"></a>Links relacionados

- [Demonstração de seletor (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey App (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Seletor de associável (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Seletor de API](xref:Xamarin.Forms.Picker)
