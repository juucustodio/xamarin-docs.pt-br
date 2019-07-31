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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647903"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layouts vinculáveis no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Os layouts vinculáveis habilitam qualquer classe de layout que [`Layout<T>`](xref:Xamarin.Forms.Layout`1) derive da classe para gerar seu conteúdo ligando a uma coleção de itens, com a opção de definir a aparência de cada item [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)com um. Os `BindableLayout` layouts vinculáveis são fornecidos pela classe, que expõe as seguintes propriedades anexadas:

- `ItemsSource`– Especifica a coleção de `IEnumerable` itens a serem exibidos pelo layout.
- `ItemTemplate`– Especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicado a cada item na coleção de itens exibida pelo layout.
- `ItemTemplateSelector`– Especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item em tempo de execução.

Essas propriedades podem ser anexadas às [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classes [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) [`Grid`](xref:Xamarin.Forms.Grid),, [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , que derivam da [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe.

> [!NOTE]
> A `ItemTemplate` propriedade tem precedência quando ambas `ItemTemplate` as `ItemTemplateSelector` Propriedades e são definidas.

A `Layout<T>` classe expõe uma [`Children`](xref:Xamarin.Forms.Layout`1.Children) coleção, à qual os elementos filho de um layout são adicionados. Quando a `BinableLayout.ItemsSource` propriedade é definida como uma coleção de itens e anexada a [`Layout<T>`](xref:Xamarin.Forms.Layout`1)uma classe derivada, cada item na `Layout<T>.Children` coleção é adicionado à coleção para exibição pelo layout. Em `Layout<T>`seguida, a classe derivada atualizará suas exibições filhas quando a coleção subjacente for alterada. Para obter mais informações sobre o ciclo de layout do Xamarin. Forms, consulte [criando um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Os layouts vinculáveis só devem ser usados quando a coleção de itens a serem exibidos for pequena, e a rolagem e a seleção não forem necessárias. Embora a rolagem possa ser fornecida ao encapsular um layout vinculável em um [`ScrollView`](xref:Xamarin.Forms.ScrollView), isso não é recomendado, pois os layouts vinculáveis não têm a virtualização da interface do usuário. Quando a rolagem é necessária, uma exibição rolável que inclui a virtualização de interface [`ListView`](xref:Xamarin.Forms.ListView) do [`CollectionView`](xref:Xamarin.Forms.CollectionView)usuário, como ou, deve ser usada. A falha ao observar essa recomendação pode levar a problemas de desempenho.

> [!IMPORTANT]
>Embora seja tecnicamente possível anexar um [`Layout<T>`](xref:Xamarin.Forms.Layout`1) layout vinculável a qualquer classe de layout derivada da classe, nem sempre é prático fazer isso, especialmente para as [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)classes, [`Grid`](xref:Xamarin.Forms.Grid)e [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Por exemplo, considere o cenário de querer exibir uma coleção de dados em um [`Grid`](xref:Xamarin.Forms.Grid) usando um layout vinculável, onde cada item na coleção é um objeto que contém várias propriedades. Cada linha no `Grid` deve exibir um objeto da coleção, com cada coluna `Grid` na exibição de uma das propriedades do objeto. Como o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para o layout vinculável pode conter apenas um único objeto, é necessário que esse objeto seja uma classe de layout contendo várias exibições que cada uma exibe uma das propriedades do objeto em uma `Grid` coluna específica. Embora esse cenário possa ser realamente com layouts vinculáveis, ele resulta em um `Grid` pai que contém `Grid` um filho para cada item na coleção associada, que é um uso altamente ineficiente `Grid` e problemático do layout.

## <a name="populating-a-bindable-layout-with-data"></a>Populando um layout vinculável com dados

Um layout vinculável é populado com dados `ItemsSource` definindo sua propriedade para qualquer coleção `IEnumerable`que implementa e anexando- [`Layout<T>`](xref:Xamarin.Forms.Layout`1)a a uma classe derivada:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

O código C# equivalente é:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando a `BindableLayout.ItemsSource` Propriedade anexada é definida em um layout, mas `BindableLayout.ItemTemplate` a propriedade anexada não é definida `IEnumerable` , cada item na coleção será `BindableLayout` exibido por [`Label`](xref:Xamarin.Forms.Label) um que é criado pela classe.

## <a name="defining-item-appearance"></a>Definindo a aparência do item

A aparência de cada item no layout vinculável pode ser definida definindo a `BindableLayout.ItemTemplate` Propriedade anexada como um: [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)

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

O código C# equivalente é:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

Neste exemplo, cada item na `TopFollowers` coleção será exibido por um `CircleImage` modo de exibição definido no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Layout vinculável a um DataTemplate](bindable-layouts-images/top-followers.png "Layout vinculável a um modelo de dados")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Escolhendo a aparência do item em tempo de execução

A aparência de cada item no layout vinculável pode ser escolhida em tempo de execução, com base no valor do item, `BindableLayout.ItemTemplateSelector` definindo a propriedade anexada como um: [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

O código C# equivalente é:

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

A `TechItemTemplateSelector` classe define `DefaultTemplate` e `XamarinFormsTemplate` [Propriedadesquesãodefinidasparamodelos`DataTemplate`](xref:Xamarin.Forms.DataTemplate) de dados diferentes. O `OnSelectTemplate` método retorna o `XamarinFormsTemplate`, que exibe um item em vermelho escuro com um coração ao lado dele, quando o item é igual a "Xamarin. Forms". Quando o item não é igual a "Xamarin. Forms", `OnSelectTemplate` o método retorna `DefaultTemplate`o, que exibe um item usando a cor padrão de [`Label`](xref:Xamarin.Forms.Label)um:

![Layout vinculável com um DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout vinculável com um seletor de modelo de dados")

Para obter mais informações sobre seletores de modelo de dados, consulte [Creating a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [Demonstração de layout vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criando um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
