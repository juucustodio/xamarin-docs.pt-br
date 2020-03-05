---
title: Dados de CarouselView do Xamarin. Forms
description: Um CarouselView é populado com dados definindo sua propriedade ItemsSource para qualquer coleção que implemente IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: 154d039e95ccc2de28e09a7162a32a19f8f84656
ms.sourcegitcommit: 5d22f37dfc358678df52a4d17c57261056a72cb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "78291557"
---
# <a name="xamarinforms-carouselview-data"></a>Dados de CarouselView do Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) inclui as seguintes propriedades que definem os dados a serem exibidos e sua aparência:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos e tem um valor padrão de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica o modelo a ser aplicado a cada item na coleção de itens a serem exibidos.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) define uma propriedade `ItemsUpdatingScrollMode` que representa o comportamento de rolagem do `CarouselView` quando novos itens são adicionados a ele. Para obter mais informações sobre essa propriedade, consulte [controlar posição de rolagem quando novos itens forem adicionados](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) também pode carregar dados de forma incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Popular um CarouselView com dados

Uma [`CarouselView`](xref:Xamarin.Forms.CarouselView) é populada com dados definindo sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) para qualquer coleção que implemente `IEnumerable`. Os itens podem ser adicionados em XAML inicializando a propriedade `ItemsSource` de uma matriz de cadeias de caracteres:

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
> Se o [`CarouselView`](xref:Xamarin.Forms.CarouselView) for necessário para atualizar à medida que os itens forem adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deverá ser uma coleção de `IEnumerable` que enviará notificações de alteração de propriedade, como `ObservableCollection`.

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe os itens horizontalmente. As capturas de tela a seguir mostram um `CarouselView` exibir itens de cadeia de caracteres diferentes no iOS e no Android:

