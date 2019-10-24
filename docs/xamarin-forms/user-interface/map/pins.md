---
title: Pins do mapa do Xamarin. Forms
description: Este artigo explica como criar Pins em um mapa do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697335"
---
# <a name="xamarinforms-map-pins"></a>Pins do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O controle `Maps` Xamarin. Forms permite que os locais sejam marcados com `Pin` objetos. Uma `Pin` é um marcador de mapa que abre uma janela de informações quando clicado ou tocado.

A classe `Pin` tem as seguintes propriedades:

- `Type` é um valor de enumeração `PinType`: Generic, Place, SavedPin ou SearchResult.
- `Position` é uma instância de `Position` que contém a latitude e a longitude do PIN.
- `Label` é uma `string` que normalmente é exibida como o título do PIN.
- `Address` é uma `string` que será exibida na janela informações. Pode ser qualquer `string` conteúdo, não apenas um endereço.

Essas propriedades têm o suporte de objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que o `Pin` pode ser o destino de associações de dados. Para obter mais informações, consulte [criar Pins com associação de dados](#create-pins-with-data-binding).

## <a name="create-map-pins"></a>Criar Pins do mapa

Uma instância de `Pin` pode ser criada no código e adicionada a um mapa:

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> O valor `PinType` afeta como os Pins são renderizados dependendo da plataforma. Para personalizar a aparência de um PIN, você deve criar um renderizador personalizado. Para obter mais informações, consulte [Personalizando um pino de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="create-pins-with-data-binding"></a>Criar Pins com associação de dados

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) expõe as seguintes propriedades:

- `ItemsSource` – especifica a coleção de itens de `IEnumerable` a serem exibidos.
- `ItemTemplate` – especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicado a cada item na coleção de itens exibidos.
- `ItemTemplateSelector` – especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.

> [!NOTE]
> A propriedade `ItemTemplate` tem precedência quando as propriedades `ItemTemplate` e `ItemTemplateSelector` são definidas.

Uma [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser populada com dados usando a vinculação de dados para associar sua propriedade `ItemsSource` a uma coleção de `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

O `ItemsSource` dados de propriedade são associados à propriedade `Locations` do modelo de exibição conectado, que retorna uma `ObservableCollection` de objetos `Location`, que é um tipo personalizado. Cada objeto de `Location` define `Address` e `Description` Propriedades, do tipo `string` e uma propriedade de `Position`, do tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

A aparência de cada item na coleção de `IEnumerable` é definida pela definição da propriedade `ItemTemplate` como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contém um objeto [`Pin`](xref:Xamarin.Forms.Maps.Pin) que vincula os dados às propriedades apropriadas.

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) exibir uma coleção de [`Pin`](xref:Xamarin.Forms.Maps.Pin) usando a associação de dados:

[![Captura de tela do mapa com Pins de associação de dados, no iOS e no Android](map-images/pins-itemssource.png "Mapear com Pins associados a dados")](map-images/pins-itemssource-large.png#lightbox "Mapear com Pins associados a dados")

### <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item na coleção de `IEnumerable` pode ser escolhida em tempo de execução, com base no valor do item, definindo a propriedade `ItemTemplateSelector` como uma [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

O exemplo a seguir mostra a classe `MapItemTemplateSelector`:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

A classe `MapItemTemplateSelector` define `DefaultTemplate` e `XamarinTemplate` propriedades [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definidas para modelos de dados diferentes. O método `OnSelectTemplate` retorna o `XamarinTemplate`, que exibe "Xamarin" como um rótulo quando um `Pin` é tocado, quando o item tem um endereço que contém "San Francisco". Quando o item não tem um endereço que contém "San Francisco", o método `OnSelectTemplate` retorna o `DefaultTemplate`.

Para obter mais informações sobre seletores de modelo de dados, consulte [Creating a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="pin-events"></a>Fixar eventos

A classe `Pin` fornece dois eventos:

- `MarkerClicked` é acionado quando o PIN é clicado ou tocado.
- `InfoWindowClicked` é acionado quando a janela de informações é clicada ou tocada.

Os manipuladores de eventos podem ser anexados a um PIN no código:

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Criando um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
