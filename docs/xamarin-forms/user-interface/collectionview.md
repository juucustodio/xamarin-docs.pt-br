---
title: Xamarin. Forms CollectionView
description: O CollectionView é uma exibição flexível e de alto desempenho para apresentações de listas de dados usando as especificações de layout diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246302"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![Visualizar](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` é um modo de exibição para apresentar as listas de dados usando as especificações de layout diferente. Tem como objetivo fornecer uma mais flexível e alternativa de alto desempenho para `ListView`. Enquanto o `CollectionView` e `ListView` APIs são semelhantes, existem algumas diferenças importantes:

- `CollectionView` não tem nenhum conceito de células. Em vez disso, os modelos de dados são usados para definir a aparência de cada item de dados na lista.
- `CollectionView` reduz a superfície de API do `ListView`. Muitas propriedades e eventos do `ListView` não estão presentes no `CollectionView`.
- `CollectionView` tem um modelo de layout flexível que permite que os dados a ser apresentado verticalmente ou horizontalmente, em uma lista ou uma grade.

Um `CollectionView` é consumido, definindo seu `ItemsSource` propriedade para qualquer coleção que implemente `IEnumerable`e sua `ItemTemplate` propriedade para um `DataTemplate` que define cada aspecto do item de lista. Além disso, sua `ItemsLayout` propriedade deve ser definida como um `ItemsLayout` classe, para definir a especificação de layout para a lista.

> [!IMPORTANT]
> O `CollectionView` atualmente é uma visualização prévia e não tem grande parte de sua funcionalidade planejada. Além disso, a API mudará conforme a implementação for concluída.

`CollectionView` está disponível no xamarin. Forms 4.0-pre1. No entanto, ele é atualmente experimental e só pode ser usado, adicionando a seguinte linha de código para seus `AppDelegate` classe no iOS, ou para seus `MainActivity` classe no Android, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>Populando um CollectionView de dados

Um `CollectionView` é preenchido com dados, definindo seu `ItemSource` propriedade para qualquer coleção que implemente `IEnumerable`. Itens podem ser adicionados no XAML, inicializando o `ItemsSource` propriedade de uma matriz de itens:

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
> Observe que o `x:Array` elemento requer um `Type` atributo que indica o tipo dos itens na matriz.

Além disso, uma `CollectionView` podem ser populados com dados por meio de associação de dados para associar seu `ItemsSource` propriedade para um `IEnumerable` coleção. No XAML, isso é feito com o `Binding` extensão de marcação:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Neste exemplo, o `ItemsSource` associa dados de propriedade para o `Monkeys` propriedade do modelo de exibição conectados, que retorna um `IList<Monkey>` coleção. O seguinte exemplo de código mostra o `Monkey` classe, que contém quatro propriedades:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>Fornecendo um modo de exibição para o estado vazio

Quando o `CollectionView` não tem nenhum dado para exibir, uma mensagem apropriada pode ser exibida para o usuário. Isso é feito definindo a `CollectionView.EmptyView` propriedade para um `object` que será exibido quando a coleção especificada pelo `ItemSource` propriedade está vazia:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

O `EmptyView` propriedade pode ser definida como um `string`, uma associação ou uma exibição e, portanto, pode incluir o conteúdo interativo, se necessário.

Além disso, o `EmptyViewTemplate` propriedade pode ser definida como uma `DataTemplate` que será usado para formatar o `EmptyView`.

## <a name="defining-list-item-appearance"></a>Definir a aparência do item de lista

A aparência dos dados para cada item na `CollectionView` pode ser definida ao configurar o `CollectionView.ItemTemplate` propriedade para um `DataTemplate`:

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

Neste exemplo, o `DataTemplate` contém uma `Grid` com um `Image`e dois `Label` instâncias, que todos se associar a propriedades do `Monkey` objeto.

