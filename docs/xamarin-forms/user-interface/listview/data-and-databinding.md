---
title: Fontes de dados de ListView
description: Este artigo explica como popular o ListView de xamarin. Forms com os dados e como usar a vinculação de dados com um ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2018
ms.openlocfilehash: 06cf5d2a862a5db2f94fca655b69c45d16375854
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059146"
---
# <a name="listview-data-sources"></a>Fontes de dados de ListView

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)

Um [ `ListView` ](xref:Xamarin.Forms.ListView) é usado para exibir listas de dados. Aprenderemos sobre preenchendo um ListView com dados e como podemos fazer a ligação para o item selecionado.

- **[Definindo o ItemsSource](#ItemsSource)**  &ndash; usa uma lista simples ou uma matriz.
- **[Associação de dados](#Data_Binding)**  &ndash; estabelece uma relação entre um modelo e o ListView. A associação é ideal para o padrão MVVM.

## <a name="itemssource"></a>ItemsSource

Um [ `ListView` ](xref:Xamarin.Forms.ListView) é preenchida com dados usando o [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade, que pode aceitar qualquer coleção que implementa `IEnumerable`. A maneira mais simples para preencher um `ListView` envolve o uso de uma matriz de cadeias de caracteres:

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

O código c# equivalente é:

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

//monochrome will not appear in the list because it was added
//after the list was populated.
listView.ItemsSource.Add("monochrome");
```

![](data-and-databinding-images/itemssource-simple.png "ListView exibindo lista de cadeias de caracteres")

A abordagem acima preencherá o `ListView` com uma lista de cadeias de caracteres. Por padrão, `ListView` chamará `ToString` e exibir o resultado em um `TextCell` para cada linha. Para personalizar como os dados são exibidos, consulte [aparência da célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Porque `ItemsSource` foi enviada para uma matriz, não será atualizado com as alterações de matriz ou lista subjacentes. Se você quiser que o ListView para atualizar automaticamente à medida que itens são adicionados, removidos ou alterados na lista subjacente, você precisará usar um `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) é definido em `System.Collections.ObjectModel` e é exatamente como qualquer `List`, exceto que ele pode notificar `ListView` de todas as alterações:

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados
Associação de dados é a "cola" que associa as propriedades de um objeto de interface do usuário para as propriedades de um objeto do CLR, como uma classe em seu ViewModel. Associação de dados é útil porque ele simplifica o desenvolvimento de interfaces do usuário, substituindo muito código clichê que sem graça.

Associação de dados funciona mantendo os objetos sincronizados como alteram seus valores associados. Em vez de precisar escrever manipuladores de eventos para sempre que um valor de controle é alterada, você estabelecer a associação e habilitar a associação em seu ViewModel.

Para obter mais informações sobre associação de dados, consulte [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) que é a parte quatro da [série de artigos de Noções básicas de XAML do xamarin. Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Células de associação
Propriedades de células (e seus filhos de células) podem ser associadas às propriedades de objetos no `ItemsSource`. Por exemplo, um ListView pode ser usado para apresentar uma lista de funcionários.

A classe do funcionário:

```csharp
public class Employee{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>` é criado e definido como o `ListView`do `ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

A lista é preenchida com dados:

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

O trecho a seguir demonstra um `ListView` associado a uma lista de funcionários:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
Title="Employee List">
  <ListView x:Name="EmployeeView">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Observe que a associação foi configurada no código para manter a simplicidade, embora ele foi vinculado em XAML.

O bit anterior de XAML define uma `ContentPage` que contém um `ListView`. Fonte de dados das `ListView` é definido por meio de `ItemsSource` atributo. O layout de cada linha na `ItemsSource`é definido dentro de `ListView.ItemTemplate` elemento.

Esse é o resultado:

![](data-and-databinding-images/bound-data.png "Usando a associação de dados de ListView")

### <a name="binding-selecteditem"></a>Associação SelectedItem

Muitas vezes você desejará ligar para o item selecionado de um `ListView`, em vez de usar um manipulador de eventos para responder a alterações. Para fazer isso no XAML, associar a `SelectedItem` propriedade:

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

Supondo `listView`do `ItemsSource` é uma lista de cadeias de caracteres `SomeLabel` terão sua propriedade de texto associada ao `SelectedItem`.

## <a name="related-links"></a>Links relacionados

- [Associação bidirecional (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
