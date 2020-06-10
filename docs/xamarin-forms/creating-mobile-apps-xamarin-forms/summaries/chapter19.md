---
Título: "Resumo do capítulo 19. Exibições de coleção "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 19. Exibições de coleção "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: 0AEC3A5C-586E-4D0F-9895-67E99A053A79 autor: davidbritch MS. Author: dabritch MS. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-19-collection-views"></a>Resumo do capítulo 19. Exibições de coleção

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Xamarin.Formsdefine três exibições que mantêm coleções e exibem seus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker)é uma lista relativamente curta de itens de cadeia de caracteres que permite ao usuário escolher um
- [`ListView`](xref:Xamarin.Forms.ListView)geralmente é uma longa lista de itens geralmente do mesmo tipo e formatação, permitindo também que o usuário escolha um
- [`TableView`](xref:Xamarin.Forms.TableView)é uma coleção de *células* (geralmente de vários tipos e aparências) para exibir dados ou gerenciar a entrada do usuário

É comum que os aplicativos MVVM usem o `ListView` para exibir uma coleção selecionável de objetos.

## <a name="program-options-with-picker"></a>Opções do programa com o seletor

A [`Picker`](xref:Xamarin.Forms.Picker) é uma boa opção quando você precisa permitir que o usuário escolha uma opção entre uma lista de itens relativamente curta `string` .

### <a name="the-picker-and-event-handling"></a>O seletor e a manipulação de eventos

O exemplo [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) demonstra como usar o XAML para definir a `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) propriedade e adicionar `string` itens à [`Items`](xref:Xamarin.Forms.Picker.Items) coleção. Quando o usuário seleciona o `Picker` , ele exibe os itens na `Items` coleção de maneira dependente da plataforma.

O [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento indica quando o usuário selecionou um item. A propriedade de base zero [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) indica o item selecionado. Se nenhum item for selecionado, será `SelectedIndex` igual a &ndash; 1.

Você também pode usar `SelectedIndex` para inicializar o item selecionado, mas ele deve ser definido depois que a `Items` coleção é preenchida. Em XAML, isso significa que você provavelmente usará um elemento Property para definir `SelectedIndex` .

### <a name="data-binding-the-picker"></a>Vinculação de dados ao seletor

A `SelectedIndex` propriedade é apoiada por uma propriedade vinculável, mas `Items` não é, portanto, usar a associação de dados com um `Picker` é difícil. Uma solução é usar o `Picker` em combinação com um como [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) o da biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . O [**pickerbinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) demonstra como isso funciona.

> [!NOTE] 
> O Xamarin.Forms `Picker` agora inclui `ItemsSource` `SelectedItem` as propriedades e que dão suporte à vinculação de dados. Consulte o [seletor](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Renderizando dados com ListView

A [`ListView`](xref:Xamarin.Forms.ListView) é a única classe que deriva de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) onde ela herda as [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) Propriedades e.

`ItemsSource`é do tipo `IEnumerable` , mas é `null` por padrão e deve ser inicializado explicitamente ou (mais comumente) definido como uma coleção por meio de uma associação de dados. Os itens nesta coleção podem ser de qualquer tipo.

`ListView`define uma [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriedade que é definida como um dos itens na `ItemsSource` coleção ou `null` se nenhum item está selecionado. `ListView`dispara o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento quando um novo item é selecionado.

### <a name="collections-and-selections"></a>Coleções e seleções

O exemplo de [**listviewlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) preenche um `ListView` com 17 `Color` valores em uma `List<Color>` coleção. Os itens são selecionáveis, mas, por padrão, são exibidos com suas representações não atraentes `ToString` . Vários exemplos neste capítulo mostram como corrigir essa exibição e torná-la tão atraente quanto desejado.

### <a name="the-row-separator"></a>O separador de linha

No iOS e no Android é exibido, uma linha fina separa as linhas. Você pode controlar isso com as [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) Propriedades e. `SeparatorVisibility`a propriedade é do tipo [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility) , uma enumeração com dois membros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), a configuração padrão
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Vinculação de dados do item selecionado

A `SelectedItem` propriedade é apoiada por uma propriedade vinculável, portanto, ela pode ser a origem ou o destino de uma associação de dados. Seu padrão `BindingMode` é `OneWayToSource` , mas geralmente é o destino de uma ligação de dados bidirecional, especialmente em cenários MVVM. O exemplo [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) demonstra esse tipo de associação.

### <a name="the-observablecollection-difference"></a>A diferença de ObservableCollection

O exemplo [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) define a `ItemsSource` propriedade de um `ListView` para uma `List<DateTime>` coleção e, em seguida, adiciona progressivamente um novo `DateTime` objeto à coleção a cada segundo usando um timer.

No entanto, o `ListView` não se atualiza automaticamente porque a `List<T>` coleção não tem um mecanismo de notificação para indicar quando os itens são adicionados ou removidos da coleção.

Uma classe muito melhor a ser usada nesses cenários é [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) definida no `System.Collections.ObjectModel` namespace. Essa classe implementa a [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) interface e, consequentemente [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) , dispara um evento quando os itens são adicionados ou removidos da coleção, ou quando eles são substituídos ou movidos dentro da coleção. Quando o `ListView` detecta internamente que uma classe implementada `INotifyCollectionChanged` foi definida como sua `ItemsSource` propriedade, ela anexa um manipulador ao `CollectionChanged` evento e atualiza sua exibição quando a coleção é alterada.

O exemplo [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) demonstra o uso de `ObservableCollection` .

### <a name="templates-and-cells"></a>Modelos e células

Por padrão, um `ListView` exibe itens em sua coleção usando o método de cada item `ToString` . Uma abordagem melhor envolve a definição de um modelo para exibir os itens.

Para experimentar esse recurso, você pode usar a [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Essa classe define uma `All` propriedade estática do tipo `IList<NamedColor>` que contém 141 `NamedColor` objetos correspondentes aos campos públicos da `Color` estrutura.

O exemplo [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) define o `ItemsSource` de a `ListView` para essa `NamedColor.All` propriedade, mas somente os nomes de classe totalmente qualificados dos `NamedColor` objetos são exibidos.

`ListView`precisa de um modelo para exibir esses itens. No código, você pode definir a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriedade definida por `ItemsView<TVisual>` para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto usando o [ `DataTemplate` Construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que faz referência a uma derivada da [`Cell`](xref:Xamarin.Forms.Cell) classe. `Cell`tem cinco derivativos:

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash;contém dois `Label` modos de exibição (em termos conceituais)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash;adiciona uma `Image` exibição a`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash;contém uma `Entry` exibição com um`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash;contém um `Switch` com`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash;pode ser qualquer `View` (provavelmente com filhos)

