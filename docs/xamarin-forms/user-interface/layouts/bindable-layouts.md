---
title: Associáveis Layouts no xamarin. Forms
description: Layouts associáveis habilitar classes de layout gerar seu conteúdo por associação a uma coleção de itens, com a opção de definir a aparência de cada item com um DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: b0e2d5e3c7923e5c3cf2adcc1dd104a97b78e727
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832194"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Associáveis Layouts no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

Layouts associáveis habilitam qualquer classe de layout que deriva de [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe para gerar seu conteúdo por associação a uma coleção de itens, com a opção de definir a aparência de cada item com um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Layouts associáveis são fornecidos pelo `BindableLayout` classe, que expõe as propriedades anexadas a seguir:

- `ItemsSource` – Especifica a coleção de `IEnumerable` itens a serem exibidos no layout.
- `ItemTemplate` – Especifica o [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para aplicar a cada item na coleção de itens exibidos pelo layout.
- `ItemTemplateSelector` – Especifica o [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para um item em tempo de execução.

Essas propriedades podem ser anexadas à [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout), [ `Grid` ](xref:Xamarin.Forms.Grid), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) , e [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) classes, que derivam de [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe.

> [!NOTE]
> O `ItemTemplate` propriedade terá precedência quando tanto o `ItemTemplate` e `ItemTemplateSelector` são definidas.

O `Layout<T>` classe expõe um [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) coleta, à qual os elementos filho de um layout são adicionados. Quando o `BinableLayout.ItemsSource` propriedade é definida como uma coleção de itens e anexada a uma [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-classe derivada, cada item na coleção é adicionado ao `Layout<T>.Children` coleção para exibição pelo layout. O `Layout<T>`-classe derivada, em seguida, atualize suas exibições filha quando a coleção subjacente é alterado. Para obter mais informações sobre o ciclo de layout do xamarin. Forms, consulte [criação de um Layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

> [!IMPORTANT]
> Layouts associáveis só devem ser usados quando a coleção de itens a serem exibidos é pequena e de rolagem e a seleção não é necessária. Enquanto a rolagem pode ser fornecida, encapsulando um layout associável em uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView), isso não é recomendado como associáveis layouts não têm a virtualização de interface do usuário. Quando a rolagem é necessária, uma exibição rolável que inclui a virtualização de interface do usuário, como [ `ListView` ](xref:Xamarin.Forms.ListView) ou `CollectionView`, deve ser usado. Para observar essa recomendação pode levar a problemas de desempenho.

## <a name="populating-a-bindable-layout-with-data"></a>Populando um layout associável com dados

Um layout associável é preenchido com dados, definindo sua `ItemsSource` propriedade para qualquer coleção que implemente `IEnumerable`e anexá-lo para um [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-classe derivada:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

O código C# equivalente é:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando o `BindableLayout.ItemsSource` propriedade anexada é definida em um layout, mas o `BindableLayout.ItemTemplate` propriedade anexada não estiver definido, todos os itens na `IEnumerable` coleção será exibida por um [ `Label` ](xref:Xamarin.Forms.Label) criado pelo `BindableLayout` classe.

## <a name="defining-item-appearance"></a>Definir a aparência do item

A aparência de cada item no layout associável pode ser definida ao configurar o `BindableLayout.ItemTemplate` anexado à propriedade um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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

Neste exemplo, todos os itens a `TopFollowers` coleção será exibida por um `CircleImage` exibição definida na [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

![Layout associável com um DataTemplate](bindable-layouts-images/top-followers.png "associável layout com um modelo de dados")

Para obter mais informações sobre modelos de dados, consulte [modelos de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Escolhendo a aparência do item em tempo de execução

A aparência de cada item no layout associável pode ser escolhida em tempo de execução, com base no valor do item, definindo a `BindableLayout.ItemTemplateSelector` anexado à propriedade um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

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

O [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) usada no exemplo de aplicativo é mostrado no exemplo a seguir:

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

O `TechItemTemplateSelector` classe define `DefaultTemplate` e `XamarinFormsTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) propriedades que são definidas para modelos de dados diferentes. O `OnSelectTemplate` método retorna o `XamarinFormsTemplate`, que exibe um item em vermelho-escuro com um núcleo ao lado dele, quando o item é igual a "Xamarin. Forms". Quando o item não for igual a "Xamarin. Forms", o `OnSelectTemplate` método retorna o `DefaultTemplate`, que exibe um item usando a cor padrão de um [ `Label` ](xref:Xamarin.Forms.Label):

![Layout associável com um DataTemplateSelector](bindable-layouts-images/favorite-tech.png "associável layout com um seletor de modelo de dados")

Para obter mais informações sobre os seletores de modelo de dados, consulte [criando um xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [Demonstração de Layout associável (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [Criar um layout personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelos de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criando um DataTemplateSelector xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
