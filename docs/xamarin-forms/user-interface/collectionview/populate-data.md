---
title: Xamarin. Forms CollectionView de dados
description: Um CollectionView é preenchido com dados, definindo sua propriedade ItemsSource para qualquer coleção que implemente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 68e28fcbe6a64834d3b594f7f639a1cdd990370d
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970577"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin. Forms CollectionView de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) Define as seguintes propriedades que definem os dados a ser exibido e sua aparência:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos, e tem um valor padrão de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), do tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), especifica o modelo a ser aplicado a cada item na coleção de itens a serem exibidos.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

## <a name="populate-a-collectionview-with-data"></a>Preencher um CollectionView de dados

Um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é preenchida com dados, definindo sua [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implemente `IEnumerable`. Itens podem ser adicionados no XAML, inicializando o `ItemsSource` propriedade de uma matriz de cadeias de caracteres:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Observe que o elemento `x:Array` requer um atributo `Type` que indica o tipo dos itens na matriz.

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> Se o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é necessária para atualizada à medida que itens são adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deve ser um `IEnumerable` coleção que envia a propriedade notificações de alteração, como `ObservableCollection`.

Por padrão, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) exibe itens em uma lista vertical, conforme mostrado nas capturas de tela seguir:

[![Captura de tela de CollectionView que contém os itens de texto, no iOS e Android](populate-data-images/text.png "itens de texto em um CollectionView")](populate-data-images/text-large.png#lightbox "itens de texto em um CollectionView")

Para obter informações sobre como alterar o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) layout, consulte [Especifica um Layout](layout.md). Para obter informações sobre como definir a aparência de cada item na `CollectionView`, consulte [definem a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Associação de dados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser preenchido com dados usando associação de dados para associar seu [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para um `IEnumerable` coleção. No XAML, isso é feito com o `Binding` extensão de marcação:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, o [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) associa dados de propriedade para o `Monkeys` propriedade do modelo de exibição conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de associação de dados em aplicativos xamarin. Forms. Para obter mais informações, consulte [compilado associações](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item na [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser definida ao configurar o [ `CollectionView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

Os elementos especificados na [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na lista. No exemplo, o layout do `DataTemplate` é gerenciada por um [ `Grid` ](xref:Xamarin.Forms.Grid). O `Grid` contém uma [ `Image` ](xref:Xamarin.Forms.Image) objeto e duas [ `Label` ](xref:Xamarin.Forms.Label) objetos, que todos se associar a propriedades do `Monkey` classe:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

As capturas de tela a seguir mostram o resultado da modelagem de cada item na lista:

[![Captura de tela de CollectionView onde cada item é modelado no iOS e Android](populate-data-images/datatemplate.png "itens de modelo em um CollectionView")](populate-data-images/datatemplate-large.png#lightbox "itens de modelo em um CollectionView")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolha a aparência do item em tempo de execução

A aparência de cada item na [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser escolhido no tempo de execução, com base no valor do item, definindo o [ `CollectionView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade para um [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

O [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) estiver definida como um `MonkeyDataTemplateSelector` objeto. A exemplo a seguir mostra o `MonkeyDataTemplateSelector` classe:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

O `MonkeyDataTemplateSelector` classe define `AmericanMonkey` e `OtherMonkey` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) propriedades que são definidas para modelos de dados diferentes. O `OnSelectTemplate` substituir retorna o `AmericanMonkey` modelo, que exibe o monkey nome e local na turquesa, quando o nome do monkey contém "América". Quando o nome do monkey não contém "América", o `OnSelectTemplate` substituir retorna o `OtherMonkey` modelo, que exibe o nome do monkey e o local em prata:

[![Captura de tela de CollectionView runtime item seleção de modelo, no iOS e Android](populate-data-images/datatemplateselector.png "seleção de modelo de item de tempo de execução em um CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "seleção de modelo de item de tempo de execução em um CollectionView")

Para obter mais informações sobre os seletores de modelo de dados, consulte [criar um xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Associação de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelos de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criar um DataTemplateSelector xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
