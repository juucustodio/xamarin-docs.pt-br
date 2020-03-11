---
title: Resumo do capítulo 19. Exibições de coleção
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 19. Exibições de coleção'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032861"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumo do capítulo 19. Exibições de coleção

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Xamarin. Forms define três modos de exibição que mantêm as coleções e exibem seus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker) é uma lista relativamente curta de itens de cadeia de caracteres que permite ao usuário escolher um
- a [`ListView`](xref:Xamarin.Forms.ListView) geralmente é uma longa lista de itens geralmente do mesmo tipo e formatação, permitindo também que o usuário escolha um
- [`TableView`](xref:Xamarin.Forms.TableView) é uma coleção de *células* (geralmente de vários tipos e aparências) para exibir dados ou gerenciar a entrada do usuário

É comum que os aplicativos MVVM usem o `ListView` para exibir uma coleção selecionável de objetos.

## <a name="program-options-with-picker"></a>Opções do programa com o seletor

A [`Picker`](xref:Xamarin.Forms.Picker) é uma boa opção quando você precisa permitir que o usuário escolha uma opção entre uma lista relativamente curta de itens de `string`.

### <a name="the-picker-and-event-handling"></a>O seletor e a manipulação de eventos

O exemplo [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) demonstra como usar o XAML para definir a propriedade `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) e adicionar itens de `string` à coleção de [`Items`](xref:Xamarin.Forms.Picker.Items) . Quando o usuário seleciona o `Picker`, ele exibe os itens na coleção de `Items` de maneira dependente da plataforma.

O evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indica quando o usuário selecionou um item. A propriedade de [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) baseada em zero indica o item selecionado. Se nenhum item for selecionado, `SelectedIndex` será igual a &ndash;1.

Você também pode usar `SelectedIndex` para inicializar o item selecionado, mas ele deve ser definido depois que a coleção de `Items` é preenchida. Em XAML, isso significa que você provavelmente usará um elemento de propriedade para definir `SelectedIndex`.

### <a name="data-binding-the-picker"></a>O seletor de vinculação de dados

A propriedade `SelectedIndex` é apoiada por uma propriedade vinculável, mas `Items` não é, portanto, usar a associação de dados com um `Picker` é difícil. Uma solução é usar o `Picker` em combinação com um [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como o da biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . O [**pickerbinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) demonstra como isso funciona.

> [!NOTE] 
> O `Picker` Xamarin. Forms agora inclui `ItemsSource` e `SelectedItem` propriedades que dão suporte à vinculação de dados. Consulte o [seletor](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Dados de renderização com ListView

A [`ListView`](xref:Xamarin.Forms.ListView) é a única classe derivada de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) da qual ela herda as propriedades [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) e [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) .

`ItemsSource` é do tipo `IEnumerable`, mas é `null` por padrão e deve ser inicializada explicitamente ou (mais comumente) definida como uma coleção por meio de uma associação de dados. Os itens nesta coleção podem ser de qualquer tipo.

`ListView` define uma propriedade [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) que é definida como um dos itens na coleção `ItemsSource` ou `null` se nenhum item for selecionado. `ListView` aciona o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) quando um novo item é selecionado.

### <a name="collections-and-selections"></a>Coleções e as seleções

O exemplo de [**listviewlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) preenche um `ListView` com 17 `Color` valores em uma coleção de `List<Color>`. Os itens são selecionáveis, mas, por padrão, são exibidos com suas representações de `ToString` não atraentes. Vários exemplos neste capítulo mostram como corrigir que exibem e torná-lo tão atraente conforme desejado.

### <a name="the-row-separator"></a>O separador de linha

No iOS e Android exibe, uma linha fina separa as linhas. Você pode controlar isso com as propriedades [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) e [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) . `SeparatorVisibility` propriedade é do tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility), uma enumeração com dois membros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), a configuração padrão
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>O item selecionado de vinculação de dados

A propriedade `SelectedItem` é apoiada por uma propriedade vinculável, portanto, ela pode ser a origem ou o destino de uma associação de dados. Seu `BindingMode` padrão é `OneWayToSource`, mas geralmente é o destino de uma ligação de dados bidirecional, especialmente em cenários MVVM. O exemplo [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) demonstra esse tipo de associação.

### <a name="the-observablecollection-difference"></a>A diferença de ObservableCollection

O exemplo [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) define a propriedade `ItemsSource` de um `ListView` para uma coleção de `List<DateTime>` e, em seguida, adiciona progressivamente um novo objeto `DateTime` à coleção a cada segundo usando um timer.

No entanto, o `ListView` não se atualiza automaticamente porque a coleção de `List<T>` não tem um mecanismo de notificação para indicar quando os itens são adicionados ou removidos da coleção.

Uma classe muito melhor a ser usada nesses cenários é [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) definida no namespace `System.Collections.ObjectModel`. Essa classe implementa a interface [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) e, consequentemente, dispara um evento [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) quando os itens são adicionados ou removidos da coleção, ou quando eles são substituídos ou movidos dentro da coleção. Quando o `ListView` detecta internamente que uma classe que implementa `INotifyCollectionChanged` foi definida como sua propriedade `ItemsSource`, ela anexa um manipulador ao evento de `CollectionChanged` e atualiza sua exibição quando a coleção é alterada.

O exemplo [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) demonstra o uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelos e células

Por padrão, um `ListView` exibe itens em sua coleção usando o método `ToString` de cada item. Uma abordagem melhor envolve a definição de um modelo para exibir os itens.

Para experimentar esse recurso, você pode usar a classe [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Essa classe define uma propriedade `All` estática do tipo `IList<NamedColor>` que contém 141 objetos `NamedColor` correspondentes aos campos públicos da estrutura de `Color`.

O exemplo [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) define a `ItemsSource` de um `ListView` para essa propriedade `NamedColor.All`, mas somente os nomes de classe totalmente qualificados dos objetos `NamedColor` são exibidos.

`ListView` precisa de um modelo para exibir esses itens. No código, você pode definir a propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) definida por `ItemsView<TVisual>` para um objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usando o [Construtor`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que faz referência a uma derivada da classe [`Cell`](xref:Xamarin.Forms.Cell) . `Cell` tem cinco derivativos:

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contém duas exibições de `Label` (em termos conceituais)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; adiciona uma exibição de `Image` para `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contém uma exibição de `Entry` com um `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contém um `Switch` com um `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; pode ser qualquer `View` (provavelmente com filhos)

Em seguida, chame [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) e [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) no objeto `DataTemplate` para associar valores às propriedades `Cell` ou para definir associações de dados nas propriedades `Cell` referenciando as propriedades dos itens na coleção de `ItemsSource`. Isso é demonstrado no exemplo de [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

Como cada item é exibido pelo `ListView`, uma pequena árvore visual é construída a partir do modelo, e as associações de dados são estabelecidas entre o item e as propriedades dos elementos nesta árvore visual. Você pode ter uma ideia desse processo instalando manipuladores para os eventos [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) e [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) do `ListView`, ou usando um [Construtor de`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo que usa uma função que é chamada cada vez que a árvore visual de um item deve ser criada.

