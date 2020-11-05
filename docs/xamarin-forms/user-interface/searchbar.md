---
title: Xamarin.Forms SearchBar
description: O Xamarin.Forms Searchbar é um controle de entrada do usuário que é usado para iniciar uma pesquisa. O controle SearchBar dá suporte a texto de espaço reservado, entrada de consulta, execução e cancelamento. Este artigo explica como usar um SearchBar em XAML e código.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 66ffbe0f45754517610a2fc2858a00a6185e1d45
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369123"
---
# <a name="no-locxamarinforms-searchbar"></a>Xamarin.Forms SearchBar

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

O Xamarin.Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) é um controle de entrada de usuário usado para iniciar uma pesquisa. O `SearchBar` controle dá suporte a texto de espaço reservado, entrada de consulta, execução de pesquisa e cancelamento. A captura de tela a seguir mostra uma `SearchBar` consulta com os resultados exibidos em um `ListView` :

[![Captura de tela de SearchBar no iOS e Android](searchbar-images/device-searchbars-cropped.png "SearchBar no iOS e Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar no iOS e Android")

A `SearchBar` classe define as seguintes propriedades:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) é um `Color` que define a cor do botão de cancelamento.
* `CharacterSpacing`, do tipo `double` , é o espaçamento entre os caracteres do `SearchBar` texto.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) é um `FontAttributes` valor de enumeração que determina se a `SearchBar` fonte é negrito, itálico ou nenhuma.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) é um `string` que determina a família de fontes usada pelo `SearchBar` .
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) pode ser um `NamedSize` valor de enumeração ou um `double` valor que represente tamanhos de fonte específicos entre plataformas.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) é um `TextAlignment` valor de enumeração que define o alinhamento horizontal do texto da consulta.
* `VerticalTextAlignment` é um `TextAlignment` valor de enumeração que define o alinhamento vertical do texto da consulta.
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) é um `string` que define o texto do espaço reservado, como "Search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) é um `Color` que define a cor do texto do espaço reservado.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) é um `object` que especifica o parâmetro que deve ser passado para o `SearchCommand` .
* [`Text`](xref:Xamarin.Forms.InputView.Text) é um `string` que contém o texto da consulta no `SearchBar` .
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) é um `Color` que define a cor do texto da consulta.
* `TextTransform` é um `TextTransform` valor que determina a capitalização do `SearchBar` texto.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `SearchBar` pode ser personalizado e ser o destino de associações de dados. Especificar propriedades de fonte no `SearchBar` é consistente com a personalização de texto em outros [ Xamarin.Forms controles de texto](~/xamarin-forms/user-interface/text/index.md). Para obter mais informações, consulte [fontes Xamarin.Forms em ](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Criar um SearchBar

Um `SearchBar` pode ser instanciado em XAML. Sua `Placeholder` propriedade opcional pode ser definida para definir o texto de dica na caixa de entrada de consulta. O valor padrão para `Placeholder` é uma cadeia de caracteres vazia, portanto nenhum espaço reservado será exibido se não estiver definido. O exemplo a seguir mostra como criar uma instância de `SearchBar` em XAML com o `Placeholder` conjunto de propriedades opcional:

```xaml
<SearchBar Placeholder="Search items..." />
```

Um `SearchBar` também pode ser criado no código:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriedades de aparência do SearchBar

O `SearchBar` controle define muitas propriedades que personalizam a aparência do controle. O exemplo a seguir mostra como criar uma instância de `SearchBar` em XAML com várias propriedades especificadas:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           TextTransform="Lowercase"
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
    TextTransform = TextTransform.Lowercase,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

A captura de tela a seguir mostra o `SearchBar` controle resultante:

[![Captura de tela de SearchBar personalizado no iOS e no Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personalizado no iOS e no Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personalizado no iOS e no Android")

> [!NOTE]
> No iOS, a `SearchBarRenderer` classe contém um método substituível `UpdateCancelButton` . Esse método controla quando o botão Cancelar é exibido e pode ser substituído em um renderizador personalizado. Para obter mais informações sobre renderizadores personalizados, consulte [ Xamarin.Forms renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Executar uma pesquisa com manipuladores de eventos

Uma pesquisa pode ser executada usando o `SearchBar` controle anexando um manipulador de eventos a um dos seguintes eventos:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) é chamado quando o usuário clica no botão de pesquisa ou pressiona a tecla Enter.
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) é chamado sempre que o texto na caixa de consulta é alterado.

O exemplo a seguir mostra um manipulador de eventos anexado ao `TextChanged` evento em XAML e usa um `ListView` para exibir os resultados da pesquisa:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Um manipulador de eventos também pode ser anexado a um `SearchBar` criado no código:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

O `TextChanged` manipulador de eventos no arquivo code-behind é o mesmo, independentemente de o `SearchBar` ser criado via XAML ou código:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

O exemplo anterior implica a existência de uma `DataService` classe com um `GetSearchResults` método capaz de retornar itens que correspondam a uma consulta. O `SearchBar` valor da `Text` Propriedade do controle é passado para o `GetSearchResults` método e o resultado é usado para atualizar a `ListView` Propriedade do controle `ItemsSource` . O efeito geral é que os resultados da pesquisa são exibidos no `ListView` controle.

O aplicativo de exemplo fornece uma `DataService` implementação de classe que pode ser usada para testar a funcionalidade de pesquisa.

## <a name="perform-a-search-using-a-viewmodel"></a>Executar uma pesquisa usando um ViewModel

Uma pesquisa pode ser executada sem manipuladores de eventos associando `SearchCommand` as `SearchCommandParameter` Propriedades e às `ICommand` implementações. O projeto de exemplo demonstra essas implementações usando o padrão Model-View-ViewModel (MVVM). Para obter mais informações sobre associações de dados com MVVM, consulte [associações de dados com MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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
                  ItemsSource="{Binding SearchResults}" />
    </StackLayout>
</ContentPage>
```

Este exemplo define o `BindingContext` para ser uma instância da `SearchViewModel` classe. Ele associa a `SearchCommand` propriedade ao `PerformSearch` `ICommand` no ViewModel e associa a `SearchBar` `Text` Propriedade à `SearchCommandParameter` propriedade de. A `ListView.ItemsSource` propriedade é associada à `SearchResults` Propriedade do ViewModel.

Para obter mais informações sobre a `ICommand` interface e associações, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [a interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações do SearchBar](/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin.Forms Controles de texto](~/xamarin-forms/user-interface/text/index.md)
* [Fontes em Xamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin.Forms Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)