Em seguida, chame [ `SetValue` ] (xref: Xamarin.Forms . DataTemplate. DefinirValor ( Xamarin.Forms . Vinculproperty, System. Object) e [ `SetBinding` ] (xref: Xamarin.Forms . DataTemplate. setbindion ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)) no `DataTemplate` objeto para associar valores às `Cell` Propriedades ou definir associações de dados nas `Cell` Propriedades que fazem referência a propriedades dos itens na `ItemsSource` coleção. Isso é demonstrado no exemplo de [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

Como cada item é exibido pelo `ListView` , uma pequena árvore visual é construída a partir do modelo e as associações de dados são estabelecidas entre o item e as propriedades dos elementos nesta árvore visual. Você pode ter uma ideia desse processo instalando manipuladores para os [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) eventos e [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) do `ListView` , ou usando um [ `DataTemplate` Construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo que usa uma função que é chamada cada vez que a árvore visual de um item deve ser criada.

O [**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra um programa funcionalmente idêntico inteiramente em XAML. Uma `DataTemplate` marca é definida como a `ItemTemplate` propriedade de `ListView` e, em seguida, `TextCell` é definida como `DataTemplate` . Associações a propriedades dos itens na coleção são definidas diretamente nas [`Text`](xref:Xamarin.Forms.TextCell.Text) [`Detail`](xref:Xamarin.Forms.TextCell.Detail) Propriedades e do `TextCell` .

### <a name="custom-cells"></a>Células personalizadas

Em XAML, é possível definir um [`ViewCell`](xref:Xamarin.Forms.ViewCell) para `DataTemplate` e, em seguida, definir uma árvore visual personalizada como a [`View`](xref:Xamarin.Forms.ViewCell.View) propriedade de `ViewCell` . ( `View` é a propriedade Content de `ViewCell` , portanto, as `ViewCell.View` marcas não são necessárias.) O exemplo [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) demonstra essa técnica:

[![Captura de tela tripla da lista de cores nomeadas personalizadas](images/ch19fg11-small.png "Lista de cores nomeadas personalizadas")](images/ch19fg11-large.png#lightbox "Lista de cores nomeadas personalizadas")

Obter o direito de dimensionamento para todas as plataformas pode ser complicado. A [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) propriedade é útil, mas em alguns casos você desejará recorrer à [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) propriedade, o que é menos eficiente, mas força o a `ListView` dimensionar as linhas. Para iOS e Android, você deve usar uma dessas duas propriedades para obter o dimensionamento adequado da linha.

### <a name="grouping-the-listview-items"></a>Agrupando os itens de ListView

`ListView`dá suporte ao agrupamento de itens e à navegação entre esses grupos. A `ItemsSource` propriedade deve ser definida como uma coleção de coleções: o objeto que `ItemsSource` está definido para deve implementar `IEnumerable` e cada item na coleção também deve implementar `IEnumerable` . Cada grupo deve incluir duas propriedades: uma descrição de texto do grupo e uma abreviação de três letras.

A [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) cria sete grupos de `NamedColor` objetos. O exemplo [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) mostra como usar esses grupos com a [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) propriedade de `ListView` definida como e `true` as [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) Propriedades e [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) associadas a propriedades em cada grupo.

### <a name="custom-group-headers"></a>Cabeçalhos de grupo personalizado

É possível criar cabeçalhos personalizados para os `ListView` grupos, substituindo a `GroupDisplayBinding` propriedade com a [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) definição de um modelo para os cabeçalhos.

### <a name="listview-and-interactivity"></a>ListView e interatividade

Geralmente, um aplicativo obtém a interação do usuário com um `ListView` anexando um manipulador `ItemSelected` ao [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento ou, ou definindo uma associação de dados na `SelectedItem` propriedade. Mas alguns tipos de célula ( `EntryCell` e `SwitchCell` ) permitem a interação do usuário e também é possível criar células personalizadas que interagem com o usuário. O [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) cria 100 instâncias do [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e permite que o usuário altere cada cor usando um trio de `Slider` elementos. O programa também usa o [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) no [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView`desempenha uma grande função em cenários MVVM. Sempre que uma `IEnumerable` coleção existe em um ViewModel, ela é frequentemente associada a um `ListView` . Além disso, os itens na coleção geralmente são implementados `INotifyPropertyChanged` para associar as propriedades em um modelo.

### <a name="a-collection-of-viewmodels"></a>Uma coleção de ViewModels

Para explorar isso, a biblioteca [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) cria várias classes com base em um [arquivo de dados XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imagens de alunos fictícios nesta escola fictícia.

A [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe deriva de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs) . A [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe é uma coleção de `Student` objetos e também deriva de `ViewModelBase` . O [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) baixa o arquivo XML e monta todos os objetos.

O programa [**studentlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa um `ImageCell` para exibir os alunos e suas imagens em um `ListView` :

[![Captura de tela tripla da lista de alunos](images/ch19fg18-small.png "Lista de alunos")](images/ch19fg18-large.png#lightbox "Lista de alunos")

O exemplo [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) adiciona uma [`Header`](xref:Xamarin.Forms.ListView.Header) propriedade, mas só aparece no Android.

### <a name="selection-and-the-binding-context"></a>Seleção e o contexto de associação

O programa [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) associa o `BindingContext` de um `StackLayout` à `SelectedItem` Propriedade do `ListView` . Isso permite que o programa exiba informações detalhadas sobre o aluno selecionado.

### <a name="context-menus"></a>Menus de contexto

Uma célula pode definir um menu de contexto que é implementado de maneira específica da plataforma. Para criar esse menu, adicione [`MenuItem`](xref:Xamarin.Forms.MenuItem) objetos à [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) Propriedade do `Cell` .

`MenuItem`define cinco propriedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)do tipo`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)do tipo`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)do tipo`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)do tipo`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)do tipo`object`

As `Command` `CommandParameter` Propriedades e sugerem que o ViewModel de cada item contém métodos para executar os comandos de menu desejados. Em cenários não MVVM, `MenuItem` também define um [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento.

O [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demonstra essa técnica. A `Command` propriedade de cada `MenuItem` está associada a uma propriedade do tipo `ICommand` na `Student` classe. Defina a `IsDestructive` propriedade como `true` para um `MenuItem` que remova ou exclua o objeto selecionado.

### <a name="varying-the-visuals"></a>Variando os visuais

Às vezes, você desejará pequenas variações nos elementos visuais dos itens do `ListView` com base em uma propriedade. Por exemplo, quando a média de pontos de nível de um aluno cai abaixo de 2,0, o exemplo [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) exibe o nome desse aluno em vermelho.
Isso é feito por meio do uso de um conversor de valor de associação, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs) , na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Atualizando o conteúdo

O `ListView` dá suporte a um gesto de pull para a atualização de seus dados. O programa deve definir a [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) propriedade como `true` para habilitar isso. O `ListView` responde ao gesto suspenso definindo sua [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propriedade como `true` e gerando o [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento e (para cenários MVVM) chamando o `Execute` método de sua [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) propriedade.

O código que manipula o `Refresh` evento ou `RefreshCommand` , possivelmente, atualiza os dados exibidos pelo `ListView` e `IsRefreshing` retorna para `false` .

O exemplo [**RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) demonstra o uso de um [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` e `IsRefreshing` Propriedades para vinculação de dados.

## <a name="the-tableview-and-its-intents"></a>O TableView e suas intenções

Embora o `ListView` geralmente exiba várias instâncias do mesmo tipo, o [`TableView`](xref:Xamarin.Forms.TableView) geralmente se concentra em fornecer uma interface do usuário para várias propriedades de vários tipos. Cada item é associado a seu próprio [`Cell`](xref:Xamarin.Forms.Cell) derivativo para exibir a propriedade ou fornecer uma interface do usuário a ela.

### <a name="properties-and-hierarchies"></a>Propriedades e hierarquias

`TableView`define apenas quatro propriedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)do tipo [`TableIntent`](xref:Xamarin.Forms.TableIntent) , uma enumeração
- [`Root`](xref:Xamarin.Forms.TableView.Root)do tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot) , a propriedade de conteúdo de`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)do tipo`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)do tipo`bool`

A `TableIntent` enumeração indica como você pretende usar `TableView` :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Esses membros também sugerem alguns usos para o `TableView` .

Várias outras classes estão envolvidas na definição de uma tabela:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)é uma classe abstrata que deriva de `BindableObject` e define uma [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) Propriedade

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)é uma classe abstrata que deriva de `TableSectionBase` e implementa `IList<T>` e`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)deriva de`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)deriva de`TableSectionBase<TableSection>`

