---
title: Dados CollectionView do Xamarin. Forms
description: Um CollectionView é populado com dados definindo sua propriedade ItemsSource para qualquer coleção que implemente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: c8d01846c9b860982cee74390dab85c7473ee141
ms.sourcegitcommit: 283810340de5310f63ef7c3e4b266fe9dc2ffcaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662321"
---
# <a name="xamarinforms-collectionview-data"></a>Dados CollectionView do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) inclui as seguintes propriedades que definem os dados a serem exibidos e sua aparência:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos e tem um valor padrão de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica o modelo a ser aplicado a cada item na coleção de itens a serem exibidos.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) define uma propriedade `ItemsUpdatingScrollMode` que representa o comportamento de rolagem do `CollectionView` quando novos itens são adicionados a ele. Para obter mais informações sobre essa propriedade, consulte [controlar posição de rolagem quando novos itens forem adicionados](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) também pode carregar dados de forma incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Preencher um CollectionView com dados

Uma [`CollectionView`](xref:Xamarin.Forms.CollectionView) é populada com dados definindo sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) para qualquer coleção que implemente `IEnumerable`. Os itens podem ser adicionados em XAML inicializando a propriedade `ItemsSource` de uma matriz de cadeias de caracteres:

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
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) gerará uma exceção se seu [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for atualizado do thread da interface do usuário.

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibe itens em uma lista vertical, conforme mostrado nas capturas de tela a seguir:

[![Captura de tela de CollectionView contendo itens de texto, no iOS e no Android](populate-data-images/text.png "Itens de texto em um CollectionView")](populate-data-images/text-large.png#lightbox "Itens de texto em um CollectionView")

> [!IMPORTANT]
> Se o [`CollectionView`](xref:Xamarin.Forms.CollectionView) for necessário para atualizar à medida que os itens forem adicionados, removidos ou alterados na coleção subjacente, a coleção subjacente deverá ser uma coleção de `IEnumerable` que enviará notificações de alteração de propriedade, como `ObservableCollection`.

Para obter informações sobre como alterar o layout de [`CollectionView`](xref:Xamarin.Forms.CollectionView) , consulte [layout CollectionView do Xamarin. Forms](layout.md). Para obter informações sobre como definir a aparência de cada item na `CollectionView`, consulte [definir a aparência do item](#define-item-appearance).

### <a name="data-binding"></a>Associação de dados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser populado com dados usando a vinculação de dados para associar sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a uma coleção de `IEnumerable`. Em XAML, isso é obtido com a extensão de marcação `Binding`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Neste exemplo, os dados da propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) são associados à propriedade `Monkeys` do ViewModel conectado.

> [!NOTE]
> Associações compiladas podem ser habilitadas para melhorar o desempenho de vinculação de dados em aplicativos Xamarin. Forms. Para saber mais, confira [Associações compiladas](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir a aparência do item

A aparência de cada item na [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser definida definindo a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na lista. No exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`Grid`](xref:Xamarin.Forms.Grid). O `Grid` contém um objeto [`Image`](xref:Xamarin.Forms.Image) e dois objetos [`Label`](xref:Xamarin.Forms.Label) , todos associados a propriedades da classe `Monkey`:

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

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item na [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser escolhida em tempo de execução, com base no valor do item, definindo a propriedade [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como um objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

A classe `MonkeyDataTemplateSelector` define `AmericanMonkey` e `OtherMonkey` propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definidas para modelos de dados diferentes. A substituição de `OnSelectTemplate` retorna o modelo de `AmericanMonkey`, que exibe o nome e o local do macaco em azul-petróleo, quando o nome do macaco contiver "America". Quando o nome do macaco não contém "America", a substituição de `OnSelectTemplate` retorna o modelo de `OtherMonkey`, que exibe o nome do macaco e o local em prata:

[![Captura de tela da seleção de modelo de item de tempo de execução CollectionView em iOS e Android](populate-data-images/datatemplateselector.png "Seleção de modelo de item de tempo de execução em um CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Seleção de modelo de item de tempo de execução em um CollectionView")

Para obter mais informações sobre seletores de modelo de dados, consulte [criar um Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Ao usar [`CollectionView`](xref:Xamarin.Forms.CollectionView), nunca defina o elemento raiz de seus objetos de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) como um `ViewCell`. Isso fará com que uma exceção seja gerada porque `CollectionView` não tem conceito de células.

## <a name="pull-to-refresh"></a>Efetuar pull para atualizar

o [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte à funcionalidade de pull para atualizar por meio do `RefreshView`, que permite que os dados sejam exibidos para serem atualizados ao puxar a lista de itens. O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para seu filho, desde que o filho ofereça suporte a conteúdo rolável. Portanto, o pull para a atualização é implementado para um `CollectionView` definindo-o como o filho de um `RefreshView`:

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

Quando o usuário inicia uma atualização, o `ICommand` definido pela propriedade `Command` é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de pull-to-Refresh CollectionView, no iOS e no Android](populate-data-images/pull-to-refresh.png "Pull para atualização de CollectionView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull para atualização de CollectionView")

O valor da propriedade `RefreshView.IsRefreshing` indica o estado atual da `RefreshView`. Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para `true`. Quando a atualização for concluída, você deverá redefinir a propriedade para `false`.

Para obter mais informações sobre `RefreshView`, consulte [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Carregar dados de forma incremental

o [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte ao carregamento de dados incrementalmente conforme os usuários rolam os itens. Isso permite cenários como o carregamento assíncrono de uma página de dados de um serviço Web, à medida que o usuário rola. Além disso, o ponto no qual mais dados são carregados é configurável para que os usuários não vejam o espaço em branco ou sejam interrompidos da rolagem.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define as propriedades a seguir para controlar o carregamento incremental de dados:

- `RemainingItemsThreshold`, do tipo `int`, o limite de itens ainda não visíveis na lista na qual o evento `RemainingItemsThresholdReached` será acionado.
- `RemainingItemsThresholdReachedCommand`, do tipo `ICommand`, que é executado quando o `RemainingItemsThreshold` é atingido.
- `RemainingItemsThresholdReachedCommandParameter`, do tipo `object`, que é o parâmetro passado para `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) também define um evento de `RemainingItemsThresholdReached` que é disparado quando o `CollectionView` é rolado muito suficiente para que `RemainingItemsThreshold` itens não tenham sido exibidos. Esse evento pode ser tratado para carregar mais itens. Além disso, quando o evento de `RemainingItemsThresholdReached` é acionado, o `RemainingItemsThresholdReachedCommand` é executado, permitindo que o carregamento de dados incremental ocorra em um ViewModel.

O valor padrão da propriedade `RemainingItemsThreshold` é-1, que indica que o evento `RemainingItemsThresholdReached` nunca será acionado. Quando o valor da propriedade for 0, o evento `RemainingItemsThresholdReached` será disparado quando o item final na [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for exibido. Para valores maiores que 0, o evento `RemainingItemsThresholdReached` será disparado quando o `ItemsSource` contiver esse número de itens ainda não rolados.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) valida a propriedade `RemainingItemsThreshold` para que seu valor seja sempre maior ou igual a-1.

O exemplo de XAML a seguir mostra um [`CollectionView`](xref:Xamarin.Forms.CollectionView) que carrega os dados de forma incremental:

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

Neste exemplo de código, o evento `RemainingItemsThresholdReached` é acionado quando há cinco itens ainda não rolados para e, em resposta, executa o manipulador de eventos `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Os dados também podem ser carregados incrementalmente ligando o `RemainingItemsThresholdReachedCommand` a uma implementação de `ICommand` no ViewModel.

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [RefreshView Xamarin. Forms](~/xamarin-forms/user-interface/refreshview.md)
- [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelos de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Criar um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
