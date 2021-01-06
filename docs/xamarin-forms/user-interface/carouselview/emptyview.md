---
title: Xamarin.Forms CarouselView EmptyView
description: No CarouselView, é possível especificar uma exibição vazia que fornece comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 26db8044111741c7198eb1b125986e20f55b6add
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940532"
---
# <a name="no-locxamarinforms-carouselview-emptyview"></a>Xamarin.Forms CarouselView EmptyView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as seguintes propriedades que podem ser usadas para fornecer comentários do usuário quando não há dados a serem exibidos:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), do tipo `object` , a cadeia de caracteres, a associação ou a exibição que será exibida quando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade for `null` , ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O valor padrão é `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , o modelo a ser usado para formatar o especificado `EmptyView` . O valor padrão é `null`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Os principais cenários de uso para definir a [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade são exibir comentários do usuário quando uma operação de filtragem em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) não produz dados e exibindo comentários do usuário enquanto os dados estão sendo recuperados de um serviço Web.

> [!NOTE]
> A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como uma exibição que inclui conteúdo interativo, se necessário.

Para obter mais informações sobre modelos de dados, consulte [ Xamarin.Forms modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Exibir uma cadeia de caracteres quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como uma cadeia de caracteres, que será exibida quando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade for `null` , ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O XAML a seguir mostra um exemplo desse cenário:

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

O resultado é que, como a coleção associada a dados é `null` , a cadeia de caracteres definida como o [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valor da propriedade é exibida.

## <a name="display-views-when-data-is-unavailable"></a>Exibir exibições quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como uma exibição, que será exibida quando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade for `null` , ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. Isso pode ser uma exibição única ou uma exibição que contenha várias exibições filhas. O exemplo de XAML a seguir mostra a `EmptyView` propriedade definida como uma exibição que contém várias exibições filho:

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <ContentView>
                <StackLayout HorizontalOptions="CenterAndExpand"
                             VerticalOptions="CenterAndExpand">
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
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Neste exemplo, o que parece um redundante [`ContentView`](xref:Xamarin.Forms) foi adicionado como o elemento raiz do [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Isso ocorre porque, internamente, `EmptyView` é adicionado a um contêiner nativo que não fornece nenhum contexto para Xamarin.Forms layout. Portanto, para posicionar as exibições que compõem seu `EmptyView` , você deve adicionar um layout raiz, cujo filho é um layout que pode se posicionar dentro do layout raiz.

Este é o código C# equivalente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new ContentView
    {
        Content = new StackLayout
        {
            Children =
            {
                new Label { Text = "No results matched your filter.", ... },
                new Label { Text = "Try a broader filter?", ... }
            }
        }
    }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand` , a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) conjunto como o [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valor da propriedade será exibido.

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Exibir um tipo personalizado de modelo quando os dados estiverem indisponíveis

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade pode ser definida como um tipo personalizado, cujo modelo é exibido quando a [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade é `null` , ou quando a coleção especificada pela `ItemsSource` propriedade é `null` ou vazia. A [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade pode ser definida como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência do `EmptyView` . O XAML a seguir mostra um exemplo desse cenário:

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

O `FilterData` tipo define uma `Filter` propriedade e um correspondente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

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

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade é definida como um `FilterData` objeto e os `Filter` dados de propriedade são associados à [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand` , a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na `Filter` propriedade. Se a operação de filtragem não gerar dados, o [`Label`](xref:Xamarin.Forms.Label) definido no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que é definido como o [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) valor da propriedade, será exibido.

> [!NOTE]
> Ao exibir um tipo personalizado de modelo quando os dados não estão disponíveis, a [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade pode ser definida como uma exibição que contém várias exibições filho.

## <a name="choose-an-emptyview-at-runtime"></a>Escolha um EmptyView em tempo de execução

Exibições que serão exibidas como um [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) quando os dados estiverem indisponíveis, podem ser definidas como [`ContentView`](xref:Xamarin.Forms.ContentView) objetos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . A `EmptyView` propriedade pode então ser definida como um específico `ContentView` , com base em alguma lógica de negócios, em tempo de execução. O exemplo de XAML a seguir mostra um exemplo desse cenário:

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

Esse XAML define dois [`ContentView`](xref:Xamarin.Forms.ContentView) objetos no nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , com o [`Switch`](xref:Xamarin.Forms.Switch) objeto que controla qual `ContentView` objeto será definido como o [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valor da propriedade. Quando o [`Switch`](xref:Xamarin.Forms.Switch) é alternado, o `OnEmptyViewSwitchToggled` manipulador de eventos executa o `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

O `ToggleEmptyView` método define a [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) Propriedade do `carouselView` objeto como um dos dois [`ContentView`](xref:Xamarin.Forms.ContentView) objetos armazenados no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , com base no valor da [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade. Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand` , a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o `ContentView` objeto definido como a `EmptyView` propriedade será exibido.

Para obter mais informações sobre dicionários de recursos, consulte [ Xamarin.Forms dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Escolha um EmptyViewTemplate em tempo de execução

A aparência do [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) pode ser escolhida em tempo de execução, com base em seu valor, definindo a [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

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

A [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriedade é definida como a [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade e a [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade é definida como um `SearchTermDataTemplateSelector` objeto.

Quando o [`SearchBar`](xref:Xamarin.Forms.SearchBar) executa o `FilterCommand` , a coleção exibida pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) é filtrada para o termo de pesquisa armazenado na [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriedade. Se a operação de filtragem não gerar dados, o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) escolhido pelo `SearchTermDataTemplateSelector` objeto será definido como a [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriedade e exibido.

O exemplo a seguir mostra a `SearchTermDataTemplateSelector` classe:

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

A `SearchTermTemplateSelector` classe define `DefaultTemplate` e `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Propriedades que são definidas para modelos de dados diferentes. A `OnSelectTemplate` substituição retorna `DefaultTemplate` , que exibe uma mensagem para o usuário, quando a consulta de pesquisa não é igual a "xamarin". Quando a consulta de pesquisa é igual a "xamarin", a `OnSelectTemplate` substituição retorna `OtherTemplate` , que exibe uma mensagem básica ao usuário.

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms Dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
