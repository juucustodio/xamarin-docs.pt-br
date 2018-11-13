---
title: Resumo do capítulo 19. Exibições de coleção
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 19. Exibições de coleção'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: charlespetzold
ms.author: chape
ms.date: 07/18/2018
ms.openlocfilehash: 84d21738298360eb8829a569b152a131e6543709
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526774"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumo do capítulo 19. Exibições de coleção

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Xamarin. Forms define três modos de exibição que mantêm as coleções e exibem seus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker) é uma lista relativamente curta de itens de cadeia de caracteres que permite ao usuário escolher um
- [`ListView`](xref:Xamarin.Forms.ListView) geralmente é uma longa lista de itens normalmente do mesmo tipo e formatação, também permitindo que o usuário escolha uma
- [`TableView`](xref:Xamarin.Forms.TableView) é uma coleção de *células* (geralmente de vários tipos e aparências) para exibir dados ou gerenciar a entrada do usuário

É comum que aplicativos MVVM para usar o `ListView` para exibir uma coleção selecionável de objetos.

## <a name="program-options-with-picker"></a>Opções do programa com o seletor

O [ `Picker` ](xref:Xamarin.Forms.Picker) é uma boa opção quando você precisa permitir que o usuário escolha uma opção dentre uma lista relativamente curta de `string` itens.

### <a name="the-picker-and-event-handling"></a>O seletor e a manipulação de eventos

O [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) exemplo demonstra como usar XAML para definir o `Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title) propriedade e adicione `string` itens para os [ `Items` ](xref:Xamarin.Forms.Picker.Items) coleção. Quando o usuário seleciona o `Picker`, ele exibe os itens a `Items` coleção de forma dependente de plataforma.

O [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento indica quando o usuário selecionou um item. O baseado em zero [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriedade indica, em seguida, o item selecionado. Se nenhum item for selecionado, `SelectedIndex` é igual a &ndash;1.

Você também pode usar `SelectedIndex` inicializar o item selecionado, mas ele deve ser definido após o `Items` coleção é preenchida. No XAML, isso significa que você provavelmente usará um elemento de propriedade para definir `SelectedIndex`.

### <a name="data-binding-the-picker"></a>O seletor de vinculação de dados

O `SelectedIndex` propriedade é feita por uma propriedade vinculável, mas `Items` for não, isso usando vinculação de dados com um `Picker` é difícil. Uma solução é usar o `Picker` em combinação com um [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. O [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) demonstra como isso funciona.

> [!NOTE] 
> O xamarin. Forms `Picker` agora inclui `ItemsSource` e `SelectedItem` propriedades que dão suporte à vinculação de dados. Ver [seletor](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Dados de renderização com ListView

O [ `ListView` ](xref:Xamarin.Forms.ListView) é a única classe que deriva de [ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1) da qual ela herda o [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) e [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedades.

`ItemsSource` é do tipo `IEnumerable` mas é `null` por padrão e deve ser explicitamente inicializados ou (mais comumente) definido como uma coleção por meio de uma associação de dados. Os itens nesta coleção podem ser de qualquer tipo.

`ListView` define uma [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade que é definido como um dos itens na `ItemsSource` coleção ou `null` se nenhum item está selecionado. `ListView` é acionado o [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento quando um novo item é selecionado.

### <a name="collections-and-selections"></a>Coleções e as seleções

O [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) preenchimentos de exemplo uma `ListView` com 17 `Color` os valores em um `List<Color>` coleção. Os itens são selecionáveis, mas por padrão, eles são exibidos com seus pouco atraente `ToString` representações. Vários exemplos neste capítulo mostram como corrigir que exibem e torná-lo tão atraente conforme desejado.

### <a name="the-row-separator"></a>O separador de linha

No iOS e Android exibe, uma linha fina separa as linhas. Você pode controlar isso com o [ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility) e [ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor) propriedades. `SeparatorVisibility` propriedade é do tipo [ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility), uma enumeração com dois membros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), a configuração padrão
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>O item selecionado de vinculação de dados

O `SelectedItem` propriedade é feita por uma propriedade vinculável, portanto, pode ser a origem ou destino de uma associação de dados. O padrão `BindingMode` é `OneWayToSource`, mas geralmente é o destino de uma associação de dados bidirecional, especialmente em cenários do MVVM. O [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) que demonstra esse tipo de associação.

### <a name="the-observablecollection-difference"></a>A diferença de ObservableCollection

O [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) conjuntos de exemplo o `ItemsSource` propriedade de um `ListView` para um `List<DateTime>` coleção e, em seguida, progressivamente adiciona um novo `DateTime` objeto à coleção cada segundo usando um temporizador.

No entanto, o `ListView` não é atualizado automaticamente em si porque o `List<T>` coleção não tem um mecanismo de notificação para indicar quando itens são adicionados ou removidos da coleção.

É uma classe muito melhor para usar em cenários assim [ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1) definidos no `System.Collections.ObjectModel` namespace. Essa classe implementa a [ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged) interface e, consequentemente, é acionado uma [ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) evento quando itens são adicionados ou removidos da coleção, ou quando são substituídos ou movidos dentro do a coleção. Quando o `ListView` internamente detecta que uma classe que implementa `INotifyCollectionChanged` foi definida como sua `ItemsSource` propriedade, ele anexa um manipulador para o `CollectionChanged` eventos e atualiza sua exibição quando a coleção é alterada.

O [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) exemplo demonstra o uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelos e células

Por padrão, uma `ListView` exibe os itens em sua coleção, usando cada item `ToString` método. Uma abordagem melhor envolve a definição de um modelo para exibir os itens.

Para fazer experiências com esse recurso, você pode usar o [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. Essa classe define um estático `All` propriedade do tipo `IList<NamedColor>` que contém 141 `NamedColor` objetos correspondentes aos campos públicos do `Color` estrutura.

O [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) conjuntos de exemplo o `ItemsSource` de um `ListView` a este `NamedColor.All` propriedade, mas somente os nomes de classe totalmente qualificado do `NamedColor` são objetos exibido.

`ListView` precisa de um modelo para exibir esses itens. No código, você pode definir as [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade definida pelo `ItemsView<TVisual>` para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) objeto usando o [ `DataTemplate` construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que faz referência a um derivativo do [ `Cell` ](xref:Xamarin.Forms.Cell) classe. `Cell` tem cinco derivados:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contém dois `Label` exibições (conceitualmente)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; Adiciona um `Image` exibir para `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contém um `Entry` exibir com um `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contém um `Switch` com um `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; pode ser qualquer `View` (provavelmente com filhos)

