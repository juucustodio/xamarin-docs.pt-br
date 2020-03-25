---
title: Fontes de dados de ListView
description: Este artigo explica como popular o ListView de xamarin. Forms com os dados e como usar a vinculação de dados com um ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: e51f0bd011750b030c0a11b9b89a2c2473f2a9ed
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247581"
---
# <a name="listview-data-sources"></a>Fontes de dados de ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Um [`ListView`](xref:Xamarin.Forms.ListView) Xamarin. Forms é usado para exibir listas de dados. Este artigo explica como preencher um `ListView` com dados e como associar dados ao item selecionado.

## <a name="itemssource"></a>ItemsSource

Uma [`ListView`](xref:Xamarin.Forms.ListView) é populada com dados usando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) , que pode aceitar qualquer coleção que implemente `IEnumerable`. A maneira mais simples de preencher uma `ListView` envolve o uso de uma matriz de cadeias de caracteres:

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

Essa abordagem irá preencher o `ListView` com uma lista de cadeias de caracteres. Por padrão, `ListView` chamará `ToString` e exibirá o resultado em uma `TextCell` para cada linha. Para personalizar como os dados são exibidos, consulte [aparência da célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Como `ItemsSource` foi enviado a uma matriz, o conteúdo não será atualizado à medida que a lista subjacente ou a matriz for alterada. Se desejar que o ListView seja atualizado automaticamente à medida que itens forem adicionados, removidos e alterados na lista subjacente, você precisará usar um `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) é definido em `System.Collections.ObjectModel` e é exatamente como `List`, exceto que ele pode notificar `ListView` de qualquer alteração:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Associação de dados

A vinculação de dados é a "cola" que associa as propriedades de um objeto de interface do usuário às propriedades de algum objeto CLR, como uma classe em seu ViewModel. Associação de dados é útil porque ele simplifica o desenvolvimento de interfaces do usuário, substituindo muito código clichê que sem graça.

Associação de dados funciona mantendo os objetos sincronizados como alteram seus valores associados. Em vez de ter que escrever manipuladores de eventos para cada vez que o valor de um controle é alterado, você estabelece a associação e habilita a associação em seu ViewModel.

Para obter mais informações sobre a vinculação de dados, consulte [noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) que são a parte quatro da [série de artigos sobre fundamentos do XAML do Xamarin. Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Células de associação

As propriedades de células (e filhos de células) podem ser associadas a propriedades de objetos no `ItemsSource`. Por exemplo, um `ListView` poderia ser usado para apresentar uma lista de funcionários.

A classe do funcionário:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Um `ObservableCollection<Employee>` é criado, definido como o `ListView` `ItemsSource`e a lista é populada com dados:

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
> Enquanto um `ListView` será atualizado em resposta às alterações em seu `ObservableCollection`subjacente, um `ListView` não será atualizado se uma instância de `ObservableCollection` diferente for atribuída à referência de `ObservableCollection` original (por exemplo, `employees = otherObservableCollection;`).

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

Este exemplo de XAML define um `ContentPage` que contém um `ListView`. A fonte de dados da `ListView` é definida por meio do atributo `ItemsSource`. O layout de cada linha na `ItemsSource` é definido dentro do elemento `ListView.ItemTemplate`. Isso resulta nas seguintes capturas de tela:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` não é thread-safe. Modificar um `ObservableCollection` faz com que as atualizações da interface do usuário ocorram no mesmo thread que realizou as modificações. Se o thread não for o thread de interface do usuário primário, ele causará uma exceção.

### <a name="binding-selecteditem"></a>Associação SelectedItem

Muitas vezes, você desejará associar ao item selecionado de um `ListView`, em vez de usar um manipulador de eventos para responder às alterações. Para fazer isso em XAML, associe a propriedade `SelectedItem`:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Supondo que `listView`de `ItemsSource` é uma lista de cadeias de caracteres, `SomeLabel` terá sua propriedade `Text` associada à `SelectedItem`.

## <a name="related-links"></a>Links relacionados

- [Associação de duas vias (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
