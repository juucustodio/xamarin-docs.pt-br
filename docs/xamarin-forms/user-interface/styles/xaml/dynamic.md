---
title: Estilos dinâmicos no xamarin. Forms
description: Este artigo explica como um aplicativo xamarin. Forms pode responder a alterações de estilo dinamicamente em tempo de execução usando os recursos dinâmicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918696"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinâmicos no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Os estilos não respondem às alterações de propriedade e permanecem inalterados durante o período de um aplicativo. Por exemplo, depois de atribuir um estilo a um elemento visual, se uma das instâncias de setter for modificada, removida ou uma nova instância de setter adicionada, as alterações não serão aplicadas ao elemento visual. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos._

A extensão de marcação de `DynamicResource` é semelhante à extensão de marcação de `StaticResource`, pois ambos usam uma chave de dicionário para buscar um valor de um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). No entanto, enquanto o `StaticResource` executa uma única pesquisa de dicionário, o `DynamicResource` mantém um link para a chave de dicionário. Portanto, se a entrada de dicionário associada com a chave for substituída, a alteração é aplicada ao elemento visual. Isso permite que as alterações de estilo de tempo de execução a ser feita em um aplicativo.

O exemplo de código a seguir demonstra os estilos *dinâmicos* em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
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

As instâncias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) usam a extensão de marcação `DynamicResource` para fazer referência a um [`Style`](xref:Xamarin.Forms.Style) chamado `searchBarStyle`, que não está definido no XAML. No entanto, como as propriedades de [`Style`](xref:Xamarin.Forms.NavigableElement.Style) das instâncias de `SearchBar` são definidas usando uma `DynamicResource`, a chave de dicionário ausente não resulta em uma exceção sendo gerada.

Em vez disso, no arquivo code-behind, o construtor cria uma entrada [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) com a chave `searchBarStyle`, conforme mostrado no exemplo de código a seguir:

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

Quando o manipulador de eventos `OnButtonClicked` for executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![exemplo de estilo dinâmico azul](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![exemplo de estilo dinâmico verde](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

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

No C#, as instâncias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) usam o método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) para fazer referência a `searchBarStyle`. O código do manipulador de eventos `OnButtonClicked` é idêntico ao exemplo de XAML e, quando executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Herança de estilo dinâmico

A derivação de um estilo de um estilo dinâmico não pode ser obtida usando a propriedade [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) . Em vez disso, a classe [`Style`](xref:Xamarin.Forms.Style) inclui a propriedade [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) , que pode ser definida como uma chave de dicionário cujo valor pode ser alterado dinamicamente.

O exemplo de código a seguir demonstra a herança de estilo *dinâmico* em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
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

As instâncias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) usam a extensão de marcação `StaticResource` para fazer referência a um [`Style`](xref:Xamarin.Forms.Style) chamado `tealSearchBarStyle`. Este `Style` define algumas propriedades adicionais e usa a propriedade [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) para fazer referência a `searchBarStyle`. A extensão de marcação de `DynamicResource` não é necessária porque `tealSearchBarStyle` não será alterado, exceto pelo `Style` de onde deriva. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo base é alterado.

No arquivo code-behind, o construtor cria uma entrada de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) com a chave `searchBarStyle`, de acordo com o exemplo anterior que demonstrou estilos dinâmicos. Quando o manipulador de eventos `OnButtonClicked` for executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![exemplo de herança de estilo dinâmico azul](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![exemplo de herança de estilo dinâmico verde](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

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

O `tealSearchBarStyle` é atribuído diretamente à propriedade [`Style`](xref:Xamarin.Forms.NavigableElement.Style) das instâncias de [`SearchBar`](xref:Xamarin.Forms.SearchBar) . Este `Style` define algumas propriedades adicionais e usa a propriedade [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) para fazer referência a `searchBarStyle`. O método [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) não é necessário aqui porque `tealSearchBarStyle` não será alterado, exceto pelo `Style` de onde deriva. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo base é alterado.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinâmicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
