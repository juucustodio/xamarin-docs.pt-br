---
title: Layout CollectionView do Xamarin. Forms
description: Por padrão, um CollectionView exibirá seus itens em uma lista vertical. No entanto, listas e grades verticais e horizontais podem ser especificadas.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: 901ea8a3b00a129d39e824e1ab0b053c8d5c743c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696814"
---
# <a name="xamarinforms-collectionview-layout"></a>Layout CollectionView do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define as seguintes propriedades que controlam o layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), do tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), especifica o layout a ser usado.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), do tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), especifica a estratégia de medida do item a ser usada.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Por padrão, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, qualquer um dos seguintes layouts pode ser usado:

- Lista vertical – uma lista de colunas única que cresce verticalmente à medida que novos itens são adicionados.
- Lista Horizontal – uma única lista de linhas que aumenta horizontalmente à medida que novos itens são adicionados.
- Grade vertical – uma grade de várias colunas que cresce verticalmente à medida que novos itens são adicionados.
- Grade horizontal – uma grade de várias linhas que cresce horizontalmente à medida que novos itens são adicionados.

Esses layouts podem ser especificados definindo a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como a classe derivada da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . Essa classe define as seguintes propriedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), do tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica a direção na qual o [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expande à medida que os itens são adicionados.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de ajuste são alinhados com os itens.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento dos pontos de ajuste ao rolar.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados. Para obter mais informações sobre pontos de encaixe, consulte [pontos de alinhamento](scrolling.md#snap-points) no guia de [rolagem de CollectionView do Xamarin. Forms](scrolling.md) .

A enumeração [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) define os seguintes membros:

- `Vertical` indica que o [`CollectionView`](xref:Xamarin.Forms.CollectionView) será expandido verticalmente à medida que os itens forem adicionados.
- `Horizontal` indica que o [`CollectionView`](xref:Xamarin.Forms.CollectionView) será expandido horizontalmente conforme os itens forem adicionados.

A classe `LinearItemsLayout` herda da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) e define uma propriedade `ItemSpacing`, do tipo `double`, que representa o espaço vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0. A classe `LinearItemsLayout` também define membros estáticos `Vertical` e `Horizontal`. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, um objeto `LinearItemsLayout` pode ser criado, especificando um membro de enumeração [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como um argumento.

A classe [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) herda da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) e define as seguintes propriedades:

- `VerticalItemSpacing`, do tipo `double`, que representa o espaço em branco vertical em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0.
- `HorizontalItemSpacing`, do tipo `double`, que representa o espaço horizontal vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0.
- `Span`, do tipo `int`, que representa o número de colunas ou linhas a serem exibidas na grade. O valor padrão dessa propriedade é 1 e seu valor sempre deve ser maior ou igual a 1.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa os mecanismos de layout nativos para executar o layout.

## <a name="vertical-list"></a>Lista vertical

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em um layout de lista vertical. Portanto, não é necessário definir a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) para usar este layout:

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

