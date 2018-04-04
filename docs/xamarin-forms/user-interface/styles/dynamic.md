---
title: Estilos dinâmicos
description: Estilos não responder a alterações de propriedade e permanecem inalterados para a duração de um aplicativo. Por exemplo, depois de atribuir um estilo para um elemento visual, se uma das instâncias de Setter é modificada, removidos ou uma nova instância de Setter adicionada, as alterações não aplicadas ao elemento visual. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: c484bdc90ec039a8d70209deabbe283cf7100610
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="dynamic-styles"></a>Estilos dinâmicos

_Estilos não responder a alterações de propriedade e permanecem inalterados para a duração de um aplicativo. Por exemplo, depois de atribuir um estilo para um elemento visual, se uma das instâncias de Setter é modificada, removidos ou uma nova instância de Setter adicionada, as alterações não aplicadas ao elemento visual. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos._

O `DynamicResource` extensão de marcação é semelhante do `StaticResource` extensão de marcação em que ambos usam uma chave de dicionário para buscar um valor de um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). No entanto, enquanto o `StaticResource` executa uma pesquisa de dicionário único, o `DynamicResource` mantém um link para a chave de dicionário. Portanto, se a entrada de dicionário associada com a chave for substituída, a alteração será aplicada ao elemento visual. Isso permite que as alterações de estilo de tempo de execução a ser feita em um aplicativo.

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

O [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) instâncias use o `DynamicResource` extensão de marcação para referenciar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) chamado `searchBarStyle`, que não está definido no XAML. No entanto, como o [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades do `SearchBar` instâncias são definidas usando um `DynamicResource`, a chave de dicionário ausente não resulta em uma exceção sendo lançada.

Em vez disso, o arquivo code-behind, o construtor cria um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) entrada com a chave `searchBarStyle`, conforme mostrado no exemplo de código a seguir:

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

[![](dynamic-images/dynamic-style-blue.png "Azul exemplo de estilos dinâmica")](dynamic-images/dynamic-style-blue-large.png#lightbox "azul exemplo de estilos dinâmica")
[![](dynamic-images/dynamic-style-green.png "verde estilos dinâmica exemplo") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verde estilo dinâmico de exemplo")

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
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button  }
        };
    }
    ...
}
```

No c#, o [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) instâncias use o [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) método para fazer referência a `searchBarStyle`. O `OnButtonClicked` código de manipulador de eventos é idêntico ao exemplo XAML e quando executada, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`.

<a name="dynamic-style-inheritance">

## <a name="dynamic-style-inheritance"></a>Herança de estilos dinâmica

Derivar um estilo de um estilo dinâmico não pode ser obtida usando o [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propriedade. Em vez disso, o [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) classe inclui o [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propriedade, que pode ser definida como uma chave de dicionário cujo valor pode ser alterado dinamicamente.

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

O [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) instâncias use o `StaticResource` extensão de marcação para referenciar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) chamado `tealSearchBarStyle`. Isso `Style` define algumas propriedades adicionais e usa o [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propriedade para fazer referência a `searchBarStyle`. O `DynamicResource` extensão de marcação não é necessária porque `tealSearchBarStyle` não será alterado, exceto para o `Style` deriva de. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo de base é alterado.

O arquivo code-behind, o construtor cria um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) entrada com a chave `searchBarStyle`, como por exemplo anterior que demonstrado estilos dinâmicos. Quando o `OnButtonClicked` manipulador de eventos é executado, `searchBarStyle` alternará entre `blueSearchBarStyle` e `greenSearchBarStyle`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Azul exemplo de herança de estilos dinâmica")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "azul exemplo de herança de estilos dinâmica")
[![](dynamic-images/dynamic-style-inheritance-green.png "verde estilos dinâmica Exemplo de herança")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verde exemplo de herança de estilos dinâmica")

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

O `tealSearchBarStyle` é atribuído diretamente para o [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedade o [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) instâncias. Isso `Style` define algumas propriedades adicionais e usa o [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propriedade para fazer referência a `searchBarStyle`. O [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) método não é necessário aqui porque `tealSearchBarStyle` não será alterado, exceto para o `Style` deriva de. Portanto, `tealSearchBarStyle` mantém um link para `searchBarStyle` e é alterado quando o estilo de base é alterado.

## <a name="summary"></a>Resumo

Estilos não responder a alterações de propriedade e permanecem inalterados para a duração de um aplicativo. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos. Além disso, *dinâmico* estilos podem ser derivados de com o [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propriedade.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinâmicos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabalhando com estilos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Style](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
