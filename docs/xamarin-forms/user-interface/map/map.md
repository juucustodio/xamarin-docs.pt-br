---
title: Controle de Mapeamento Xamarin. Forms
description: O controle de mapa é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426226"
---
# <a name="xamarinforms-map-control"></a>Controle de Mapeamento Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O controle de [`Map`](xref:Xamarin.Forms.Maps.Map) é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários:

[![Captura de tela do controle de mapa, no iOS e no Android](map-images/map-default.png "Controle de mapa")](map-images/map-default-large.png#lightbox "Controle de mapa")

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define as propriedades a seguir que controlam a aparência e o comportamento do mapa:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), do tipo `bool`, indica se o mapa está mostrando o local atual do usuário.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), do tipo `IEnumerable`, que especifica a coleção de itens de `IEnumerable` a serem exibidos.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que especifica a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicada a cada item na coleção de itens exibidos.
- `ItemTemplateSelector`, do tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), que especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), do tipo `bool`, determina se o mapa tem permissão para rolar.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), do tipo `bool`, determina se o mapa tem permissão para aplicar zoom.
- `MapElements`, do tipo `IList<MapElement>`, representa a lista de elementos no mapa, como polígonos e polilinhas.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), do tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), indica o estilo de exibição do mapa.
- `MoveToLastRegionOnLayoutChange`, do tipo `bool`, controla se a região de mapa exibida será movida de sua região atual para sua região definida anteriormente quando ocorrer uma alteração de layout.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), do tipo `IList<Pin>`, representa a lista de Pins no mapa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), do tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), retorna a região exibida no momento do mapa.

Essas propriedades, com exceção das propriedades `MapElements`, `Pins`e `VisibleRegion`, são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que podem ser destinos de associações de dados.

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) também define um evento `MapClicked` que é acionado quando o mapa é tocado. O objeto `MapClickedEventArgs` que acompanha o evento tem uma única propriedade chamada `Position`, do tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Quando o evento é acionado, a propriedade `Position` é definida como o local do mapa que foi tocado. Para obter informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md).

