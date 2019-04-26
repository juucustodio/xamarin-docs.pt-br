---
title: Preencher CollectionView de xamarin. Forms com dados
description: Um CollectionView é preenchido com dados, definindo sua propriedade ItemsSource para qualquer coleção que implemente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 57012202d981b96dba42f3017a19f2e32e4982ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366298"
---
# <a name="populate-xamarinforms-collectionview-with-data"></a>Preencher CollectionView de xamarin. Forms com dados

![Visualizar](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> O `CollectionView` atualmente é uma visualização e não tem algumas das suas funcionalidades planejada. Além disso, a API pode mudar conforme a implementação for concluída.

`CollectionView` Define as seguintes propriedades que definem os dados a ser exibido e sua aparência:

- `ItemsSource`, do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos, e tem um valor padrão de `null`.
- `ItemTemplate`, do tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), especifica o modelo a ser aplicado a cada item na coleção de itens a serem exibidos.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

## <a name="populate-a-collectionview-with-data"></a>Preencher um CollectionView de dados

Um `CollectionView` é preenchido com dados, definindo seu `ItemsSource` propriedade para qualquer coleção que implemente `IEnumerable`. Itens podem ser adicionados no XAML, inicializando o `ItemsSource` propriedade de uma matriz de cadeias de caracteres:

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
> Se o `CollectionView` é necessária para atualizada à medida que itens são adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deve ser um `IEnumerable` coleção que envia a propriedade notificações de alteração, como `ObservableCollection`.

Por padrão, `CollectionView` exibe itens em uma lista vertical, conforme mostrado nas capturas de tela seguir:

[![Captura de tela de CollectionView que contém os itens de texto, no iOS e Android](populate-data-images/text.png "itens de texto em um CollectionView")](populate-data-images/text-large.png#lightbox "itens de texto em um CollectionView")

Para obter informações sobre como alterar o `CollectionView` layout, consulte [especificar um Layout](layout.md). Para obter informações sobre como definir a aparência de cada item na `CollectionView`, consulte [definem a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Associação de dados

`CollectionView` pode ser preenchido com dados usando associação de dados para associar seu `ItemsSource` propriedade para um `IEnumerable` coleção. No XAML, isso é feito com o `Binding` extensão de marcação:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, o `ItemsSource` associa dados de propriedade para o `Monkeys` propriedade do modelo de exibição conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de associação de dados em aplicativos xamarin. Forms. Para obter mais informações, consulte [compilado associações](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item na `CollectionView` pode ser definida ao configurar o `CollectionView.ItemTemplate` propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Associação de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelos de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
