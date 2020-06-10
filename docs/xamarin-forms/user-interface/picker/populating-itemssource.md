---
title: "definindo a propriedade ItemsSource de um seletor" Descrição: "a exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor com dados definindo a propriedade ItemsSource e como responder à seleção de itens pelo usuário. "
MS. Prod: xamarin MS. AssetID: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 02/26/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setting-a-pickers-itemssource-property"></a>Definir a propriedade ItemsSource de um seletor

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_A exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor com dados definindo a propriedade ItemsSource e como responder à seleção de itens pelo usuário._

Xamarin.Formso 2.3.4 aprimorou a [`Picker`](xref:Xamarin.Forms.Picker) exibição adicionando a capacidade de preenchê-la com os dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriedade e para recuperar o item selecionado da [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriedade. Além disso, a cor do texto do item selecionado pode ser alterada definindo a [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) propriedade como a [`Color`](xref:Xamarin.Forms.Color) .

## <a name="populating-a-picker-with-data"></a>Populando um seletor com dados

Um [`Picker`](xref:Xamarin.Forms.Picker) pode ser preenchido com dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriedade como uma `IList` coleção. Cada item na coleção deve ser de, ou derivado de, Type `object` . Os itens podem ser adicionados em XAML inicializando a `ItemsSource` propriedade de uma matriz de itens:

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
> Observe que o elemento `x:Array` requer um atributo `Type` que indica o tipo dos itens na matriz.

O código C# equivalente é mostrado abaixo:

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

## <a name="responding-to-item-selection"></a>Respondendo à seleção de item

Um [`Picker`](xref:Xamarin.Forms.Picker) dá suporte à seleção de um item por vez. Quando um usuário seleciona um item, o [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado, a [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é atualizada para um inteiro que representa o índice do item selecionado na lista e a [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriedade é atualizada para a `object` representação do item selecionado. A [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é um número de base zero que indica o item selecionado pelo usuário. Se nenhum item for selecionado, que é o caso quando o [`Picker`](xref:Xamarin.Forms.Picker) é criado e inicializado pela primeira vez, `SelectedIndex` será-1.

> [!NOTE]
> O comportamento de seleção de item em um [`Picker`](xref:Xamarin.Forms.Picker) pode ser personalizado no Ios com uma plataforma específica. Para obter mais informações, consulte [controle de seleção de item de seletor](~/xamarin-forms/platform/ios/picker-selection.md).

O exemplo de código a seguir mostra como recuperar o [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valor da Propriedade do [`Picker`](xref:Xamarin.Forms.Picker) em XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

O código C# equivalente é mostrado abaixo:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Além disso, um manipulador de eventos pode ser executado quando o [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é acionado:

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

Esse método obtém o [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valor da propriedade e usa o valor para recuperar o item selecionado da [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) coleção. Isso é funcionalmente equivalente a recuperar o item selecionado da [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriedade. Observe que cada item na `ItemsSource` coleção é do tipo `object` e, portanto, deve ser convertido em um `string` para exibição.

> [!NOTE]
> Um [`Picker`](xref:Xamarin.Forms.Picker) pode ser inicializado para exibir um item específico definindo as [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Propriedades ou. No entanto, essas propriedades devem ser definidas após a inicialização da [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) coleção.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Populando um seletor com dados usando Associação de dados

Um [`Picker`](xref:Xamarin.Forms.Picker) também pode ser preenchido com dados usando a vinculação de dados para associar sua [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) Propriedade a uma `IList` coleção. Em XAML, isso é obtido com a [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extensão de marcação:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

O código C# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Os [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) dados de propriedade são associados à `Monkeys` Propriedade do modelo de exibição conectado, que retorna uma `IList<Monkey>` coleção. O exemplo de código a seguir mostra a `Monkey` classe, que contém quatro propriedades:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Ao associar a uma lista de objetos, o [`Picker`](xref:Xamarin.Forms.Picker) deve ser informado sobre qual propriedade Exibir de cada objeto. Isso é feito definindo a [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) propriedade para a propriedade Required de cada objeto. Nos exemplos de código acima, o `Picker` é definido para exibir cada `Monkey.Name` valor de propriedade.

### <a name="responding-to-item-selection"></a>Respondendo à seleção de item

A ligação de dados pode ser usada para definir um objeto para o [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valor da propriedade quando ele for alterado:

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

O código C# equivalente é mostrado abaixo:

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

Os [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) dados de propriedade são associados à `SelectedMonkey` Propriedade do modelo de exibição conectado, que é do tipo `Monkey` . Portanto, quando o usuário seleciona um item no [`Picker`](xref:Xamarin.Forms.Picker) , a `SelectedMonkey` propriedade será definida como o `Monkey` objeto selecionado. Os `SelectedMonkey` dados do objeto são exibidos na interface do usuário por [`Label`](xref:Xamarin.Forms.Label) e [`Image`](xref:Xamarin.Forms.Image) modos de exibição:

![](populating-itemssource-images/monkeys.png "Picker Item Selection")

> [!NOTE]
> Observe que as [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) Propriedades e dão suporte a associações bidirecionais por padrão.

## <a name="related-links"></a>Links relacionados

- [Demonstração do seletor (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Aplicativo de macaco (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [Seletor vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [API do seletor](xref:Xamarin.Forms.Picker)
