---
Título: "fontes de dados ListView" Descrição: "Este artigo explica como preencher o Xamarin.Forms ListView com dados e como usar a vinculação de dados com um ListView".
MS. Prod: xamarin MS. AssetID: B5571660-1E82-4379-95C3-0725288CF5D9 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 03/23/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-data-sources"></a>Fontes de dados ListView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Um Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) é usado para exibir listas de dados. Este artigo explica como preencher um `ListView` com dados e como associar dados ao item selecionado.

## <a name="itemssource"></a>ItemsSource

Um [`ListView`](xref:Xamarin.Forms.ListView) é populado com dados usando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade, que pode aceitar qualquer coleção Implementando `IEnumerable` . A maneira mais simples de preencher um `ListView` envolve o uso de uma matriz de cadeias de caracteres:

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

Este é o código C# equivalente:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![](data-and-databinding-images/itemssource-simple.png "ListView Displaying List of Strings")

Essa abordagem irá preencher o `ListView` com uma lista de cadeias de caracteres. Por padrão, `ListView` o chamará `ToString` e exibirá o resultado em um `TextCell` para cada linha. Para personalizar como os dados são exibidos, consulte [aparência da célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Como `ItemsSource` foi enviado a uma matriz, o conteúdo não será atualizado à medida que a lista subjacente ou a matriz for alterada. Se desejar que o ListView seja atualizado automaticamente à medida que itens forem adicionados, removidos e alterados na lista subjacente, você precisará usar um `ObservableCollection` . [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1)o é definido em `System.Collections.ObjectModel` e é exatamente como `List` , exceto pelo fato de que ele pode notificar `ListView` as alterações:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Associação de dados

A vinculação de dados é a "cola" que associa as propriedades de um objeto de interface do usuário às propriedades de algum objeto CLR, como uma classe em seu ViewModel. A vinculação de dados é útil porque simplifica o desenvolvimento de interfaces do usuário, substituindo muito código clichê enfadonho.

A vinculação de dados funciona mantendo os objetos sincronizados à medida que seus valores associados mudam. Em vez de ter que escrever manipuladores de eventos para cada vez que o valor de um controle é alterado, você estabelece a associação e habilita a associação em seu ViewModel.

Para obter mais informações sobre vinculação de dados, consulte [noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) que são a parte quatro da [ Xamarin.Forms série de artigos sobre elementos básicos do XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Células de associação

As propriedades de células (e filhos de células) podem ser associadas a propriedades de objetos no `ItemsSource` . Por exemplo, um `ListView` pode ser usado para apresentar uma lista de funcionários.

A classe Employee:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Um `ObservableCollection<Employee>` é criado, definido como `ListView` `ItemsSource` e a lista é populada com dados:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> Enquanto um `ListView` será atualizado em resposta às alterações em seu subjacente `ObservableCollection` , um `ListView` não será atualizado se uma `ObservableCollection` instância diferente for atribuída à referência original `ObservableCollection` (por exemplo, `employees = otherObservableCollection;` ).

O trecho a seguir demonstra um `ListView` associado a uma lista de funcionários:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Este exemplo de XAML define um `ContentPage` que contém um `ListView` . A fonte de dados do `ListView` é definida por meio do atributo `ItemsSource`. O layout de cada linha no `ItemsSource` é definido dentro do elemento `ListView.ItemTemplate`. Isso resulta nas seguintes capturas de tela:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` não é segura para thread. Modificar um `ObservableCollection` faz com que as atualizações da interface do usuário ocorram no mesmo thread que realizou as modificações. Se o thread não for o thread de interface do usuário primário, ele causará uma exceção.

### <a name="binding-selecteditem"></a>Associação SelectedItem

Muitas vezes, você desejará associar ao item selecionado de a `ListView` , em vez de usar um manipulador de eventos para responder a alterações. Para fazer isso em XAML, associe a `SelectedItem` Propriedade:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Supondo `listView` `ItemsSource` que seja uma lista de cadeias de caracteres, terá `SomeLabel` sua `Text` propriedade associada ao `SelectedItem` .

## <a name="related-links"></a>Links relacionados

- [Associação de duas vias (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
