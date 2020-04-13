---
title: Resumo do Capítulo 19. Exibições de coleção
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 19. Exibições de coleção'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032861"
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumo do Capítulo 19. Exibições de coleção

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Xamarin.Forms define três pontos de vista que mantêm coleções e exibem seus elementos:

- [`Picker`](xref:Xamarin.Forms.Picker)é uma lista relativamente curta de itens de seqüência que permite ao usuário escolher um
- [`ListView`](xref:Xamarin.Forms.ListView)é muitas vezes uma longa lista de itens geralmente do mesmo tipo e formatação, permitindo também que o usuário escolha um
- [`TableView`](xref:Xamarin.Forms.TableView)é uma coleção de *células* (geralmente de vários tipos e aparências) para exibir dados ou gerenciar a entrada do usuário

É comum que aplicativos MVVM `ListView` usem o para exibir uma coleção selecionável de objetos.

## <a name="program-options-with-picker"></a>Opções de programa com Picker

A [`Picker`](xref:Xamarin.Forms.Picker) é uma boa escolha quando você precisa permitir que o usuário `string` escolha uma opção entre uma lista relativamente curta de itens.

### <a name="the-picker-and-event-handling"></a>O Picker e o manuseio de eventos

A amostra [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) demonstra como usar xaml para definir `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) a propriedade e adicionar `string` itens à [`Items`](xref:Xamarin.Forms.Picker.Items) coleção. Quando o usuário `Picker`seleciona o , ele `Items` exibe os itens da coleção de forma dependente da plataforma.

O [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento indica quando o usuário selecionou um item. A propriedade [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) baseada em zero indica então o item selecionado. Se nenhum item `SelectedIndex` for &ndash;selecionado, é igual a 1.

Você também `SelectedIndex` pode usar para inicializar o item selecionado, mas ele deve ser definido após o preenchimento da `Items` coleção. Em XAML, isso significa que você provavelmente `SelectedIndex`usará um elemento de propriedade para definir .

### <a name="data-binding-the-picker"></a>Dados que ligam o Picker

A `SelectedIndex` propriedade é apoiada por `Items` uma propriedade vinculável, mas `Picker` não é, então usar a vinculação de dados com um é difícil. Uma solução é `Picker` usar a [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) combinação com uma como a da biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) O [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) demonstra como isso funciona.

> [!NOTE] 
> O Xamarin.Forms `Picker` agora `ItemsSource` `SelectedItem` inclui e propriedades que suportam a vinculação de dados. Veja [Picker](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Renderização de dados com ListView

A [`ListView`](xref:Xamarin.Forms.ListView) é a única classe [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) que deriva da [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) qual herda as propriedades.

`ItemsSource`é de `IEnumerable` tipo, `null` mas é por padrão e deve ser explicitamente inicializado ou (mais comumente) definido para uma coleta através de uma vinculação de dados. Os itens desta coleção podem ser de qualquer tipo.

`ListView`define uma [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriedade que é definida como um dos `ItemsSource` itens `null` da coleção ou se nenhum item for selecionado. `ListView`desfaz o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento quando um novo item é selecionado.

### <a name="collections-and-selections"></a>Coleções e seleções

A amostra [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) `ListView` preenche um `Color` com `List<Color>` 17 valores em uma coleção. Os itens são selecionáveis, mas por padrão são `ToString` exibidos com suas representações pouco atraentes. Vários exemplos neste capítulo mostram como corrigir essa exibição e torná-la tão atraente quanto desejado.

### <a name="the-row-separator"></a>O separador de linha

Nas telas do iOS e Android, uma linha fina separa as linhas. Você pode controlar [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) isso [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) com as propriedades. `SeparatorVisibility`propriedade é [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)do tipo , uma enumeração com dois membros:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), a configuração padrão
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Vinculação de dados do item selecionado

A `SelectedItem` propriedade é apoiada por uma propriedade vinculável, por isso pode ser a fonte ou alvo de uma vinculação de dados. Seu `BindingMode` padrão `OneWayToSource`é , mas geralmente é o alvo de uma vinculação de dados bidirecionais, particularmente em cenários MVVM. A amostra [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) demonstra esse tipo de vinculação.

### <a name="the-observablecollection-difference"></a>A diferença da Coleção Observável

A amostra [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) `ItemsSource` define `ListView` a `List<DateTime>` propriedade de a a `DateTime` a uma coleção e, em seguida, adiciona progressivamente um novo objeto à coleção a cada segundo usando um temporizador.

No entanto, o `ListView` não se `List<T>` atualiza automaticamente porque a coleção não tem um mecanismo de notificação para indicar quando os itens são adicionados ou removidos da coleção.

Uma classe muito melhor para [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) usar em `System.Collections.ObjectModel` tais cenários é definida no namespace. Esta classe implementa a [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) interface [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) e, consequentemente, aciona um evento quando os itens são adicionados ou removidos da coleção, ou quando são substituídos ou movidos dentro da coleção. Quando `ListView` a implementação interna de `INotifyCollectionChanged` uma classe foi `ItemsSource` definida em sua propriedade, `CollectionChanged` ela anexa um manipulador ao evento e atualiza sua exibição quando a coleção muda.

A amostra [**ObservávelLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) demonstra o uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelos e células

Por padrão, `ListView` um exibe itens em sua coleção `ToString` usando o método de cada item. Uma abordagem melhor envolve a definição de um modelo para exibir os itens.

Para experimentar esse recurso, você [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) pode usar a classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Esta classe define `All` uma propriedade `IList<NamedColor>` estática do `NamedColor` tipo que contém 141 objetos correspondentes aos campos públicos da `Color` estrutura.

A amostra [**IngênuoNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) `ItemsSource` define `ListView` a `NamedColor.All` de a para esta propriedade, `NamedColor` mas apenas os nomes de classe totalmente qualificados dos objetos são exibidos.

`ListView`precisa de um modelo para exibir esses itens. Em código, você [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) pode definir `ItemsView<TVisual>` a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriedade definida por um objeto usando [`Cell`](xref:Xamarin.Forms.Cell) o [ `DataTemplate` construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) que faz referência a uma derivada da classe. `Cell`tem cinco derivativos:

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash; contém `Label` dois pontos de vista (conceitualmente falando)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash; adiciona `Image` uma visão para`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash; contém `Entry` uma visão com um`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash; contém `Switch` um com um`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash; pode ser `View` qualquer (provável com crianças)

