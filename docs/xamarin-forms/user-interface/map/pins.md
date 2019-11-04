---
title: Pins do mapa do Xamarin. Forms
description: Este artigo explica como criar Pins em um mapa do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 930d2dcc701f88e2a350ec1011405bb18b86de6e
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425557"
---
# <a name="xamarinforms-map-pins"></a>Pins do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O controle [`Map`](xref:Xamarin.Forms.Maps.Map) Xamarin. Forms permite que os locais sejam marcados com [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Uma `Pin` é um marcador de mapa que abre uma janela de informações quando tocado:

[![Captura de tela de um pino de mapa e sua janela de informações, no iOS e no Android](pins-images/pin-and-information-window.png "Mapear o PIN com a janela de informações")](pins-images/pin-and-information-window-large.png#lightbox "Mapear o PIN com a janela de informações")

Quando um objeto de [`Pin`](xref:Xamarin.Forms.Maps.Pin) é adicionado à coleção de [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) , o PIN é renderizado no mapa.

A classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) tem as seguintes propriedades:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), do tipo `string`, que normalmente representa o endereço para o local do PIN. No entanto, pode ser qualquer `string` conteúdo, não apenas um endereço.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), do tipo `string`, que normalmente representa o título do PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), do tipo [`Position`](xref:Xamarin.Forms.Maps.Position), que representa a latitude e a longitude do PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), do tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), que representa o tipo de PIN.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que uma `Pin` pode ser o destino de associações de dados. Para obter mais informações sobre vinculação de dados `Pin` objetos, consulte [exibir uma coleção de PIN](#display-a-pin-collection).

Além disso, a classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) define `MarkerClicked` e `InfoWindowClicked` eventos. O evento `MarkerClicked` é disparado quando um PIN é tocado e o evento `InfoWindowClicked` é acionado quando a janela de informações é tocada. O objeto `PinClickedEventArgs` que acompanha ambos os eventos tem uma única propriedade `HideInfoWindow`, do tipo `bool`.

## <a name="display-a-pin"></a>Exibir um PIN

Um [`Pin`](xref:Xamarin.Forms.Maps.Pin) pode ser adicionado a um [`Map`](xref:Xamarin.Forms.Maps.Map) em XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False">
         <x:Arguments>
             <maps:MapSpan>
                 <x:Arguments>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     <x:Double>0.01</x:Double>
                     <x:Double>0.01</x:Double>
                 </x:Arguments>
             </maps:MapSpan>
         </x:Arguments>
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

Esse XAML cria um objeto [`Map`](xref:Xamarin.Forms.Maps.Map) que mostra a região especificada pelo objeto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . O objeto `MapSpan` é centralizado na latitude e na longitude representada por um objeto [`Position`](xref:Xamarin.Forms.Maps.Position) , que estende os graus 0, 1 de latitude e longitude. Um objeto [`Pin`](xref:Xamarin.Forms.Maps.Pin) é adicionado à coleção de [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) e desenhado no `Map` no local especificado por sua propriedade [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) . Para obter informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md). Para obter informações sobre como passar argumentos em XAML para objetos que não têm construtores padrão, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

Este é o código C# equivalente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> A falha ao definir a propriedade [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) resultará na geração de um `ArgumentException` quando o [`Pin`](xref:Xamarin.Forms.Maps.Pin) for adicionado a um [`Map`](xref:Xamarin.Forms.Maps.Map).

Este código de exemplo resulta em um único PIN sendo renderizado em um mapa:

[![Captura de tela de um pino de mapa, no iOS e no Android](pins-images/pin-only.png "Fixar PIN")](pins-images/pin-only-large.png#lightbox "Fixar PIN")

## <a name="interact-with-a-pin"></a>Interagir com um PIN

Por padrão, quando um [`Pin`](xref:Xamarin.Forms.Maps.Pin) é tocado, sua janela de informações é exibida:

[![Captura de tela de um pino de mapa e sua janela de informações, no iOS e no Android](pins-images/pin-and-information-window.png "Mapear o PIN com a janela de informações")](pins-images/pin-and-information-window-large.png#lightbox "Mapear o PIN com a janela de informações")

Tocar em outro lugar no mapa fecha a janela de informações.

A classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) define um evento `MarkerClicked`, que é acionado quando um `Pin` é tocado. Não é necessário manipular esse evento para exibir a janela de informações. Em vez disso, esse evento deve ser tratado quando houver um requisito para ser notificado de que um PIN específico foi tocado.

A classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) também define um evento `InfoWindowClicked` que é acionado quando uma janela de informações é tocada. Esse evento deve ser tratado quando houver um requisito para ser notificado de que uma janela de informações específica foi tocada.

O código a seguir mostra um exemplo de tratamento desses eventos:

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

O objeto `PinClickedEventArgs` que acompanha ambos os eventos tem uma única propriedade `HideInfoWindow`, do tipo `bool`. Quando essa propriedade é definida como `true` dentro de um manipulador de eventos, a janela de informações será ocultada.

## <a name="pin-types"></a>Tipos de PIN

os objetos [`Pin`](xref:Xamarin.Forms.Maps.Pin) incluem uma propriedade [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) , do tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), que representa o tipo de PIN. A enumeração `PinType` define os seguintes membros:

- `Generic`, representa um PIN genérico.
- `Place`, representa um PIN para um local.
- `SavedPin`, representa um PIN para um local salvo.
- `SearchResult`, representa um PIN para um resultado de pesquisa.

No entanto, definir a propriedade [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) como qualquer membro de [`PinType`](xref:Xamarin.Forms.Maps.PinType) não altera a aparência do pino renderizado. Em vez disso, você deve criar um renderizador personalizado para personalizar a aparência do PIN. Para obter mais informações, consulte [Personalizando um pino de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="display-a-pin-collection"></a>Exibir uma coleção de PIN

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define as seguintes propriedades:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), do tipo `IEnumerable`, que especifica a coleção de itens de `IEnumerable` a serem exibidos.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicada a cada item na coleção de itens exibidos.
- `ItemTemplateSelector`, do tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.

> [!IMPORTANT]
> A propriedade [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) tem precedência quando as propriedades `ItemTemplate` e `ItemTemplateSelector` são definidas.

Uma [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser populada com Pins usando a vinculação de dados para associar sua propriedade [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) a uma coleção de `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
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

O [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) dados de propriedade são associados à propriedade `Locations` do ViewModel conectado, que retorna uma `ObservableCollection` de objetos `Location`, que é um tipo personalizado. Cada objeto de `Location` define `Address` e `Description` Propriedades, do tipo `string` e uma propriedade de `Position`, do tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

A aparência de cada item na coleção de `IEnumerable` é definida pela definição da propriedade [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) como uma [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contém um objeto [`Pin`](xref:Xamarin.Forms.Maps.Pin) que vincula os dados às propriedades apropriadas.

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) exibir uma coleção de [`Pin`](xref:Xamarin.Forms.Maps.Pin) usando a associação de dados:

[![Captura de tela do mapa com Pins de associação de dados, no iOS e no Android](pins-images/pins-itemsource.png "Mapear com Pins associados a dados")](pins-images/pins-itemsource-large.png#lightbox "Mapear com Pins associados a dados")

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

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Criando um DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
