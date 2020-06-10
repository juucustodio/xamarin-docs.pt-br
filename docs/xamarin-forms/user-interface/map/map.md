---
Título: " Xamarin.Forms controle de mapeamento" Descrição: "o controle de mapa é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários. "
MS. Prod: xamarin MS. AssetID: 22C99029-0B16-43A6-BF58-26B48C4AED38 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/29/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map-control"></a>Xamarin.FormsControle de Mapeamento

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários:

[![Captura de tela do controle de mapa, no iOS e no Android](map-images/map-default.png "Controle de mapa")](map-images/map-default-large.png#lightbox "Controle de mapa")

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define as seguintes propriedades que controlam a aparência e o comportamento do mapa:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), do tipo `bool` , indica se o mapa está mostrando o local atual do usuário.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), do tipo `IEnumerable` , que especifica a coleção de `IEnumerable` itens a serem exibidos.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que especifica o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) a ser aplicado a cada item na coleção de itens exibidos.
- `ItemTemplateSelector`, do tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , que especifica o [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que será usado para escolher um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para um item no tempo de execução.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), do tipo `bool` , determina se o mapa tem permissão para rolar.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), do tipo `bool` , determina se o mapa tem permissão para aplicar zoom.
- `MapElements`, do tipo `IList<MapElement>` , representa a lista de elementos no mapa, como polígonos e polilinhas.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), do tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) , indica o estilo de exibição do mapa.
- `MoveToLastRegionOnLayoutChange`, do tipo `bool` , controla se a região de mapa exibida será movida de sua região atual para sua região definida anteriormente quando ocorrer uma alteração de layout.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), do tipo `IList<Pin>` representa a lista de Pins no mapa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), do tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , retorna a região exibida no momento do mapa.

Essas propriedades, com exceção das `MapElements` `Pins` Propriedades, e `VisibleRegion` , são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que podem ser destinos de associações de dados.

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe também define um `MapClicked` evento que é acionado quando o mapa é tocado. O `MapClickedEventArgs` objeto que acompanha o evento tem uma única propriedade chamada `Position` , do tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Quando o evento é acionado, a `Position` propriedade é definida como o local do mapa que foi tocado. Para obter informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md).

Para obter informações sobre [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) as [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) Propriedades, e `ItemTemplateSelector` , consulte [exibir uma coleção de PIN](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Exibir um mapa

Um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser exibido adicionando-o a um layout ou página:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Uma `xmlns` definição de namespace adicional é necessária para fazer referência ao Xamarin.Forms . Controles de mapas. No exemplo anterior, o `Xamarin.Forms.Maps` namespace é referenciado por meio da `maps` palavra-chave.

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

Este exemplo chama o [`Map`](xref:Xamarin.Forms.Maps.Map) construtor padrão, que centraliza o mapa em Roma:

[![Captura de tela do controle de mapa com o local padrão, no iOS e no Android](map-images/map-default.png "Controle de mapa com local padrão")](map-images/map-default-large.png#lightbox "Controle de mapa com local padrão")

Como alternativa, um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento pode ser passado para um [`Map`](xref:Xamarin.Forms.Maps.Map) construtor para definir o ponto central e o nível de zoom do mapa quando ele é carregado. Para obter mais informações, consulte [exibir um local específico em um mapa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipos de mapa

A [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriedade pode ser definida como um [`MapType`](xref:Xamarin.Forms.Maps.MapType) membro de enumeração para definir o estilo de exibição do mapa. A enumeração `MapType` define os seguintes membros:

- `Street`Especifica que um mapa de ruas será exibido.
- `Satellite`Especifica que um mapa que contém imagens satélite será exibido.
- `Hybrid`Especifica que um mapa que combina a rua e os dados de satélite serão exibidos.

Por padrão, um [`Map`](xref:Xamarin.Forms.Maps.Map) exibirá um mapa de rua se a [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriedade for indefinida. Como alternativa, a `MapType` propriedade pode ser definida como um dos [`MapType`](xref:Xamarin.Forms.Maps.MapType) membros da enumeração:

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

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriedade é definida como `Street` :

[![Captura de tela do controle de mapa com o tipo de mapa de rua, no iOS e no Android](map-images/maptype-street.png "Controle de mapa com a rua MapType")](map-images/maptype-street-large.png#lightbox "Controle de mapa com o tipo de mapa de rua")

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriedade é definida como `Satellite` :

[![Captura de tela do controle de mapa com o tipo de mapa satélite, no iOS e no Android](map-images/maptype-satellite.png "Controle de mapa com o satélite MapType")](map-images/maptype-satellite-large.png#lightbox "Controle de mapa com o tipo de mapa satélite")

As capturas de tela a seguir mostram um [`Map`](xref:Xamarin.Forms.Maps.Map) quando a [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriedade é definida como `Hybrid` :

[![Captura de tela do controle de mapa com o tipo de mapa híbrido, no iOS e no Android](map-images/maptype-hybrid.png "Controle de mapa com o MapType híbrido")](map-images/maptype-hybrid-large.png#lightbox "Controle de mapa com o tipo de mapa híbrido")

## <a name="display-a-specific-location-on-a-map"></a>Exibir um local específico em um mapa

A região de um mapa a ser exibida quando um mapa é carregado pode ser definida passando um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento para o [`Map`](xref:Xamarin.Forms.Maps.Map) Construtor:

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

Este exemplo cria um [`Map`](xref:Xamarin.Forms.Maps.Map) objeto que mostra a região especificada pelo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objeto. O `MapSpan` objeto é centralizado na latitude e na longitude representada por um [`Position`](xref:Xamarin.Forms.Maps.Position) objeto e abrange os graus de 0, 1 latitude e 0, 1 longitude. Para obter informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md). Para obter informações sobre como passar argumentos em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

O resultado é que, quando o mapa é exibido, ele é centralizado em um local específico e abrange um número específico de graus de latitude e Longitude:

[![Captura de tela do controle de mapa com o local especificado, no iOS e no Android](map-images/map-region.png "Controle de mapa com um local especificado")](map-images/map-region-large.png#lightbox "Controle de mapa com um local especificado")

## <a name="create-a-mapspan-object"></a>Criar um objeto MapSpan

Há várias abordagens para a criação de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objetos. Uma abordagem comum é fornecer os argumentos necessários ao `MapSpan` Construtor. Essas são uma latitude e longitude representadas por um [`Position`](xref:Xamarin.Forms.Maps.Position) objeto e `double` valores que representam os graus de latitude e longitude que são abrangidos pelo `MapSpan` . Para obter informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md).

Como alternativa, há três métodos na [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) classe que retornam novos `MapSpan` objetos:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)Retorna um `MapSpan` com o mesmo `LongitudeDegrees` que a instância de classe do método e um raio definido por seus `north` `south` argumentos e.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)Retorna um `MapSpan` que é definido por seus [`Position`](xref:Xamarin.Forms.Maps.Position) [`Distance`](xref:Xamarin.Forms.Maps.Distance) argumentos e.
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)Retorna um `MapSpan` com o mesmo centro da instância de classe do método, mas com um raio multiplicado por seu `double` argumento.

Para obter informações sobre a [`Distance`](xref:Xamarin.Forms.Maps.Distance) estrutura, consulte [posição e distância do mapa](position-distance.md).

Depois que um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) tiver sido criado, as seguintes propriedades podem ser acessadas para recuperar dados sobre ele:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), que representa o [`Position`](xref:Xamarin.Forms.Maps.Position) no centro geográfico do `MapSpan` .
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), que representa os graus de latitude que são abrangidos pelo `MapSpan` .
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), que representa os graus de longitude que são abrangidos pelo `MapSpan` .
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), que representa o `MapSpan` raio.