Para obter informações sobre as propriedades [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)e `ItemTemplateSelector`, consulte [exibir uma coleção de PIN](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Exibir um mapa

Um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser exibido adicionando-o a um layout ou página:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Uma definição de namespace `xmlns` adicional é necessária para fazer referência aos controles Xamarin. Forms. Maps. No exemplo anterior, o namespace `Xamarin.Forms.Maps` é referenciado por meio da palavra-chave `maps`.

Este é o código C# equivalente:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

Este exemplo chama o construtor de [`Map`](xref:Xamarin.Forms.Maps.Map) padrão, que centraliza o mapa em Roma:

[![Captura de tela do controle de mapa com o local padrão, no iOS e no Android](map-images/map-default.png "Controle de mapa com local padrão")](map-images/map-default-large.png#lightbox "Controle de mapa com local padrão")

Como alternativa, um argumento de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) pode ser passado para um construtor de [`Map`](xref:Xamarin.Forms.Maps.Map) para definir o ponto central e o nível de zoom do mapa quando ele é carregado. Para obter mais informações, consulte [exibir um local específico em um mapa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipos de mapa

A propriedade [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) pode ser definida como um membro de enumeração [`MapType`](xref:Xamarin.Forms.Maps.MapType) para definir o estilo de exibição do mapa. A enumeração `MapType` define os seguintes membros:

- `Street` especifica que um mapa de ruas será exibido.
- `Satellite` especifica que um mapa que contém imagens satélite será exibido.
- `Hybrid` especifica que um mapa que combina a rua e os dados de satélite serão exibidos.

Por padrão, um [`Map`](xref:Xamarin.Forms.Maps.Map) exibirá um mapa de rua se a propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) for indefinida. Como alternativa, a propriedade `MapType` pode ser definida como um dos membros de enumeração [`MapType`](xref:Xamarin.Forms.Maps.MapType) :

```xaml
<maps:Map MapType="Satellite" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) é definida como `Street`:

[![Captura de tela do controle de mapa com o tipo de mapa de rua, no iOS e no Android](map-images/maptype-street.png "Controle de mapa com a rua MapType")](map-images/maptype-street-large.png#lightbox "Map control with the street map type")

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) é definida como `Satellite`:

[![Captura de tela do controle de mapa com o tipo de mapa satélite, no iOS e no Android](map-images/maptype-satellite.png "Controle de mapa com o satélite MapType")](map-images/maptype-satellite-large.png#lightbox "Map control with the satellite map type")

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) é definida como `Hybrid`:

[![Captura de tela do controle de mapa com o tipo de mapa híbrido, no iOS e no Android](map-images/maptype-hybrid.png "Controle de mapa com o MapType híbrido")](map-images/maptype-hybrid-large.png#lightbox "Map control with the hybrid map type")

## <a name="display-a-specific-location-on-a-map"></a>Exibir um local específico em um mapa

A região de um mapa a ser exibida quando um mapa é carregado pode ser definida passando um argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) para o construtor [`Map`](xref:Xamarin.Forms.Maps.Map) :

```xaml
<maps:Map>
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
</maps:Map>
```

Este é o código C# equivalente:

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

Este exemplo cria um objeto [`Map`](xref:Xamarin.Forms.Maps.Map) que mostra a região especificada pelo objeto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . O objeto `MapSpan` é centralizado na latitude e na longitude representada por um objeto [`Position`](xref:Xamarin.Forms.Maps.Position) e abrange 0, 1 latitude e 0, 1 longitude graus. Para obter informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md). Para obter informações sobre como passar argumentos em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

O resultado é que, quando o mapa é exibido, ele é centralizado em um local específico e abrange um número específico de graus de latitude e Longitude:

[![Captura de tela do controle de mapa com o local especificado, no iOS e no Android](map-images/map-region.png "Controle de mapa com um local especificado")](map-images/map-region-large.png#lightbox "Controle de mapa com um local especificado")

## <a name="create-a-mapspan-object"></a>Criar um objeto MapSpan

Há várias abordagens para a criação de objetos de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . Uma abordagem comum é fornecer os argumentos necessários para o construtor de `MapSpan`. Essas são uma latitude e longitude representadas por um objeto [`Position`](xref:Xamarin.Forms.Maps.Position) e `double` valores que representam os graus de latitude e longitude que são abrangidos pelo `MapSpan`. Para obter informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md).

Como alternativa, há três métodos na classe [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que retornam novos objetos `MapSpan`:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) retorna um `MapSpan` com o mesmo `LongitudeDegrees` da instância de classe do método e um raio definido por seus argumentos `north` e `south`.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) retorna uma `MapSpan` que é definida por seus argumentos [`Position`](xref:Xamarin.Forms.Maps.Position) e [`Distance`](xref:Xamarin.Forms.Maps.Distance) .
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) retorna um `MapSpan` com o mesmo centro que a instância de classe do método, mas com um raio multiplicado por seu argumento `double`.

Para obter informações sobre a estrutura [`Distance`](xref:Xamarin.Forms.Maps.Distance) , consulte [posição e distância do mapa](position-distance.md).

Depois que um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) foi criado, as seguintes propriedades podem ser acessadas para recuperar dados sobre ele:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), que representa a [`Position`](xref:Xamarin.Forms.Maps.Position) no centro geográfico da `MapSpan`.
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), que representa os graus de latitude que são abrangidos pelo `MapSpan`.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), que representa os graus de longitude que são abrangidos pelo `MapSpan`.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), que representa o `MapSpan` RADIUS.

## <a name="move-the-map"></a>Mover o mapa

O método [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) pode ser chamado para alterar a posição e o nível de zoom de um mapa. Esse método aceita um argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que define a região do mapa a ser exibido e seu nível de zoom.

O código a seguir mostra um exemplo de como mover a região exibida em um mapa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Aplicar zoom ao mapa

O nível de zoom de um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser alterado sem alterar seu local. Isso pode ser feito usando a interface do usuário do mapa ou programaticamente chamando o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) com um argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que usa o local atual como o argumento [`Position`](xref:Xamarin.Forms.Maps.Position) :

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

Neste exemplo, o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) é chamado com um argumento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) que especifica o local atual do mapa, por meio da propriedade [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) , e o nível de zoom como graus de latitude e longitude. O resultado geral é que o nível de zoom do mapa é alterado, mas seu local não é. Uma abordagem alternativa para implementar o zoom em um mapa é usar o método [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) para controlar o fator de zoom.

> [!IMPORTANT]
> Aplicar zoom a um mapa, seja por meio da interface do usuário do mapa ou programaticamente, requer que a propriedade [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) seja `true`. Para obter mais informações sobre essa propriedade, consulte [Disable zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizar comportamento do mapa

O comportamento de um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser personalizado definindo algumas de suas propriedades e manipulando o evento `MapClicked`.

> [!NOTE]
> O comportamento de mapa adicional customizatin pode ser obtido com a criação de um renderizador personalizado de mapa. Para obter mais informações, consulte [Personalizando um mapa do Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

### <a name="disable-scroll"></a>Desabilitar rolagem

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define uma propriedade [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) do tipo `bool`. Por padrão, essa propriedade é `true`, o que indica que o mapa tem permissão para rolar. Quando essa propriedade for definida como `false`, o mapa não será rolado. O exemplo a seguir mostra a configuração desta propriedade:

```xaml
<maps:Map HasScrollEnabled="false" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>Desabilitar zoom

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define uma propriedade [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) do tipo `bool`. Por padrão, essa propriedade é `true`, o que indica que o zoom pode ser executado no mapa. Quando essa propriedade é definida como `false`, o mapa não pode ser ampliado. O exemplo a seguir mostra a configuração desta propriedade:

```xaml
<maps:Map HasZoomEnabled="false" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>Mostrar o local do usuário

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define uma propriedade [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) do tipo `bool`. Por padrão, essa propriedade é `false`, o que indica que o mapa não está mostrando o local atual do usuário. Quando essa propriedade é definida como `true`, o mapa mostra o local atual do usuário. O exemplo a seguir mostra a configuração desta propriedade:

```xaml
<maps:Map IsShowingUser="true" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> No iOS, no Android e no Plataforma Universal do Windows, o acesso ao local do usuário requer permissões de localização para que o aplicativo tenha sido concedido. Para obter mais informações, consulte [configuração de plataforma](setup.md#platform-configuration).

### <a name="maintain-map-region-on-layout-change"></a>Manter a região do mapa na alteração de layout

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define uma propriedade `MoveToLastRegionOnLayoutChange` do tipo `bool`. Por padrão, essa propriedade é `true`, o que indica que a região do mapa exibida será movida de sua região atual para sua região definida anteriormente quando ocorrer uma alteração de layout, como na rotação do dispositivo. Quando essa propriedade é definida como `false`, a região de mapa exibida permanecerá centralizada quando ocorrer uma alteração de layout. O exemplo a seguir mostra a configuração desta propriedade:

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>Cliques de mapa

A classe [`Map`](xref:Xamarin.Forms.Maps.Map) define um evento `MapClicked` que é acionado quando o mapa é tocado. O objeto `MapClickedEventArgs` que acompanha o evento tem uma única propriedade chamada `Position`, do tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Quando o evento é acionado, a propriedade `Position` é definida como o local do mapa que foi tocado. Para obter informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md).

O exemplo de código a seguir mostra um manipulador de eventos para o evento `MapClicked`:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Neste exemplo, o manipulador de eventos `OnMapClicked` gera a latitude e a longitude que representam o local do mapa tocado. O manipulador de eventos pode ser registrado com o evento `MapClicked` da seguinte maneira:

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

Este é o código C# equivalente:

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Posição e distância do mapa](position-distance.md)
- [Personalizando um mapa do Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
