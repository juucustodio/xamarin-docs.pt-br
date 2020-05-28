---
title: Xamarin.FormsDados CollectionView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e381184271d4a7bfa9872d2502d2281b1f3864bf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134557"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin.FormsDados CollectionView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)inclui as seguintes propriedades que definem os dados a serem exibidos e sua aparência:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), do tipo `IEnumerable` , especifica a coleção de itens a serem exibidos e tem um valor padrão de `null` .
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , especifica o modelo a ser aplicado a cada item na coleção de itens a ser exibida.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)define uma `ItemsUpdatingScrollMode` propriedade que representa o comportamento de rolagem do `CollectionView` quando novos itens são adicionados a ele. Para obter mais informações sobre essa propriedade, consulte [controlar posição de rolagem quando novos itens forem adicionados](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte à virtualização de dados incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Preencher um CollectionView com dados

Um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é populado com dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implementa `IEnumerable` . Os itens podem ser adicionados em XAML inicializando a `ItemsSource` propriedade de uma matriz de cadeias de caracteres:

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

Este é o código C# equivalente:

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

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)gerará uma exceção se sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for atualizada fora do thread da interface do usuário.

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) o exibe itens em uma lista vertical, conforme mostrado nas capturas de tela a seguir:

[![Captura de tela de CollectionView contendo itens de texto, no iOS e no Android](populate-data-images/text.png "Itens de texto em um CollectionView")](populate-data-images/text-large.png#lightbox "Itens de texto em um CollectionView")

> [!IMPORTANT]
> Se o [`CollectionView`](xref:Xamarin.Forms.CollectionView) for necessário para atualizar à medida que os itens forem adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deverá ser uma `IEnumerable` coleção que enviará notificações de alteração de propriedade, como `ObservableCollection` .

Para obter informações sobre como alterar o [`CollectionView`](xref:Xamarin.Forms.CollectionView) layout, consulte [ Xamarin.Forms layout CollectionView](layout.md). Para obter informações sobre como definir a aparência de cada item no `CollectionView` , consulte [definir a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Vinculação de dados

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode ser preenchido com dados usando a vinculação de dados para associar sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) Propriedade a uma `IEnumerable` coleção. Em XAML, isso é obtido com a `Binding` extensão de marcação:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, os [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) dados de propriedade são associados à `Monkeys` Propriedade do ViewModel conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de vinculação de dados em Xamarin.Forms aplicativos. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre a vinculação de dados, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser definida definindo a [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

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

Este é o código C# equivalente:

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

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na lista. No exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`Grid`](xref:Xamarin.Forms.Grid) . O `Grid` contém um [`Image`](xref:Xamarin.Forms.Image) objeto e dois [`Label`](xref:Xamarin.Forms.Label) objetos, todos associados a propriedades da `Monkey` classe:

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

[![Captura de tela de CollectionView em que cada item é modelado, no iOS e no Android](populate-data-images/datatemplate.png "Itens de modelo em um CollectionView")](populate-data-images/datatemplate-large.png#lightbox "Itens de modelo em um CollectionView")

Para obter mais informações sobre modelos de dados, consulte [ Xamarin.Forms modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser escolhida em tempo de execução, com base no valor do item, definindo a [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

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

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

A [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade é definida como um `MonkeyDataTemplateSelector` objeto. O exemplo a seguir mostra a `MonkeyDataTemplateSelector` classe:

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

A `MonkeyDataTemplateSelector` classe define `AmericanMonkey` e `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Propriedades que são definidas para modelos de dados diferentes. A `OnSelectTemplate` substituição retorna o `AmericanMonkey` modelo, que exibe o nome do macaco e o local em azul-petróleo, quando o nome do macaco contiver "America". Quando o nome do macaco não contém "America", a `OnSelectTemplate` substituição retorna o `OtherMonkey` modelo, que exibe o nome do macaco e o local em prata:

[![Captura de tela da seleção de modelo de item de tempo de execução CollectionView em iOS e Android](populate-data-images/datatemplateselector.png "Seleção de modelo de item de tempo de execução em um CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Seleção de modelo de item de tempo de execução em um CollectionView")

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Ao usar [`CollectionView`](xref:Xamarin.Forms.CollectionView) , nunca defina o elemento raiz de seus [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos como um `ViewCell` . Isso resultará em uma exceção sendo gerada porque `CollectionView` não tem nenhum conceito de células.

## <a name="context-menus"></a>Menus de contexto

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte a menus de contexto para itens de dados por meio do `SwipeView` , que revela o menu de contexto com um gesto de passar o dedo. O `SwipeView` é um controle de contêiner que encapsula em um item de conteúdo e fornece itens de menu de contexto para esse item de conteúdo. Portanto, os menus de contexto são implementados para um `CollectionView` criando um `SwipeView` que define o conteúdo que o `SwipeView` encapsulado e os itens do menu de contexto que são revelados pelo gesto de passar o dedo. Isso é feito definindo o `SwipeView` como a exibição raiz no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada item de dados no `CollectionView` :

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

Neste exemplo, o `SwipeView` conteúdo é um [`Grid`](xref:Xamarin.Forms.Grid) que define a aparência de cada item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Os itens do dedo são usados para executar ações no `SwipeView` conteúdo e são revelados quando o controle é transdedodo do lado esquerdo:

[![Captura de tela de itens de menu de contexto CollectionView, em iOS e Android](populate-data-images/swipeview.png "CollectionView com itens de menu de contexto SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView com itens de menu de contexto SwipeView")

`SwipeView`dá suporte a quatro direções de toque diferentes, com a direção do dedo sendo definida pela `SwipeItems` coleção direcional à `SwipeItems` qual os objetos são adicionados. Por padrão, um item de dedo é executado quando ele é tocado pelo usuário. Além disso, quando um item de dedo for executado, os itens do dedo serão ocultados e o `SwipeView` conteúdo será exibido novamente. No entanto, esses comportamentos podem ser alterados.

Para obter mais informações sobre o `SwipeView` controle, consulte [ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Puxar para atualizar

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte à funcionalidade de pull para atualizar por meio do `RefreshView` , que permite que os dados sejam exibidos para serem atualizados ao puxar a lista de itens. O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para seu filho, desde que o filho ofereça suporte a conteúdo rolável. Portanto, o pull para a atualização é implementado para um definindo `CollectionView` -o como o filho de um `RefreshView` :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
</RefreshView>
```

Este é o código C# equivalente:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

Quando o usuário inicia uma atualização, o `ICommand` definido pela `Command` propriedade é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de pull-to-Refresh CollectionView, no iOS e no Android](populate-data-images/pull-to-refresh.png "Pull para atualização de CollectionView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull para atualização de CollectionView")

O valor da `RefreshView.IsRefreshing` propriedade indica o estado atual do `RefreshView` . Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para o `true` . Quando a atualização for concluída, você deverá redefinir a propriedade para `false` .

Para obter mais informações sobre o `RefreshView` , consulte [ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Carregar dados de forma incremental

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte à virtualização de dados incremental à medida que o usuário rola. Isso permite cenários como o carregamento assíncrono de uma página de dados de um serviço Web, à medida que o usuário rola. Além disso, o ponto no qual mais dados são carregados é configurável para que os usuários não vejam o espaço em branco ou sejam interrompidos da rolagem.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define as propriedades a seguir para controlar o carregamento incremental de dados:

- `RemainingItemsThreshold`, do tipo `int` , o limite de itens ainda não visíveis na lista na qual o `RemainingItemsThresholdReached` evento será acionado.
- `RemainingItemsThresholdReachedCommand`, do tipo `ICommand` , que é executado quando o `RemainingItemsThreshold` é atingido.
- `RemainingItemsThresholdReachedCommandParameter`, do tipo `object`, que é o parâmetro passado para `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)também define um `RemainingItemsThresholdReached` evento que é acionado quando o `CollectionView` é rolado muito suficiente para que `RemainingItemsThreshold` os itens não tenham sido exibidos. Esse evento pode ser tratado para carregar mais itens. Além disso, quando o `RemainingItemsThresholdReached` evento é acionado, o `RemainingItemsThresholdReachedCommand` é executado, permitindo que o carregamento de dados incremental ocorra em um ViewModel.

O valor padrão da `RemainingItemsThreshold` propriedade é-1, que indica que o `RemainingItemsThresholdReached` evento nunca será acionado. Quando o valor da propriedade for 0, o `RemainingItemsThresholdReached` evento será acionado quando o item final no [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for exibido. Para valores maiores que 0, o `RemainingItemsThresholdReached` evento será acionado quando o `ItemsSource` contiver esse número de itens ainda não rolados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)valida a `RemainingItemsThreshold` propriedade para que seu valor seja sempre maior ou igual a-1.

O exemplo de XAML a seguir mostra um [`CollectionView`](xref:Xamarin.Forms.CollectionView) que carrega dados de forma incremental:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

Neste exemplo de código, o `RemainingItemsThresholdReached` evento é acionado quando há 5 itens ainda não rolados para e, em resposta, executa o `OnCollectionViewRemainingItemsThresholdReached` manipulador de eventos:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Os dados também podem ser carregados incrementalmente ligando o `RemainingItemsThresholdReachedCommand` a uma `ICommand` implementação no ViewModel.

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.FormsRefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.FormsSwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Xamarin.FormsAssociação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsModelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
