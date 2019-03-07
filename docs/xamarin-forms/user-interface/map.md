---
title: Mapa do xamarin. Forms
description: Este artigo explica como usar a classe de mapa do xamarin. Forms para usar o mapa APIs nativo em cada plataforma para fornecer que um familiar mapeia a experiência dos usuários.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2018
ms.openlocfilehash: edba18eea3ea2b7b843dba70ff0b4b67cbab1ab1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557110"
---
# <a name="xamarinforms-map"></a>Mapa do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/WorkingWithMaps/)

_Xamarin. Forms usa o mapa nativo APIs em cada plataforma._

Xamarin.Forms.Maps usa as APIs de mapa nativa em cada plataforma. Isso fornece uma experiência de mapas rápido e familiar para os usuários, mas significa que algumas etapas de configuração são necessários para atender aos requisitos de API cada plataformas.
Uma vez configurado, o `Map` controlar funciona exatamente como qualquer outro elemento de xamarin. Forms em código comum.

O controle de mapa tenha sido usado na [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/) sample, que é mostrado abaixo.

 [![Mapas no exemplo MobileCRM](map-images/maps-zoom-sml.png "exemplo de controle de mapa")](map-images/maps-zoom.png#lightbox "exemplo de controle de mapa")

Funcionalidade de mapa pode ser aprimorada ainda mais com a criação de um [mapear um renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Inicialização de mapas

Ao adicionar mapas a um aplicativo xamarin. Forms, **Xamarin.Forms.Maps** é um pacote do NuGet separado que você deve adicionar a todos os projetos na solução.
No Android, isso também tem uma dependência no GooglePlayServices (NuGet outro), que é baixada automaticamente quando você adiciona Xamarin.Forms.Maps.

Depois de instalar o pacote do NuGet, um código de inicialização é necessária em cada projeto de aplicativo *após* o `Xamarin.Forms.Forms.Init` chamada de método. Para iOS, use o seguinte código:

```csharp
Xamarin.FormsMaps.Init();
```

No Android, você deve passar os mesmos parâmetros que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para a Universal Windows Platform (UWP), use o seguinte código:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Adicione esta chamada nos seguintes arquivos para cada plataforma:

-  **iOS** -arquivo AppDelegate.cs, além de `FinishedLaunching` método.
-  **Android** -MainActivity.cs de arquivos, além de `OnCreate` método.
-  **UWP** -arquivo MainPage.xaml.cs, além de `MainPage` construtor.

Depois que o pacote do NuGet foi adicionado e o método de inicialização é chamado dentro de cada aplicativo, `Xamarin.Forms.Maps` APIs podem ser usadas no código do projeto compartilhado ou projeto de biblioteca .NET Standard comum.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuração de plataforma

Etapas de configuração adicionais são necessários em algumas plataformas antes do mapa será exibido.

### <a name="ios"></a>iOS

Para acessar os serviços de localização no iOS, você deve definir as seguintes chaves **Info. plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – Para usar os serviços de localização quando o aplicativo está em uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – Para usar os serviços de localização em todos os momentos
- iOS 10 e versões anteriores
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – Para usar os serviços de localização quando o aplicativo está em uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – Para usar os serviços de localização em todos os momentos    

Para dar suporte a iOS 11 e versões anteriores, você pode incluir todos os três chaves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, e `NSLocationAlwaysUsageDescription`.

A representação XML para essas chaves na **Info. plist** é mostrado abaixo. Você deve atualizar o `string` valores para refletir como seu aplicativo está usando as informações de local:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

O **Info. plist** entradas também podem ser adicionadas no **fonte** exibição ao editar o **Info. plist** arquivo:

![Info. plist para iOS 8](map-images/ios8-map-permissions.png "entradas de info. plist necessário iOS 8")

### <a name="android"></a>Android

Para usar o [API do Google Maps v2](https://developers.google.com/maps/documentation/android/) no Android, você deve gerar uma chave de API e adicioná-lo ao seu projeto Android.
Siga as instruções no documento Xamarin na [obtendo uma chave de API do Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Depois de seguir essas instruções, cole a chave de API na **androidmanifest** arquivo (Exibir código-fonte e localizar/atualizar o seguinte elemento):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sem uma chave de API válida no controle maps será exibido como uma caixa cinza no Android.

> [!NOTE]
> Observe que, na ordem de seu APK acessar o Google Maps, você deve incluir as impressões digitais de SHA-1 e nomes para cada repositório de chaves (depuração e versão) que você usa para assinar o APK do pacote. Por exemplo, se você usar um computador para outro computador para gerar a versão APK e de depuração, você deve incluir a impressão digital SHA-1 do certificado do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de versão o segundo computador. Lembre-se também ao editar as credenciais de chave, se o aplicativo **nome do pacote** alterações. Ver [obtendo uma chave de API do Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Você também precisará habilitar as permissões apropriadas pelo botão direito do mouse no projeto do Android e selecionando **opções > Build > aplicativo Android** e tique o seguinte:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Alguns deles são mostradas na captura de tela abaixo:

![Permissões necessárias para Android](map-images/android-map-permissions.png "permissões necessárias para Android")

As duas últimas são necessárias porque os aplicativos exigem uma conexão de rede para baixar os dados de mapa. Leia sobre o Android [permissões](http://developer.android.com/reference/android/Manifest.permission.html) para saber mais.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Para usar mapas na plataforma Universal do Windows, você deve gerar um token de autorização. Para obter mais informações, consulte [solicitar uma chave de autenticação de mapas](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) no MSDN.

O token de autenticação deve ser especificado, em seguida, no `FormsMaps.Init("AUTHORIZATION_TOKEN")` chamada de método, para autenticar o aplicativo com o Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Uso de mapas

Consulte a [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) na amostra MobileCRM para obter um exemplo de como o controle de mapa pode ser usado no código. Um simples `MapPage` classe pode parecer com este - aviso de que um novo `MapSpan` é criado para posicionar a exibição do mapa:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Tipo de mapa

O conteúdo do mapa também pode ser alterado definindo o `MapType` propriedade, para mostrar um mapa rodoviário regular (o padrão), imagens de satélite ou uma combinação de ambos.

```csharp
map.MapType == MapType.Street;
```

Válido `MapType` valores são:

-  Híbrido
-  Satélite
-  Rua (o padrão)

### <a name="map-region-and-mapspan"></a>Região do mapa e MapSpan

Conforme mostrado no trecho de código acima, fornecendo um `MapSpan` instância para um construtor de mapa define a exibição inicial (o ponto central, e o nível de zoom) do mapa quando ele for carregado. O `MoveToRegion` método da classe map, em seguida, pode ser usado para alterar o nível de zoom ou de posição do mapa. Há duas maneiras para criar um novo `MapSpan` instância:

-  **MapSpan.FromCenterAndRadius()** -um método estático para criar um período de um `Position` e especificando um `Distance` .
-  **New () de MapSpan** -construtor que usa um `Position` e os graus de latitude e longitude para exibir.


Para alterar o nível de zoom do mapa sem alterar o local, crie um novo `MapSpan` usando o local atual do `VisibleRegion.Center` propriedade do controle de mapa. Um `Slider` poderia ser usado para controle de zoom do mapa como este (no entanto, aumentar o zoom diretamente no controle de mapa, atualmente não é possível atualizar o valor do controle deslizante):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mapas com zoom](map-images/maps-zoom-sml.png "mapa de controle de Zoom")](map-images/maps-zoom.png#lightbox "Zoom do controle de mapa")

### <a name="map-pins"></a>Pinos de mapa

Locais podem ser marcados no mapa com `Pin` objetos.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` pode ser definido como um dos valores a seguir, que podem afetar a maneira que o pin é renderizado (dependendo da plataforma):

-  Genérico
-  Local
-  SavedPin
-  SearchResult

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando XAML

Mapas também podem ser posicionados em layouts XAML, conforme mostrado neste trecho de código.

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
                  x:Name="MyMap"
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Adicional `xmlns` definição de namespace é necessário para referenciar os controles Xamarin.Forms.Maps.

O `MapRegion` e `Pins` pode ser definido no código usando o `MyMap` referência (ou o mapa de tudo o que é chamado).

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populating-a-map-with-data-using-data-binding"></a>Preenchendo um mapa com dados usando a associação de dados

O [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe também expõe as propriedades a seguir:

- `ItemsSource` – Especifica a coleção de `IEnumerable` itens a serem exibidos.
- `ItemTemplate` – Especifica o [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para aplicar a cada item na coleção de itens exibidos.

Portanto, uma [ `Map` ](xref:Xamarin.Forms.Maps.Map) podem ser populados com dados por meio de associação de dados para associar seu `ItemsSource` propriedade para um `IEnumerable` coleção:

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

O `ItemsSource` associa dados de propriedade para o `Locations` propriedade do modelo de exibição conectados, que retorna um `ObservableCollection` de `Location` objetos, que é um tipo personalizado. Cada `Location` objeto define `Address` e `Description` propriedades do tipo `string`e um `Position` propriedade do tipo [ `Position` ](xref:Xamarin.Forms.Maps.Position).

A aparência de cada item na `IEnumerable` coleção é definida pela configuração o `ItemTemplate` propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que contém um [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) que associa para dados de objeto propriedades adequadas.

As capturas de tela a seguir mostram uma [ `Map` ](xref:Xamarin.Forms.Maps.Map) exibindo um [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) usando vinculação de dados de coleção:

[![Captura de tela do mapa com os dados associados pins no iOS e Android](map-images/pins-itemssource.png "mapear com dados pins associados")](map-images/pins-itemssource-large.png#lightbox "alfinetes com os dados associados")

## <a name="related-links"></a>Links relacionados

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [Mapear o renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