Para obter mais informações sobre modelos de dados, consulte [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="specifying-a-layout"></a>Especificando um layout

Por padrão, um `CollectionView` exibirá seus itens em uma lista vertical. No entanto, qualquer uma das especificações de layout a seguir podem ser usados:

- Lista vertical – uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados.
- Lista horizontal – uma lista de única linha cresce horizontalmente conforme novos itens são adicionados.
- Grade vertical – uma grade de várias coluna que cresce verticalmente conforme novos itens são adicionados.
- Grade horizontal – uma grade de várias linhas cresce horizontalmente conforme novos itens são adicionados.

Esses layouts podem ser especificados definindo a `CollectionView.ItemsLayout` propriedade para um `ItemsLayout` classe, com o `ListItemsLayout` classe que fornece um layout da lista e o `GridItemsLayout` classe fornece um layout de grade.

O `ItemsLayout` classe define o `Orientation` propriedade do tipo `ItemsLayoutOrientation`. O `ItemsLayoutOrientation` enumeração define `Vertical` e `Horizontal` valores de membro.

O `ListItemsLayout` herda a `ItemsLayout` classe e define estático `VerticalList` e `HorizontalList` membros. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, uma `ListItemsLayout` instância pode ser criada, especificando um `ItemsLayoutOrientation` membro de enumeração como um argumento.

O `GridItemsLayout` herda o `ItemsLayout` de classe e define um `Span` propriedade do tipo `int`, que representa o número de colunas ou linhas a serem exibidas na grade. O valor padrão de `Span` propriedade é 1, e seu valor deve sempre ser maior que ou igual a 1.

### <a name="vertical-list"></a>Lista vertical

Por padrão, um `CollectionView` exibirá seus itens em um layout da lista vertical. Portanto, não é necessário definir o `ItemsLayout` propriedade para usar este layout:

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

Como alternativa, isso também pode ser feito definindo a `ItemsLayout` propriedade a uma instância das `ListItemsLayout` classe, especificando o `Vertical` `ItemsLayoutOrientation` membro de enumeração como um argumento:

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

Isso resulta em uma lista de coluna única que cresce verticalmente conforme novos itens são adicionados:

[![Layout da lista vertical de CollectionView](collectionview-images/vertical-list.png "layout da lista vertical de CollectionView")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>Lista horizontal

Um `CollectionView` pode exibir seus itens em uma lista horizontal, definindo seu `ItemsLayout` propriedade estática `ListItemsLayout.HorizontalList` membro:

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

Como alternativa, isso também pode ser feito definindo a `ItemsLayout` propriedade a uma instância das `ListItemsLayout` classe, especificando o `Horizontal` `ItemsLayoutOrientation` membro de enumeração como um argumento:

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

Isso resulta em uma lista de única linha, que cresce horizontalmente conforme novos itens são adicionados:

[![Layout de lista horizontal CollectionView](collectionview-images/horizontal-list.png "CollectionView layout de lista horizontal")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>Grade vertical

Um `CollectionView` pode exibir seus itens em uma grade vertical, definindo seu `ItemsLayout` propriedade para um `GridItemsLayout` instância cujo `Orientation` estiver definida como `Vertical`:

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

Por padrão, um vertical `GridItemsLayout` exibirá itens em uma única coluna. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade como 2. Isso resulta em uma grade de duas colunas, cresce verticalmente conforme novos itens são adicionados:

[![Layout de grade vertical CollectionView](collectionview-images/vertical-grid.png "CollectionView layout de grade vertical")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>Grade horizontal

Um `CollectionView` pode exibir seus itens em uma grade horizontal, definindo seu `ItemsLayout` propriedade para um `GridItemsLayout` instância cujo `Orientation` estiver definida como `Horizontal`:

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

Por padrão, um horizontal `GridItemsLayout` exibirá itens em uma única linha. No entanto, este exemplo define o `GridItemsLayout.Span` propriedade para 4. Isso resulta em uma grade de quatro linhas, que cresce horizontalmente conforme novos itens são adicionados:

[![Layout de grade horizontal CollectionView](collectionview-images/horizontal-grid.png "CollectionView layout de grade horizontal")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>Rolagem

`CollectionView` pode rolar os itens solicitados na exibição com o `ScrollTo` método. Esse método rola o item no índice especificado na exibição:

```csharp
_collectionView.ScrollTo(12);
```

Como alternativa, uma sobrecarga desse método pode ser usada para rolar o item especificado na exibição:

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

Ambas as sobrecargas permitem que os argumentos adicionais sejam especificados que indicam o grupo que o item está no, a posição exata do item depois que a rolagem tiver concluído (Iniciar), center, end e se deseja animar a rolagem.

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
