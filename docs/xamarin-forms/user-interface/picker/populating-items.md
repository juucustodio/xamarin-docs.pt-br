---
Título: "adicionando dados à coleção de itens de um seletor" Descrição: "a exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor com dados adicionando-o à coleção de itens e como responder à seleção de itens pelo usuário. "
MS. Prod: xamarin MS. AssetID: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 02/26/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="adding-data-to-a-pickers-items-collection"></a>Adicionar dados à coleção de itens do seletor

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_A exibição do seletor é um controle para selecionar um item de texto de uma lista de dados. Este artigo explica como preencher um seletor com dados adicionando-o à coleção de itens e como responder à seleção de itens pelo usuário._

## <a name="populating-a-picker-with-data"></a>Populando um seletor com dados

Antes do Xamarin.Forms 2.3.4, o processo de preenchimento de um [`Picker`](xref:Xamarin.Forms.Picker) com dados era adicionar os dados a serem exibidos à coleção somente leitura [`Items`](xref:Xamarin.Forms.Picker.Items) , que é do tipo `IList<string>` . Cada item na coleção deve ser do tipo `string` . Os itens podem ser adicionados em XAML inicializando a `Items` propriedade com uma lista de `x:String` itens:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
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

O código C# equivalente é mostrado abaixo:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Além de adicionar dados usando o `Items.Add` método, os dados também podem ser inseridos na coleção usando o `Items.Insert` método.

## <a name="responding-to-item-selection"></a>Respondendo à seleção de item

Um [`Picker`](xref:Xamarin.Forms.Picker) dá suporte à seleção de um item por vez. Quando um usuário seleciona um item, o [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento é disparado e a [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade é atualizada para um inteiro que representa o índice do item selecionado na lista. A `SelectedIndex` propriedade é um número de base zero que indica o item selecionado pelo usuário. Se nenhum item for selecionado, que é o caso quando o `Picker` é criado e inicializado pela primeira vez, `SelectedIndex` será-1.

> [!NOTE]
> O comportamento de seleção de item em um [`Picker`](xref:Xamarin.Forms.Picker) pode ser personalizado no Ios com uma plataforma específica. Para obter mais informações, consulte [controle de seleção de item de seletor](~/xamarin-forms/platform/ios/picker-selection.md).

O exemplo de código a seguir mostra o `OnPickerSelectedIndexChanged` método do manipulador de eventos, que é executado quando o evento é disparado [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) :

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

Esse método obtém o [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valor da propriedade e usa o valor para recuperar o item selecionado da [`Items`](xref:Xamarin.Forms.Picker.Items) coleção. Como cada item na `Items` coleção é um `string` , eles podem ser exibidos por um [`Label`](xref:Xamarin.Forms.Label) sem exigir uma conversão.

> [!NOTE]
> Um [`Picker`](xref:Xamarin.Forms.Picker) pode ser inicializado para exibir um item específico definindo a [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade. No entanto, a `SelectedIndex` propriedade deve ser definida após a inicialização da [`Items`](xref:Xamarin.Forms.Picker.Items) coleção.

## <a name="related-links"></a>Links relacionados

- [Demonstração do seletor (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Seletor](xref:Xamarin.Forms.Picker)
