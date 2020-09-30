---
title: Xamarin.Forms Pins do mapa
description: Este artigo explica como criar Pins em um Xamarin.Forms mapa.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 476ef1b8fd6c8e16c5fb3e5fdd1ac60eafd90b7c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559838"
---
# <a name="no-locxamarinforms-map-pins"></a>Xamarin.Forms Pins do mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O Xamarin.Forms [`Map`](xref:Xamarin.Forms.Maps.Map) controle permite que os locais sejam marcados com [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Um `Pin` é um marcador de mapa que abre uma janela de informações quando tocado:

[![Captura de tela de um pino de mapa e sua janela de informações, no iOS e no Android](pins-images/pin-and-information-window.png "Mapear o PIN com a janela de informações")](pins-images/pin-and-information-window-large.png#lightbox "Mapear o PIN com a janela de informações")

Quando um [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto é adicionado à [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) coleção, o PIN é renderizado no mapa.

A [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe tem as seguintes propriedades:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), do tipo `string` , que normalmente representa o endereço para o local do PIN. No entanto, pode ser qualquer `string` conteúdo, não apenas um endereço.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), do tipo `string` , que normalmente representa o título do PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), do tipo [`Position`](xref:Xamarin.Forms.Maps.Position) , que representa a latitude e a longitude do PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), do tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType) , que representa o tipo de PIN.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que um `Pin` pode ser o destino de associações de dados. Para obter mais informações sobre objetos de associação de dados `Pin` , consulte [exibir uma coleção de PIN](#display-a-pin-collection).

Além disso, a [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe define `MarkerClicked` e `InfoWindowClicked` eventos. O `MarkerClicked` evento é acionado quando um PIN é tocado e o `InfoWindowClicked` evento é acionado quando a janela de informações é tocada. O `PinClickedEventArgs` objeto que acompanha ambos os eventos tem uma única `HideInfoWindow` propriedade, do tipo `bool` .

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

Esse XAML cria um [`Map`](xref:Xamarin.Forms.Maps.Map) objeto que mostra a região especificada pelo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objeto. O `MapSpan` objeto é centralizado na latitude e na longitude representada por um [`Position`](xref:Xamarin.Forms.Maps.Position) objeto, que estende os graus 0, 1 de latitude e longitude. Um [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto é adicionado à [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) coleção e desenhado no `Map` no local especificado por sua [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) propriedade. Para obter informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md). Para obter informações sobre como passar argumentos em XAML para objetos que não têm construtores padrão, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

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
> A falha ao definir a [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) Propriedade resultará em uma geração de uma `ArgumentException` que será gerada quando o [`Pin`](xref:Xamarin.Forms.Maps.Pin) for adicionado a um [`Map`](xref:Xamarin.Forms.Maps.Map) .

Este código de exemplo resulta em um único PIN sendo renderizado em um mapa:

[![Captura de tela de um pino de mapa, no iOS e no Android](pins-images/pin-only.png "Fixar PIN")](pins-images/pin-only-large.png#lightbox "Fixar PIN")

## <a name="interact-with-a-pin"></a>Interagir com um PIN

Por padrão, quando um [`Pin`](xref:Xamarin.Forms.Maps.Pin) é tocado, sua janela de informações é exibida:

[![Captura de tela de um pino de mapa e sua janela de informações, no iOS e no Android](pins-images/pin-and-information-window.png "Mapear o PIN com a janela de informações")](pins-images/pin-and-information-window-large.png#lightbox "Mapear o PIN com a janela de informações")

Tocar em outro lugar no mapa fecha a janela de informações.

A [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe define um `MarkerClicked` evento, que é acionado quando um `Pin` é tocado. Não é necessário manipular esse evento para exibir a janela de informações. Em vez disso, esse evento deve ser tratado quando houver um requisito para ser notificado de que um PIN específico foi tocado.

A [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe também define um `InfoWindowClicked` evento que é acionado quando uma janela de informações é tocada. Esse evento deve ser tratado quando houver um requisito para ser notificado de que uma janela de informações específica foi tocada.

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

O `PinClickedEventArgs` objeto que acompanha ambos os eventos tem uma única `HideInfoWindow` propriedade, do tipo `bool` . Quando essa propriedade for definida como `true` dentro de um manipulador de eventos, a janela de informações ficará oculta.

## <a name="pin-types"></a>Tipos de PIN

[`Pin`](xref:Xamarin.Forms.Maps.Pin) os objetos incluem uma [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) propriedade, do tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType) , que representa o tipo de PIN. A enumeração `PinType` define os seguintes membros:

- `Generic`, representa um PIN genérico.
- `Place`, representa um PIN para um local.
- `SavedPin`, representa um PIN para um local salvo.
- `SearchResult`, representa um PIN para um resultado de pesquisa.

No entanto, definir a [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) propriedade para qualquer [`PinType`](xref:Xamarin.Forms.Maps.PinType) membro não altera a aparência do PIN renderizado. Em vez disso, você deve criar um renderizador personalizado para personalizar a aparência do PIN. Para obter mais informações, consulte [Personalizando um pino de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

## <a name="display-a-pin-collection"></a>Exibir uma coleção de marcadores

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define as seguintes propriedades:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), do tipo `IEnumerable` , que especifica a coleção de `IEnumerable` itens a serem exibidos.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicado a cada item na coleção de itens exibidos.
- `ItemTemplateSelector`, do tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , que especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.

> [!IMPORTANT]
> A [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propriedade tem precedência quando ambas `ItemTemplate` as `ItemTemplateSelector` Propriedades e são definidas.

Um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser preenchido com Pins usando a vinculação de dados para associar sua [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) Propriedade a uma `IEnumerable` coleção:

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

Os [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) dados de propriedade são associados à `Locations` Propriedade do ViewModel conectado, que retorna um `ObservableCollection` dos `Location` objetos, que é um tipo personalizado. Cada `Location` objeto define `Address` e `Description` Propriedades, do tipo e de `string` uma `Position` propriedade, do tipo [`Position`](xref:Xamarin.Forms.Maps.Position) .

A aparência de cada item na `IEnumerable` coleção é definida pela definição da [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contém um [`Pin`](xref:Xamarin.Forms.Maps.Pin) objeto que vincula os dados às propriedades apropriadas.

As capturas de tela a seguir mostram uma [`Map`](xref:Xamarin.Forms.Maps.Map) exibição de uma [`Pin`](xref:Xamarin.Forms.Maps.Pin) coleção usando a associação de dados:

[![Captura de tela do mapa com Pins de associação de dados, no iOS e no Android](pins-images/pins-itemsource.png "Mapear com Pins associados a dados")](pins-images/pins-itemsource-large.png#lightbox "Mapear com Pins associados a dados")

### <a name="choose-item-appearance-at-runtime"></a>Escolher a aparência do item em tempo de execução

A aparência de cada item na `IEnumerable` coleção pode ser escolhida em tempo de execução, com base no valor do item, definindo a `ItemTemplateSelector` propriedade como um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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
                    <!-- Change the property values, or the properties that are bound to. -->
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

O exemplo a seguir mostra a `MapItemTemplateSelector` classe:

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

A `MapItemTemplateSelector` classe define `DefaultTemplate` e `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Propriedades que são definidas para modelos de dados diferentes. O `OnSelectTemplate` método retorna o `XamarinTemplate` , que exibe "Xamarin" como um rótulo quando um `Pin` é tocado, quando o item tem um endereço que contém "San Francisco". Quando o item não tem um endereço que contém "San Francisco", o `OnSelectTemplate` método retorna o `DefaultTemplate` .

> [!NOTE]
> Um caso de uso para essa funcionalidade é associar Propriedades de objetos de subclasse [`Pin`](xref:Xamarin.Forms.Maps.Pin) a propriedades diferentes, com base no `Pin` subtipo.

Para obter mais informações sobre seletores de modelo de dados, consulte [Creating a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Criando um Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)