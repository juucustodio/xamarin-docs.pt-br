---
title: Especifique o Layout do xamarin. Forms CollectionView
description: Por padrão, um CollectionView exibirá seus itens em uma lista vertical. No entanto, grades e listas verticais e horizontais podem ser especificadas.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 8ed365ed41ac31c66d41f1a32a7a16929cdc6770
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329752"
---
# <a name="specify-xamarinforms-collectionview-layout"></a>Especifique o Layout do xamarin. Forms CollectionView

![Visualizar](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> O `CollectionView` atualmente é uma visualização e não tem algumas das suas funcionalidades planejada. Além disso, a API pode mudar conforme a implementação for concluída.

`CollectionView` Define as seguintes propriedades que controlam o layout:

- `ItemsLayout`, do tipo `IItemsLayout`, especifica o layout a ser usado.
- `ItemSizingStrategy`, do tipo `ItemSizingStrategy`, especifica a estratégia de medida do item a ser usado.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

Por padrão, um `CollectionView` exibirá seus itens em uma lista vertical. No entanto, qualquer um dos seguintes layouts podem ser usados:

- Lista vertical – uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados.
- Lista horizontal – uma lista de única linha cresce horizontalmente conforme novos itens são adicionados.
- Grade vertical – uma grade de várias coluna que cresce verticalmente conforme novos itens são adicionados.
- Grade horizontal – uma grade de várias linhas cresce horizontalmente conforme novos itens são adicionados.

Esses layouts podem ser especificados definindo a `ItemsLayout` propriedade para a classe que deriva de `ItemsLayout` classe. Essa classe define as propriedades a seguir:

- `Orientation`, do tipo `ItemsLayoutOrientation`, especifica a direção na qual o `CollectionView` se expande conforme itens são adicionados.
- `SnapPointsAlignment`, do tipo `SnapPointsAlignment`, especifica como os pontos de alinhamento são alinhados com os itens.
- `SnapPointsType`, do tipo `SnapPointsType`, especifica o comportamento de pontos de alinhamento durante a rolagem.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados. Para obter mais informações sobre pontos de alinhamento, consulte [ajustar pontos](scrolling.md#snap-points) na [rolar um Item na exibição](scrolling.md) guia.

O `ItemsLayoutOrientation` enumeração define os seguintes membros:

- `Vertical` indica que o `CollectionView` será expandida verticalmente conforme os itens são adicionados.
- `Horizontal` indica que o `CollectionView` expandirá horizontalmente conforme itens são adicionados.

O `ListItemsLayout` herda a `ItemsLayout` classe e define estático `VerticalList` e `HorizontalList` membros. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, uma `ListItemsLayout` objeto pode ser criado, especificando um `ItemsLayoutOrientation` membro de enumeração como um argumento.

O `GridItemsLayout` herda o `ItemsLayout` de classe e define um `Span` propriedade do tipo `int`, que representa o número de colunas ou linhas a serem exibidas na grade. O valor padrão de `Span` propriedade é 1, e seu valor deve sempre ser maior que ou igual a 1.

> [!NOTE]
> `CollectionView` usa os mecanismos de layout nativo para executar o layout.

## <a name="vertical-list"></a>Lista vertical

Por padrão, `CollectionView` exibirá seus itens em um layout da lista vertical. Portanto, não é necessário definir o `ItemsLayout` propriedade para usar este layout:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

No entanto, para fins de integridade, uma `CollectionView` pode ser definido para exibir seus itens em uma lista vertical definindo seu `ItemsLayout` a um estático `ListItemsLayout.VerticalList` membro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

Como alternativa, isso também pode ser feito definindo a `ItemsLayout` propriedade para um objeto do `ListItemsLayout` classe, especificando as `Vertical` `ItemsLayoutOrientation` membro de enumeração como um argumento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.VerticalList
};
```

Isso resulta em uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados:

[![Captura de tela de um layout da lista vertical CollectionView, no iOS e Android](layout-images/vertical-list.png "layout da lista vertical de CollectionView")](layout-images/vertical-list-large.png#lightbox "layout da lista vertical de CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

`CollectionView` pode exibir seus itens em uma lista horizontal, definindo sua `ItemsLayout` propriedade para estático `ListItemsLayout.HorizontalList` membro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Como alternativa, isso também pode ser feito definindo a `ItemsLayout` propriedade para um `ListItemsLayout` do objeto, especificando as `Horizontal` `ItemsLayoutOrientation` membro de enumeração como um argumento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.HorizontalList
};
```

Isso resulta em uma lista de única linha, que cresce horizontalmente conforme novos itens são adicionados:

[![Captura de tela de um layout da lista horizontal CollectionView, no iOS e Android](layout-images/horizontal-list.png "layout de lista horizontal CollectionView")](layout-images/horizontal-list-large.png#lightbox "CollectionView layout de lista horizontal")

## <a name="vertical-grid"></a>Grade vertical

`CollectionView` pode exibir seus itens em uma grade vertical, definindo sua `ItemsLayout` propriedade para um `GridItemsLayout` do objeto cuja `Orientation` estiver definida como `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Por padrão, um vertical `GridItemsLayout` exibirá itens em uma única coluna. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade como 2. Isso resulta em uma grade de duas colunas, cresce verticalmente conforme novos itens são adicionados:

[![Captura de tela de um layout de grade vertical CollectionView, no iOS e Android](layout-images/vertical-grid.png "layout de grade vertical CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView layout de grade vertical")

## <a name="horizontal-grid"></a>Grade horizontal

`CollectionView` pode exibir seus itens em uma grade horizontal, definindo sua `ItemsLayout` propriedade para um `GridItemsLayout` do objeto cuja `Orientation` estiver definida como `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Por padrão, um horizontal `GridItemsLayout` exibirá itens em uma única linha. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade para 4. Isso resulta em uma grade de quatro linhas, que cresce horizontalmente conforme novos itens são adicionados:

[![Captura de tela de um layout de grade horizontal CollectionView, no iOS e Android](layout-images/horizontal-grid.png "layout de grade horizontal CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView layout de grade horizontal")

## <a name="right-to-left-layout"></a>Layout da direita para esquerda

`CollectionView` pode definir o layout de seu conteúdo em uma direção de fluxo da direita para esquerda, definindo sua [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). No entanto, o `FlowDirection` propriedade idealmente deve ser definida em um layout de página ou raiz, que faz com que todos os elementos dentro da página ou o layout de raiz, para responder a direção do fluxo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

O padrão [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) para um elemento com um pai é [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, o `CollectionView` herda a `FlowDirection` valor da propriedade a [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), que por sua vez herda o `FlowDirection` valor da propriedade do [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) . Isso resulta no layout da direita para esquerda mostrado nas capturas de tela seguir:

[![Captura de tela de um layout da lista vertical da direita para esquerda CollectionView, no iOS e Android](layout-images/vertical-list-rtl.png "layout de lista vertical da direita para esquerda CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView vertical de direita para esquerda layout da lista")

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="item-sizing"></a>Dimensionamento do item

Por padrão, cada item em uma `CollectionView` é individualmente medido e dimensionado, desde que os elementos de interface do usuário na [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) não especificar tamanhos fixos. Esse comportamento, que pode ser alterado, é especificado pelo `CollectionView.ItemSizingStrategy` valor da propriedade. Esse valor de propriedade pode ser definido como um do `ItemSizingStrategy` membros de enumeração:

- `MeasureAllItems` – cada item é medida individualmente. Este é o valor padrão.
- `MeasureFirstItem` – somente o primeiro item é medido, com todos os itens subsequentes que está sendo fornecidos do mesmo tamanho que o primeiro item.

> [!IMPORTANT]
> O `MeasureFirstItem` estratégia de dimensionamento deve ser usado em situações em que o tamanho do item se destina a ser uniforme em todos os itens e resultará em um melhor desempenho.

O exemplo de código a seguir mostra a configuração de `ItemSizingStrategy` propriedade:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Localização da direita para esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Rolar um Item na exibição](scrolling.md)
