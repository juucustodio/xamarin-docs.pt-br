---
title: SearchBar Xamarin. Forms
description: O SearchBar Xamarin. Forms é um controle de entrada do usuário que é usado para iniciar uma pesquisa. O controle SearchBar dá suporte a texto de espaço reservado, entrada de consulta, execução e cancelamento. Este artigo explica como usar um SearchBar em XAML e código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 41bb9e082f042e7ca2933d72b4b71a4ff6c4fef4
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658031"
---
# <a name="xamarinforms-searchbar"></a>SearchBar Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

O Xamarin. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) é um controle de entrada do usuário usado para iniciar uma pesquisa. O `SearchBar` controle dá suporte a texto de espaço reservado, entrada de consulta, execução de pesquisa e cancelamento. A captura de tela a `SearchBar` seguir mostra uma consulta com os `ListView`resultados exibidos em um:

[ ![Captura de tela de Searchbar em Ios e Android](searchbar-images/device-searchbars-cropped.png "Searchbar no Ios e Android") ] (searchbar-images/device-searchbars.png#lightbox "Searchbar no Ios e Android")

A `SearchBar` classe define as seguintes propriedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)é um `Color` que define a cor do botão de cancelamento.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)é um `FontAttributes` valor de enumeração que determina se `SearchBar` a fonte é negrito, itálico ou nenhuma.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)é um `string` que determina a família de fontes usada `SearchBar`pelo.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)pode ser um `NamedSize` valor de enumeração ou `double` um valor que represente tamanhos de fonte específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)é um `TextAlignment` valor de enumeração que define o alinhamento horizontal do texto da consulta.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)é um `string` que define o texto do espaço reservado, como "Search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)é um `Color` que define a cor do texto do espaço reservado.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)é um `object` que especifica o parâmetro que deve ser passado para o `SearchCommand`.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)é um `string` que contém o texto da consulta `SearchBar`no.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)é um `Color` que define a cor do texto da consulta.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa `SearchBar` que o pode ser personalizado e ser o destino de associações de dados. Especificar propriedades de fonte no `SearchBar` é consistente com a personalização de texto em outros [controles de texto do Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Para obter mais informações, consulte [fontes no Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Criar um SearchBar

Um `SearchBar` pode ser instanciado em XAML. Sua propriedade `Placeholder` opcional pode ser definida para definir o texto de dica na caixa de entrada de consulta. O valor padrão para `Placeholder` é uma cadeia de caracteres vazia, portanto nenhum espaço reservado será exibido se não estiver definido. O exemplo a seguir mostra como criar uma `SearchBar` instância de em XAML com `Placeholder` o conjunto de propriedades opcional:

```xaml
<SearchBar Placeholder="Search items..." />
```

Um `SearchBar` também pode ser criado no código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriedades de aparência do SearchBar

O `SearchBar` controle define muitas propriedades que personalizam a aparência do controle. O exemplo a seguir mostra como criar uma `SearchBar` instância de em XAML com várias propriedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Essas propriedades também podem ser especificadas ao criar um `SearchBar` objeto no código:

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

A captura de tela a seguir `SearchBar` mostra o controle resultante:

[ ![Captura de tela de Searchbar personalizado no Ios e Android](searchbar-images/device-searchbars-styled-cropped.png "Searchbar personalizado no Ios e no Android") ] (searchbar-images/device-searchbars-styled.png#lightbox "Searchbar personalizado no Ios e no Android")

## <a name="perform-a-search-with-event-handlers"></a>Executar uma pesquisa com manipuladores de eventos

Uma pesquisa pode ser executada usando o `SearchBar` controle anexando um manipulador de eventos a um dos seguintes eventos:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)é chamado quando o usuário clica no botão de pesquisa ou pressiona a tecla Enter.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)é chamado sempre que o texto na caixa de consulta é alterado.

O exemplo a seguir mostra um manipulador de eventos anexado `TextChanged` ao evento em XAML e usa `ListView` um para exibir os resultados da pesquisa:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Um manipulador de eventos também pode ser anexado a `SearchBar` um criado no código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

O `TextChanged` manipulador de eventos no arquivo code-behind é o mesmo, independentemente de `SearchBar` o ser criado via XAML ou código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

O exemplo anterior implica a existência de uma `DataService` classe com um `GetSearchResults` método capaz de retornar itens que correspondam a uma consulta. O `SearchBar` valor da `Text` Propriedade do controle é passado para `GetSearchResults` o método e o resultado é usado para atualizar `ListView` a propriedade `ItemsSource` do controle. O efeito geral é que os resultados da pesquisa são exibidos `ListView` no controle.

O aplicativo de exemplo fornece `DataService` uma implementação de classe que pode ser usada para testar a funcionalidade de pesquisa.

## <a name="perform-a-search-using-a-viewmodel"></a>Executar uma pesquisa usando um ViewModel

Uma pesquisa pode ser executada sem manipuladores de eventos associando `SearchCommand` as `SearchCommandParameter` Propriedades e `ICommand` às implementações. O projeto de exemplo demonstra essas implementações usando o padrão Model-View-ViewModel (MVVM). Para obter mais informações sobre associações de dados com MVVM, consulte [associações de dados com MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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
> O ViewModel pressupõe a existência de uma `DataService` classe capaz de executar pesquisas. A `DataService` classe, incluindo dados de exemplo, está disponível no aplicativo de exemplo.

O XAML a seguir mostra como associar um `SearchBar` ao ViewModel de exemplo, com um `ListView` controle exibindo os resultados da pesquisa:

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

Este exemplo define o `BindingContext` para ser uma instância `SearchViewModel` da classe. Ele `SearchCommand` associa a `SearchCommandParameter` propriedade `PerformSearch` `SearchBar` ao no ViewModel e associa a `Text` Propriedade à propriedade de. `ICommand` A `ListView.ItemsSource` propriedade é associada `SearchResults` à propriedade do ViewModel.

Para obter mais informações sobre `ICommand` a interface e associações, consulte [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [a interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações do SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controles de texto Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Fontes no Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
