---
title: Inicialização e configuração do mapa do Xamarin. Forms
description: Este artigo explica como usar a classe de mapa Xamarin. Forms para usar as APIs de mapa nativas em cada plataforma para fornecer uma experiência de mapas familiar para os usuários.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697384"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inicialização e configuração do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_O Xamarin. Forms usa as APIs de mapa nativas em cada plataforma._

O Xamarin. Forms. Maps usa as APIs de mapa nativas em cada plataforma. Isso fornece uma experiência de mapas rápida e familiar para os usuários, mas significa que algumas etapas de configuração são necessárias para aderir a cada uma das plataformas requisitos de API.
Uma vez configurado, o controle de `Map` funciona como qualquer outro elemento Xamarin. Forms em código comum.

O controle de mapa foi usado no exemplo [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) , que é mostrado abaixo.

 [![Mapas no exemplo de MobileCRM](map-images/maps-zoom-sml.png "Exemplo de Controle de Mapeamento")](map-images/maps-zoom.png#lightbox "Exemplo de Controle de Mapeamento")

A funcionalidade de mapa pode ser aprimorada com a criação de um [renderizador personalizado de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inicialização do mapa

Ao adicionar mapas a um aplicativo Xamarin. Forms, o **Xamarin. Forms. Maps** é um pacote NuGet separado que você deve adicionar a todos os projetos na solução.
No Android, isso também tem uma dependência em GooglePlayServices (outro NuGet) que é baixado automaticamente quando você adiciona Xamarin. Forms. Maps.

Depois de instalar o pacote NuGet, um código de inicialização é necessário em cada projeto de aplicativo, *após* a chamada do método `Xamarin.Forms.Forms.Init`. Para o iOS, use o seguinte código:

```csharp
Xamarin.FormsMaps.Init();
```

No Android, você deve passar os mesmos parâmetros que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Para o Plataforma Universal do Windows (UWP), use o seguinte código:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Adicione essa chamada nos seguintes arquivos para cada plataforma:

- arquivo **Ios** -AppDelegate.cs, no método `FinishedLaunching`.
- Arquivo **Android** -MainActivity.cs, no método `OnCreate`.
- O arquivo **UWP** -MainPage.XAML.cs, no Construtor `MainPage`.

Depois que o pacote NuGet tiver sido adicionado e o método de inicialização chamado dentro de cada aplicativo, `Xamarin.Forms.Maps` APIs poderão ser usadas no projeto Common .NET Standard Library ou no código do projeto compartilhado.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuração da plataforma

Etapas de configuração adicionais são necessárias em algumas plataformas antes que o mapa seja exibido.

### <a name="ios"></a>iOS

Para acessar os serviços de localização no iOS, você deve definir as seguintes chaves em **info. plist**:

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – para usar serviços de localização em todos os momentos
- iOS 10 e anterior
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – para usar serviços de localização quando o aplicativo está em uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – para usar serviços de localização em todos os momentos    

Para dar suporte ao iOS 11 e anteriores, você pode incluir todas as três chaves: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` e `NSLocationAlwaysUsageDescription`.

A representação XML para essas chaves no **info. plist** é mostrada abaixo. Você deve atualizar os valores de `string` para refletir como seu aplicativo está usando as informações de local:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

As entradas **info. plist** também podem ser adicionadas na exibição de **código-fonte** durante a edição do arquivo **info. plist** :

![Info. plist para iOS 8](map-images/ios8-map-permissions.png "Entradas do info. plist necessárias do iOS 8")

### <a name="android"></a>Android

Para usar a [API do Google Maps v2](https://developers.google.com/maps/documentation/android/) no Android, você deve gerar uma chave de API e adicioná-la ao seu projeto do Android.
Siga as instruções no documento do Xamarin sobre [como obter uma chave de API do Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Depois de seguir essas instruções, Cole a chave de API no arquivo **Properties/AndroidManifest. xml** (Exibir código-fonte e localizar/atualizar o seguinte elemento):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sem uma chave de API válida, o controle de mapas será exibido como uma caixa cinza no Android.

> [!NOTE]
> Observe que, para que seu APK acesse o Google Maps, você deve incluir impressões digitais e nomes de pacote do SHA-1 para cada repositório de chaves (depuração e versão) que você usa para assinar seu APK. Por exemplo, se você usar um computador para depuração e outro computador para gerar a versão APK, deverá incluir a impressão digital do certificado SHA-1 do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de lançamento do o segundo computador. Lembre-se também de editar as credenciais de chave se o **nome do pacote** do aplicativo for alterado. Consulte [obtendo uma chave de API do Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Você também precisará habilitar as permissões apropriadas clicando com o botão direito do mouse no projeto do Android e selecionando **opções > compilar > aplicativo Android** e marcando o seguinte:

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Alguns deles são mostrados na captura de tela abaixo:

![Permissões necessárias para Android](map-images/android-map-permissions.png "Permissões necessárias para Android")

Os dois últimos são necessários porque os aplicativos exigem uma conexão de rede para baixar dados do mapa. Leia sobre [as permissões](https://developer.android.com/reference/android/Manifest.permission.html) do Android para saber mais.

Além disso, o Android 9 removeu a biblioteca de cliente HTTP do Apache do bootclasspath e, portanto, não está disponível para aplicativos destinados à API 28 ou superior. A linha a seguir deve ser adicionada ao nó `application` do seu arquivo **AndroidManifest. xml** para continuar usando o cliente Apache HTTP em aplicativos que têm a API de destino 28 ou superior:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Para usar mapas no Plataforma Universal do Windows você deve gerar um token de autorização. Para obter mais informações, consulte [solicitar uma chave de autenticação do Maps](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) no msdn.

O token de autenticação deve ser especificado na chamada do método `FormsMaps.Init("AUTHORIZATION_TOKEN")`, para autenticar o aplicativo com o Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuração do mapa

Consulte o [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) no exemplo de MobileCRM para obter um exemplo de como o controle de mapa pode ser usado no código. Uma classe simples de `MapPage` pode ser parecida com esta – Observe que uma nova `MapSpan` é criada para posicionar a exibição do mapa:

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

O conteúdo do mapa também pode ser alterado definindo a propriedade `MapType`, para mostrar um mapa de rua regular (o padrão), imagens satélite ou uma combinação de ambos.

```csharp
map.MapType = MapType.Street;
```

Os valores de `MapType` válidos são:

- `Hybrid`
- `Satellite`
- `Street` (o padrão)

### <a name="map-region-and-mapspan"></a>Mapear região e MapSpan

Conforme mostrado no trecho de código acima, o fornecimento de uma instância de `MapSpan` a um construtor de mapa define a exibição inicial (ponto central e nível de zoom) do mapa quando ele é carregado. Há duas maneiras de criar uma nova instância de `MapSpan`:

- **MapSpan. FromCenterAndRadius ()** – método estático para criar um intervalo de um `Position` e especificar um `Distance`.
- **novo MapSpan ()** -Construtor que usa um `Position` e os graus de latitude e longitude a serem exibidos.

O método `MoveToRegion` na classe Map pode ser usado para alterar a posição ou o nível de zoom do mapa. Para alterar o nível de zoom do mapa sem alterar o local, crie um novo `MapSpan` usando o local atual da propriedade `VisibleRegion.Center` do controle de mapa. Um `Slider` pode ser usado para controlar o zoom do mapa como este (no entanto, aplicar zoom diretamente no controle de mapa não pode atualizar o valor do controle deslizante no momento):

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Mapas com zoom](map-images/maps-zoom-sml.png "Controle de Mapeamento zoom")](map-images/maps-zoom.png#lightbox "Controle de Mapeamento zoom")

Além disso, a classe [`Map`](xref:Xamarin.Forms.Maps.Map) tem uma propriedade `MoveToLastRegionOnLayoutChange` do tipo `bool`, que é apoiada por uma propriedade vinculável. Por padrão, essa propriedade é `true`, o que indica que a região do mapa exibida será movida de sua região atual para sua região definida anteriormente quando ocorrer uma alteração de layout, como na rotação do dispositivo. Quando essa propriedade é definida como `false`, a região de mapa exibida permanecerá centralizada quando ocorrer uma alteração de layout. O exemplo a seguir mostra a configuração desta propriedade:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>Cliques de mapa

`Map` define um evento de `MapClicked` que é disparado quando o mapa é tocado. O objeto `MapClickedEventArgs` que acompanha o evento `MapClicked` tem uma única propriedade denominada `Position`, do tipo `Position`. Quando o evento é acionado, o valor da propriedade `Position` é definido como o local do mapa que foi tocado.

O exemplo de código a seguir mostra um manipulador de eventos para o evento `MapClicked`:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Neste exemplo, o manipulador de eventos `OnMapClicked` gera a latitude e a longitude que representam o local do mapa tocado.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Criar um mapa em XAML

Os mapas também podem ser criados em XAML, conforme mostrado neste exemplo:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Uma definição de namespace `xmlns` adicional é necessária para fazer referência aos controles Xamarin. Forms. Maps. No exemplo anterior, o namespace `Xamarin.Forms.Maps` é referenciado por meio da palavra-chave `maps`.

O `MapRegion` pode ser definido no código usando a referência nomeada para o `Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Pins do Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API de mapas](xref:Xamarin.Forms.Maps)
- [Mapear renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
