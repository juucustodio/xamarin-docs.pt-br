---
title: Xamarin. Forms CarouselView EmptyView
description: No CarouselView, é possível especificar uma exibição vazia que fornece comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2019
ms.openlocfilehash: 55944b422495c9c3a7c93c6a2eab90a2db790780
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697566"
---
# <a name="xamarinforms-carouselview-emptyview"></a>Xamarin. Forms CarouselView EmptyView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as seguintes propriedades que podem ser usadas para fornecer comentários do usuário quando não há dados a serem exibidos:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), do tipo `object`, a cadeia de caracteres, a associação ou a exibição que será exibida quando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for `null` ou quando a coleção especificada pela propriedade `ItemsSource` for `null` ou vazia. O valor padrão é `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), o modelo a ser usado para formatar o `EmptyView` especificado. O valor padrão é `null`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Os principais cenários de uso para definir a propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) são exibir comentários do usuário quando uma operação de filtragem em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) não produz dados e exibe comentários do usuário enquanto os dados estão sendo recuperados de um serviço Web.

> [!NOTE]
> A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser definida como uma exibição que inclui conteúdo interativo, se necessário.

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Exibir uma cadeia de caracteres quando os dados estiverem indisponíveis

A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser definida como uma cadeia de caracteres, que será exibida quando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for `null` ou quando a coleção especificada pela propriedade `ItemsSource` for `null` ou vazia. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<CarouselView ItemsSource="{Binding EmptyMonkeys}"
              EmptyView="No items to display." />
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    EmptyView = "No items to display."
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

O resultado é que, como a coleção associada a dados é `null`, a cadeia de caracteres definida como o valor da propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) é exibida.

## <a name="display-views-when-data-is-unavailable"></a>Exibir exibições quando os dados estiverem indisponíveis

A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser definida como uma exibição, que será exibida quando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for `null` ou quando a coleção especificada pela propriedade `ItemsSource` for `null` ou vazia. Isso pode ser uma exibição única ou uma exibição que contenha várias exibições filhas. O exemplo de XAML a seguir mostra a propriedade `EmptyView` definida como uma exibição que contém várias exibições filho:

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
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
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
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
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na propriedade [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se a operação de filtragem não gerar dados, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) definido como o valor da propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) será exibido.

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Exibir um tipo personalizado de modelo quando os dados estiverem indisponíveis

A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser definida como um tipo personalizado, cujo modelo é exibido quando a propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) é `null` ou quando a coleção especificada pela propriedade `ItemsSource` é `null` ou vazia. A propriedade [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) pode ser definida como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência da `EmptyView`. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <controls:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CarouselView.EmptyView>
        <CarouselView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CarouselView.EmptyViewTemplate>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

O tipo de `FilterData` define uma propriedade `Filter` e uma [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)correspondente:

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

A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) é definida como um objeto `FilterData` e os dados da propriedade `Filter` são associados à propriedade [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na propriedade `Filter`. Se a operação de filtragem não gerar dados, o [`Label`](xref:Xamarin.Forms.Label) definido na [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que é definido como o valor da propriedade [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) , será exibido.

> [!NOTE]
> Ao exibir um tipo personalizado de modelo quando os dados não estão disponíveis, a propriedade [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) pode ser definida como uma exibição que contém várias exibições filho.

## <a name="choose-an-emptyview-at-runtime"></a>Escolha um EmptyView em tempo de execução

Exibições que serão exibidas como um [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) quando os dados estiverem indisponíveis, podem ser definidos como objetos [`ContentView`](xref:Xamarin.Forms.ContentView) em uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). A propriedade `EmptyView` pode então ser definida como uma `ContentView` específica, com base em alguma lógica de negócios, em tempo de execução. O exemplo de XAML a seguir mostra um exemplo desse cenário:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:viewmodels="clr-namespace:CarouselViewDemos.ViewModels"
             x:Class="CarouselViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.BindingContext>
        <viewmodels:MonkeysViewModel />
    </ContentPage.BindingContext>
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
        <SearchBar SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CarouselView x:Name="carouselView"
                      ItemsSource="{Binding Monkeys}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Esse XAML define dois objetos [`ContentView`](xref:Xamarin.Forms.ContentView) no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de nível de página, com o objeto [`Switch`](xref:Xamarin.Forms.Switch) controlando qual `ContentView` objeto será definido como o valor da propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Quando o [`Switch`](xref:Xamarin.Forms.Switch) é alternado, o manipulador de eventos `OnEmptyViewSwitchToggled` executa o método `ToggleEmptyView`:

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

O método `ToggleEmptyView` define a propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) do objeto `carouselView` como um dos dois objetos [`ContentView`](xref:Xamarin.Forms.ContentView) armazenados no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), com base no valor da propriedade [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) . Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na propriedade [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se a operação de filtragem não gerar dados, o objeto `ContentView` definido como a propriedade `EmptyView` será exibido.

Para obter mais informações sobre dicionários de recursos, consulte [dicionários de recursos do Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Escolha um EmptyViewTemplate em tempo de execução

A aparência da [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser escolhida em tempo de execução, com base em seu valor, definindo a propriedade [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) como um objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls">
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
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <CarouselView ItemsSource="{Binding Monkeys}"
                      EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                      EmptyViewTemplate="{StaticResource SearchSelector}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView()
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

A propriedade [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) é definida como a propriedade [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) e a propriedade [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) é definida como um objeto `SearchTermDataTemplateSelector`.

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand`, a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na propriedade [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se a operação de filtragem não gerar dados, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) escolhido pelo objeto `SearchTermDataTemplateSelector` será definido como a propriedade [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) e exibido.

O exemplo a seguir mostra a classe `SearchTermDataTemplateSelector`:

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

A classe `SearchTermTemplateSelector` define `DefaultTemplate` e `OtherTemplate` propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definidas para modelos de dados diferentes. A substituição de `OnSelectTemplate` retorna `DefaultTemplate`, que exibe uma mensagem para o usuário, quando a consulta de pesquisa não é igual a "xamarin". Quando a consulta de pesquisa é igual a "xamarin", a substituição de `OnSelectTemplate` retorna `OtherTemplate`, que exibe uma mensagem básica ao usuário.

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dicionários de recursos do Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Criar um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