Em seguida, chame [ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) e [ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) no `DataTemplate` objeto para associar valores com o `Cell` propriedades, ou para definir associações de dados no `Cell` propriedades referenciando propriedades dos itens no `ItemsSource` coleção. Isso é demonstrado na [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) exemplo.

Como cada item é exibido pelo `ListView`, uma pequena árvore visual é construída a partir do modelo e associações de dados são estabelecidas entre o item e as propriedades dos elementos nesta árvore visual. Você pode ter uma ideia desse processo, instalando manipuladores para o [ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing) e [ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing) eventos do `ListView`, ou usando uma alternativa [ `DataTemplate`construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) que usa uma função que é chamada sempre que a árvore visual de um item deve ser criado.

O [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra um programa funcionalmente idêntico inteiramente em XAML. Um `DataTemplate` marca é definida como o `ItemTemplate` propriedade da `ListView`e, em seguida, o `TextCell` é definido como o `DataTemplate`. Associações às propriedades dos itens na coleção são definidas diretamente na [ `Text` ](xref:Xamarin.Forms.TextCell.Text) e [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propriedades do `TextCell`.

### <a name="custom-cells"></a>Células personalizadas

No XAML, é possível definir uma [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) para o `DataTemplate` e, em seguida, definir uma árvore visual personalizada como o [ `View` ](xref:Xamarin.Forms.ViewCell.View) propriedade do `ViewCell`. (`View` é a propriedade content de `ViewCell` para que o `ViewCell.View` marcas não são necessárias.) O [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) que demonstra essa técnica:

[![Tripla captura de tela da lista de cores do personalizado chamado](images/ch19fg11-small.png "personalizada denominada lista de cores")](images/ch19fg11-large.png#lightbox "personalizada denominada lista de cores")

Obter o dimensionamento ideal para todas as plataformas pode ser complicado. O [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight) propriedade é útil, mas em alguns casos você desejará recorrer ao [ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows) propriedade, que é menos eficiente, mas força o `ListView` para as linhas de tamanho. Para iOS e Android, você deve usar uma dessas duas propriedades para obter o dimensionamento apropriado de linha.

### <a name="grouping-the-listview-items"></a>Agrupar os itens de ListView

`ListView` dá suporte o agrupamento de itens e navegando entre esses grupos. O `ItemsSource` propriedade deve ser definida como uma coleção de coleções: O objeto que `ItemsSource` está definido para deve implementar `IEnumerable`, e cada item na coleção também deve implementar `IEnumerable`. Cada grupo deve incluir duas propriedades: uma descrição de texto do grupo e uma abreviação de três letras.

O [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca cria sete grupos de `NamedColor` objetos. O [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) exemplo mostra como usar esses grupos com o [ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled) propriedade de `ListView` definido como `true`e o [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) e [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) propriedades associadas a propriedades em cada grupo.

### <a name="custom-group-headers"></a>Cabeçalhos de grupo personalizado

É possível criar cabeçalhos personalizados para o `ListView` grupos, substituindo o `GroupDisplayBinding` propriedade com o [ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) definir um modelo para os cabeçalhos.

### <a name="listview-and-interactivity"></a>ListView e interatividade

Geralmente, um aplicativo obtiver a interação do usuário com um `ListView` anexando um manipulador para o `ItemSelected` ou [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento, ou definindo uma associação de dados no `SelectedItem` propriedade. Mas alguns tipos de célula (`EntryCell` e `SwitchCell`) permitem a interação do usuário, e também é possível criar células personalizadas que também interagir com o usuário. O [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) cria 100 instâncias [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e permite que o usuário altere cada cor usando um trio de `Slider` elementos. O programa também usa o [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) no [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e o MVVM

`ListView` desempenha um papel importante em cenários do MVVM. Sempre que um `IEnumerable` coleção existe em um ViewModel, geralmente está associado a um `ListView`. Além disso, os itens na coleção que normalmente implementam `INotifyPropertyChanged` para associar com propriedades em um modelo.

### <a name="a-collection-of-viewmodels"></a>Uma coleção de ViewModels

Para explorar isso, o [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca cria várias classes com base em um [arquivo de dados XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imagens de alunos fictícias esse School fictícia.

O [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe deriva [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). O [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe é uma coleção de `Student` objetos e também deriva de `ViewModelBase`. O [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) baixa o arquivo XML e reúne todos os objetos.

O [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programa usa uma `ImageCell` para exibir os alunos e suas imagens em um `ListView`:

[![Tripla captura de tela da lista de aluno](images/ch19fg18-small.png "lista de alunos")](images/ch19fg18-large.png#lightbox "lista de alunos")

O [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) exemplo adiciona um [ `Header` ](xref:Xamarin.Forms.ListView.Header) propriedade, mas ele é mostrado apenas no Android.

### <a name="selection-and-the-binding-context"></a>Seleção e o contexto de associação

O [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programa associa o `BindingContext` de um `StackLayout` para o `SelectedItem` propriedade do `ListView`. Isso permite que o programa para exibir informações detalhadas sobre o aluno selecionado.

### <a name="context-menus"></a>Menus de contexto

Uma célula pode definir um menu de contexto que é implementado de maneira específica da plataforma. Para criar esse menu, adicione [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) objetos para o [ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions) propriedade do `Cell`.

`MenuItem` define cinco propriedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) do tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) do tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) do tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) do tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) do tipo `object`

O `Command` e `CommandParameter` propriedades implicam que o ViewModel para cada item contém métodos para executar os comandos de menu desejado. Em cenários de não-MVVM `MenuItem` também define uma [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) eventos.

O [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demonstra essa técnica. O `Command` propriedade de cada `MenuItem` está associado a uma propriedade do tipo `ICommand` no `Student` classe. Defina as `IsDestructive` propriedade para `true` para um `MenuItem` que remove ou exclui o objeto selecionado.

### <a name="varying-the-visuals"></a>Variando os elementos visuais

Às vezes, você desejará pequenas variações nos visuais dos itens no `ListView` com base em uma propriedade. Por exemplo, quando a média de ponto de nível empresarial de um aluno fica abaixo de 2.0, o [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) exemplo exibe o nome do aluno em vermelho.
Isso é feito por meio do uso de um conversor de valor de associação [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="refreshing-the-content"></a>Atualizando o conteúdo

O `ListView` dá suporte a um gesto suspenso para atualizar seus dados. O programa deve definir a [ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) propriedade `true` para habilitar isso. O `ListView` responde ao gesto suspenso definindo seu [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) propriedade a ser `true`e gerando o [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) eventos e (para cenários MVVM) chamando o `Execute` método de seu [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) propriedade.

Tratamento de código a `Refresh` evento ou o `RefreshCommand` , em seguida, atualiza, possivelmente, os dados exibidos pelo `ListView` e define `IsRefreshing` para `false`.

O [ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) exemplo demonstra como usar um [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` e `IsRefreshing` propriedades de associação de dados.

## <a name="the-tableview-and-its-intents"></a>O modo de tabela e suas intenções

Enquanto o `ListView` geralmente exibe várias instâncias do mesmo tipo, o [ `TableView` ](xref:Xamarin.Forms.TableView) geralmente se concentra em fornecer uma interface do usuário para várias propriedades de vários tipos. Cada item está associado com seu próprio [ `Cell` ](xref:Xamarin.Forms.Cell) derivados exibindo a propriedade ou fornece uma interface do usuário a ele.

### <a name="properties-and-hierarchies"></a>Propriedades e as hierarquias

`TableView` define apenas quatro propriedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) do tipo [ `TableIntent` ](xref:Xamarin.Forms.TableIntent), uma enumeração
- [`Root`](xref:Xamarin.Forms.TableView.Root) do tipo [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), a propriedade de conteúdo de `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) do tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) do tipo `bool`

O `TableIntent` enumeração indica como você pretende usar o `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Esses membros também sugerem alguns usos para o `TableView`.

Várias outras classes envolvidas na definição de uma tabela:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) é uma classe abstrata que deriva `BindableObject` e define uma [ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title) propriedade

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) é uma classe abstrata que deriva `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva de `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva de `TableSectionBase<TableSection>`

Em resumo, `TableView` tem uma `Root` propriedade definida como um `TableRoot` objeto, que é uma coleção de `TableSection` objetos, cada um deles é uma coleção de `Cell` objetos. Uma tabela com várias seções e cada seção tem várias células. A própria tabela pode ter um título, e cada seção pode ter um título. Embora `TableView` utiliza `Cell` derivados, ele não faz uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Um formulário prosaico

O [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) exemplo define uma [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modelo de exibição, torna-se uma instância da qual o `BindingContext` do `TableView`. Cada `Cell` derivados no seu `TableSection` , em seguida, pode ter associações a propriedades do `PersonalInformation` classe.

### <a name="custom-cells"></a>Células personalizadas

O [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) exemplo expande **EntryForm**. O [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe inclui uma propriedade booleana que rege a aplicabilidade das duas propriedades adicionais. Para essas duas propriedades adicionais, o programa usa um personalizado `PickerCell` com base em um [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) no [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

Embora o `IsEnabled` as propriedades dos dois `PickerCell` elementos associados à propriedade Boolean no `ProgrammerInformation`, essa técnica não parece funcionar, que solicita o próximo exemplo.

### <a name="conditional-sections"></a>Seções condicionais

O [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) exemplo coloca os dois itens que são condicionais na seleção do item booliano em um separado `TableSection`. O arquivo code-behind remove essa seção do `TableView` ou adiciona de volta com base na propriedade Boolean.

### <a name="a-tableview-menu"></a>Um menu do modo de tabela

Outro uso de um `TableView` é um menu. O [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) exemplo demonstra um menu que permite que você mova um pouco `BoxView` pela tela.



## <a name="related-links"></a>Links relacionados

- [Capítulo 19 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Exemplos do capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Seletor](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
