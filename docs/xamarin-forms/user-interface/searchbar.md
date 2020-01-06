---
title: SearchBar Xamarin. Forms
description: O SearchBar Xamarin. Forms é um controle de entrada do usuário que é usado para iniciar uma pesquisa. O controle SearchBar dá suporte a texto de espaço reservado, entrada de consulta, execução e cancelamento. Este artigo explica como usar um SearchBar em XAML e código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: 8888f1615f250a908930cec9058a54bd6e7fedc2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490071"
---
# <a name="xamarinforms-searchbar"></a>SearchBar Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

O [`SearchBar`](xref:Xamarin.Forms.SearchBar) Xamarin. Forms é um controle de entrada do usuário usado para iniciar uma pesquisa. O controle de `SearchBar` dá suporte a texto de espaço reservado, entrada de consulta, execução de pesquisa e cancelamento. A captura de tela a seguir mostra uma consulta `SearchBar` com os resultados exibidos em uma `ListView`:

[![Captura de tela de SearchBar no iOS e Android](searchbar-images/device-searchbars-cropped.png "SearchBar no iOS e Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar no iOS e Android")

A classe `SearchBar` define as seguintes propriedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) é uma `Color` que define a cor do botão de cancelamento.
* `CharacterSpacing`, do tipo `double`, é o espaçamento entre os caracteres do texto do `SearchBar`.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) é um valor de enumeração `FontAttributes` que determina se a fonte `SearchBar` é negrito, itálico ou não.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) é um `string` que determina a família de fontes usada pelo `SearchBar`.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) pode ser um valor de enumeração `NamedSize` ou um valor `double` que representa tamanhos de fonte específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) é um valor de enumeração `TextAlignment` que define o alinhamento horizontal do texto da consulta.
* `VerticalTextAlignment` é um valor de enumeração `TextAlignment` que define o alinhamento vertical do texto da consulta.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder) é uma `string` que define o texto do espaço reservado, como "Search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor) é uma `Color` que define a cor do texto do espaço reservado.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) é um `object` que especifica o parâmetro que deve ser passado para o `SearchCommand`.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text) é um `string` que contém o texto da consulta no `SearchBar`.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor) é uma `Color` que define a cor do texto da consulta.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `SearchBar` pode ser personalizado e ser o destino de associações de dados. Especificar propriedades de fonte na `SearchBar` é consistente com a personalização de texto em outros [controles de texto do Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Para obter mais informações, consulte [fontes no Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Criar um SearchBar

Um `SearchBar` pode ser instanciado em XAML. Sua propriedade de `Placeholder` opcional pode ser definida para definir o texto de dica na caixa de entrada de consulta. O valor padrão para o `Placeholder` é uma cadeia de caracteres vazia, portanto nenhum espaço reservado será exibido se não estiver definido. O exemplo a seguir mostra como criar uma instância de um `SearchBar` em XAML com a propriedade opcional `Placeholder` definida:

```xaml
<SearchBar Placeholder="Search items..." />
```

Um `SearchBar` também pode ser criado no código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriedades de aparência do SearchBar

O controle de `SearchBar` define muitas propriedades que personalizam a aparência do controle. O exemplo a seguir mostra como criar uma instância de um `SearchBar` em XAML com várias propriedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Essas propriedades também podem ser especificadas ao criar um objeto de `SearchBar` no código:

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

A captura de tela a seguir mostra o controle de `SearchBar` resultante:

[![Captura de tela de SearchBar personalizado no iOS e no Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personalizado no iOS e no Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personalizado no iOS e no Android")

> [!NOTE]
> No iOS, a classe `SearchBarRenderer` contém um método de `UpdateCancelButton` substituível. Esse método controla quando o botão Cancelar é exibido e pode ser substituído em um renderizador personalizado. Para obter mais informações sobre renderizadores personalizados, consulte [renderizadores personalizados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Executar uma pesquisa com manipuladores de eventos

Uma pesquisa pode ser executada usando o controle de `SearchBar` anexando um manipulador de eventos a um dos seguintes eventos:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) é chamado quando o usuário clica no botão de pesquisa ou pressiona a tecla Enter.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) é chamado sempre que o texto na caixa de consulta é alterado.

O exemplo a seguir mostra um manipulador de eventos anexado ao evento `TextChanged` em XAML e usa uma `ListView` para exibir os resultados da pesquisa:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Um manipulador de eventos também pode ser anexado a um `SearchBar` criado no código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

O manipulador de eventos `TextChanged` no arquivo code-behind é o mesmo, independentemente de o `SearchBar` ser criado via XAML ou código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

O exemplo anterior implica a existência de uma classe `DataService` com um método `GetSearchResults` capaz de retornar itens que correspondam a uma consulta. O valor da propriedade `Text` do controle de `SearchBar` é passado para o método `GetSearchResults` e o resultado é usado para atualizar a propriedade `ItemsSource` do controle de `ListView`. O efeito geral é que os resultados da pesquisa são exibidos no controle de `ListView`.

O aplicativo de exemplo fornece uma implementação de classe `DataService` que pode ser usada para testar a funcionalidade de pesquisa.

## <a name="perform-a-search-using-a-viewmodel"></a>Executar uma pesquisa usando um ViewModel

Uma pesquisa pode ser executada sem manipuladores de eventos associando as propriedades `SearchCommand` e `SearchCommandParameter` para `ICommand` implementações. O projeto de exemplo demonstra essas implementações usando o padrão Model-View-ViewModel (MVVM). Para obter mais informações sobre associações de dados com MVVM, consulte [associações de dados com MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

O ViewModel no aplicativo de exemplo contém o seguinte código:

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> O ViewModel pressupõe a existência de uma `DataService` classe capaz de executar pesquisas. A classe `DataService`, incluindo dados de exemplo, está disponível no aplicativo de exemplo.

O XAML a seguir mostra como associar um `SearchBar` ao ViewModel de exemplo, com um controle de `ListView` exibindo os resultados da pesquisa:

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

Este exemplo define o `BindingContext` como uma instância da classe `SearchViewModel`. Ele associa a propriedade `SearchCommand` à `PerformSearch` `ICommand` no ViewModel e associa a propriedade `SearchBar` `Text` à propriedade `SearchCommandParameter`. A propriedade `ListView.ItemsSource` é associada à propriedade `SearchResults` do ViewModel.

Para obter mais informações sobre a interface `ICommand` e associações, consulte [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [a interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações do SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controles de texto Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Fontes no Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
