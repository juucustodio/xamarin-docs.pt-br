---
title: API de mapas
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: fc16178a4068b2dcf22fc19047e0ef403e83633f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773518"
---
# <a name="maps-api"></a>API de mapas

Usando o aplicativo de mapas é ótimo, mas às vezes você deseja incluir mapas diretamente no seu aplicativo. Além de internos mapeia aplicativos, Google também oferece um [API de mapeamento nativo para o Android](https://developers.google.com/maps/documentation/android/).
A API de mapas é adequada para casos em que você deseja manter mais controle sobre a experiência de mapeamento. Coisas que são possíveis com a API do Maps incluem:

-  Alterar programaticamente o ponto de vista do mapa.
-  Adicionar e personalizar marcadores.
-  Anotar um mapa com sobreposição.

Ao contrário de v1 a API do Google Maps Android agora preterido, Google Maps Android API v2 faz parte do [Google executar serviços](http://developer.android.com/google/play-services/index.html).
Portanto, é necessário para atender a alguns pré-requisitos obrigatórios antes que seja possível usar a API do Android Google Maps em um aplicativo xamarin.


## <a name="google-maps-api-prerequisites"></a>Google mapeia os pré-requisitos da API

Vários itens precisam ser configurados antes de usar a API de mapas, incluindo:

-  Instalar o SDK dos serviços do Google Play
-  Criar um emulador com as APIs do Google
-  Obter uma chave de API de mapas
-  Especifique as permissões necessárias



### <a name="install-the-google-play-services-sdk"></a>Instalar o SDK dos serviços do Google Play

Serviços do Google reproduzir é uma tecnologia do Google que permite que aplicativos Android aproveitar os vários recursos do Google, como Google + no aplicativo de cobrança e mapas. Esses recursos podem ser acessados em dispositivos Android como serviços em segundo plano que estão contidos no [APK de serviços do Google reproduzir](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Aplicativos do Android interagem com o Google executar serviços por meio da biblioteca de cliente de serviços do Google reproduzir. Esta biblioteca contém as interfaces e classes para os serviços individuais, como mapas. O diagrama a seguir mostra a relação entre um aplicativo do Android e Google executar serviços:

![Diagrama que ilustra o Google Play Store atualizando o Google executar serviços APK](maps-api-images/play-services-diagram.png)

A API do Android Maps é fornecida como uma parte dos serviços de reprodução do Google.
Antes de um aplicativo xamarin pode usar a API de mapas, o SDK de serviços do Google reproduzir deve ser instalado e vinculado. O SDK de serviços do Google reproduzir é instalado por meio do Gerenciador de SDK do Android. Captura de tela a seguir mostra onde no Gerenciador de SDK do Android o cliente de serviços do Google Play pode ser encontrado:

![Serviços de reprodução do Google aparece sob Extras no Gerenciador de SDK do Android](maps-api-images/image01.png)

> [!NOTE]
> Os serviços do Google Play APK é um produto licenciado que pode não estar presente em todos os dispositivos. Se não estiver instalado, Google Maps não funcionará no dispositivo.


#### <a name="binding-google-play-services"></a>Serviços de associação Google Play

Depois de instalar a biblioteca de cliente de serviços de reprodução do Google, deve ser vinculado por uma biblioteca de ligação xamarin Java. Há duas maneiras de fazer isso:

-  **Use o pacote NuGet de mapas de serviços de reproduzir Google** -essa é a abordagem mais simples (disponível apenas no xamarin 4.8 ou superior).
   Instalar o [Xamarin Google executar serviços mapas NuGet](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); isso também irá instalar qualquer pacote de dependência de serviços do Google reproduzir.
   O restante deste guia se concentra nessa abordagem.

-  **Vincular manualmente a biblioteca de cliente de serviços do Google reproduzir** -isso é uma abordagem mais complexa e é a única maneira associar o SDK de serviços do Google reproduzir 4.4 xamarin ou xamarin 4.6.
   Associação manualmente a biblioteca de cliente de serviços do Google reproduzir está além do escopo deste documento, mas um exemplo de como fazer isso pode ser encontrado no [mapas e demonstração local v3 exemplo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3) no Github.


#### <a name="adding-the-google-play-services-map-package"></a>Adicionar o pacote de mapa de serviços do Google Play

Para adicionar o pacote de mapa do Google executar serviços, clique com botão direito do **referências** pasta do seu projeto no Gerenciador de soluções e clique em **gerenciar pacotes NuGet...** :

![Item de menu de contexto mostrando do Solution Explorer gerenciar pacotes NuGet em referências](maps-api-images/image02.png)

Isso abre o **NuGet Package Manager**. Clique em **procurar** e digite **Xamarin Google executar serviços Maps** no campo de pesquisa. Selecione **Xamarin.GooglePlayServices.Maps** e clique em **instalar**. (Se este pacote tivesse sido instalado anteriormente, clique em **atualização**.):

[![Gerenciador de pacotes do NuGet com Xamarin.GooglePlayServices.Maps pacote selecionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Observe que os pacotes de dependência a seguir também são instalados:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**



### <a name="create-an-emulator-with-google-apis"></a>Criar um emulador com APIs do Google

Embora não seja recomendado, é possível configurar o emulador para dar suporte a API de mapas do Android. O emulador deve ser configurado para direcionar o 17 de nível de API do Google (4.2.2 Android) ou superior. Captura de tela a seguir, uma imagem do emulador estiver configurada para 19 de nível de API: 

![Gerenciador de emulador do Android com um AVD configurado para 19 de nível de API](maps-api-images/image04.png)



### <a name="obtain-a-google-maps-api-key"></a>Obter uma chave de API do Google Maps

A etapa final é obter uma chave de API do Google Maps (Observe que não é possível reutilizar uma chave de API de v1 herdados do Google Maps). Para obter informações sobre como obter e usar a chave de API com xamarin, consulte [obter a chave de API de um Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 


### <a name="specify-the-required-permissions"></a>Especifique as permissões necessárias

As seguintes permissões devem ser especificadas no **AndroidManifest.XML** da API do Google Maps Android:

-  **Acesso ao estado da rede** &ndash; a API Maps deve ser capaz de verificar se ele pode baixar as peças de mapas.

-  **Acesso à Internet** &ndash; acesso à Internet é necessário para baixar as peças de mapas e se comunicar com os servidores do Google reproduzir para acesso à API.

-  **OpenGL ES v2** &ndash; o aplicativo deve declarar o requisito de OpenGL ES v2.

-  **Chave de API do Google Maps** &ndash; a API chave é usada para confirmar que o aplicativo é registrado e autorizado a usar os serviços do Google reproduzir. Consulte [como obter uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obter detalhes sobre essa chave.

-  **Gravar no armazenamento externo** &ndash; a API Android Google Maps armazenará em cache os blocos baixados para o armazenamento externo.

-  **Acesso aos serviços baseados na Google Web** &ndash; o aplicativo precisa de permissões para acessar os serviços web do Google que dão suporte a API do Android mapas.

-  **Permissões para notificações de serviços do Google reproduzir** &ndash; o aplicativo deve ter a permissão receber notificações remotas dos serviços do Google reproduzir.

-  **Acesso a provedores de local** &ndash; essas são permissões opcionais.
   Elas permitirão que o `GoogleMap` classe para exibir o local do dispositivo no mapa.


O trecho a seguir é um exemplo das configurações que devem ser adicionados ao **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```


## <a name="the-googlemap-class"></a>A classe GoogleMap

Depois que os pré-requisitos são manipulados, é hora de começar a desenvolver o aplicativo e usar a API do Android mapas. O [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap) classe é a principal API que um aplicativo xamarin serão usadas para exibir e interagir com um Google Maps para Android. Essa classe tem as seguintes responsabilidades:

-  Interagir com serviços do Google Play para autorizar o aplicativo com o serviço de web do Google.

-  Baixar, cache e exibindo as peças de mapas.

-  Exibindo controles de interface do usuário, como Panorâmica e zoom para o usuário.

-  Marcadores de desenho e formas geométricas em mapas.

O `GoogleMap` é adicionado a uma atividade em uma das seguintes maneiras:

-  **MapFragment** - o [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) é um fragmento especializado que atua como host para o `GoogleMap` objeto. O `MapFragment` requer o nível de API do Android 12 ou superior.
   Versões mais antigas do Android podem usar o [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html).

-  **MapView** - o [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/) é uma subclasse de exibição especializada que pode agir como um host para um `GoogleMap` objeto. Os usuários dessa classe devem encaminhar todos os métodos de ciclo de vida de atividade para o `MapView` classe.

Cada um desses contêineres expor um `Map` propriedade que retorna uma instância de `GoogleMap`. Preferência deve ser fornecida para o [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) classe como é uma API simples que reduz o código clichê quantidade que um desenvolvedor deve implementar manualmente.


### <a name="adding-a-mapfragment-to-an-activity"></a>Adicionando um MapFragment a uma atividade

Captura de tela a seguir está um exemplo de uma simples `MapFragment`:

[![Captura de tela de um dispositivo que está exibindo um fragmento de mapa](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Semelhante a outras classes de fragmento, há duas maneiras de adicionar isso `MapFragment` a uma atividade:

-   **Declarativamente** - o `MapFragment` podem ser adicionados por meio do arquivo XML layout para a atividade. O trecho XML a seguir mostra um exemplo de como usar o `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Programaticamente** - o `MapFragment` podem ser adicionados programaticamente, conforme descrito a seguir.

Para adicionar programaticamente uma `MapFragment`, sua atividade deve implementar o `IOnMapReadyCallback` interface. Porque a inicialização de um `GoogleMap` objeto pode levar algum tempo para concluir a API se comunica com o Google Play, você deve fornecer um retorno de chamada que notifica o aplicativo quando o `GoogleMap` está pronto.

Primeiro, adicione `IOnMapReadyCallback` para o `Activity` declaração de classe.
Por exemplo:

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

Em seguida, no `OnCreate` método, adicione o `MapFragment` conforme mostrado no exemplo de código a seguir (o `GoogleMapOptions` classe é explicada mais adiante neste guia):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

Um `GoogleMap` deve ser adquirido usando `GetMapAsync`, conforme mostrado no final do exemplo de código anterior &ndash; Isso inicia automaticamente o sistema de mapas e o modo de exibição. (Observe que esse método não usa `await` / `async` semântica &ndash; o `Async` comportamento é implementado pelo Android.) Quando o `GoogleMap` objeto estiver pronto, Android chama seu aplicativo `OnMapReady` método (que você deve implementar como parte do `IOnMapReadyCallback` interface). Por exemplo:

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

No exemplo de código acima, o `OnMapReady` inicializa de retorno de chamada de `_map` variável para as `GoogleMap` objeto.

Como um exemplo de como usar esse resultado, quando `OnResume` é chamado, ele pode verificar se `_map` não for nulo. Se `_map` é definido como um `GoogleMap` objeto `OnResume` pode chamar métodos para adicionar marcadores e mover sua câmera para um especificado longitude e latitude. Para obter um exemplo de código completo, consulte [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo).



### <a name="map-types"></a>Tipos de mapa

Há cinco tipos diferentes de mapas de API de mapas do Google:

-  **Normal** -esse é o tipo de mapa padrão. Ele mostra estradas e recursos naturais importantes juntamente com alguns humano pontos de interesse (por exemplo, edifícios e pontes).

-  **Satélite** -este mapa mostra a fotografia de satélite.

-  **Híbrido** - este mapa mostra a fotografia de satélite e mapas de estrada.

-  **Tipos de terrenos** -principalmente mostra topográficos recursos com alguns estradas.

-  **Nenhum** -este mapa não carregar qualquer lado a lado, ela é processada como uma grade vazia.


A imagem a seguir mostra três diferentes tipos de mapas, da esquerda para a direita (normal, híbrido, tipos de terrenos):

[![Três mapeiam capturas de tela de exemplo: Normal, híbrido e tipos de terrenos](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

O `GoogleMap.MapType` propriedade é usada para definir ou alterar o tipo de mapa é exibido. O trecho de código a seguir mostra como exibir um mapa de satélite.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```


### <a name="googlemap-properties"></a>GoogleMap Properties

`GoogleMap` define várias propriedades que podem controlar a funcionalidade e a aparência do mapa. Uma maneira de configurar o estado inicial de um `GoogleMap` é passar uma [GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html) objeto durante a criação de um `MapFragment`. O trecho de código a seguir é um exemplo do uso de um `GoogleMapOptions` objeto durante a criação de um `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

A outra maneira de configurar um `GoogleMap` objeto está definindo valores de [configurações de UI](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html) propriedade do objeto de mapa. O próximo exemplo de código mostra como configurar um `GoogleMap` para exibir os controles de zoom e uma bússola:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```


## <a name="interacting-with-the-map"></a>Interagir com o mapa

A API do Android Maps fornece APIs que permitem que uma atividade para alterar o ponto de Vista, adicionar marcadores, coloque sobreposições personalizadas ou desenhar formas geométricas. Esta seção aborda como realizar algumas dessas tarefas em xamarin.

### <a name="changing-the-viewpoint"></a>Alterar o ponto de vista

Mapas são modelados como um plano simples na tela, com base na projeção Mercator. A exibição do mapa é de um *câmera* buscam diretamente nesse plano. A posição da câmera pode ser controlada por alterar o local, zoom, inclinação e relevância. O [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate) classe é usada para mover o local de câmera. `CameraUpdate` objetos não serão instanciados diretamente, em vez disso, a API do Maps fornece o [CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html) classe.

Uma vez um `CameraUpdate` objeto foi criado, ele é passado como um parâmetro como o [GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera(com.google.maps.CameraUpdate)) ou [GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera(com.google.maps.CameraUpdate)) métodos. O `MoveCamera` método atualiza o mapa instantaneamente durante o `AnimateCamera` método fornece uma transição suave, animada.

Este trecho de código é um exemplo simples de como usar o `CameraUpdateFactory` para criar um `CameraUpdate` incrementará o nível de zoom do mapa em um:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

A API do Maps fornece um [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que será agregada a todos os valores possíveis para a posição da câmera. Uma instância desta classe pode ser fornecida para o [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition(com.google.android.gms.maps.model.CameraPosition)) método que retorna um `CameraUpdate` objeto. A API do Maps inclui também o [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) classe que fornece uma API fluente para criar `CameraPosition` objetos.
O trecho de código a seguir mostra um exemplo de como criar um `CameraUpdate` de um `CameraPosition` e usá-lo para alterar a posição da câmera em um `GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

No trecho de código anterior, um local específico no mapa é representado pela um [LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng) classe. O nível de zoom é definido como 18. A relevância é a medida bússola no sentido horário de Norte. A inclinação propriedade controla o ângulo de exibição e especifica um ângulo de graus 25 de vertical. A captura de tela a seguir mostra o `GoogleMap` depois de executar o código anterior:

[![Ângulo de visualização do mapa do Google de exemplo mostrando um local especificado com um inclinada](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Desenho do mapa

A API do Android Maps fornece da API para desenhar os itens a seguir em um mapa:

-  **Marcadores de** -esses são ícones especiais que são usados para identificar um único local em um mapa.

-  **Sobreposições de** -esta é uma imagem que pode ser usada para identificar uma coleção de locais ou a área do mapa.

-  **Linhas, polígonos e círculos** -esses são APIs que permitem que as atividades adicionar formas a um mapa.


#### <a name="markers"></a>Marcadores

A API do Maps fornece um [marcador](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) classe que encapsula todos os dados sobre um único local em um mapa. Por padrão, eles usam um ícone padrão fornecido pelo Google Maps. É possível personalizar a aparência de um marcador e responder a cliques do usuário.


##### <a name="adding-a-marker"></a>Adicionando um marcador

Para adicionar um marcador para um mapa, é necessário criar um novo [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) do objeto e, em seguida, chame o [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker(com.google.android.gms.maps.model.MarkerOptions)) método em um `GoogleMap` instância. Este método retornará um [marcador](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) objeto.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(marker1);
}
```

O título do marcador será exibido em uma *janela informações* quando o usuário toca no marcador. Captura de tela a seguir mostra a aparência esse marcador:

[![Exemplo de mapa de Google com um marcador e uma janela de informações para Vimy Montanhas](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Personalizando um marcador

É possível personalizar o ícone usado pelo marcador chamando o `MarkerOptions.InvokeIcon` método ao adicionar o marcador para o mapa.
Esse método usa um [BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html) objeto que contém os dados necessários para renderizar o ícone. O [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html) classe fornece alguns métodos auxiliares para simplificar a criação de um `BitmapDescriptor`. A lista a seguir apresenta alguns dos métodos a seguir:

-   `DefaultMarker(float colour)` &ndash; Use o marcador de Google Maps padrão, mas alterar a cor.

-   `FromAsset(string assetName)` &ndash; Use um ícone personalizado do arquivo especificado na pasta recursos.

-   `FromBitmap(Bitmap image)` &ndash; Use o bitmap especificado como o ícone.

-   `FromFile(string fileName` &ndash; Crie o ícone personalizado a partir do arquivo no caminho especificado.

-   `FromResource(int resourceId)` &ndash; Crie um ícone personalizado do recurso especificado.

O trecho de código a seguir mostra um exemplo de criação de um marcador padrão coloridos ciano:

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(marker1);
}
```


#### <a name="info-windows"></a>Informações Windows

*Windows informações* são windows especiais que pop-up para exibir informações para o usuário quando eles tocam em um marcador específico. Por padrão, a janela de informações exibirá o conteúdo do título do marcador. Se o título não tiver sido atribuído, nenhuma janela de informações será exibida. Apenas uma janela de informações pode ser mostrada por vez.

É possível personalizar a janela de informações implementando o [GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interface. Há dois métodos importantes nesta interface:

-  `public View GetInfoWindow(Marker marker)` &ndash; Este método é chamado para obter uma janela de informações personalizadas sobre um marcador. Se ele retornar `null` , a renderização de janela padrão será usado. Se esse método retorna uma exibição, essa exibição será colocada dentro do quadro de janela de informações.

-  `public View GetInfoContents(Marker marker)` &ndash; Esse método só será chamado se retorna GetInfoWindow `null` . Esse método pode retornar um `null` valor se for usado o processamento padrão do conteúdo da janela de informações. Caso contrário, esse método deve retornar uma exibição com o conteúdo da janela de informações.

Uma janela de informações não é uma exibição ao vivo - em vez disso, Android converterá a exibição para um bitmap estático e exibir que na imagem. Isso significa que uma janela de informações não pode responder a qualquer evento de toque ou gestos nem ele se atualiza automaticamente. Para atualizar uma janela de informações, é necessário chamar o [GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) método.

A imagem a seguir mostra alguns exemplos de algumas janelas de informações personalizadas. A imagem à esquerda tem seu conteúdo personalizado, enquanto a imagem à direita tem da janela e conteúdo personalizado:

![Janelas de marcador de exemplo para Melbourne, incluindo o ícone e população. A janela direita tem cantos arredondados.](maps-api-images/marker-infowindows.png)


#### <a name="ground-overlays"></a>Sobreposições de início

Ao contrário de marcadores, que identificam um local específico em um mapa, uma [GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html) é uma imagem que é usado para identificar uma coleção de locais ou em uma área do mapa.


##### <a name="adding-a-groundoverlay"></a>Adicionando um GroundOverlay

Adicionando uma sobreposição de terra a um mapa é muito semelhante à adição de um marcador para um mapa. Primeiro, um [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html) objeto é criado. Este objeto, em seguida, é passado como um parâmetro para o `GoogleMap.AddGroundOverlay` método, que retornará um `GroundOverlay` objeto. Este trecho de código é um exemplo de como adicionar uma sobreposição de início para um mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

Captura de tela a seguir mostra essa sobreposição em um mapa:

[![Exemplo de mapa com uma imagem sobreposta de um lembre polar](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Linhas, círculos e polígonos

Há três tipos simples de figuras geométricas que podem ser adicionados a um mapa:

-  **Polilinha** -essa é uma série de segmentos de linha conectada. Ele pode marcar um caminho em um mapa ou formar qualquer forma necessária.

-  **Polígono** -esta é uma forma fechada para marcar áreas em um mapa.

-  **Círculo** -isso desenhará um círculo no mapa.



##### <a name="polylines"></a>Polilinhas

Um [polilinha](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline) é uma lista de consecutivas `LatLng` objetos que especificam os vértices de cada segmento de linha. É criada uma polilinha criando primeiro uma `PolylineOptions` objeto e adicionar os pontos a ele. O `PolylineOption` objeto é transmitido para um `GoogleMap` objeto chamando o `AddPolyline` método.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```


##### <a name="polygons"></a>Polígonos

`Polygon`s são muito semelhantes às `Polyline`s, mas eles não estiverem abertos terminou. `Polygon`s são um loop fechado e têm seu interior preenchido.
`Polygon`s são criadas da mesma maneira exata como um `Polyline`, exceto o [GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) método invocado.

Ao contrário de um `Polyline`, um `Polygon` é de fechamento automático. Quando `AddPolygon` é chamado, o método fechará automaticamente desativado polígono ao desenhar uma linha que conecta o primeiro e último pontos. O trecho de código a seguir cria um retângulo preenchido pela mesma área do trecho de código anterior no `Polyline` exemplo.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```


##### <a name="circles"></a>Círculos

Círculos são criados pelo primeiro criando um [CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions) objeto que especificará o centro e o raio do círculo na metres. O círculo é desenhado no mapa chamando [GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions)).
O trecho de código a seguir mostra como desenhar um círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (CircleOptions);
```


## <a name="responding-to-events"></a>Respondendo a eventos

Há três tipos de interações que um usuário pode ter um mapa:

-  **Clique em de marcador** -o usuário clica em um marcador.

-  **Arraste marcador** -o usuário longa-clicou em um mparger

-  **Informações clique** -o usuário clica em uma janela de informações.

Cada um desses eventos será discutida em mais detalhes abaixo.


### <a name="marker-click-events"></a>Eventos de clique do marcador

Quando o usuário clica em um marcador de `MarkerClick` evento será gerado e um `GoogleMap.MarkerClickEventArgs` passado. Essa classe contém duas propriedades:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Essa propriedade deve ser definida como `true` para indicar que o manipulador de eventos consumiu o evento. Se isso for definido como `false` , em seguida, o comportamento padrão ocorrerá além do comportamento personalizado do manipulador de eventos.

-  `P0` &ndash; Esse parâmetro de nome inadequado é uma referência para o marcador que gerou o `MarkerClick` evento.


Este trecho de código mostra um exemplo de um `MarkerClick` isso alterará a posição da câmera para um novo local no mapa:

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>Eventos de arrastar do marcador

Esse evento é gerado quando o usuário deseja arraste o marcador. Por padrão, os marcadores não estão arrastáveis. Um marcador pode ser definido como arrastável definindo o `Marker.Draggable` propriedade `true` ou invocando o `MarkerOptions.Draggable` método com `true` como um parâmetro.

Para o primeiro, arraste o marcador, o usuário deve longa clique nele e manter seu dedo no mapa. Quando eles arraste seu dedo na tela, o marcador será movido. Quando o dedo do usuário retira fora da tela, o marcador permanecerá em vigor.

A lista a seguir descreve os vários eventos que serão gerados para um marcador arrastável:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Esse evento é gerado quando o usuário arrasta primeiro marcador.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Esse evento é gerado como o marcador está sendo arrastado.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Esse evento é gerado quando o quando o usuário for concluído arrastando o marcador.

Cada uma da `EventArgs` contém uma única propriedade chamada `P0` que é uma referência para o `Marker` objeto arrastado.


### <a name="info-window-click-events"></a>Janela de informações de eventos de clique

Apenas uma janela de informações pode ser exibida por vez. Quando o usuário clica em uma janela de informações em um mapa, o objeto de mapa gerará um `InfoWindowClick` eventos. O trecho de código a seguir mostra como conectar um manipulador para o evento:

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

Lembre-se de que uma janela de informações é static `View` que é renderizado como uma imagem do mapa. Qualquer widgets como botões, caixas de seleção ou modos de exibição de texto que são colocados dentro da janela de informações serão inerte e não podem responder a qualquer um dos seus eventos de usuário inteiro.



## <a name="related-links"></a>Links relacionados

- [Serviços do Google Play](http://developer.android.com/google/play-services/index.html)
- [Mapas de Google Android API v2](https://developers.google.com/maps/documentation/android/)
- [APK de serviços do Google Play](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Como obter uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Problema 57880: Serviços do Google Play atualizados mas AVD não](https://code.google.com/p/android/issues/detail?id=57880)
