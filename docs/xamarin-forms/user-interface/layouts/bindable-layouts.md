---
title: Layouts vinculáveis no Xamarin. Forms
description: Os layouts vinculáveis permitem que as classes de layout gerem seu conteúdo ligando-se a uma coleção de itens, com a opção de definir a aparência de cada item com um DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68647903"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layouts vinculáveis no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Os layouts vinculáveis habilitam qualquer classe de layout derivada da classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) para gerar seu conteúdo ligando-se a uma coleção de itens, com a opção de definir a aparência de cada item com um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Os layouts vinculáveis são fornecidos pela classe `BindableLayout`, que expõe as seguintes propriedades anexadas:

- `ItemsSource` – especifica a coleção de itens de `IEnumerable` a serem exibidos pelo layout.
- `ItemTemplate` – especifica a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicada a cada item na coleção de itens exibida pelo layout.
- `ItemTemplateSelector` – especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.

Essas propriedades podem ser anexadas às classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , que derivam da classe [1](xref:Xamarin.Forms.Layout`1) .

> [!NOTE]
> A propriedade `ItemTemplate` tem precedência quando as propriedades `ItemTemplate` e `ItemTemplateSelector` são definidas.

A classe `Layout<T>` expõe uma coleção de [`Children`](xref:Xamarin.Forms.Layout`1.Children) , à qual os elementos filho de um layout são adicionados. Quando a propriedade `BinableLayout.ItemsSource` é definida como uma coleção de itens e anexada a uma classe derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1), cada item da coleção é adicionado à coleção de `Layout<T>.Children` para exibição pelo layout. Em seguida, a classe derivada de `Layout<T>` atualizará suas exibições filhas quando a coleção subjacente for alterada. Para obter mais informações sobre o ciclo de layout do Xamarin. Forms, consulte [criando um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Os layouts vinculáveis só devem ser usados quando a coleção de itens a serem exibidos for pequena, e a rolagem e a seleção não forem necessárias. Embora a rolagem possa ser fornecida ao encapsular um layout vinculável em um [`ScrollView`](xref:Xamarin.Forms.ScrollView), isso não é recomendado, pois os layouts vinculáveis não têm a virtualização da interface do usuário. Quando a rolagem é necessária, uma exibição rolável que inclui a virtualização de interface do usuário, como [`ListView`](xref:Xamarin.Forms.ListView) ou [`CollectionView`](xref:Xamarin.Forms.CollectionView), deve ser usada. A falha ao observar essa recomendação pode levar a problemas de desempenho.

> [!IMPORTANT]
>Embora seja tecnicamente possível anexar um layout vinculável a qualquer classe de layout derivada da classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) , nem sempre é prático fazer isso, especialmente para as classes [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`Grid`](xref:Xamarin.Forms.Grid)e [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Por exemplo, considere o cenário de querer exibir uma coleção de dados em um [`Grid`](xref:Xamarin.Forms.Grid) usando um layout vinculável, onde cada item na coleção é um objeto que contém várias propriedades. Cada linha na `Grid` deve exibir um objeto da coleção, com cada coluna na `Grid` exibindo uma das propriedades do objeto. Como o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para o layout vinculável pode conter apenas um único objeto, é necessário que esse objeto seja uma classe de layout contendo várias exibições que cada uma exibe uma das propriedades do objeto em uma coluna de `Grid` específica. Embora esse cenário possa ser realamente com layouts vinculáveis, ele resulta em um `Grid` pai contendo um `Grid` filho para cada item na coleção associada, que é um uso altamente ineficiente e problemático do layout de `Grid`.

## <a name="populating-a-bindable-layout-with-data"></a>Populando um layout vinculável com dados

Um layout vinculável é populado com dados definindo sua propriedade `ItemsSource` como qualquer coleção que implementa `IEnumerable` e anexando-a a uma classe derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Este é o código C# equivalente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando a propriedade anexada `BindableLayout.ItemsSource` é definida em um layout, mas a propriedade `BindableLayout.ItemTemplate` anexada não é definida, cada item na coleção de `IEnumerable` será exibido por um [`Label`](xref:Xamarin.Forms.Label) que é criado pela classe `BindableLayout`.

## <a name="defining-item-appearance"></a>Definindo a aparência do item

A aparência de cada item no layout vinculável pode ser definida definindo a propriedade `BindableLayout.ItemTemplate` anexada como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Neste exemplo, cada item na coleção de `TopFollowers` será exibido por uma exibição de `CircleImage` definida no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Layout vinculável a um DataTemplate](bindable-layouts-images/top-followers.png "Layout vinculável a um modelo de dados")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Escolhendo a aparência do item em tempo de execução

A aparência de cada item no layout vinculável pode ser escolhida em tempo de execução, com base no valor do item, definindo a propriedade `BindableLayout.ItemTemplateSelector` anexada como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

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

A classe `TechItemTemplateSelector` define `DefaultTemplate` e `XamarinFormsTemplate` propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definidas para modelos de dados diferentes. O método `OnSelectTemplate` retorna o `XamarinFormsTemplate`, que exibe um item em vermelho escuro com um coração ao lado dele, quando o item é igual a "Xamarin. Forms". Quando o item não é igual a "Xamarin. Forms", o método `OnSelectTemplate` retorna o `DefaultTemplate`, que exibe um item usando a cor padrão de um [`Label`](xref:Xamarin.Forms.Label):

![Layout vinculável com um DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout vinculável com um seletor de modelo de dados")

Para obter mais informações sobre seletores de modelo de dados, consulte [Creating a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [Demonstração de layout vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criando um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
