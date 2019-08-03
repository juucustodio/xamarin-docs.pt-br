---
title: Layout CollectionView do Xamarin. Forms
description: Por padrão, um CollectionView exibirá seus itens em uma lista vertical. No entanto, listas e grades verticais e horizontais podem ser especificadas.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 5fb92882f443007e5b3dd693f54e582757db1905
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739016"
---
# <a name="xamarinforms-collectionview-layout"></a>Layout CollectionView do Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define as seguintes propriedades que controlam o layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), do tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), especifica o layout a ser usado.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), do tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), especifica a estratégia de medida do item a ser usada.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Por padrão, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, qualquer um dos seguintes layouts pode ser usado:

- Lista vertical – uma lista de colunas única que cresce verticalmente à medida que novos itens são adicionados.
- Lista Horizontal – uma única lista de linhas que aumenta horizontalmente à medida que novos itens são adicionados.
- Grade vertical – uma grade de várias colunas que cresce verticalmente à medida que novos itens são adicionados.
- Grade horizontal – uma grade de várias linhas que cresce horizontalmente à medida que novos itens são adicionados.

Esses layouts podem ser especificados definindo a [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) Propriedade como classe derivada [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) da classe. Essa classe define as seguintes propriedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), do tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica a direção na qual o [`CollectionView`](xref:Xamarin.Forms.CollectionView) expande como itens são adicionados.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de ajuste são alinhados com os itens.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento dos pontos de ajuste ao rolar.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados. Para obter mais informações sobre pontos de encaixe, consulte [pontos de alinhamento](scrolling.md#snap-points) no guia de rolagem de [CollectionView do Xamarin. Forms](scrolling.md) .

A [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeração define os seguintes membros:

- `Vertical`indica que o [`CollectionView`](xref:Xamarin.Forms.CollectionView) será expandido verticalmente à medida que os itens forem adicionados.
- `Horizontal`indica que o [`CollectionView`](xref:Xamarin.Forms.CollectionView) será expandido horizontalmente conforme os itens são adicionados.

A [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) classe herda [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) da classe e define uma `ItemSpacing` Propriedade, do tipo `double`, que representa o espaço vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0. A `ListItemsLayout` classe também define static `Vertical` e `Horizontal` Members. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, um `ListItemsLayout` objeto pode ser criado, especificando um [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento.

A [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) classe herda [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) da classe e define as seguintes propriedades:

- `VerticalItemSpacing`, do tipo `double`, que representa o espaço vertical vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0.
- `HorizontalItemSpacing`, do tipo `double`, que representa o espaço horizontal vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0.
- `Span`, do tipo `int`, que representa o número de colunas ou linhas a serem exibidas na grade. O valor padrão dessa propriedade é 1 e seu valor sempre deve ser maior ou igual a 1.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)usa os mecanismos de layout nativos para executar o layout.

## <a name="vertical-list"></a>Lista vertical

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) o exibirá seus itens em um layout de lista vertical. Portanto, não é necessário definir a [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para usar este layout:

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

No entanto, para fins de [`CollectionView`](xref:Xamarin.Forms.CollectionView) integridade, um pode ser definido para exibir seus itens em uma lista vertical [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) definindo sua `VerticalList`Propriedade como:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Como alternativa, isso também pode ser feito definindo [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) a propriedade como um objeto [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) da classe, especificando o `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento:

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

Isso resulta em uma única lista de colunas, que cresce verticalmente à medida que novos itens são adicionados:

[ ![Captura de tela de um layout de lista vertical CollectionView, no layout de lista vertical de CollectionView do Android e Ios](layout-images/vertical-list.png "") ] (layout-images/vertical-list-large.png#lightbox "Layout da lista vertical CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir seus itens em uma lista horizontal definindo sua [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) Propriedade como: `HorizontalList`

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

Como alternativa, isso também pode [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) ser feito definindo a propriedade como um [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) objeto, especificando o `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento:

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

Isso resulta em uma única lista de linhas, que cresce horizontalmente à medida que novos itens são adicionados:

[ ![Captura de tela de um layout de lista horizontal CollectionView, em layout de](layout-images/horizontal-list.png "lista horizontal CollectionView") do Android e Ios] (layout-images/horizontal-list-large.png#lightbox "Layout da lista horizontal CollectionView")

## <a name="vertical-grid"></a>Grade vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir seus itens em uma [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) grade vertical definindo sua propriedade como um [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto cuja [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) Propriedade está definida como `Vertical`:

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

Por padrão, um vertical [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) exibirá itens em uma única coluna. No entanto, este exemplo `GridItemsLayout.Span` define a propriedade como 2. Isso resulta em uma grade de duas colunas, que cresce verticalmente à medida que novos itens são adicionados:

[ ![Captura de tela de um layout de grade vertical CollectionView, em layout de](layout-images/vertical-grid.png "grade vertical CollectionView") do Android e Ios] (layout-images/vertical-grid-large.png#lightbox "Layout de grade vertical CollectionView")

## <a name="horizontal-grid"></a>Grade horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir seus itens em uma [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) grade horizontal definindo sua propriedade como um [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto cuja[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) Propriedade está definida como `Horizontal`:

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

Por padrão, um horizontal [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) exibirá itens em uma única linha. No entanto, este exemplo `GridItemsLayout.Span` define a propriedade como 4. Isso resulta em uma grade de quatro linhas, que cresce horizontalmente à medida que novos itens são adicionados:

[ ![Captura de tela de um layout de grade horizontal CollectionView, em layout de](layout-images/horizontal-grid.png "grade horizontal CollectionView") do Android e Ios] (layout-images/horizontal-grid-large.png#lightbox "Layout de grade horizontal CollectionView")

## <a name="item-spacing"></a>Espaçamento de item

Por padrão, cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) não tem espaço vazio em volta dele. Esse comportamento pode ser alterado definindo as propriedades no layout de itens usado pelo `CollectionView`.

Quando um [`CollectionView`](xref:Xamarin.Forms.CollectionView) define sua [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para um [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) objeto, a `ListItemsLayout.ItemSpacing` propriedade pode ser definida como um `double` valor que representa o espaço vazio em volta de cada item:

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
> A `ListItemsLayout.ItemSpacing` propriedade tem um conjunto de retorno de chamada de validação, que garante que o valor da propriedade seja sempre maior ou igual a 0.

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

Esse código resulta em uma lista vertical de colunas únicas, que tem um espaçamento de 20 em volta de cada item:

[ ![Captura de tela de um CollectionView com espaçamento de item, em espaçamento de item CollectionView do IOS e Android](layout-images/vertical-list-spacing.png "") ] (layout-images/vertical-list-spacing-large.png#lightbox "Espaçamento de item CollectionView")

Quando um [`CollectionView`](xref:Xamarin.Forms.CollectionView) define sua [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriedade para um [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto, as `GridItemsLayout.VerticalItemSpacing` propriedades `GridItemsLayout.HorizontalItemSpacing` e podem ser definidas como `double` valores que representam o espaço vazio vertical e horizontalmente em volta de cada item:

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
> As `GridItemsLayout.VerticalItemSpacing` propriedades `GridItemsLayout.HorizontalItemSpacing` e têm conjuntos de chamadas de validação definidos, o que garante que os valores das propriedades sejam sempre maiores ou iguais a 0.

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

Esse código resulta em uma grade vertical de duas colunas, que tem um espaçamento vertical de 20 em volta de cada item e um espaçamento horizontal de 30 em volta de cada item:

[ ![Captura de tela de um CollectionView com espaçamento de item, em espaçamento de item CollectionView do IOS e Android](layout-images/vertical-grid-spacing.png "") ] (layout-images/vertical-grid-spacing-large.png#lightbox "Espaçamento de item CollectionView")

## <a name="item-sizing"></a>Dimensionamento de item

Por padrão, cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é individualmente medido e dimensionado, desde que os elementos da interface [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) do usuário em não especifiquem tamanhos fixos. Esse comportamento, que pode ser alterado, é especificado pelo valor [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) da propriedade. Esse valor de propriedade pode ser definido como um dos [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) membros da enumeração:

- `MeasureAllItems`– cada item é medido individualmente. Este é o valor padrão.
- `MeasureFirstItem`– somente o primeiro item é medido, sendo que todos os itens subsequentes recebem o mesmo tamanho do primeiro item.

> [!IMPORTANT]
> A `MeasureFirstItem` estratégia de dimensionamento resultará em maior desempenho quando usada em situações em que o tamanho do item se destina a ser uniforme em todos os itens.

O exemplo de código a seguir mostra [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) a definição da propriedade:

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

Os itens em [`CollectionView`](xref:Xamarin.Forms.CollectionView) um podem ser redimensionados dinamicamente em tempo de execução alterando as propriedades relacionadas ao [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)layout de elementos no. Por exemplo, o exemplo de código a seguir [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) altera [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) as propriedades e [`Image`](xref:Xamarin.Forms.Image) de um objeto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

O `OnImageTapped` manipulador de eventos é executado em resposta a [`Image`](xref:Xamarin.Forms.Image) um objeto que está sendo tocado e altera as dimensões da imagem para que seja exibido mais facilmente:

[ ![Captura de tela de um CollectionView com dimensionamento de item dinâmico, em dimensionamento de](layout-images/runtime-resizing.png "Item dinâmico de CollectionView") em Ios e Android] (layout-images/runtime-resizing-large.png#lightbox "Dimensionamento de item dinâmico CollectionView")

## <a name="right-to-left-layout"></a>Layout da direita para a esquerda

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode definir o layout de seu conteúdo em uma direção de fluxo da direita para a [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) esquerda definindo [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)sua propriedade como. No entanto `FlowDirection` , a propriedade deve ser idealmente definida em um layout de página ou raiz, o que faz com que todos os elementos dentro da página ou layout raiz respondam à direção do fluxo:

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

O padrão [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) para um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, o [`CollectionView`](xref:Xamarin.Forms.CollectionView) herda o `FlowDirection` valor da Propriedade do [`StackLayout`](xref:Xamarin.Forms.StackLayout), que, por sua vez `FlowDirection` , herda o valor [`ContentPage`](xref:Xamarin.Forms.ContentPage)da Propriedade do. Isso resulta no layout da direita para a esquerda mostrado nas seguintes capturas de tela:

[ ![Captura de tela de um layout de lista vertical de CollectionView da direita para a esquerda, em](layout-images/vertical-list-rtl.png "layout de lista vertical de CollectionView e Android da direita para a esquerda") ] (layout-images/vertical-list-rtl-large.png#lightbox "Layout de lista vertical da direita para a esquerda de CollectionView")

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Rolagem de CollectionView do Xamarin. Forms](scrolling.md)
