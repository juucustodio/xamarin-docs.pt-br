---
title: "Resumo do capítulo 19. Exibições de coleção"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 37afa3a54fd20745a65312fb5a24d958c8ec405f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-19-collection-views"></a>Resumo do capítulo 19. Exibições de coleção

Xamarin. Forms define três exibições que mantêm a coleções e seus elementos de exibição:

- [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) é uma lista relativamente curta de itens de cadeia de caracteres que permite que o usuário escolha uma
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) geralmente é uma longa lista de itens normalmente do mesmo tipo e formatação, também permitindo que o usuário escolha uma
- [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) é uma coleção de *células* (normalmente de vários tipos e aparências) para exibir dados ou gerenciar a entrada do usuário

É comum que aplicativos MVVM para usar o `ListView` para exibir uma coleção selecionável de objetos.

## <a name="program-options-with-picker"></a>Opções do programa com o seletor

O [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) é uma boa opção quando você precisa permitir que o usuário escolha uma opção dentre uma lista relativamente curta de `string` itens.

### <a name="the-picker-and-event-handling"></a>O seletor e manipulação de eventos

O [ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) exemplo demonstra como usar XAML para definir o `Picker` [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propriedade e adicionar `string` itens para o [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) coleção. Quando o usuário seleciona o `Picker`, ele exibe os itens a `Items` coleção de forma dependente de plataforma.

O [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento indica quando o usuário selecionou um item. O com base em zero [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propriedade indica, em seguida, o item selecionado. Se nenhum item for selecionado, `SelectedIndex` igual a & #x 2013; 1.

Você também pode usar `SelectedIndex` inicializar o item selecionado, mas ele deve ser definida após o `Items` coleção é preenchida. Em XAML, isso significa que você provavelmente usará um elemento de propriedade para definir `SelectedIndex`.

### <a name="data-binding-the-picker"></a>O seletor de associação de dados

O `SelectedIndex` propriedade é feita por uma propriedade ligável mas `Items` não for, portanto, usar associação de dados com um `Picker` é difícil. Uma solução é usar o `Picker` em combinação com um [ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) como o o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. O [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) demonstra como isso funciona.

## <a name="rendering-data-with-listview"></a>Dados de renderização com ListView

O [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é a única classe que deriva de [ `ItemsView<TVisual>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) da qual ela herda o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) e [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propriedades.

`ItemsSource` é do tipo `IEnumerable` , mas é `null` por padrão e deve ser explicitamente inicializados ou (mais comumente) definido como uma coleção por meio de uma associação de dados. Os itens na coleção podem ser de qualquer tipo.

`ListView` define um [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) propriedade que é definida como um dos itens a `ItemsSource` coleção ou `null` se nenhum item selecionado. `ListView` aciona o [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) eventos quando um novo item é selecionado.

### <a name="collections-and-selections"></a>Coleções e as seleções

O [ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) exemplo preenchimentos um `ListView` com 17 `Color` valores em um `List<Color>` coleção. Por padrão, eles são exibidos com sua atraente, mas os itens são selecionáveis `ToString` representações. Vários exemplos neste capítulo mostram como corrigir exibidos e torná-lo tão bons conforme desejado.

### <a name="the-row-separator"></a>O separador de linha

No iOS e Android exibe, uma linha fina separa as linhas. Você pode controlar isso com o [ `SeparatorVisibiliy` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorVisibility/) e [ `SeparatorColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorColor/) propriedades. `SeparatorVisibility` a propriedade é do tipo [ `SeparatorVisbility` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SeparatorVisibility/), uma enumeração com dois membros:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.Default/), a configuração padrão
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.None/)

### <a name="data-binding-the-selected-item"></a>O item selecionado de associação de dados

O `SelectedItem` propriedade é feita por uma propriedade ligável, portanto, pode ser a origem ou o destino de uma associação de dados. O padrão `BindingMode` é `OneWayToSource`, mas geralmente é o destino de uma associação de dados bidirecional, particularmente em cenários MVVM. O [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) demonstra esse tipo de associação.

### <a name="the-observablecollection-difference"></a>A diferença de ObservableCollection

O [ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) conjuntos de exemplo de `ItemsSource` propriedade de um `ListView` para um `List<DateTime>` coleção e, em seguida, progressivamente adiciona um novo `DateTime` objeto à coleção cada segundo usando um timer.

No entanto, o `ListView` não se atualiza automaticamente porque o `List<T>` coleção não tem um mecanismo de notificação para indicar quando itens são adicionados ou removidos da coleção.

É uma classe melhor usar nesses cenários [ `ObservableCollection<T>` ](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) definido no `System.Collections.ObjectModel` namespace. Essa classe implementa o [ `INotifyCollectionChanged` ](https://developer.xamarin.com/api/type/System.Collections.Specialized.INotifyCollectionChanged/) interface e, consequentemente, aciona uma [ `CollectionChanged` ](https://developer.xamarin.com/api/event/System.Collections.ObjectModel.ObservableCollection%3CT%3E.CollectionChanged/) evento quando itens são adicionados ou removidos da coleção, ou quando eles são substituídos ou movidos dentro de a coleção. Quando o `ListView` internamente detecta que uma implementação da classe `INotifyCollectionChanged` foi definido como seu `ItemsSource` propriedade anexa um manipulador do `CollectionChanged` eventos e atualiza sua exibição quando a coleção é alterada.

O [ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) exemplo demonstra o uso de `ObservableCollection`.

### <a name="templates-and-cells"></a>Modelos e células

Por padrão, um `ListView` exibe itens em sua coleção de uso de cada item `ToString` método. Uma abordagem melhor envolve a definição de um modelo para exibir os itens.

Para fazer experiências com esse recurso, você pode usar o [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. Essa classe define um estático `All` propriedade do tipo `IList<NamedColor>` que contém 141 `NamedColor` objetos correspondentes aos campos de público a `Color` estrutura.

O [ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) conjuntos de exemplo de `ItemsSource` de um `ListView` a este `NamedColor.All` propriedade, mas apenas os nomes de classe totalmente qualificado do `NamedColor` objetos são exibido.

`ListView` precisa de um modelo para exibir esses itens. No código, você pode definir o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propriedade definida pelo `ItemsView<TVisual>` para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) objeto usando o [ `DataTemplate` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) que faz referência a um derivado do [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) classe. `Cell` tem cinco derivados:

- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) & #x 2014; contém duas `Label` exibições (ponto de vista conceitual)
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) & #x 2014; Adiciona um `Image` exibição `TextCell`
- [`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) & #x 2014; contém um `Entry` exibir com um `Label`
- [`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) & #x 2014; contém um `Switch` com um `Label`
- [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) & #x 2014; pode ser qualquer `View` (provavelmente com filhos)

Em seguida, chame [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) e [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) no `DataTemplate` objeto para associar valores com o `Cell` propriedades, ou para definir associações de dados no `Cell` Propriedades da referência de propriedades de itens no `ItemsSource` coleção. Isso é demonstrado no [ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) exemplo.

Como cada item é exibido pelo `ListView`, uma pequena árvore visual é construída a partir do modelo e associações de dados são estabelecidas entre o item e as propriedades dos elementos nesta árvore visual. Você pode obter uma ideia desse processo instalando manipuladores para o [ `ItemAppearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemAppearing/) e [ `ItemDisappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemDisappearing/) eventos de `ListView`, ou usando uma alternativa [ `DataTemplate`construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Func%7BSystem.Object%7D/) que usa uma função que é chamada sempre que a árvore visual do item deve ser criado.

O [ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) mostra um programa funcionalmente idêntico inteiramente em XAML. Um `DataTemplate` marca é definida como o `ItemTemplate` propriedade do `ListView`e, em seguida, o `TextCell` é definido como o `DataTemplate`. Associações para propriedades de itens na coleção são definidas diretamente no [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) propriedades da `TextCell`.

### <a name="custom-cells"></a>Células personalizadas

Em XAML, é possível definir um [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) para o `DataTemplate` e, em seguida, definir uma árvore visual personalizada como o [ `View` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ViewCell.View/) propriedade `ViewCell`. (`View` é a propriedade de conteúdo de `ViewCell` as `ViewCell.View` marcas não são necessárias.) O [ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) demonstra essa técnica:

[![Tripla captura de tela da lista de cores denominado personalizada](images/ch19fg11-small.png "personalizado chamado cor lista")](images/ch19fg11-large.png "personalizado chamado lista de cores")

Obter o tamanho certo para todas as plataformas pode ser difícil. O [ `RowHeight` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RowHeight/) propriedade é útil, mas em alguns casos, você vai querer recorrer ao [ `HasUnevenRows` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HasUnevenRows/) propriedade, que é menos eficiente, mas força o `ListView` para dimensionar linhas. Para iOS e Android, você deve usar uma dessas duas propriedades para obter dimensionamento de linha adequada.

### <a name="grouping-the-listview-items"></a>Agrupar os itens de ListView

`ListView` oferece suporte a agrupamento de itens e navegando entre esses grupos. O `ItemsSource` propriedade deve ser definida para uma coleção de coleções: O objeto que `ItemsSource` é definido como deve implementar `IEnumerable`, e cada item na coleção também deve implementar `IEnumerable`. Cada grupo deve incluir duas propriedades: uma descrição de texto do grupo e uma abreviação de três letras.

O [ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca cria sete grupos de `NamedColor` objetos. O [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) exemplo mostra como usar esses grupos com o [ `IsGroupingEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsGroupingEnabled/) propriedade `ListView` definida como `true`e o [ `GroupDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/) e [ `GroupShortNameBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/) propriedades associadas às propriedades de cada grupo.

### <a name="custom-group-headers"></a>Cabeçalhos de grupo personalizado

É possível criar cabeçalhos personalizados para o `ListView` grupos, substituindo o `GroupDisplayBinding` propriedade com o [ `GroupHeaderTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/) definir um modelo para os cabeçalhos.

### <a name="listview-and-interactivity"></a>ListView e interatividade

Um aplicativo obtém interação do usuário com um `ListView` anexando um manipulador para o `ItemSelected` ou [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento, ou definindo uma associação de dados no `SelectedItem` propriedade. Mas alguns tipos de célula (`EntryCell` e `SwitchCell`) permitem a interação do usuário, e também é possível criar células personalizadas que se interagir com o usuário. O [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) cria 100 instâncias de [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) e permite que o usuário altere cada cor usando um trio de `Slider` elementos. O programa também usa o [ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) no [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView e MVVM

`ListView` desempenha um papel importante em cenários MVVM. Sempre que um `IEnumerable` coleção existe em um ViewModel, geralmente está associado a um `ListView`. Além disso, os itens na coleção geralmente implementam `INotifyPropertyChanged` para associar com propriedades em um modelo.

### <a name="a-collection-of-viewmodels"></a>Uma coleção de ViewModels

Para explorar a isso, o [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca cria várias classes com base em um [arquivo de dados XML](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) e as imagens de alunos fictícias este School fictícios.

O [ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe derivada de [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). O [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe é uma coleção de `Student` objetos e também deriva de `ViewModelBase`. O [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) baixa o arquivo XML e monta todos os objetos.

O [ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) programa usa uma `ImageCell` para exibir os alunos e das imagens em um `ListView`:

[![Tripla captura de tela da lista de aluno](images/ch19fg18-small.png "aluno lista")](images/ch19fg18-large.png "aluno lista")

O [ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) exemplo adiciona um [ `Header` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.Header/) propriedade, mas aparece somente no Android.

### <a name="selection-and-the-binding-context"></a>Seleção e o contexto de associação

O [ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) programa associa o `BindingContext` de um `StackLayout` para o `SelectedItem` propriedade do `ListView`. Isso permite que o programa para exibir informações detalhadas sobre o aluno selecionado.

### <a name="context-menus"></a>Menus de contexto

Uma célula pode definir um menu de contexto que é implementado de maneira específica de plataforma. Para criar esse menu, adicionar [ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) objetos para o [ `ContextActions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Cell.ContextActions/) propriedade o `Cell`.

`MenuItem` define cinco propriedades:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) do tipo `string`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) do tipo `FileImageSource`
- [`IsDestructive`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.IsDestructive/) do tipo `bool`
- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Command/) do tipo `ICommand`
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.CommandParameter/) do tipo `object`

O `Command` e `CommandParameter` propriedades implicam que o ViewModel para cada item contém métodos para executar os comandos de menu desejado. Em cenários de não-MVVM `MenuItem` também define uma [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/) eventos.

O [ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) demonstra essa técnica. O `Command` propriedade de cada `MenuItem` está associada a uma propriedade do tipo `ICommand` no `Student` classe. Definir o `IsDestructive` propriedade `true` para um `MenuItem` que remove ou exclui o objeto selecionado.

### <a name="varying-the-visuals"></a>Variável visuais

Às vezes, você desejará pequenas variações em visuais dos itens a `ListView` com base em uma propriedade. Por exemplo, quando a média de ponto de série do aluno cai abaixo 2.0, o [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) exemplo exibe o nome do aluno que em vermelho.
Isso é feito por meio do uso de um conversor de valor de associação, [ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), além de [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="refreshing-the-content"></a>Atualizando o conteúdo

O `ListView` oferece suporte a um gesto suspenso para atualizar seus dados. O programa deve definir o [ `IsPullToRefresh` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsPullToRefreshEnabled/) propriedade `true` para permitir isso. O `ListView` responde para o gesto suspenso definindo seu [ `IsRefreshing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsRefreshing/) propriedade `true`e gerando o [ `Refreshing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.Refreshing/) eventos e (para cenários MVVM) chamando o `Execute` método de sua [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/) propriedade.

Tratamento de código a `Refresh` evento ou o `RefreshCommand` possivelmente atualiza os dados exibidos pelo `ListView` e define `IsRefreshing` para `false`.

O [ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) exemplo demonstra como usar um [ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) que implementa `RefreshCommand` e `IsRefreshing` propriedades de associação de dados.

## <a name="the-tableview-and-its-intents"></a>O modo de tabela e seus propósitos.

Enquanto o `ListView` geralmente exibe várias instâncias do mesmo tipo, o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) geralmente se concentra em fornecer uma interface do usuário para várias propriedades de vários tipos. Cada item é associado com seu próprio [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) derivados para exibir a propriedade ou fornecer uma interface de usuário para ele.

### <a name="properties-and-hierarchies"></a>Propriedades e as hierarquias

`TableView` define apenas quatro propriedades:

- [`Intent`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Intent/) tipo [ `TableIntent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableIntent/), uma enumeração
- [`Root`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/), a propriedade de conteúdo de `TableView`
- [`RowHeight`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.RowHeight/) do tipo `int`
- [`HasUnevenRows`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.HasUnevenRows/) do tipo `bool`

O `TableIntent` enumeração indica como você pretende usar o `TableView`:

- [`Data`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Data/)
- [`Form`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Form/)
- [`Settings`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Settings/)
- [`Menu`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Menu/)

Esses membros também sugerem alguns usos para o `TableView`.

Várias outras classes envolvidas na definição de uma tabela:

- [`TableSectionBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase/) é uma classe abstrata da qual deriva `BindableObject` e define uma [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableSectionBase.Title/) propriedade

- [`TableSectionBase<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase%3CT%3E/) é uma classe abstrata da qual deriva `TableSectionBase` e implementa `IList<T>` e `INotifyCollectionChanged`

- [`TableSection`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) derivado de `TableSectionBase<Cell>`

- [`TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/) derivado de `TableSectionBase<TableSection>`

Em resumo, `TableView` tem um `Root` propriedade definida como um `TableRoot` objeto, que é uma coleção de `TableSection` objetos, cada um deles é uma coleção de `Cell` objetos. Uma tabela com várias seções, e cada seção tem várias células. A própria tabela pode ter um título, e cada seção pode ter um título. Embora `TableView` usa `Cell` derivados, ele não faz uso de `DataTemplate`.

### <a name="a-prosaic-form"></a>Um formulário prosaico

O [ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) exemplo define um [ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modelo de exibição, uma instância do que se torna o `BindingContext` do `TableView`. Cada `Cell` derivados no seu `TableSection` , em seguida, podem ter associações às propriedades a `PersonalInformation` classe.

### <a name="custom-cells"></a>Células personalizadas

O [ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) exemplo expande **EntryForm**. O [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe inclui uma propriedade booleana que controla a aplicabilidade das duas propriedades adicionais. Para essas duas propriedades adicionais, o programa usa um personalizado `PickerCell` com base em um [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) e [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) no [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

Embora o `IsEnabled` propriedades dos dois `PickerCell` elementos estão associados à propriedade Boolean `ProgrammerInformation`, essa técnica não parece funcionar, solicitando que o próximo exemplo.

### <a name="conditional-sections"></a>Seções condicionais

O [ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) exemplo coloca os dois itens que são condicionais na seleção do item em uma função booliana `TableSection`. O arquivo code-behind remove essa seção do `TableView` ou adiciona novamente com base na propriedade Boolean.

### <a name="a-tableview-menu"></a>Um menu do modo de tabela

Outro uso de um `TableView` é um menu. O [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) exemplo demonstra um menu que permite que você mova um pouco `BoxView` pela tela.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 19 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Exemplos de capítulo 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
