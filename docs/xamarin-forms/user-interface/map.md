---
title: Mapa
description: Xamarin. Forms usa o mapa nativo APIs em cada plataforma.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 6063732e08680974b8d4a2358bfd85b176b36aec
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="map"></a>Mapa

_Xamarin. Forms usa o mapa nativo APIs em cada plataforma._

Xamarin.Forms.Maps usa o mapa nativo APIs em cada plataforma. Isso fornece uma experiência de mapas rápida e familiar para os usuários, mas significa que algumas etapas de configuração são necessárias para cumprir requisitos de API específicos cada plataformas.
Uma vez configurado, o `Map` controlar funciona exatamente como qualquer outro elemento em código comum xamarin. Forms.

* [Mapas de inicialização](#Maps_Initialization) - usando `Map` requer o código de inicialização adicional na inicialização.
* [Configuração de plataforma](#Platform_Configuration) -cada plataforma exige alguma configuração de mapas trabalhar.
* [Usando mapas em c#](#Using_Maps) -exibindo mapeia e fixar usando c#.
* [Usando mapas em XAML](#Using_Xaml) -exibir um mapa com XAML.

O controle de mapa foi usado no [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/) sample, que é mostrado abaixo.

 [![Mapas no exemplo MobileCRM](map-images/maps-zoom-sml.png "exemplo de controle de mapa")](map-images/maps-zoom.png#lightbox "exemplo de controle de mapa")

Funcionalidade de mapa pode ser aprimorada ainda mais com a criação de um [mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Inicialização de mapas

Ao adicionar mapas a um aplicativo xamarin. Forms, **Xamarin.Forms.Maps** é um um pacote do NuGet separado que você deve adicionar a todos os projetos na solução.
No Android, isso também tem uma dependência no GooglePlayServices (NuGet outro), que é baixada automaticamente quando você adiciona Xamarin.Forms.Maps.

Depois de instalar o pacote NuGet, um código de inicialização é necessária em cada projeto de aplicativo, *depois* o `Xamarin.Forms.Forms.Init` chamada de método. Para iOS, use o seguinte código:

```csharp
Xamarin.FormsMaps.Init();
```

No Android, você deve passar os mesmos parâmetros `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para o Windows UWP (plataforma Universal), use o seguinte código:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Adicione esta chamada nos seguintes arquivos para cada plataforma:

-  **iOS** -arquivo appdelegate. cs, além de `FinishedLaunching` método.
-  **Android** -MainActivity.cs de arquivos, além de `OnCreate` método.
-  **UWP** -arquivo MainPage.xaml.cs, além de `MainPage` construtor.

Depois que o pacote NuGet foi adicionado e o método de inicialização é chamado dentro de cada aplicativos `Xamarin.Forms.Maps` APIs podem ser usadas no código do projeto compartilhado ou projeto de biblioteca .NET padrão comum.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuração de plataforma

Etapas de configuração adicionais são necessários em algumas plataformas antes do mapa será exibido.

### <a name="ios"></a>iOS

Para acessar os serviços de localização no iOS, você deve definir as seguintes chaves no **Info. plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – Para usar serviços de localização quando o aplicativo está em uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – Para usar serviços de localização em todos os momentos
- iOS 10 e versões anteriores
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – Para usar serviços de localização quando o aplicativo está em uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – Para usar serviços de localização em todos os momentos    
    
Para dar suporte a iOS 11 e versões anteriores, você pode incluir todos os três chaves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, e `NSLocationAlwaysUsageDescription`.

A representação XML para essas chaves no **Info. plist** é mostrado abaixo. Você deve atualizar o `string` valores para refletir como o aplicativo está usando as informações de local:

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
Siga as instruções no documento Xamarin na [como obter uma chave de API do Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Depois de seguir essas instruções, cole a chave de API no **Properties/AndroidManifest.xml** arquivo (Exibir código-fonte e localizar/atualizar o seguinte elemento):

```xml
<meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="YOUR_API_KEY"/>
```

Sem uma chave de API válida o controle de mapas será exibido como uma caixa cinza no Android.

> [!NOTE]
> Lembre-se de gerar outra chave usando o arquivo de armazenamento de chaves que é usado para assinar a versão de qualquer aplicativo que é carregado para o Google Play store. A chave que você gerar para desenvolvimento e depuração não funcionará e o aplicativo baixado do Google Play será desfeito exibição do mapa. Lembre-se também ao regenerar a chave se o aplicativo **nome do pacote** alterações.

Você também precisará habilitar as permissões apropriadas, clicando duas vezes no projeto Android e selecionando **opções > compilar > aplicativo Android** e marcar o seguinte:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Algumas delas são mostradas na captura de tela abaixo:

![Permissões necessárias para o Android](map-images/android-map-permissions.png "permissões necessárias para Android")

Os dois últimos serão necessários porque os aplicativos exigem uma conexão de rede para baixar os dados de mapa. Leia sobre Android [permissões](http://developer.android.com/reference/android/Manifest.permission.html) para saber mais.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Para usar mapas na plataforma Universal do Windows, você deve gerar um token de autorização. Para obter mais informações, consulte [solicitar uma chave de autenticação de mapas](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) no MSDN.

O token de autenticação deve ser especificado no `FormsMaps.Init("AUTHORIZATION_TOKEN")` chamada de método, autenticar o aplicativo com o Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Usando mapas

Consulte o [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) no exemplo MobileCRM para obter um exemplo de como o controle de mapa pode ser usado no código. Um simples `MapPage` classe pode parecer com este - aviso de que um novo `MapSpan` é criado para posicionar a exibição do mapa:

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

O conteúdo do mapa também pode ser alterado definindo a `MapType` propriedade, para mostrar um mapa rodoviário regular (o padrão), imagens de satélite ou uma combinação de ambos.

```csharp
map.MapType == MapType.Street;
```

Válido `MapType` os valores são:

-  Híbrido
-  Satélite
-  Rua (o padrão)


### <a name="map-region-and-mapspan"></a>MapSpan e região do mapa

Conforme mostrado no trecho de código acima, fornecendo um `MapSpan` instância para um construtor de mapa define a exibição inicial (center ponto e o nível de zoom) do mapa quando ele é carregado. O `MoveToRegion` método da classe map, em seguida, pode ser usado para alterar o nível de zoom ou de posição do mapa. Há duas maneiras para criar um novo `MapSpan` instância:

-  **MapSpan.FromCenterAndRadius()** -método estático para criar um período de um `Position` e especificando um `Distance` .
-  **New () de MapSpan** -construtor que usa um `Position` e degress de latitude e longitude para exibir.


Para alterar o nível de zoom do mapa sem alterar o local, crie um novo `MapSpan` usando o local atual do `VisibleRegion.Center` propriedade do controle de mapa. Um `Slider` pode ser usado para controle de zoom do mapa assim (no entanto, aumentar o zoom diretamente no controle de mapa atualmente não é possível atualizar o valor do controle deslizante):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mapas com zoom](map-images/maps-zoom-sml.png "Zoom de controle de mapa")](map-images/maps-zoom.png#lightbox "Zoom de controle de mapa")

### <a name="map-pins"></a>Pins de mapa

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

 `PinType` pode ser definido como um dos valores a seguir, que podem afetar a maneira como o pin em renderizado (dependendo da plataforma):

-  Genérico
-  Local
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando Xaml

Maps também pode ser posicionado em layouts de Xaml, conforme mostrado neste trecho.

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
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

O `MapRegion` e `Pins` pode ser definida no código usando o `MyMap` referência (ou que o mapa é nomeado). Observe que adicional `xmlns` definição de namespace é necessária para fazer referência os controles Xamarin.Forms.Maps.

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>Resumo

O Xamarin.Forms.Maps é um NuGet separado que deve ser adicionado para cada projeto em uma solução xamarin. Forms. Código de inicialização adicional é necessário, como bem como algumas etapas de configuração para iOS, Android e UWP.

Uma vez configurada, a API mapeia pode ser usada para renderizar o maps com marcadores de pino em apenas algumas linhas de código. Mapas de podem ser aprimorados ainda mais com um [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).


## <a name="related-links"></a>Links relacionados

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [Renderizador de mapa personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
