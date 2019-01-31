---
title: Estilos dinâmicos no xamarin. Forms
description: Este artigo explica como um aplicativo xamarin. Forms pode responder a alterações de estilo dinamicamente em tempo de execução usando os recursos dinâmicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 260c215df52eb31139998438cc0eda10a887be65
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291954"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinâmicos no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Estilos não responder a alterações de propriedade e permanecem inalterados durante o período de um aplicativo. Por exemplo, depois de atribuir um estilo a um elemento visual, se uma das instâncias de Setter for modificada, removido, ou uma nova instância de Setter adicionada, as alterações não aplicadas para o elemento visual. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando os recursos dinâmicos._

O `DynamicResource` extensão de marcação é semelhante de `StaticResource` extensão de marcação em que ambos usam uma chave de dicionário para buscar um valor de uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). No entanto, enquanto o `StaticResource` executa uma pesquisa de dicionário único, o `DynamicResource` mantém um link para a chave do dicionário. Portanto, se a entrada de dicionário associada com a chave for substituída, a alteração é aplicada ao elemento visual. Isso permite que as alterações de estilo de tempo de execução a ser feita em um aplicativo.

O exemplo de código a seguir demonstra *dinâmico* estilos em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instâncias uso o `DynamicResource` extensão de marcação para fazer referência a um [ `Style` ](xref:Xamarin.Forms.Style) chamado `searchBarStyle`, que não é definido no XAML. No entanto, porque o [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades do `SearchBar` instâncias são definidas usando um `DynamicResource`, a chave de dicionário ausente não resulta em uma exceção sendo lançada.

Em vez disso, no arquivo code-behind, o construtor cria um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrada com a chave `searchBarStyle`, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Quando o `OnButtonClicked` manipulador de eventos é executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](dynamic-images/dynamic-style-blue.png "Estilo dinâmico de exemplo de azul")](dynamic-images/dynamic-style-blue-large.png#lightbox "azul estilo dinâmico de exemplo")
[![](dynamic-images/dynamic-style-green.png "verde estilo dinâmico de exemplo") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verde estilo dinâmico de exemplo")

O exemplo de código a seguir demonstra a página equivalente em c#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

No c#, o [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instâncias de uso de [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) método a referenciar `searchBarStyle`. O `OnButtonClicked` código do manipulador de eventos é idêntico ao exemplo XAML e, quando executada, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Herança de estilo dinâmica

Derivar um estilo de um estilo dinâmico não pode ser feito usando o [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propriedade. Em vez disso, o [ `Style` ](xref:Xamarin.Forms.Style) classe inclui os [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade, que pode ser definida como uma chave de dicionário cujo valor pode alterar dinamicamente.

O exemplo de código a seguir demonstra *dinâmico* herança de estilo em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instâncias de uso de `StaticResource` extensão de marcação para fazer referência a um [ `Style` ](xref:Xamarin.Forms.Style) chamado `tealSearchBarStyle`. Isso `Style` define algumas propriedades adicionais e usa o [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade a referenciar `searchBarStyle`. O `DynamicResource` extensão de marcação não é necessária porque `tealSearchBarStyle` não será alterado, exceto para o `Style` deriva. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo de base é alterado.

No arquivo code-behind, o construtor cria um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) entrada com a chave `searchBarStyle`, de acordo com o exemplo anterior que demonstrou estilos dinâmicos. Quando o `OnButtonClicked` manipulador de eventos é executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Exemplo de herança de estilo dinâmica de azul")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "azul exemplo de herança de estilo dinâmica")
[![](dynamic-images/dynamic-style-inheritance-green.png "verde estilos dinâmica Exemplo de herança")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verde de exemplo de herança de estilo dinâmica")

O exemplo de código a seguir demonstra a página equivalente em c#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

O `tealSearchBarStyle` é atribuído diretamente para o [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade do [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) instâncias. Isso `Style` define algumas propriedades adicionais e usa o [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade a referenciar `searchBarStyle`. O [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) método não é necessário aqui porque `tealSearchBarStyle` não será alterado, exceto para o `Style` deriva. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo de base é alterado.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinâmicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabalhar com estilos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
