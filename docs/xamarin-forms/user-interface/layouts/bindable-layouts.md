---
title: Layouts vinculáveis no Xamarin. Forms
description: Os layouts vinculáveis permitem que as classes de layout gerem seu conteúdo ligando-se a uma coleção de itens, com a opção de definir a aparência de cada item com um DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
ms.openlocfilehash: d084d910786c24a4b0333ecbc3e893cfe144d404
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517222"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layouts vinculáveis no Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Os layouts vinculáveis habilitam qualquer classe de layout que [`Layout<T>`](xref:Xamarin.Forms.Layout`1) derive da classe para gerar seu conteúdo ligando a uma coleção de itens, com a opção de definir a aparência de cada item [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)com um. Os `BindableLayout` layouts vinculáveis são fornecidos pela classe, que expõe as seguintes propriedades anexadas:

- `ItemsSource`– Especifica a coleção de `IEnumerable` itens a serem exibidos pelo layout.
- `ItemTemplate`– Especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicado a cada item na coleção de itens exibida pelo layout.
- `ItemTemplateSelector`– Especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item em tempo de execução.

> [!NOTE]
> A `ItemTemplate` propriedade tem precedência quando ambas `ItemTemplate` as `ItemTemplateSelector` Propriedades e são definidas.

Além disso, a `BindableLayout` classe expõe as seguintes propriedades vinculáveis:

- `EmptyView`– Especifica a `string` `ItemsSource` exibição ou que será exibida quando a propriedade for `null`, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O valor padrão é `null`.
- `EmptyViewTemplate`– Especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `ItemsSource` que será exibido quando a propriedade for `null`, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O valor padrão é `null`.

> [!NOTE]
> A `EmptyViewTemplate` propriedade tem precedência quando ambas `EmptyView` as `EmptyViewTemplate` Propriedades e são definidas.