O [**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra um programa funcionalmente idêntico inteiramente em XAML. Uma marca de `DataTemplate` é definida como a propriedade `ItemTemplate` da `ListView`e, em seguida, a `TextCell` é definida como a `DataTemplate`. Associações a propriedades dos itens na coleção são definidas diretamente nas propriedades [`Text`](xref:Xamarin.Forms.TextCell.Text) e [`Detail`](xref:Xamarin.Forms.TextCell.Detail) da `TextCell`.

### <a name="custom-cells"></a>Células personalizadas

Em XAML, é possível definir um [`ViewCell`](xref:Xamarin.Forms.ViewCell) para o `DataTemplate` e, em seguida, definir uma árvore visual personalizada como a propriedade [`View`](xref:Xamarin.Forms.ViewCell.View) de `ViewCell`. (`View` é a propriedade Content de `ViewCell` para que as marcas `ViewCell.View` não sejam necessárias.) O exemplo [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) demonstra essa técnica:

[![Captura de tela tripla da lista de cores nomeadas personalizadas](images/ch19fg11-small.png "Lista de cores nomeadas personalizadas")](images/ch19fg11-large.png#lightbox "Lista de cores nomeadas personalizadas")

Obter o dimensionamento ideal para todas as plataformas pode ser complicado. A propriedade [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) é útil, mas em alguns casos você desejará recorrer à propriedade [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) , o que é menos eficiente, mas força o `ListView` a dimensionar as linhas. Para iOS e Android, você deve usar uma dessas duas propriedades para obter o dimensionamento apropriado de linha.

### <a name="grouping-the-listview-items"></a>Agrupar os itens de ListView

`ListView` dá suporte ao agrupamento de itens e à navegação entre esses grupos. A propriedade `ItemsSource` deve ser definida como uma coleção de coleções: o objeto que `ItemsSource` está definido deve implementar `IEnumerable`, e cada item na coleção também deve implementar `IEnumerable`. Cada grupo deve incluir duas propriedades: uma descrição de texto do grupo e uma abreviação de três letras.

A classe [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) cria sete grupos de objetos `NamedColor`. O exemplo [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) mostra como usar esses grupos com a propriedade [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) de `ListView` definida como `true`e as propriedades [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) e [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) associadas a propriedades em cada grupo.

### <a name="custom-group-headers"></a>Cabeçalhos de grupo personalizado

É possível criar cabeçalhos personalizados para os grupos de `ListView` substituindo a propriedade `GroupDisplayBinding` pela [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) definindo um modelo para os cabeçalhos.

### <a name="listview-and-interactivity"></a>ListView e interatividade

Geralmente, um aplicativo obtém a interação do usuário com um `ListView` anexando um manipulador ao evento `ItemSelected` ou [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) ou definindo uma associação de dados na propriedade `SelectedItem`. Mas alguns tipos de célula (`EntryCell` e `SwitchCell`) permitem a interação do usuário e também é possível criar células personalizadas que interagem com o usuário. O [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) cria 100 instâncias de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e permite que o usuário altere cada cor usando um trio de elementos `Slider`. O programa também usa o [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) no [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e o MVVM

`ListView` desempenha uma grande função em cenários MVVM. Sempre que uma coleção de `IEnumerable` existe em um ViewModel, ela geralmente é associada a um `ListView`. Além disso, os itens na coleção geralmente implementam `INotifyPropertyChanged` para associar a propriedades em um modelo.

### <a name="a-collection-of-viewmodels"></a>Uma coleção de ViewModels

Para explorar isso, a biblioteca [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) cria várias classes com base em um [arquivo de dados XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imagens de alunos fictícios nesta escola fictícia.

A classe [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) deriva de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). A classe [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) é uma coleção de objetos `Student` e também deriva de `ViewModelBase`. O [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) baixa o arquivo XML e monta todos os objetos.

O programa [**studentlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa uma `ImageCell` para exibir os alunos e suas imagens em um `ListView`:

[![Captura de tela tripla da lista de alunos](images/ch19fg18-small.png "Lista de alunos")](images/ch19fg18-large.png#lightbox "Lista de alunos")

O exemplo [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) adiciona uma propriedade [`Header`](xref:Xamarin.Forms.ListView.Header) , mas só aparece no Android.

### <a name="selection-and-the-binding-context"></a>Seleção e o contexto de associação

O programa [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) associa a `BindingContext` de um `StackLayout` à propriedade `SelectedItem` do `ListView`. Isso permite que o programa para exibir informações detalhadas sobre o aluno selecionado.

### <a name="context-menus"></a>Menus de contexto

Uma célula pode definir um menu de contexto que é implementado de maneira específica da plataforma. Para criar esse menu, adicione [`MenuItem`](xref:Xamarin.Forms.MenuItem) objetos à propriedade [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) da `Cell`.

`MenuItem` define cinco propriedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) do tipo `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) do tipo `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) do tipo `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) do tipo `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) do tipo `object`

As propriedades `Command` e `CommandParameter` sugerem que o ViewModel de cada item contém métodos para executar os comandos de menu desejados. Em cenários não MVVM, `MenuItem` também define um evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) .

O [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demonstra essa técnica. A propriedade `Command` de cada `MenuItem` está associada a uma propriedade do tipo `ICommand` na classe `Student`. Defina a propriedade `IsDestructive` como `true` para uma `MenuItem` que remove ou exclui o objeto selecionado.

### <a name="varying-the-visuals"></a>Variando os elementos visuais

Às vezes, você desejará pequenas variações nos elementos visuais dos itens na `ListView` com base em uma propriedade. Por exemplo, quando a média de pontos de nível de um aluno cai abaixo de 2,0, o exemplo [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) exibe o nome desse aluno em vermelho.
Isso é feito por meio do uso de um conversor de valor de associação, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Atualizando o conteúdo

O `ListView` dá suporte a um gesto de pull para a atualização de seus dados. O programa deve definir a propriedade [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) como `true` para habilitá-la. O `ListView` responde ao gesto suspenso definindo sua propriedade [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) como `true`e gerando o evento de [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) e (para cenários MVVM) chamando o método `Execute` de sua propriedade [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) .

O código que manipula o evento `Refresh` ou o `RefreshCommand`, possivelmente, atualiza os dados exibidos pelo `ListView` e define `IsRefreshing` de volta para `false`.

O exemplo [**RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) demonstra como usar um [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` e `IsRefreshing` Propriedades para associação de dados.

## <a name="the-tableview-and-its-intents"></a>O modo de tabela e suas intenções

Embora o `ListView` geralmente exiba várias instâncias do mesmo tipo, o [`TableView`](xref:Xamarin.Forms.TableView) geralmente se concentra em fornecer uma interface do usuário para várias propriedades de vários tipos. Cada item é associado a seu próprio [`Cell`](xref:Xamarin.Forms.Cell) derivativo para exibir a propriedade ou fornecer uma interface do usuário a ela.

### <a name="properties-and-hierarchies"></a>Propriedades e as hierarquias

`TableView` define apenas quatro propriedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) do tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent), uma enumeração
- [`Root`](xref:Xamarin.Forms.TableView.Root) do tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot), a propriedade de conteúdo de `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) do tipo `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) do tipo `bool`

A enumeração `TableIntent` indica como você pretende usar o `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Esses membros também sugerem alguns usos para o `TableView`.

Várias outras classes envolvidas na definição de uma tabela:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) é uma classe abstrata que deriva de `BindableObject` e define uma propriedade [`Title`](xref:Xamarin.Forms.TableSectionBase.Title)

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) é uma classe abstrata que deriva de `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) deriva de `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) deriva de `TableSectionBase<TableSection>`

