---
title: Xamarin. Forms CollectionView EmptyView
description: Em CollectionView, um modo de exibição vazio pode ser especificado que fornece comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou vários modos de exibição.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 78e9ddcb1d9dd91dadea94016b206867ac9508e6
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048180"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin. Forms CollectionView EmptyView

![](~/media/shared/preview.png "Essa API é atualmente pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` Define as propriedades a seguir que podem ser usadas para fornecer comentários do usuário quando não há nenhum dado para exibir:

- `EmptyView`, do tipo `object`, a cadeia de caracteres, a associação ou o modo de exibição que será exibido quando o `ItemsSource` é de propriedade `null`, ou quando a coleção especificada pelo `ItemsSource` é de propriedade `null` ou está vazio. O valor padrão é `null`.
- `EmptyViewTemplate`, do tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), o modelo a ser usado para formatar especificado `EmptyView`. O valor padrão é `null`.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

Os cenários de uso principal para a configuração de `EmptyView` propriedade estiver exibindo comentários do usuário quando uma operação de filtragem em um `CollectionView` produz sem dados e exibir comentários do usuário, enquanto os dados estão sendo recuperados de um serviço web.

> [!NOTE]
> O `EmptyView` propriedade pode ser definida como uma exibição que inclui conteúdo interativo, se necessário.

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Exibir uma cadeia de caracteres quando dados não estão disponíveis

O `EmptyView` propriedade pode ser definida como uma cadeia de caracteres, que será exibido quando o `ItemsSource` é de propriedade `null`, ou quando a coleção especificada pelo `ItemsSource` é de propriedade `null` ou está vazio. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

O resultado é que, como os dados associados a coleção é `null`, a cadeia de caracteres é definido como o `EmptyView` valor da propriedade é exibido:

[![Captura de tela de uma lista vertical de CollectionView com uma exibição vazia de texto, no iOS e Android](emptyview-images/null-itemssource.png "CollectionView de lista vertical com o modo de exibição de texto vazio")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView de lista vertical com texto vazio modo de exibição")

## <a name="display-views-when-data-is-unavailable"></a>Exibir modos de exibição quando dados não estão disponíveis

O `EmptyView` propriedade pode ser definida como um modo de exibição, que será exibido quando o `ItemsSource` é de propriedade `null`, ou quando a coleção especificada pelo `ItemsSource` é de propriedade `null` ou está vazio. Isso pode ser uma única exibição ou uma exibição que contém vários modos de exibição filho. A exemplo XAML a seguir mostra o `EmptyView` propriedade definida como uma exibição que contém vários modos de exibição filho:

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

O código C# equivalente é:

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

Quando o [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo `CollectionView` é filtrado para o termo de pesquisa são armazenados no [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade. Se a operação de filtragem não produz nenhum dado, o [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) definido como o `EmptyView` valor da propriedade é exibido:

[![Captura de tela de uma lista vertical de CollectionView com exibição personalizada de vazia no iOS e Android](emptyview-images/filter-multiple-views.png "CollectionView de lista vertical com o modo de exibição personalizado vazio")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView de lista vertical com personalizado modo de exibição vazio")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Exibir um tipo de modelo personalizado quando dados não estão disponíveis

O `EmptyView` propriedade pode ser definida como um tipo personalizado, cujo modelo é exibido quando o `ItemsSource` é de propriedade `null`, ou quando a coleção especificada pelo `ItemsSource` é de propriedade `null` ou está vazio. O `EmptyViewTemplate` propriedade pode ser definida como uma [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que define a aparência do `EmptyView`. O XAML a seguir mostra um exemplo desse cenário:

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

O código C# equivalente é:

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

O `FilterData` tipo define uma `Filter` propriedade e um correspondente [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty):

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

O `EmptyView` estiver definida como uma `FilterData` objeto e o `Filter` associa dados de propriedade para o [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade. Quando o [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo `CollectionView` é filtrado para o termo de pesquisa são armazenados no `Filter` propriedade. Se a operação de filtragem não produz nenhum dado, o [ `Label` ](xref:Xamarin.Forms.Label) definidos no [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), que é definido como o `EmptyViewTemplate` valor da propriedade, é exibida:

[![Captura de tela de uma lista vertical de CollectionView com um modelo de exibição vazio, no iOS e Android](emptyview-images/emptyviewtemplate.png "CollectionView de lista vertical com o modelo de exibição vazio")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView de lista vertical com modelo de exibição vazio")

> [!NOTE]
> Ao exibir um tipo de modelo personalizado quando dados não estiverem disponíveis, o `EmptyViewTemplate` propriedade pode ser definida como uma exibição que contém vários modos de exibição filho.

## <a name="choose-an-emptyview-at-runtime"></a>Escolha um EmptyView em tempo de execução

Modos de exibição que serão exibidos como uma `EmptyView` quando os dados não estiverem disponíveis, pode ser definida como [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos em um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). O `EmptyView` propriedade, em seguida, pode ser definida para um determinado `ContentView`, com base em alguma lógica de negócios, em tempo de execução. O exemplo XAML a seguir mostra um exemplo desse cenário:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
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

Esse XAML define dois [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos no nível da página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), com o [ `Switch` ](xref:Xamarin.Forms.Switch) objeto controlando quais `ContentView` objeto será definido como o `EmptyView` valor da propriedade. Quando o [ `Switch` ](xref:Xamarin.Forms.Switch) é alternado, o `OnEmptyViewSwitchToggled` manipulador de eventos executa o `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

O `ToggleEmptyView` método define o `EmptyView` propriedade da `collectionView` objeto para um dos dois [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos armazenados no [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), com base no valor da [ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propriedade. Quando o [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo `CollectionView` é filtrado para o termo de pesquisa são armazenados no [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade. Se a operação de filtragem não produz nenhum dado, o `ContentView` objeto definido como o `EmptyView` propriedade é exibida:

[![Captura de tela de uma lista vertical de CollectionView com exibições vazias trocadas, no iOS e Android](emptyview-images/swap.png "CollectionView de lista vertical com modos de exibição vazios trocadas")](emptyview-images/swap-large.png#lightbox "CollectionView de lista vertical com trocado modos de exibição vazios")

Para obter mais informações sobre dicionários de recursos, consulte [dicionários de recursos do xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Escolha um EmptyViewTemplate em tempo de execução

A aparência do `EmptyView` pode ser escolhido no tempo de execução, com base em seu valor, definindo o `CollectionView.EmptyViewTemplate` propriedade para um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) objeto:

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

O código C# equivalente é:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

O `EmptyView` estiver definida como o [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade e o `EmptyViewTemplate` estiver definida como um `SearchTermDataTemplateSelector` objeto.

Quando o [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo `CollectionView` é filtrado para o termo de pesquisa são armazenados no [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propriedade. Se a operação de filtragem não produz nenhum dado, o [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) escolhido pelo `SearchTermDataTemplateSelector` objeto é definido como o `EmptyViewTemplate` propriedade e exibido.

A exemplo a seguir mostra o `SearchTermDataTemplateSelector` classe:

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

O `SearchTermTemplateSelector` classe define `DefaultTemplate` e `OtherTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) propriedades que são definidas para modelos de dados diferentes. O `OnSelectTemplate` substituir retorna `DefaultTemplate`, que exibe uma mensagem para o usuário, quando a consulta de pesquisa não for igual a "xamarin". Quando a consulta de pesquisa é igual a "xamarin", o `OnSelectTemplate` substituir retorna `OtherTemplate`, que exibe uma mensagem básica para o usuário:

[![Captura de tela da seleção de modelo CollectionView runtime modo de exibição vazio, no iOS e Android](emptyview-images/datatemplateselector.png "seleção de modelo de exibição vazio de tempo de execução em um CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "modelo vazio do modo de exibição de tempo de execução seleção em um CollectionView")

Para obter mais informações sobre os seletores de modelo de dados, consulte [criar um xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Modelos de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dicionários de recursos do xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Criar um DataTemplateSelector xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