Todas essas propriedades podem ser anexadas às classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , que derivam da [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

A `Layout<T>` classe expõe uma [`Children`](xref:Xamarin.Forms.Layout`1.Children) coleção, à qual os elementos filho de um layout são adicionados. Quando a `BinableLayout.ItemsSource` propriedade é definida como uma coleção de itens e anexada a [`Layout<T>`](xref:Xamarin.Forms.Layout`1)uma classe derivada, cada item na coleção é adicionado à `Layout<T>.Children` coleção para exibição pelo layout. Em `Layout<T>`seguida, a classe derivada atualizará suas exibições filhas quando a coleção subjacente for alterada. Para obter mais informações sobre o ciclo de layout do Xamarin. Forms, consulte [criando um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Os layouts vinculáveis só devem ser usados quando a coleção de itens a serem exibidos for pequena, e a rolagem e a seleção não forem necessárias. Embora a rolagem possa ser fornecida ao encapsular um layout vinculável em um [`ScrollView`](xref:Xamarin.Forms.ScrollView), isso não é recomendado, pois os layouts vinculáveis não têm a virtualização da interface do usuário. Quando a rolagem é necessária, uma exibição rolável que inclui a virtualização de interface [`ListView`](xref:Xamarin.Forms.ListView) do [`CollectionView`](xref:Xamarin.Forms.CollectionView)usuário, como ou, deve ser usada. A falha ao observar essa recomendação pode levar a problemas de desempenho.

> [!IMPORTANT]
> Embora seja tecnicamente possível anexar um layout vinculável a qualquer classe de [`Layout<T>`](xref:Xamarin.Forms.Layout`1) layout derivada da classe, nem sempre é prático fazer isso, especialmente para as [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classes, [`Grid`](xref:Xamarin.Forms.Grid)e. [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Por exemplo, considere o cenário de querer exibir uma coleção de dados em um [`Grid`](xref:Xamarin.Forms.Grid) usando um layout vinculável, onde cada item na coleção é um objeto que contém várias propriedades. Cada linha no `Grid` deve exibir um objeto da coleção, com cada coluna na `Grid` exibição de uma das propriedades do objeto. Como o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para o layout vinculável pode conter apenas um único objeto, é necessário que esse objeto seja uma classe de layout contendo várias exibições que cada uma exibe uma das propriedades do objeto em uma `Grid` coluna específica. Embora esse cenário possa ser realamente com layouts vinculáveis, ele resulta em um `Grid` pai que contém `Grid` um filho para cada item na coleção associada, que é um uso altamente ineficiente e problemático do `Grid` layout.

## <a name="populate-a-bindable-layout-with-data"></a>Preencher um layout vinculável com dados

Um layout vinculável é populado com dados `ItemsSource` definindo sua propriedade para qualquer coleção `IEnumerable`que implementa e anexando- [`Layout<T>`](xref:Xamarin.Forms.Layout`1)a a uma classe derivada:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Este é o código C# equivalente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando a `BindableLayout.ItemsSource` Propriedade anexada é definida em um layout, mas `BindableLayout.ItemTemplate` a propriedade anexada não é definida, cada `IEnumerable` item na coleção será exibido por [`Label`](xref:Xamarin.Forms.Label) um que é criado pela `BindableLayout` classe.

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item no layout vinculável pode ser definida definindo a `BindableLayout.ItemTemplate` Propriedade anexada como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

Este é o código C# equivalente:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

Neste exemplo, cada item na `TopFollowers` coleção será exibido por um `CircleImage` modo de exibição definido no: [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

![Layout vinculável a um DataTemplate](bindable-layouts-images/top-followers.png "Layout vinculável a um modelo de dados")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item no layout vinculável pode ser escolhida em tempo de execução, com base no valor do item, `BindableLayout.ItemTemplateSelector` definindo a propriedade anexada como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

Este é o código C# equivalente:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

O [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) usado no aplicativo de exemplo é mostrado no exemplo a seguir:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

A `TechItemTemplateSelector` classe define `DefaultTemplate` e `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriedades que são definidas para modelos de dados diferentes. O `OnSelectTemplate` método retorna o `XamarinFormsTemplate`, que exibe um item em vermelho escuro com um coração ao lado dele, quando o item é igual a "Xamarin. Forms". Quando o item não é igual a "Xamarin. Forms", `OnSelectTemplate` o método retorna `DefaultTemplate`o, que exibe um item usando a cor padrão de [`Label`](xref:Xamarin.Forms.Label)um:

![Layout vinculável com um DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout vinculável com um seletor de modelo de dados")

Para obter mais informações sobre seletores de modelo de dados, consulte [Creating a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Exibir uma cadeia de caracteres quando os dados estiverem indisponíveis

A `EmptyView` propriedade pode ser definida como uma cadeia de caracteres, que será exibida por [`Label`](xref:Xamarin.Forms.Label) um quando `ItemsSource` a propriedade `null`for, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

O resultado é que, quando a coleção associada a `null`dados é, a cadeia de `EmptyView` caracteres definida como o valor da propriedade é exibida:

[![Captura de tela de uma exibição de cadeia de layout vinculável vazia, no iOS e no Android](bindable-layouts-images/emptyview-string.png "Exibição vazia da cadeia de layout vinculável")](bindable-layouts-images/emptyview-string-large.png#lightbox "Exibição vazia da cadeia de layout vinculável")

## <a name="display-views-when-data-is-unavailable"></a>Exibir exibições quando os dados estiverem indisponíveis

A `EmptyView` `ItemsSource` propriedade pode ser definida como uma exibição, que será exibida quando a propriedade for `null`, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. Isso pode ser uma exibição única ou uma exibição que contenha várias exibições filhas. O exemplo de XAML a seguir `EmptyView` mostra a propriedade definida como uma exibição que contém várias exibições filho:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

O resultado é que, quando a coleção associada a `null`dados é [`StackLayout`](xref:Xamarin.Forms.StackLayout) , a e suas exibições filho são exibidas.

[![Captura de tela de uma exibição vazia de layout vinculável com várias exibições, no iOS e no Android](bindable-layouts-images/emptyview-views.png "Exibição vazia de layout vinculável")](bindable-layouts-images/emptyview-views-large.png#lightbox "Exibição vazia de layout vinculável")

Da mesma forma `EmptyViewTemplate` , o pode ser definido [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)como a, que será exibido quando `ItemsSource` a propriedade `null`for, ou quando a coleção especificada pela `ItemsSource` propriedade for `null` ou vazia. O `DataTemplate` pode conter uma única exibição ou uma exibição que contém várias exibições filho. Além disso, o `BindingContext` de `EmptyViewTemplate` será herdado do `BindingContext` do. `BindableLayout` O exemplo de XAML a seguir `EmptyViewTemplate` mostra a propriedade definida `DataTemplate` como um que contém uma única exibição:

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

O resultado é que, quando a coleção associada a `null`dados é [`Label`](xref:Xamarin.Forms.Label) , o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) no é exibido:

[![Captura de tela de um modelo de exibição vazio de layout vinculável, no iOS e no Android](bindable-layouts-images/emptyviewtemplate.png "Modelo de exibição vazio de layout vinculável")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "Modelo de exibição vazio de layout vinculável")

> [!NOTE]
> A `EmptyViewTemplate` propriedade não pode ser definida por [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)meio de um.

## <a name="choose-an-emptyview-at-runtime"></a>Escolha um EmptyView em tempo de execução

Exibições que serão exibidas como um `EmptyView` quando os dados estiverem indisponíveis, podem ser definidas [`ContentView`](xref:Xamarin.Forms.ContentView) como objetos em [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)um. A `EmptyView` propriedade pode então ser definida como um específico `ContentView`, com base em alguma lógica de negócios, em tempo de execução. O XAML a seguir mostra um exemplo desse cenário:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

O XAML define dois [`ContentView`](xref:Xamarin.Forms.ContentView) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)objetos no nível de página, com [`Switch`](xref:Xamarin.Forms.Switch) o objeto que controla qual `ContentView` objeto será definido como o valor `EmptyView` da propriedade. Quando o `Switch` é alternado, o `OnEmptyViewSwitchToggled` manipulador de eventos executa o `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

O `ToggleEmptyView` método define a `EmptyView` Propriedade do `stackLayout` objeto como um dos dois [`ContentView`](xref:Xamarin.Forms.ContentView) objetos armazenados no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), com base no valor da [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriedade. Em seguida, quando a coleção associada a `null`dados é `ContentView` , o objeto definido `EmptyView` como a propriedade é exibido:

[![Captura de tela da opção de exibição vazia no tempo de execução, no iOS e no Android](bindable-layouts-images/emptyview-runtime.png "Opção de tempo de execução de exibição de layout vinculável vazio")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "Opção de tempo de execução de exibição de layout vinculável vazio")

## <a name="related-links"></a>Links relacionados

- [Demonstração de layout vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criando um DataTemplateSelector do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