[![Captura de tela de CarouselView contendo itens de texto, no iOS e no Android](populate-data-images/text.png "Itens de texto em um CarouselView")](populate-data-images/text-large.png#lightbox "Itens de texto em um CarouselView")

Para obter informações sobre como alterar a orientação de [`CarouselView`](xref:Xamarin.Forms.CarouselView) , consulte [layout CarouselView do Xamarin. Forms](layout.md). Para obter informações sobre como definir a aparência de cada item na `CarouselView`, consulte [definir a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Associação de dados

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser populado com dados usando a vinculação de dados para associar sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a uma coleção de `IEnumerable`. Em XAML, isso é obtido com a extensão de marcação `Binding`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, os dados da propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) são associados à propriedade `Monkeys` do ViewModel conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de vinculação de dados em aplicativos Xamarin. Forms. Para obter mais informações, confira [Associações compiladas do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item na [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definida definindo a propriedade [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na `CarouselView`. No exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`StackLayout`](xref:Xamarin.Forms.StackLayout), e os dados são exibidos com um objeto [`Image`](xref:Xamarin.Forms.Image) e três objetos [`Label`](xref:Xamarin.Forms.Label) , todos associados a propriedades da classe `Monkey`:

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

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item na [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser escolhida em tempo de execução, com base no valor do item, definindo a propriedade [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como um objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

A propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) é definida como um objeto `MonkeyDataTemplateSelector`. O exemplo a seguir mostra a classe `MonkeyDataTemplateSelector`:

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

A classe `MonkeyDataTemplateSelector` define `AmericanMonkey` e `OtherMonkey` propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definidas para modelos de dados diferentes. A substituição de `OnSelectTemplate` retorna o modelo de `AmericanMonkey` quando o nome do macaco contém "America". Quando o nome do macaco não contém "America", a substituição de `OnSelectTemplate` retorna o modelo de `OtherMonkey`, que exibe seus dados esmaecidos:

[![Captura de tela da seleção de modelo de item de tempo de execução CarouselView em iOS e Android](populate-data-images/datatemplateselector.png "Seleção de modelo de item de tempo de execução em um CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Seleção de modelo de item de tempo de execução em um CarouselView")

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Ao usar [`CarouselView`](xref:Xamarin.Forms.CarouselView), nunca defina o elemento raiz de seus objetos de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) como um `ViewCell`. Isso fará com que uma exceção seja gerada porque `CarouselView` não tem conceito de células.

## <a name="display-indicators"></a>Exibir indicadores

Os indicadores, que representam o número de itens e a posição atual em uma `CarouselView`, podem ser exibidos ao lado do `CarouselView`. Isso pode ser feito com o controle de `IndicatorView`:

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

Neste exemplo, a `IndicatorView` é renderizada abaixo da `CarouselView`, com um indicador para cada item na `CarouselView`. O `IndicatorView` é populado com dados definindo a propriedade `CarouselView.IndicatorView` para o objeto `IndicatorView`. Cada indicador é um círculo cinza claro, enquanto o indicador que representa o item atual no `CarouselView` é cinza escuro:

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](populate-data-images/indicators.png "Círculos de IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos de IndicatorView")

> [!IMPORTANT]
> Definir a propriedade `CarouselView.IndicatorView` resulta na associação de propriedade `IndicatorView.Position` à propriedade `CarouselView.Position` e a propriedade `IndicatorView.ItemsSource` associando à propriedade `CarouselView.ItemsSource`.

Para obter mais informações sobre indicadores, consulte [Xamarin. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="pull-to-refresh"></a>Efetuar pull para atualizar

o [`CarouselView`](xref:Xamarin.Forms.CarouselView) dá suporte à funcionalidade de pull para atualizar por meio do `RefreshView`, que permite que os dados sejam exibidos para serem atualizados ao puxar os itens. O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para seu filho, desde que o filho ofereça suporte a conteúdo rolável. Portanto, o pull para a atualização é implementado para um `CarouselView` definindo-o como o filho de um `RefreshView`:

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

Quando o usuário inicia uma atualização, o `ICommand` definido pela propriedade `Command` é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de CarouselView pull para atualização, no iOS e no Android](populate-data-images/pull-to-refresh.png "CarouselView pull para atualização")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView pull para atualização")

O valor da propriedade `RefreshView.IsRefreshing` indica o estado atual da `RefreshView`. Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para `true`. Quando a atualização for concluída, você deverá redefinir a propriedade para `false`.

Para obter mais informações sobre `RefreshView`, consulte [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Carregar dados de forma incremental

o [`CarouselView`](xref:Xamarin.Forms.CarouselView) dá suporte ao carregamento de dados incrementalmente conforme os usuários rolam os itens. Isso permite cenários como o carregamento assíncrono de uma página de dados de um serviço Web, à medida que o usuário rola. Além disso, o ponto no qual mais dados são carregados é configurável para que os usuários não vejam o espaço em branco ou sejam interrompidos da rolagem.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as propriedades a seguir para controlar o carregamento incremental de dados:

- `RemainingItemsThreshold`, do tipo `int`, o limite de itens ainda não visíveis na lista na qual o evento `RemainingItemsThresholdReached` será acionado.
- `RemainingItemsThresholdReachedCommand`, do tipo `ICommand`, que é executado quando o `RemainingItemsThreshold` é atingido.
- `RemainingItemsThresholdReachedCommandParameter`, do tipo `object`, que é o parâmetro passado para `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) também define um evento de `RemainingItemsThresholdReached` que é disparado quando o `CarouselView` é rolado muito suficiente para que `RemainingItemsThreshold` itens não tenham sido exibidos. Esse evento pode ser tratado para carregar mais itens. Além disso, quando o evento de `RemainingItemsThresholdReached` é acionado, o `RemainingItemsThresholdReachedCommand` é executado, permitindo que o carregamento de dados incremental ocorra em um ViewModel.

O valor padrão da propriedade `RemainingItemsThreshold` é-1, que indica que o evento `RemainingItemsThresholdReached` nunca será acionado. Quando o valor da propriedade for 0, o evento `RemainingItemsThresholdReached` será disparado quando o item final na [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for exibido. Para valores maiores que 0, o evento `RemainingItemsThresholdReached` será disparado quando o `ItemsSource` contiver esse número de itens ainda não rolados.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) valida a propriedade `RemainingItemsThreshold` para que seu valor seja sempre maior ou igual a-1.

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que carrega os dados de forma incremental:

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

Neste exemplo de código, o evento `RemainingItemsThresholdReached` é acionado quando há dois itens ainda não rolados para, e em resposta executa o manipulador de eventos `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Os dados também podem ser carregados incrementalmente ligando o `RemainingItemsThresholdReachedCommand` a uma implementação de `ICommand` no ViewModel.

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [IndicatorView Xamarin. Forms](~/xamarin-forms/user-interface/indicatorview.md)
- [RefreshView Xamarin. Forms](~/xamarin-forms/user-interface/refreshview.md)
- [Associação de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criar um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