Em [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) seguida, ligue e [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) `DataTemplate` no `Cell` objeto para associar valores com `Cell` as propriedades ou para definir `ItemsSource` vinculações de dados nas propriedades referentes às propriedades dos itens da coleção. Isso é demonstrado na amostra [**TextCellListCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)

À medida que cada item é exibido pelo `ListView`, uma pequena árvore visual é construída a partir do modelo, e as vinculações de dados são estabelecidas entre o item e as propriedades dos elementos nesta árvore visual. Você pode ter uma ideia desse processo instalando [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) manipuladores `ListView`para os [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) eventos e eventos do , ou usando um [ `DataTemplate` construtor](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternativo que usa uma função que é chamada cada vez que a árvore visual de um item deve ser criada.

O [**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra um programa funcionalmente idêntico inteiramente em XAML. Uma `DataTemplate` tag é `ItemTemplate` definida como `ListView`propriedade do `TextCell` , e, em seguida, a é definida como . `DataTemplate` As vinculações às propriedades dos itens da coleção [`Text`](xref:Xamarin.Forms.TextCell.Text) são [`Detail`](xref:Xamarin.Forms.TextCell.Detail) definidas `TextCell`diretamente nas propriedades do .

### <a name="custom-cells"></a>Células personalizadas

Em XAML é possível [`ViewCell`](xref:Xamarin.Forms.ViewCell) definir `DataTemplate` um para o e, [`View`](xref:Xamarin.Forms.ViewCell.View) em `ViewCell`seguida, definir uma árvore visual personalizada como propriedade de . (`View` é propriedade `ViewCell` de conteúdo `ViewCell.View` de modo que as tags não são necessárias.) A amostra [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) demonstra essa técnica:

[![Captura de tela tripla da lista de cores personalizada nomeada](images/ch19fg11-small.png "Lista de cores personalizada nomeada")](images/ch19fg11-large.png#lightbox "Lista de cores personalizada nomeada")

Acertar o tamanho para todas as plataformas pode ser complicado. A [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) propriedade é útil, mas em alguns [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) casos você vai querer recorrer `ListView` à propriedade, que é menos eficiente, mas força o tamanho das linhas. Para iOS e Android, você deve usar uma dessas duas propriedades para obter o dimensionamento adequado da linha.

### <a name="grouping-the-listview-items"></a>Agrupando os itens ListView

`ListView`suporta o agrupamento de itens e a navegação entre esses grupos. O `ItemsSource` imóvel deve ser definido como um acervo de coleções: O objeto definido `ItemsSource` deve ser implementado, `IEnumerable`e cada item da coleção também deve ser implementado. `IEnumerable` Cada grupo deve incluir duas propriedades: uma descrição de texto do grupo e uma abreviação de três letras.

A [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) `NamedColor` cria sete grupos de objetos. A amostra [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) mostra como usar [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) esses `ListView` grupos `true`com [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) a [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) propriedade de set to , e as propriedades vinculadas às propriedades em cada grupo.

### <a name="custom-group-headers"></a>Cabeçalhos de grupo personalizados

É possível criar cabeçalhos `ListView` personalizados para `GroupDisplayBinding` os grupos [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) substituindo a propriedade pela definição de um modelo para os cabeçalhos.

### <a name="listview-and-interactivity"></a>ListVer e interatividade

Geralmente, um aplicativo obtém `ListView` interação do usuário com `ItemSelected` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) um anexando um manipulador ao `SelectedItem` evento ou definindo uma vinculação de dados na propriedade. Mas alguns`EntryCell` tipos `SwitchCell`de células ( e ) permitem a interação do usuário, e também é possível criar células personalizadas que interagem com o usuário. O [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) cria 100 instâncias [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e permite que o `Slider` usuário altere cada cor usando um trio de elementos. O programa também faz [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) uso do [**xamarin.formsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView`desempenha um grande papel nos cenários da MVVM. Sempre `IEnumerable` que uma coleção existe em um ViewModel, muitas vezes ela é vinculada a um `ListView`. Além disso, os itens da `INotifyPropertyChanged` coleção geralmente implementam para vincular-se com propriedades em um modelo.

### <a name="a-collection-of-viewmodels"></a>Uma coleção de ViewModels

Para explorar isso, a biblioteca [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) cria várias aulas baseadas em um arquivo de [dados XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e imagens de alunos fictícios nesta escola fictícia.

A [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe deriva [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)de . A [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe é `Student` uma coleção de `ViewModelBase`objetos e também deriva de . O [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) download do arquivo XML e monta todos os objetos.

O [**programa StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) usa um `ImageCell` para exibir `ListView`os alunos e suas imagens em um :

[![Captura de tela tripla da Lista de Estudantes](images/ch19fg18-small.png "Lista de Estudantes")](images/ch19fg18-large.png#lightbox "Lista de Estudantes")

A amostra [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) adiciona uma [`Header`](xref:Xamarin.Forms.ListView.Header) propriedade, mas ela só aparece no Android.

### <a name="selection-and-the-binding-context"></a>Seleção e o contexto vinculante

O programa [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) `BindingContext` vincula `StackLayout` o `SelectedItem` de `ListView`a à propriedade do . Isso permite que o programa exiba informações detalhadas sobre o aluno selecionado.

### <a name="context-menus"></a>Menus de contexto

Uma célula pode definir um menu de contexto que é implementado de forma específica da plataforma. Para criar este [`MenuItem`](xref:Xamarin.Forms.MenuItem) menu, [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) adicione objetos à propriedade do `Cell`.

`MenuItem`define cinco propriedades:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)de tipo`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de tipo`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)de tipo`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)de tipo`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)de tipo`object`

As `Command` `CommandParameter` propriedades e implicam que o ViewModel para cada item contém métodos para realizar os comandos de menu desejados. Em cenários não-MVVM, `MenuItem` [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) também define um evento.

O [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demonstra essa técnica. A `Command` propriedade `MenuItem` de cada um está `ICommand` vinculada `Student` a uma propriedade do tipo na classe. Defina `IsDestructive` a `true` propriedade `MenuItem` para um que remove ou exclui o objeto selecionado.

### <a name="varying-the-visuals"></a>Variando o visual

Às vezes você vai querer pequenas variações no visual `ListView` dos itens na base de uma propriedade. Por exemplo, quando a média de pontos de nota de um aluno fica abaixo de 2,0, a amostra [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) exibe o nome desse aluno em vermelho.
Isso é feito através do uso [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)de um conversor de valor de vinculação, na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="refreshing-the-content"></a>Refrescando o conteúdo

O `ListView` suporte a um gesto pull-down para atualizar seus dados. O programa deve [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) definir `true` a propriedade para habilitar isso. O `ListView` responde ao gesto pull-down [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) definindo `true`sua propriedade [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) para , e levantando o evento `Execute` e [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) (para cenários MVVM) chamando o método de sua propriedade.

O código `Refresh` que manuseia o evento `RefreshCommand` ou, `ListView` em `IsRefreshing` seguida, possivelmente atualiza os dados exibidos pelo e volta para `false`.

A amostra [**RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) demonstra o `RefreshCommand` `IsRefreshing` uso de um que implementa e propriedades para vinculação de dados.

## <a name="the-tableview-and-its-intents"></a>O TableView e suas intenções

Embora `ListView` o geralmente exiba várias instâncias do mesmo tipo, o [`TableView`](xref:Xamarin.Forms.TableView) geralmente é focado em fornecer uma interface de usuário para múltiplas propriedades de vários tipos. Cada item está associado [`Cell`](xref:Xamarin.Forms.Cell) com sua própria derivada para exibir a propriedade ou fornecer uma interface de usuário para ele.

### <a name="properties-and-hierarchies"></a>Propriedades e hierarquias

`TableView`define apenas quatro propriedades:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)de [`TableIntent`](xref:Xamarin.Forms.TableIntent)tipo, uma enumeração
- [`Root`](xref:Xamarin.Forms.TableView.Root)de [`TableRoot`](xref:Xamarin.Forms.TableRoot)tipo , a propriedade de conteúdo de`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)de tipo`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)de tipo`bool`

A `TableIntent` enumeração indica como você `TableView`pretende usar o :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Esses membros também sugerem `TableView`alguns usos para o .

Várias outras classes estão envolvidas na definição de uma tabela:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)é uma classe abstrata `BindableObject` que deriva [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) e define uma propriedade

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)é uma classe abstrata `TableSectionBase` que deriva `IList<T>` e implementa e`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)deriva de`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)deriva de`TableSectionBase<TableSection>`

Em `TableView` suma, `Root` tem uma propriedade `TableRoot` que você define `TableSection` para um objeto, que `Cell` é uma coleção de objetos, cada um dos quais é uma coleção de objetos. Uma tabela tem várias seções, e cada seção tem várias células. A tabela em si pode ter um título, e cada seção pode ter um título. Embora `TableView` faça `Cell` uso de derivativos, `DataTemplate`ele não faz uso de .

### <a name="a-prosaic-form"></a>Uma forma prosaica

A amostra [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) define um modelo de exibição, `TableView`uma instância da qual se torna a `BindingContext` do . Cada `Cell` derivada `TableSection` em seu pode então `PersonalInformation` ter vinculações às propriedades da classe.

### <a name="custom-cells"></a>Células personalizadas

A amostra [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) se expande no **EntryForm**. A [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe inclui uma propriedade booleana que rege a aplicabilidade de duas propriedades adicionais. Para essas duas propriedades adicionais, `PickerCell` o programa usa um personalizado baseado em um [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Embora `IsEnabled` as propriedades `PickerCell` dos dois elementos estejam `ProgrammerInformation`vinculadas à propriedade booleana em , esta técnica não parece funcionar, o que solicita a próxima amostra.

### <a name="conditional-sections"></a>Seções condicionais

A amostra [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) coloca os dois itens condicionados à seleção do `TableSection`item booleano em um separado . O arquivo de código atrás remove `TableView` esta seção do ou adiciona-a de volta com base na propriedade booleana.

### <a name="a-tableview-menu"></a>Um menu TableView

Outro uso `TableView` de a é um menu. A amostra [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) demonstra um menu que `BoxView` permite que você se mova um pouco ao redor da tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 19 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Capítulo 19 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Seletor](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