## <a name="move-the-map"></a>Mover o mapa

O [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método pode ser chamado para alterar a posição e o nível de zoom de um mapa. Esse método aceita um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que define a região do mapa a ser exibido e seu nível de zoom.

O código a seguir mostra um exemplo de como mover a região exibida em um mapa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Aplicar zoom ao mapa

O nível de zoom de um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser alterado sem alterar seu local. Isso pode ser feito usando a interface do usuário do mapa ou programaticamente chamando o [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método com um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que usa o local atual como o [`Position`](xref:Xamarin.Forms.Maps.Position) argumento:

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

Neste exemplo, o [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método é chamado com um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argumento que especifica o local atual do mapa, por meio da [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriedade, e o nível de zoom como graus de latitude e longitude. O resultado geral é que o nível de zoom do mapa é alterado, mas seu local não é. Uma abordagem alternativa para implementar o zoom em um mapa é usar o [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) método para controlar o fator de zoom.

> [!IMPORTANT]
> Aplicar zoom a um mapa, seja por meio da interface do usuário do mapa ou programaticamente, requer que a [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propriedade seja `true` . Para obter mais informações sobre essa propriedade, consulte [Disable zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizar comportamento do mapa

O comportamento de um [`Map`](xref:Xamarin.Forms.Maps.Map) pode ser personalizado definindo algumas de suas propriedades e manipulando o `MapClicked` evento.

> [!NOTE]
> A personalização de comportamento de mapa adicional pode ser obtida com a criação de um renderizador personalizado de mapa. Para obter mais informações, consulte [Personalizando um Xamarin.Forms mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

### <a name="disable-scroll"></a>Desabilitar rolagem

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define uma [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) Propriedade do tipo `bool` . Por padrão, essa propriedade é `true` , que indica que o mapa tem permissão para rolar. Quando essa propriedade for definida como `false` , o mapa não será rolado. O exemplo a seguir mostra a configuração desta propriedade:

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

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define uma [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) Propriedade do tipo `bool` . Por padrão, essa propriedade é `true` , que indica que o zoom pode ser executado no mapa. Quando essa propriedade é definida como `false` , o mapa não pode ser ampliado. O exemplo a seguir mostra a configuração desta propriedade:

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

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define uma [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) Propriedade do tipo `bool` . Por padrão, essa propriedade é `false` , que indica que o mapa não está mostrando o local atual do usuário. Quando essa propriedade é definida como `true` , o mapa mostra o local atual do usuário. O exemplo a seguir mostra a configuração desta propriedade:

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

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define uma `MoveToLastRegionOnLayoutChange` Propriedade do tipo `bool` . Por padrão, essa propriedade é `true` , que indica que a região do mapa exibida será movida de sua região atual para sua região definida anteriormente quando ocorrer uma alteração de layout, como na rotação do dispositivo. Quando essa propriedade é definida como `false` , a região de mapa exibida permanecerá centralizada quando ocorrer uma alteração de layout. O exemplo a seguir mostra a configuração desta propriedade:

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

A [`Map`](xref:Xamarin.Forms.Maps.Map) classe define um `MapClicked` evento que é acionado quando o mapa é tocado. O `MapClickedEventArgs` objeto que acompanha o evento tem uma única propriedade chamada `Position` , do tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Quando o evento é acionado, a `Position` propriedade é definida como o local do mapa que foi tocado. Para obter informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md).

O exemplo de código a seguir mostra um manipulador de eventos para o `MapClicked` evento:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Neste exemplo, o `OnMapClicked` manipulador de eventos gera a latitude e a longitude que representam o local do mapa tocado. O manipulador de eventos pode ser registrado com o `MapClicked` evento da seguinte maneira:

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
- [Personalizando um Xamarin.Forms mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