No entanto, para fins de integridade, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser definido para exibir seus itens em uma lista vertical definindo sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Como alternativa, isso também pode ser feito definindo a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, especificando o `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como o valor da propriedade `Orientation`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Isso resulta em uma única lista de colunas, que cresce verticalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout de lista vertical CollectionView, no iOS e no Android](layout-images/vertical-list.png "Layout da lista vertical CollectionView")](layout-images/vertical-list-large.png#lightbox "Layout da lista vertical CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma lista horizontal definindo sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como `HorizontalList`:

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

Como alternativa, esse layout também pode ser feito definindo a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, especificando o `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como o valor da propriedade `Orientation`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Isso resulta em uma única lista de linhas, que cresce horizontalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout de lista horizontal CollectionView, no iOS e no Android](layout-images/horizontal-list.png "Layout da lista horizontal CollectionView")](layout-images/horizontal-list-large.png#lightbox "Layout da lista horizontal CollectionView")

## <a name="vertical-grid"></a>Grade vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma grade vertical definindo sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) cuja propriedade [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) está definida como `Vertical`:

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

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Por padrão, um [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) vertical exibirá itens em uma única coluna. No entanto, este exemplo define a propriedade `GridItemsLayout.Span` como 2. Isso resulta em uma grade de duas colunas, que cresce verticalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout de grade vertical CollectionView, no iOS e no Android](layout-images/vertical-grid.png "Layout de grade vertical CollectionView")](layout-images/vertical-grid-large.png#lightbox "Layout de grade vertical CollectionView")

## <a name="horizontal-grid"></a>Grade horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir seus itens em uma grade horizontal definindo sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) cuja propriedade[ `Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) está definida como `Horizontal`:

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

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Por padrão, um [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) horizontal exibirá itens em uma única linha. No entanto, este exemplo define a propriedade `GridItemsLayout.Span` como 4. Isso resulta em uma grade de quatro linhas, que cresce horizontalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout de grade horizontal CollectionView, no iOS e no Android](layout-images/horizontal-grid.png "Layout de grade horizontal CollectionView")](layout-images/horizontal-grid-large.png#lightbox "Layout de grade horizontal CollectionView")

## <a name="headers-and-footers"></a>Cabeçalhos e rodapés

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode apresentar um cabeçalho e um rodapé que rolam com os itens na lista. O cabeçalho e o rodapé podem ser cadeias de caracteres, exibições ou objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define as seguintes propriedades para especificar o cabeçalho e o rodapé:

- `Header`, do tipo `object`, especifica a cadeia de caracteres, a associação ou a exibição que será exibida no início da lista.
- `HeaderTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica o `DataTemplate` a ser usado para formatar o `Header`.
- `Footer`, do tipo `object`, especifica a cadeia de caracteres, a associação ou a exibição que será exibida no final da lista.
- `FooterTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica o `DataTemplate` a ser usado para formatar o `Footer`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Quando um cabeçalho é adicionado a um layout que cresce horizontalmente, da esquerda para a direita, o cabeçalho é exibido à esquerda da lista. Da mesma forma, quando um rodapé é adicionado a um layout que cresce horizontalmente, da esquerda para a direita, o rodapé é exibido à direita da lista.

### <a name="display-strings-in-the-header-and-footer"></a>Exibir cadeias de caracteres no cabeçalho e no rodapé

As propriedades `Header` e `Footer` podem ser definidas como `string` valores, conforme mostrado no exemplo a seguir:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Esse código resulta nas seguintes capturas de tela, com o cabeçalho mostrado na captura de tela do iOS e o rodapé mostrado na captura de tela do Android:

[![Captura de tela de um cabeçalho e rodapé de cadeia de caracteres CollectionView, no iOS e no Android](layout-images/header-footer-string.png "Cabeçalho e rodapé da cadeia de caracteres CollectionView")](layout-images/header-footer-string-large.png#lightbox "Cabeçalho e rodapé da cadeia de caracteres CollectionView")

### <a name="display-views-in-the-header-and-footer"></a>Exibir exibições no cabeçalho e no rodapé

As propriedades `Header` e `Footer` podem ser definidas como uma exibição. Isso pode ser uma exibição única ou uma exibição que contenha várias exibições filhas. O exemplo a seguir mostra as propriedades `Header` e `Footer` cada uma delas definida como um objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contém um objeto [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Esse código resulta nas seguintes capturas de tela, com o cabeçalho mostrado na captura de tela do iOS e o rodapé mostrado na captura de tela do Android:

[![Captura de tela de um cabeçalho e rodapé CollectionView usando exibições, no iOS e no Android](layout-images/header-footer-view.png "Cabeçalho e rodapé da exibição CollectionView")](layout-images/header-footer-view-large.png#lightbox "Cabeçalho e rodapé da exibição CollectionView")

### <a name="display-a-templated-header-and-footer"></a>Exibir um cabeçalho e um rodapé de modelo

As propriedades `HeaderTemplate` e `FooterTemplate` podem ser definidas como [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que são usados para formatar o cabeçalho e o rodapé. Nesse cenário, as propriedades `Header` e `Footer` devem ser associadas à fonte atual para que os modelos sejam aplicados, conforme mostrado no exemplo a seguir:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Esse código resulta nas seguintes capturas de tela, com o cabeçalho mostrado na captura de tela do iOS e o rodapé mostrado na captura de tela do Android:

[![Captura de tela de um cabeçalho e rodapé CollectionView usando modelos, no iOS e no Android](layout-images/header-footer-template.png "Cabeçalho e rodapé do modelo CollectionView")](layout-images/header-footer-template-large.png#lightbox "Cabeçalho e rodapé do modelo CollectionView")

## <a name="item-spacing"></a>Espaçamento de item

Por padrão, cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) não tem espaço vazio em volta dele. Esse comportamento pode ser alterado definindo as propriedades no layout de itens usado pelo `CollectionView`.

Quando um [`CollectionView`](xref:Xamarin.Forms.CollectionView) define sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, a propriedade `LinearItemsLayout.ItemSpacing` pode ser definida como um valor de `double` que representa o espaço vazio em volta de cada item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> A propriedade `LinearItemsLayout.ItemSpacing` tem um conjunto de retorno de chamada de validação, que garante que o valor da propriedade seja sempre maior ou igual a 0.

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Esse código resulta em uma lista vertical de colunas únicas, que tem um espaçamento de 20 em volta de cada item:

[![Captura de tela de um CollectionView com espaçamento de item, no iOS e no Android](layout-images/vertical-list-spacing.png "Espaçamento de item CollectionView")](layout-images/vertical-list-spacing-large.png#lightbox "Espaçamento de item CollectionView")

Quando um [`CollectionView`](xref:Xamarin.Forms.CollectionView) define sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) , as propriedades `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` podem ser definidas para `double` valores que representam o espaço vazio vertical e horizontalmente em volta de cada item:

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
> As propriedades `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` têm retornos de chamada de validação definidos, o que garante que os valores das propriedades sejam sempre maiores ou iguais a 0.

Este é o código C# equivalente:

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

[![Captura de tela de um CollectionView com espaçamento de item, no Android](layout-images/vertical-grid-spacing.png "Espaçamento de item CollectionView")](layout-images/vertical-grid-spacing-large.png#lightbox "Espaçamento de item CollectionView")

## <a name="item-sizing"></a>Dimensionamento de item

Por padrão, cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é individualmente medido e dimensionado, desde que os elementos da interface do usuário no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) não especifiquem tamanhos fixos. Esse comportamento, que pode ser alterado, é especificado pelo valor da propriedade [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) . Esse valor de propriedade pode ser definido como um dos membros de enumeração de [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) :

- `MeasureAllItems` – cada item é medido individualmente. Este é o valor padrão.
- `MeasureFirstItem` – somente o primeiro item é medido, com todos os itens subsequentes que recebem o mesmo tamanho do primeiro item.

> [!IMPORTANT]
> A estratégia de dimensionamento de `MeasureFirstItem` resultará em maior desempenho quando usada em situações em que o tamanho do item se destina a ser uniforme em todos os itens.

O exemplo de código a seguir mostra a definição da propriedade [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) :

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Redimensionamento dinâmico de itens

Os itens em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) podem ser redimensionados dinamicamente em tempo de execução alterando as propriedades relacionadas ao layout de elementos no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Por exemplo, o exemplo de código a seguir altera as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de um objeto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

O manipulador de eventos `OnImageTapped` é executado em resposta a um objeto [`Image`](xref:Xamarin.Forms.Image) que está sendo tocado e altera as dimensões da imagem para que seja exibida mais facilmente:

[![Captura de tela de um CollectionView com dimensionamento de item dinâmico, no iOS e no Android](layout-images/runtime-resizing.png "Dimensionamento de item dinâmico CollectionView")](layout-images/runtime-resizing-large.png#lightbox "Dimensionamento de item dinâmico CollectionView")

## <a name="right-to-left-layout"></a>Layout da direita para a esquerda

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode definir o layout de seu conteúdo em uma direção de fluxo da direita para a esquerda definindo sua propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). No entanto, a propriedade `FlowDirection`, idealmente, deve ser definida em um layout de página ou raiz, o que faz com que todos os elementos dentro da página ou layout raiz respondam à direção do fluxo:

```xaml
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

O [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) padrão para um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, o [`CollectionView`](xref:Xamarin.Forms.CollectionView) herda o valor da propriedade `FlowDirection` da [`StackLayout`](xref:Xamarin.Forms.StackLayout)que, por sua vez, herda o valor da propriedade `FlowDirection` da [`ContentPage`](xref:Xamarin.Forms.ContentPage). Isso resulta no layout da direita para a esquerda mostrado nas seguintes capturas de tela:

[![Captura de tela de um layout de lista vertical de CollectionView da direita para a esquerda, no iOS e no Android](layout-images/vertical-list-rtl.png "Layout de lista vertical da direita para a esquerda de CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "Layout de lista vertical da direita para a esquerda de CollectionView")

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Rolagem de CollectionView do Xamarin. Forms](scrolling.md)
