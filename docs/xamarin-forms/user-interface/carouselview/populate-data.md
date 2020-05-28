---
title: Xamarin.FormsDados do CarouselView
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
ms.openlocfilehash: 1081bfafae8e4d7a7a522414e9b45cde48037f1d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136546"
---
# <a name="xamarinforms-carouselview-data"></a>Xamarin.FormsDados do CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)inclui as seguintes propriedades que definem os dados a serem exibidos e sua aparência:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), do tipo `IEnumerable` , especifica a coleção de itens a serem exibidos e tem um valor padrão de `null` .
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , especifica o modelo a ser aplicado a cada item na coleção de itens a ser exibida.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)define uma `ItemsUpdatingScrollMode` propriedade que representa o comportamento de rolagem do `CarouselView` quando novos itens são adicionados a ele. Para obter mais informações sobre essa propriedade, consulte [controlar posição de rolagem quando novos itens forem adicionados](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView)dá suporte à virtualização de dados incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Popular um CarouselView com dados

Um [`CarouselView`](xref:Xamarin.Forms.CarouselView) é populado com dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implementa `IEnumerable` . Os itens podem ser adicionados em XAML inicializando a `ItemsSource` propriedade de uma matriz de cadeias de caracteres:

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> Observe que o elemento `x:Array` requer um atributo `Type` que indica o tipo dos itens na matriz.

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
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
> Se o [`CarouselView`](xref:Xamarin.Forms.CarouselView) for necessário para atualizar à medida que os itens forem adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deverá ser uma `IEnumerable` coleção que enviará notificações de alteração de propriedade, como `ObservableCollection` .

Por padrão, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe os itens horizontalmente. As capturas de tela a seguir mostram uma `CarouselView` exibição de itens de cadeia de caracteres diferentes no Ios e no Android:

[![Captura de tela de CarouselView contendo itens de texto, no iOS e no Android](populate-data-images/text.png "Itens de texto em um CarouselView")](populate-data-images/text-large.png#lightbox "Itens de texto em um CarouselView")

Para obter informações sobre como alterar a [`CarouselView`](xref:Xamarin.Forms.CarouselView) orientação, consulte [ Xamarin.Forms layout CarouselView](layout.md). Para obter informações sobre como definir a aparência de cada item no `CarouselView` , consulte [definir a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Vinculação de dados

[`CarouselView`](xref:Xamarin.Forms.CarouselView)pode ser preenchido com dados usando a vinculação de dados para associar sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) Propriedade a uma `IEnumerable` coleção. Em XAML, isso é obtido com a `Binding` extensão de marcação:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, os [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) dados de propriedade são associados à `Monkeys` Propriedade do ViewModel conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de vinculação de dados em Xamarin.Forms aplicativos. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre a vinculação de dados, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item no [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definida definindo a [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item no `CarouselView` . No exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , e os dados são exibidos com um [`Image`](xref:Xamarin.Forms.Image) objeto e três [`Label`](xref:Xamarin.Forms.Label) objetos, todos associados a propriedades da `Monkey` classe:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

As capturas de tela a seguir mostram o resultado de Templating de cada item:

[![Captura de tela de CarouselView em que cada item é modelado, no iOS e no Android](populate-data-images/datatemplate.png "Itens de modelo em um CarouselView")](populate-data-images/datatemplate-large.png#lightbox "Itens de modelo em um CarouselView")

Para obter mais informações sobre modelos de dados, consulte [ Xamarin.Forms modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item no [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser escolhida em tempo de execução, com base no valor do item, definindo a [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
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

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
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

A `MonkeyDataTemplateSelector` classe define `AmericanMonkey` e `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Propriedades que são definidas para modelos de dados diferentes. A `OnSelectTemplate` substituição retorna o `AmericanMonkey` modelo quando o nome do macaco contém "America". Quando o nome do macaco não contém "America", a `OnSelectTemplate` substituição retorna o `OtherMonkey` modelo, que exibe seus dados esmaecidos:

[![Captura de tela da seleção de modelo de item de tempo de execução CarouselView em iOS e Android](populate-data-images/datatemplateselector.png "Seleção de modelo de item de tempo de execução em um CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Seleção de modelo de item de tempo de execução em um CarouselView")

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Ao usar [`CarouselView`](xref:Xamarin.Forms.CarouselView) , nunca defina o elemento raiz de seus [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos como um `ViewCell` . Isso resultará em uma exceção sendo gerada porque `CarouselView` não tem nenhum conceito de células.

## <a name="display-indicators"></a>exibir indicadores

Os indicadores, que representam o número de itens e a posição atual em um `CarouselView` , podem ser exibidos ao lado de `CarouselView` . Isso pode ser feito com o `IndicatorView` controle:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

Neste exemplo, o `IndicatorView` é renderizado abaixo do `CarouselView` , com um indicador para cada item no `CarouselView` . O `IndicatorView` é preenchido com dados definindo a `CarouselView.IndicatorView` propriedade para o `IndicatorView` objeto. Cada indicador é um círculo cinza claro, enquanto o indicador que representa o item atual no `CarouselView` é cinza escuro:

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](populate-data-images/indicators.png "Círculos de IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos de IndicatorView")

> [!IMPORTANT]
> Definir a `CarouselView.IndicatorView` Propriedade resulta na `IndicatorView.Position` Associação de propriedade à `CarouselView.Position` propriedade e a associação de `IndicatorView.ItemsSource` Propriedade à `CarouselView.ItemsSource` propriedade.

Para obter mais informações sobre indicadores, consulte [ Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="context-menus"></a>Menus de contexto

[`CarouselView`](xref:Xamarin.Forms.CarouselView)dá suporte a menus de contexto para itens de dados por meio do `SwipeView` , que revela o menu de contexto com um gesto de passar o dedo. O `SwipeView` é um controle de contêiner que encapsula em um item de conteúdo e fornece itens de menu de contexto para esse item de conteúdo. Portanto, os menus de contexto são implementados para um `CarouselView` criando um `SwipeView` que define o conteúdo que o `SwipeView` encapsulado e os itens do menu de contexto que são revelados pelo gesto de passar o dedo. Isso é obtido com a adição de um `SwipeView` ao [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada item de dados no `CarouselView` :

```xaml
<CarouselView x:Name="carouselView"
              ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                    <Frame HasShadow="True"
                           BorderColor="DarkGray"
                           CornerRadius="5"
                           Margin="20"
                           HeightRequest="300"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand">
                        <SwipeView>
                            <SwipeView.TopItems>
                                <SwipeItems>
                                    <SwipeItem Text="Favorite"
                                               IconImageSource="favorite.png"
                                               BackgroundColor="LightGreen"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.FavoriteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.TopItems>
                            <SwipeView.BottomItems>
                                <SwipeItems>
                                    <SwipeItem Text="Delete"
                                               IconImageSource="delete.png"
                                               BackgroundColor="LightPink"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.DeleteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.BottomItems>
                            <StackLayout>
                                <!-- Define item appearance -->
                            </StackLayout>
                        </SwipeView>
                    </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    StackLayout stackLayout = new StackLayout();
    Frame frame = new Frame { ... };

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: carouselView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: carouselView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.TopItems = new SwipeItems { favoriteSwipeItem };
    swipeView.BottomItems = new SwipeItems { deleteSwipeItem };

    StackLayout swipeViewStackLayout = new StackLayout { ... };
    swipeView.Content = swipeViewStackLayout;
    frame.Content = swipeView;
    stackLayout.Children.Add(frame);

    return stackLayout;
});
```

Neste exemplo, o `SwipeView` conteúdo é um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define a aparência de cada item que está circundado por um [`Frame`](xref:Xamarin.Forms.Frame) no [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Os itens do dedo são usados para executar ações no `SwipeView` conteúdo e são revelados quando o controle é transdedodo da parte superior e da parte inferior:

[![Captura de tela do item de menu de contexto inferior do CarouselView, no Ios e no Android](populate-data-images/swipeview-bottom.png "CarouselView com item de menu de contexto SwipeView inferior")](populate-data-images/swipeview-bottom-large.png#lightbox "CarouselView com item de menu de contexto SwipeView inferior") 
 [ ![Captura de tela do item de menu superior do CarouselView, no Ios e no Android](populate-data-images/swipeview-top.png "CarouselView com o item de menu de contexto SwipeView superior")](populate-data-images/swipeview-top-large.png#lightbox "CarouselView com o item de menu de contexto SwipeView superior")

`SwipeView`dá suporte a quatro direções de toque diferentes, com a direção do dedo sendo definida pela `SwipeItems` coleção direcional à `SwipeItems` qual os objetos são adicionados. Por padrão, um item de dedo é executado quando ele é tocado pelo usuário. Além disso, quando um item de dedo for executado, os itens do dedo serão ocultados e o `SwipeView` conteúdo será exibido novamente. No entanto, esses comportamentos podem ser alterados.

Para obter mais informações sobre o `SwipeView` controle, consulte [ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Puxar para atualizar

[`CarouselView`](xref:Xamarin.Forms.CarouselView)dá suporte à funcionalidade de pull para atualizar por meio do `RefreshView` , que permite que os dados sejam exibidos para serem atualizados ao puxar os itens. O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para seu filho, desde que o filho ofereça suporte a conteúdo rolável. Portanto, o pull para a atualização é implementado para um definindo `CarouselView` -o como o filho de um `RefreshView` :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
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

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

Quando o usuário inicia uma atualização, o `ICommand` definido pela `Command` propriedade é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de CarouselView pull para atualização, no iOS e no Android](populate-data-images/pull-to-refresh.png "CarouselView pull para atualização")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView pull para atualização")

O valor da `RefreshView.IsRefreshing` propriedade indica o estado atual do `RefreshView` . Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para o `true` . Quando a atualização for concluída, você deverá redefinir a propriedade para `false` .

Para obter mais informações sobre o `RefreshView` , consulte [ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Carregar dados de forma incremental

[`CarouselView`](xref:Xamarin.Forms.CarouselView)dá suporte à virtualização de dados incremental à medida que o usuário rola. Isso permite cenários como o carregamento assíncrono de uma página de dados de um serviço Web, à medida que o usuário rola. Além disso, o ponto no qual mais dados são carregados é configurável para que os usuários não vejam o espaço em branco ou sejam interrompidos da rolagem.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define as propriedades a seguir para controlar o carregamento incremental de dados:

- `RemainingItemsThreshold`, do tipo `int` , o limite de itens ainda não visíveis na lista na qual o `RemainingItemsThresholdReached` evento será acionado.
- `RemainingItemsThresholdReachedCommand`, do tipo `ICommand` , que é executado quando o `RemainingItemsThreshold` é atingido.
- `RemainingItemsThresholdReachedCommandParameter`, do tipo `object`, que é o parâmetro passado para `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)também define um `RemainingItemsThresholdReached` evento que é acionado quando o `CarouselView` é rolado muito suficiente para que `RemainingItemsThreshold` os itens não tenham sido exibidos. Esse evento pode ser tratado para carregar mais itens. Além disso, quando o `RemainingItemsThresholdReached` evento é acionado, o `RemainingItemsThresholdReachedCommand` é executado, permitindo que o carregamento de dados incremental ocorra em um ViewModel.

O valor padrão da `RemainingItemsThreshold` propriedade é-1, que indica que o `RemainingItemsThresholdReached` evento nunca será acionado. Quando o valor da propriedade for 0, o `RemainingItemsThresholdReached` evento será acionado quando o item final no [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for exibido. Para valores maiores que 0, o `RemainingItemsThresholdReached` evento será acionado quando o `ItemsSource` contiver esse número de itens ainda não rolados.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)valida a `RemainingItemsThreshold` propriedade para que seu valor seja sempre maior ou igual a-1.

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que carrega dados de forma incremental:

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

Neste exemplo de código, o `RemainingItemsThresholdReached` evento é acionado quando há dois itens ainda não rolados para e, em resposta, executa o `OnCollectionViewRemainingItemsThresholdReached` manipulador de eventos:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Os dados também podem ser carregados incrementalmente ligando o `RemainingItemsThresholdReachedCommand` a uma `ICommand` implementação no ViewModel.

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsIndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin.FormsRefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.FormsAssociação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsModelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criar um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