Em suma, `TableView` tem uma `Root` propriedade que você define para um `TableRoot` objeto, que é uma coleção de `TableSection` objetos, cada um deles é uma coleção de `Cell` objetos. Uma tabela tem várias seções e cada seção tem várias células. A tabela em si pode ter um título e cada seção pode ter um título. Embora `TableView` faça uso de `Cell` derivativos, ele não usa `DataTemplate` .

### <a name="a-prosaic-form"></a>Um formulário prosaicas

O exemplo [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) define um [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modelo de exibição, uma instância do que se torna o `BindingContext` do `TableView` . Cada `Cell` derivação em seu `TableSection` pode então ter associações a propriedades da `PersonalInformation` classe.

### <a name="custom-cells"></a>Células personalizadas

O exemplo [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) expande em **EntryForm**. A [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe inclui uma propriedade booliana que governa a aplicabilidade de duas propriedades adicionais. Para essas duas propriedades adicionais, o programa usa um personalizado `PickerCell` com base em um [PickerCell. xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Embora as `IsEnabled` Propriedades dos dois `PickerCell` elementos estejam associadas à propriedade booliana no `ProgrammerInformation` , essa técnica não parece funcionar, o que solicita o próximo exemplo.

### <a name="conditional-sections"></a>Seções condicionais

O exemplo de [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) coloca os dois itens condicionais na seleção do item booliano em separado `TableSection` . O arquivo code-behind remove esta seção do `TableView` ou a adiciona de volta com base na propriedade booliana.

### <a name="a-tableview-menu"></a>Um menu do TableView

Outro uso de um `TableView` é um menu. O exemplo [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) demonstra um menu que permite que você mova um pouco `BoxView` da tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 19 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Capítulo 19 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Seletor](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