Em suma, `TableView` tem uma propriedade `Root` que você define como um objeto `TableRoot`, que é uma coleção de objetos `TableSection`, cada um deles é uma coleção de objetos de `Cell`. Uma tabela com várias seções e cada seção tem várias células. A própria tabela pode ter um título, e cada seção pode ter um título. Embora `TableView` faça uso de derivações de `Cell`, ele não faz uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Um formulário prosaico

O exemplo [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) define um modelo de exibição de [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) , uma instância do que se torna o `BindingContext` do `TableView`. Cada `Cell` derivativo em seu `TableSection` pode então ter associações a propriedades da classe `PersonalInformation`.

### <a name="custom-cells"></a>Células personalizadas

O exemplo [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) expande em **EntryForm**. A classe [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) inclui uma propriedade booliana que governa a aplicabilidade de duas propriedades adicionais. Para essas duas propriedades adicionais, o programa usa um `PickerCell` personalizado com base em um [PickerCell. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Embora as propriedades `IsEnabled` dos dois elementos `PickerCell` estejam associadas à propriedade booliana em `ProgrammerInformation`, essa técnica não parece funcionar, o que solicita o próximo exemplo.

### <a name="conditional-sections"></a>Seções condicionais

O exemplo [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) coloca os dois itens condicionais na seleção do item booliano em um `TableSection`separado. O arquivo code-behind remove esta seção da `TableView` ou a adiciona de volta com base na propriedade booliana.

### <a name="a-tableview-menu"></a>Um menu do modo de tabela

Outro uso de um `TableView` é um menu. O exemplo de [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) demonstra um menu que permite que você mova um pouco `BoxView` em toda a tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 19 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Capítulo 19 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Seletor](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
