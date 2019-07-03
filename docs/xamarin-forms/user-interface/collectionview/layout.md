---
title: Layout de CollectionView do xamarin. Forms
description: Por padrão, um CollectionView exibirá seus itens em uma lista vertical. No entanto, grades e listas verticais e horizontais podem ser especificadas.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 786cea04718022847bba2ecffed8f377dd49bd8b
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512825"
---
# <a name="xamarinforms-collectionview-layout"></a>Layout de CollectionView do xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) Define as seguintes propriedades que controlam o layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), do tipo [ `IItemsLayout` ](xref:Xamarin.Forms.IItemsLayout), especifica o layout a ser usado.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), do tipo [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy), especifica a estratégia de medida do item a ser usado.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

Por padrão, uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, qualquer um dos seguintes layouts podem ser usados:

- Lista vertical – uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados.
- Lista horizontal – uma lista de única linha cresce horizontalmente conforme novos itens são adicionados.
- Grade vertical – uma grade de várias coluna que cresce verticalmente conforme novos itens são adicionados.
- Grade horizontal – uma grade de várias linhas cresce horizontalmente conforme novos itens são adicionados.

Esses layouts podem ser especificados definindo a [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para a classe que deriva de [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe. Essa classe define as propriedades a seguir:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), do tipo [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica a direção na qual o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se expande conforme itens são adicionados.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de alinhamento são alinhados com os itens.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento de pontos de alinhamento durante a rolagem.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados. Para obter mais informações sobre pontos de alinhamento, consulte [ajustar pontos](scrolling.md#snap-points) na [rolagem do xamarin. Forms CollectionView](scrolling.md) guia.

O [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeração define os seguintes membros:

- `Vertical` indica que o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) será expandida verticalmente conforme os itens são adicionados.
- `Horizontal` indica que o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) expandirá horizontalmente conforme itens são adicionados.

O [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) herda o [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe e define um `ItemSpacing` propriedade do tipo `double`, que representa o espaço vazio ao redor de cada item. O valor padrão dessa propriedade é 0, e seu valor deve sempre ser maior que ou igual a 0. O `ListItemsLayout` classe define também estático `Vertical` e `Horizontal` membros. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, uma `ListItemsLayout` objeto pode ser criado, especificando um [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento.

O [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) herda o [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) de classe e define as propriedades a seguir:

- `VerticalItemSpacing`, do tipo `double`, que representa o espaço vertical vazio ao redor de cada item. O valor padrão dessa propriedade é 0, e seu valor deve sempre ser maior que ou igual a 0.
- `HorizontalItemSpacing`, do tipo `double`, que representa o espaço horizontal vazio ao redor de cada item. O valor padrão dessa propriedade é 0, e seu valor deve sempre ser maior que ou igual a 0.
- `Span`, do tipo `int`, que representa o número de colunas ou linhas a serem exibidas na grade. O valor padrão dessa propriedade é 1, e seu valor deve sempre ser maior que ou igual a 1.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa os mecanismos de layout nativo para executar o layout.

## <a name="vertical-list"></a>Lista vertical

Por padrão, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) exibirá seus itens em um layout da lista vertical. Portanto, não é necessário definir as [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para usar este layout:

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

No entanto, para fins de integridade, uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser definido para exibir seus itens em uma lista vertical definindo seu [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Como alternativa, isso também pode ser feito definindo a [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade em um objeto do [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) classe, especificando o `Vertical` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento:

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

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Vertical
};
```

Isso resulta em uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados:

[![Captura de tela de um layout da lista vertical CollectionView, no iOS e Android](layout-images/vertical-list.png "layout da lista vertical de CollectionView")](layout-images/vertical-list-large.png#lightbox "layout da lista vertical de CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma lista horizontal, definindo sua [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade `HorizontalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
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

Como alternativa, isso também pode ser feito definindo a [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade como um [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) do objeto, especificando o `Horizontal` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento:

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

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Horizontal
};
```

Isso resulta em uma lista de única linha, que cresce horizontalmente conforme novos itens são adicionados:

[![Captura de tela de um layout da lista horizontal CollectionView, no iOS e Android](layout-images/horizontal-list.png "layout de lista horizontal CollectionView")](layout-images/horizontal-list-large.png#lightbox "CollectionView layout de lista horizontal")

## <a name="vertical-grid"></a>Grade vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma grade vertical, definindo sua [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade como um [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) do objeto cuja [ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) estiver definida como `Vertical`:

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

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Por padrão, um vertical [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) exibirá itens em uma única coluna. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade como 2. Isso resulta em uma grade de duas colunas, cresce verticalmente conforme novos itens são adicionados:

[![Captura de tela de um layout de grade vertical CollectionView, no iOS e Android](layout-images/vertical-grid.png "layout de grade vertical CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView layout de grade vertical")

## <a name="horizontal-grid"></a>Grade horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma grade horizontal, definindo sua [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade como um [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) do objeto cuja[ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) estiver definida como `Horizontal`:

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

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Por padrão, um horizontal [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) exibirá itens em uma única linha. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade para 4. Isso resulta em uma grade de quatro linhas, que cresce horizontalmente conforme novos itens são adicionados:

[![Captura de tela de um layout de grade horizontal CollectionView, no iOS e Android](layout-images/horizontal-grid.png "layout de grade horizontal CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView layout de grade horizontal")

## <a name="item-spacing"></a>Espaçamento de item

Por padrão, cada item em uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) não tem um espaço vazio ao redor dele. Esse comportamento pode ser alterado definindo propriedades do layout de itens usados pelo `CollectionView`.

Quando um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) define seu [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para um [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) objeto, a `ListItemsLayout.ItemSpacing` propriedade pode ser definida como um `double` valor que representa o espaço vazio ao redor de cada item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> O `ListItemsLayout.ItemSpacing` propriedade tem um conjunto de retorno de chamada de validação, que garante que o valor da propriedade é sempre maior que ou igual a 0.

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Esse código resulta em uma lista vertical de coluna única, que tem um espaçamento de 20 ao redor de cada item:

[![Captura de tela de um CollectionView de espaçamento de item, no iOS e Android](layout-images/vertical-list-spacing.png "CollectionView item espaçamento")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView espaçamento de item")

Quando um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) define seu [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para um [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) objeto, a `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` propriedades podem ser definido como `double` valores que representam o espaço vazio vertical e horizontalmente em torno de cada item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> O `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` propriedades têm retornos de chamada de validação definido, que garantir que os valores das propriedades são sempre maior que ou igual a 0.

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

Esse código resulta em uma grade vertical de duas colunas, que tem um espaçamento vertical de 20 ao redor de cada item e um espaçamento horizontal de 30 ao redor de cada item:

[![Captura de tela de um CollectionView de espaçamento de item, no iOS e Android](layout-images/vertical-grid-spacing.png "CollectionView item espaçamento")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView espaçamento de item")

## <a name="item-sizing"></a>Dimensionamento do item

Por padrão, cada item em uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é individualmente medido e dimensionado, desde que os elementos de interface do usuário na [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) não especificar tamanhos fixos. Esse comportamento, o que pode ser alterado, for especificado o [ `CollectionView.ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) valor da propriedade. Esse valor de propriedade pode ser definido como um dos [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy) membros de enumeração:

- `MeasureAllItems` – cada item é medida individualmente. Este é o valor padrão.
- `MeasureFirstItem` – somente o primeiro item é medido, com todos os itens subsequentes que está sendo fornecidos do mesmo tamanho que o primeiro item.

> [!IMPORTANT]
> O `MeasureFirstItem` estratégia de dimensionamento resultará em maior desempenho quando usado em situações em que o tamanho do item deve ser uniforme entre todos os itens.

O exemplo de código a seguir mostra a configuração de [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) propriedade:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Redimensionamento dinâmico de itens

Itens de um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser redimensionada dinamicamente em tempo de execução por meio da alteração layout relacionadas as propriedades dos elementos dentro de [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Por exemplo, o código a seguir exemplo altera a [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) e [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) propriedades de um [ `Image` ](xref:Xamarin.Forms.Image) objeto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

O `OnImageTapped` manipulador de eventos é executado em resposta a uma [ `Image` ](xref:Xamarin.Forms.Image) de objeto que está sendo tocado e altera as dimensões da imagem para que ele é exibido com mais facilidade:

[![Captura de tela de uma CollectionView com dimensionamento dinâmico de item, no iOS e Android](layout-images/runtime-resizing.png "CollectionView item dinâmico dimensionamento")](layout-images/runtime-resizing-large.png#lightbox "CollectionView item dinâmico dimensionamento")

## <a name="right-to-left-layout"></a>Layout da direita para esquerda

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode definir o layout de seu conteúdo em uma direção de fluxo da direita para esquerda, definindo sua [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). No entanto, o `FlowDirection` propriedade idealmente deve ser definida em um layout de página ou raiz, que faz com que todos os elementos dentro da página ou o layout de raiz, para responder a direção do fluxo:

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

O padrão [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) para um elemento com um pai é [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) herda o `FlowDirection` valor de propriedade do [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), que por sua vez herda o `FlowDirection` valor da propriedade a [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Isso resulta no layout da direita para esquerda mostrado nas capturas de tela seguir:

[![Captura de tela de um layout da lista vertical da direita para esquerda CollectionView, no iOS e Android](layout-images/vertical-list-rtl.png "layout de lista vertical da direita para esquerda CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView vertical de direita para esquerda layout da lista")

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Localização da direita para esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Rolagem de CollectionView de xamarin. Forms](scrolling.md)
