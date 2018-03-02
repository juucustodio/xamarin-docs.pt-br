---
title: Fontes de dados de ListView
description: Saiba como preencher sua ListView com dados.
ms.topic: article
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 78659aff0b6b4e6401bec96a55935c141d1199f1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="listview-data-sources"></a>Fontes de dados de ListView

ListView é usado para exibir listas de dados. Aprenderemos sobre preenchendo uma ListView com dados e como podemos associar ao item selecionado.

- **[Definir ItemsSource](#ItemsSource)**  &ndash; usa uma lista simples ou uma matriz.
- **[Associação de dados](#Data_Binding)**  &ndash; estabelece uma relação entre um modelo e a ListView. A associação é ideal para o padrão MVVM.

## <a name="itemssource"></a>ItemsSource
ListView é preenchido com dados usando o `ItemsSource` propriedade, que pode aceitar qualquer implementação de coleção `IEnumerable`. A maneira mais simples para preencher um `ListView` envolve o uso de uma matriz de cadeias de caracteres:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]{
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

A abordagem acima preencherá o `ListView` com uma lista de cadeias de caracteres. Por padrão, `ListView` chamará `ToString` e exibir o resultado em um `TextCell` para cada linha. Para personalizar como os dados são exibidos, consulte [aparência célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Porque `ItemsSource` foi enviada para uma matriz, não será atualizado com as alterações de lista ou matriz subjacentes. Se você quiser ListView para atualizar automaticamente conforme os itens são adicionados, removidos ou alterados na lista subjacente, você precisará usar um `ObservableCollection`. [`ObservableCollection`](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) é definido em `System.Collections.ObjectModel` e é como `List`, exceto que ele pode notificar `ListView` das alterações:

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados
Associação de dados é a "cola" que associa as propriedades de um objeto de interface de usuário para as propriedades de um objeto CLR, como uma classe em seu ViewModel. Associação de dados é útil porque ele simplifica o desenvolvimento de interfaces do usuário, substituindo muitos códigos de boilerplate entediante.

Associação de dados funciona, mantendo os objetos sincronizados como alteram seus valores associados. Em vez de precisar escrever manipuladores de eventos sempre que o valor do controle é alterado, você estabelecer a associação e habilite a associação no seu ViewModel.

Para obter mais informações sobre associação de dados, consulte [Noções básicas de associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) que faz parte quatro a [xamarin. Forms Noções básicas de XAML artigo série](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Células de associação
Propriedades de células (e seus filhos de células) podem ser associadas às propriedades de objetos no `ItemsSource`. Por exemplo, uma ListView pode ser usada para apresentar uma lista de funcionários com imagens.

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

Observe que a associação foi configurada no código para manter a simplicidade, embora ela foi associada em XAML.

Define o bit anterior de XAML um `ContentPage` que contém um `ListView`. A fonte de dados do `ListView` é definido por meio de `ItemsSource` atributo. O layout de cada linha no `ItemsSource`é definido dentro do `ListView.ItemTemplate` elemento.

Este é o resultado:

![](data-and-databinding-images/bound-data.png "ListView usando associação de dados")

### <a name="binding-selecteditem"></a>Associação SelectedItem

Geralmente você desejará ligar para o item selecionado de um `ListView`, em vez de usar um manipulador de eventos para responder a alterações. Para fazer isso em XAML, associar a `SelectedItem` propriedade:

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

Supondo que `listView`do `ItemsSource` é uma lista de cadeias de caracteres, `SomeLabel` terão sua propriedade de texto associada para o `SelectedItem`.



## <a name="related-links"></a>Links relacionados

- [Associação bidirecional (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [Notas de versão 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Notas de versão 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
