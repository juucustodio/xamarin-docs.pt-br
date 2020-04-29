---
title: EmptyView CollectionView do Xamarin. Forms
description: Em CollectionView, um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando nenhum dado está disponível para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: d5a22d110e52a397827fb451bc16872b72293755
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517484"
---
# <a name="xamarinforms-collectionview-emptyview"></a>EmptyView CollectionView do Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define as seguintes propriedades que podem ser usadas para fornecer comentários do usuário quando não há dados a serem exibidos:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), do tipo `object`, a cadeia de caracteres, a associação ou a exibição que será exibida [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) quando a `null`propriedade for, ou quando a coleção especificada `ItemsSource` pela propriedade `null` for ou vazia. O valor padrão é `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), o modelo a ser usado para formatar o `EmptyView`especificado. O valor padrão é `null`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Os principais cenários de uso para definir [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) a propriedade são exibir comentários do usuário quando uma operação de [`CollectionView`](xref:Xamarin.Forms.CollectionView) filtragem em um não produz dados e exibindo comentários do usuário enquanto os dados estão sendo recuperados de um serviço Web.

> [!NOTE]
> A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como uma exibição que inclui conteúdo interativo, se necessário.

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Exibir uma cadeia de caracteres quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como uma cadeia de caracteres, que será exibida quando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a propriedade `null`for, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

O resultado é que, como a coleção associada a dados `null`é, a cadeia de caracteres [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) definida como o valor da propriedade é exibida:

[![Captura de tela de uma lista vertical CollectionView com uma exibição de texto vazio, no iOS e no Android](emptyview-images/null-itemssource.png "Lista vertical CollectionView com exibição de texto vazio")](emptyview-images/null-itemssource-large.png#lightbox "Lista vertical CollectionView com exibição de texto vazio")

## <a name="display-views-when-data-is-unavailable"></a>Exibir exibições quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade pode ser definida como uma exibição, que será exibida quando a propriedade for `null`, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. Isso pode ser uma exibição única ou uma exibição que contenha várias exibições filhas. O exemplo de XAML a seguir `EmptyView` mostra a propriedade definida como uma exibição que contém várias exibições filho:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CollectionView`](xref:Xamarin.Forms.CollectionView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) conjunto como o [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valor da propriedade será exibido:

[![Captura de tela de uma lista vertical CollectionView com exibição vazia personalizada, no iOS e no Android](emptyview-images/filter-multiple-views.png "Lista vertical CollectionView com exibição vazia personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "Lista vertical CollectionView com exibição vazia personalizada")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Exibir um tipo personalizado de modelo quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como um tipo personalizado, cujo modelo é exibido quando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade é `null`, ou quando a coleção especificada pela `ItemsSource` propriedade é `null` ou vazia. A [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade pode ser definida como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência do `EmptyView`. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

O `FilterData` tipo define uma `Filter` Propriedade e um correspondente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty):

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade é definida como um `FilterData` objeto e os dados `Filter` de propriedade são associados à [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CollectionView`](xref:Xamarin.Forms.CollectionView) é filtrada para o termo de pesquisa armazenado na `Filter` propriedade. Se a operação de filtragem não gerar dados, o [`Label`](xref:Xamarin.Forms.Label) definido no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que é definido como o valor [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) da propriedade, será exibido:

[![Captura de tela de uma lista vertical CollectionView com um modelo de exibição vazio, no iOS e no Android](emptyview-images/emptyviewtemplate.png "Lista vertical de CollectionView com modelo de exibição vazio")](emptyview-images/emptyviewtemplate-large.png#lightbox "Lista vertical de CollectionView com modelo de exibição vazio")

> [!NOTE]
> Ao exibir um tipo personalizado de modelo quando os dados não estão disponíveis, [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) a propriedade pode ser definida como uma exibição que contém várias exibições filho.

## <a name="choose-an-emptyview-at-runtime"></a>Escolha um EmptyView em tempo de execução

Exibições que serão exibidas como um [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) quando os dados estiverem indisponíveis, podem ser definidas [`ContentView`](xref:Xamarin.Forms.ContentView) como objetos em [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)um. A `EmptyView` propriedade pode então ser definida como um específico `ContentView`, com base em alguma lógica de negócios, em tempo de execução. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

Esse XAML define dois [`ContentView`](xref:Xamarin.Forms.ContentView) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)objetos no nível de página, com [`Switch`](xref:Xamarin.Forms.Switch) o objeto que controla qual `ContentView` objeto será definido como o valor [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) da propriedade. Quando o [`Switch`](xref:Xamarin.Forms.Switch) é alternado, o `OnEmptyViewSwitchToggled` manipulador de eventos executa o `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

O `ToggleEmptyView` método define a [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) Propriedade do `collectionView` objeto como um dos dois [`ContentView`](xref:Xamarin.Forms.ContentView) objetos armazenados no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), com base no valor da [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade. Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CollectionView`](xref:Xamarin.Forms.CollectionView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o `ContentView` objeto definido como a `EmptyView` propriedade será exibido:

[![Captura de tela de uma lista vertical CollectionView com exibições vazias trocadas, no iOS e no Android](emptyview-images/swap.png "Lista vertical CollectionView com exibições vazias trocadas")](emptyview-images/swap-large.png#lightbox "Lista vertical CollectionView com exibições vazias trocadas")

Para obter mais informações sobre dicionários de recursos, consulte [dicionários de recursos do Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Escolha um EmptyViewTemplate em tempo de execução

A aparência do [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser escolhida em tempo de execução, com base em seu valor, definindo [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) a propriedade como [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) um objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade é definida como a [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) Propriedade e a [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade é definida como um `SearchTermDataTemplateSelector` objeto.

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CollectionView`](xref:Xamarin.Forms.CollectionView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) escolhido pelo `SearchTermDataTemplateSelector` objeto será definido como a [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) Propriedade e exibido.

O exemplo a seguir mostra `SearchTermDataTemplateSelector` a classe:

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

A `SearchTermTemplateSelector` classe define `DefaultTemplate` e `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriedades que são definidas para modelos de dados diferentes. A `OnSelectTemplate` substituição retorna `DefaultTemplate`, que exibe uma mensagem para o usuário, quando a consulta de pesquisa não é igual a "xamarin". Quando a consulta de pesquisa é igual a "xamarin", `OnSelectTemplate` a substituição `OtherTemplate`retorna, que exibe uma mensagem básica ao usuário:

[![Captura de tela de uma seleção de modelo de exibição vazia do tempo de execução CollectionView, no iOS e no Android](emptyview-images/datatemplateselector.png "Seleção de modelo de exibição vazia em tempo de execução em um CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Seleção de modelo de exibição vazia em tempo de execução em um CollectionView")

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dicionários de recursos do Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Criar um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
