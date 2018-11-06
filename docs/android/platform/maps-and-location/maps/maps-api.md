---
title: Usando os Google Maps API em seu aplicativo
description: Como implementar recursos de v2 de API do Google Maps em seu aplicativo xamarin. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: fb7d18e6434e32941531d2c37cd8b938ec21ba90
ms.sourcegitcommit: 28dbb5fa2fbcc7e66cd09c1d0077496fcdefac56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51027321"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Usando a API do Google Maps em seu aplicativo

Usando o aplicativo de mapas é ótimo, mas às vezes você deseja incluir mapas diretamente em seu aplicativo. Além de interno aplicativo de mapas, Google também oferece uma [API do mapeamento nativo para Android](https://developers.google.com/maps/documentation/android-sdk/intro).
A API de mapas é adequada para casos em que você deseja manter mais controle sobre a experiência de mapeamento. Coisas que são possíveis com a API do Maps incluem:

-  Alterar programaticamente o ponto de vista do mapa.
-  Adicionando e personalizando os marcadores.
-  Anotar um mapa com sobreposições.

Ao contrário de v1 de API do Google Maps Android agora foi preterida v2 de API do Google Maps Android faz parte do [Google Play Services](https://developers.google.com/android/guides/overview).
Um aplicativo xamarin. Android deve atender a alguns pré-requisitos obrigatórios antes que seja possível usar a API do Google Maps Android.


## <a name="google-maps-api-prerequisites"></a>Pré-requisitos de API do Google Maps

Várias etapas que precisam ser realizadas antes de poder usar a API de mapas, incluindo:

-  [Obter uma chave de API de mapas](#obtain-maps-key)
-  [Instalar o SDK dos serviços do Google Play](#install-gps-sdk)
-  [Instale o pacote de Xamarin.GooglePlayServices.Maps do NuGet](#install-gpsmaps-nuget)
-  [Especifique as permissões necessárias](#declare-permissions)
-  [Opcionalmente, crie um emulador com as APIs do Google](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Obter uma chave de API do Google Maps

A primeira etapa é obter uma chave de API do Google Maps (Observe que não é possível reutilizar uma chave de API do Google Maps v1 API herdada). Para obter informações sobre como obter e usar a chave de API com o xamarin. Android, consulte [obter a chave de API de um Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> Instalar o SDK dos serviços do Google Play

Google Play Services é uma tecnologia do Google que permite que aplicativos Android tirar proveito dos diversos recursos do Google como o Google +, no aplicativo de cobrança e mapas. Esses recursos são acessíveis em dispositivos Android, como serviços em segundo plano, f que estão contidas na [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Aplicativos Android interagem com o Google Play Services por meio da biblioteca de cliente do Google Play Services. Esta biblioteca contém as interfaces e classes para os serviços individuais, como mapas. O diagrama a seguir mostra a relação entre um aplicativo do Android e o Google Play Services:

![Diagrama que ilustra o Store do Play Google atualizando o APK de serviços do Google Play](maps-api-images/play-services-diagram.png)

API de mapas do Android é fornecido como parte do Google Play Services.
Antes de um aplicativo xamarin. Android pode usar a API de mapas, o SDK do Google Play Services deve ser instalado usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md). Captura de tela a seguir mostra onde no Gerenciador de SDK do Android o cliente de serviços do Google Play pode ser encontrado:

![Google Play Services aparece sob Extras no Gerenciador de SDK do Android](maps-api-images/image01.png)

> [!NOTE]
> Os serviços do Google Play APK é um produto licenciado que não pode estar presente em todos os dispositivos. Se não estiver instalado, Google Maps não funcionará no dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> Instale o pacote de Xamarin.GooglePlayServices.Maps do NuGet

O [Xamarin.GooglePlayServices.Maps pacote](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contém as associações do xamarin. Android para a API de mapas do Google Play Services.
Para adicionar o pacote de mapa de serviços do Google Play, clique com botão direito do **referências** pasta do seu projeto no Gerenciador de soluções e clique **gerenciar pacotes NuGet...** :

![Item de menu de contexto do Gerenciador de soluções mostrando gerenciar pacotes NuGet em referências](maps-api-images/image02.png)

Isso abre o **Gerenciador de pacotes NuGet**. Clique em **navegue** e insira **Xamarin Google Play Services Maps** no campo de pesquisa. Selecione **Xamarin.GooglePlayServices.Maps** e clique em **instalar**. (Se este pacote tivesse sido instalado anteriormente, clique em **atualização**.):

[![Gerenciador de pacotes do NuGet com Xamarin.GooglePlayServices.Maps pacote selecionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Observe que os pacotes de dependência a seguir também são instalados:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> Especifique as permissões necessárias

Aplicativos devem identificar os requisitos de hardware e a permissão para usar a API do Google Maps.  Algumas permissões são concedidas automaticamente pelo Google Play Services SDK, e não é necessário para um desenvolvedor para adicioná-las para explicitamente **AndroidManfest.XML**:

-  **Acesso ao estado da rede** &ndash; a API de mapas deve ser capaz de verificar se ele pode baixar as peças de mapa.

-  **Acesso à Internet** &ndash; acesso à Internet é necessário fazer o download de peças de mapa e se comunicar com os servidores do Google Play para acesso à API.

As permissões e os recursos a seguir devem ser especificados na **androidmanifest. XML** para a API do Google Maps Android:

-  **V2 OpenGL ES** &ndash; o aplicativo deve declarar o requisito para o OpenGL ES v2.

-  **Chave de API do Google Maps** &ndash; a chave de API é usada para confirmar que o aplicativo é registrado e autorizado a usar o Google Play Services. Ver [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obter detalhes sobre essa chave.
   
- **O cliente herdado do Apache HTTP de solicitação** &ndash; aplicativos destinados a Android 9.0 (API nível 28) ou superior, deverá especificar que o cliente herdado do Apache HTTP é uma biblioteca opcional para usar. 

-  **Acesso aos serviços baseados na Web do Google** &ndash; o aplicativo precisa de permissões para acessar serviços web do Google que dão suporte a mapas de API do Android.

-  **Permissões para notificações de serviços do Google Play** &ndash; o aplicativo deve ter a permissão para receber notificações remotas do Google Play Services.

-  **Acesso aos provedores de localização** &ndash; essas são permissões opcionais.
   Elas permitirão que o `GoogleMap` classe para exibir o local do dispositivo no mapa.


> [!NOTE]
> Versões muito antigas do SDK do Google Play necessárias a um aplicativo para solicitar o `WRITE_EXTERNAL_STORAGE` permissão. Esse requisito não é mais necessário, com as recentes associações do Xamarin para o Google Play Services.

O trecho a seguir está um exemplo das configurações que devem ser adicionados ao **androidmanifest. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />
    
    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />


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

Além de solicitar as permissões **androidmanifest. XML**, um aplicativo também deve executar verificações de permissão de tempo de execução para o `ACCESS_COARSE_LOCATION` e o `ACCESS_FINE_LOCATION` permissões. Consulte a [xamarin. Android permissões](~/android/app-fundamentals/permissions.md) guia para obter mais informações sobre como executar verificações de permissão de tempo de execução.


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Criar um emulador com APIs do Google

No caso de um dispositivo Android físico com o Google Play services não estiver instalado, é possível criar uma imagem de emulador para desenvolvimento. Para obter mais informações, consulte o [Gerenciador de dispositivos](~/android/get-started/installation/android-emulator/device-manager.md).


## <a name="the-googlemap-class"></a>A classe GoogleMap

Depois que os pré-requisitos forem atendidos, é hora de começar a desenvolver o aplicativo e usar a API de mapas do Android. O [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) classe é a API principal que um aplicativo xamarin. Android usará para exibir e interagir com um Google Maps para o Android. Essa classe tem as seguintes responsabilidades:

-  Interagindo com serviços do Google Play para autorizar o aplicativo com o serviço de web do Google.

-  Baixar, armazenamento em cache e exibir os blocos de mapa.

-  Exibindo controles de interface do usuário, como Panorâmica e zoom para o usuário.

-  Marcadores de desenho e formas geométricas em mapas.

O `GoogleMap` é adicionado a uma atividade em uma das duas maneiras:

-  **MapFragment** - o [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) é um fragmento especializado que atua como host para o `GoogleMap` objeto. O `MapFragment` requer o nível de API do Android 12 ou superior.
   Versões mais antigas do Android podem usar o [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Este guia se concentrará no uso de `MapFragment` classe.

-  **MapView** - o [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) é uma subclasse de exibição especializada, que pode atuar como um host para um `GoogleMap` objeto. Os usuários dessa classe devem encaminhar todos os métodos de ciclo de vida de atividade para o `MapView` classe.

Cada um desses contêineres expõe um `Map` propriedade que retorna uma instância de `GoogleMap`. Preferência deve ser fornecida para o [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) classe conforme ele é uma API mais simples que reduz o código clichê de quantidade que um desenvolvedor deve implementar manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Adicionando um MapFragment a uma atividade

Captura de tela a seguir está um exemplo de um simples `MapFragment`:

[![Captura de tela de um dispositivo exibindo um fragmento de mapa do Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Assim como outras classes de fragmento, há duas maneiras de adicionar um `MapFragment` a uma atividade:

-   **Declarativamente** – o `MapFragment` podem ser adicionados por meio do arquivo de layout XML para a atividade. O trecho XML a seguir mostra um exemplo de como usar o `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **Por meio de programação** - o `MapFragment` pode ser instanciada programaticamente usando o [ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) método e, em seguida, adicionado a uma atividade. Este trecho mostra a maneira mais simples para instanciar um `MapFragment` do objeto e adicionar a uma atividade:
    
    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    É possível configurar o `MapFragment` objeto, passando um [ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) objeto `NewInstance`. Isso é discutido na seção [GoogleMap propriedades](#googlemap_object) que são exibidas mais adiante neste guia.

O `MapFragment.GetMapAsync` método é usado para inicializar o [ `GoogleMap` ](#googlemap_object) que é hospedado pelo fragmento e obter uma referência ao objeto de mapa que é hospedado pela `MapFragment`. Esse método usa um objeto que implementa o `IOnMapReadyCallback` interface. 

Essa interface tem um único método, `IMapReadyCallback.OnMapReady(MapFragment map)` que será invocado quando for possível para o aplicativo interagir com o `GoogleMap` objeto. O trecho de código a seguir mostra como uma atividade do Android pode inicializar uma `MapFragment` e implemente o `IOnMapReadyCallback` interface:
```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);
    
        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);
    
        // remainder of code omitted
    }
    
    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Tipos de mapa

Há cinco tipos diferentes de mapas disponíveis da API de mapas do Google:

-  **Normal** -esse é o tipo de mapa padrão. Ele mostra estradas e importantes recursos naturais, juntamente com alguns artifical pontos de interesse (por exemplo, edifícios e pontes).

-  **Satélite** -este mapa mostra fotografia de satélite.

-  **Híbrido** - este mapa mostra fotografia de satélite e mapas de estrada.

-  **Terreno** -isso principalmente mostra recursos topográficos com algumas estradas.

-  **Nenhum** -este mapa não carregar os blocos, ela é processada como uma grade vazia.


A imagem a seguir mostra três dos diferentes tipos de mapas, da esquerda para a direita (normal, híbrido, terreno):

[![Três capturas de tela de exemplo do mapa: Normal, híbrido e terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

O `GoogleMap.MapType` propriedade é usada para definir ou alterar o tipo de mapa é exibido. O trecho de código a seguir mostra como exibir um mapa de satélite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />Propriedades de GoogleMap

`GoogleMap` define várias propriedades que podem controlar a funcionalidade e a aparência do mapa. Uma maneira de configurar o estado inicial de um `GoogleMap` é passar uma [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) do objeto durante a criação de um `MapFragment`. O trecho de código a seguir é um exemplo do uso de um `GoogleMapOptions` do objeto durante a criação de um `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

A outra maneira de configurar uma `GoogleMap` está manipulando propriedades em de [configurações de UI](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) do objeto de mapa. O próximo exemplo de código mostra como configurar um `GoogleMap` para exibir os controles de zoom e uma bússola:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interagindo com o GoogleMap

API de mapas do Android fornece APIs que permitem que uma atividade para alterar o ponto de Vista, adicionar marcadores, coloque as sobreposições personalizadas ou desenhar formas geométricas. Esta seção aborda como realizar algumas dessas tarefas no xamarin. Android.

### <a name="changing-the-viewpoint"></a>Alterando o ponto de vista

Mapas são modelados como um plano simples na tela, com base na projeção Mercator. A exibição do mapa é o de um *câmera* atraente diretamente para baixo desse plano. A posição da câmera pode ser controlada por alterar o local, zoom, inclinação e influência. O [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) classe é usada para mover o local da câmera. `CameraUpdate` objetos não são instanciados diretamente, em vez disso, a API de mapas fornece o [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) classe.

Uma vez um `CameraUpdate` objeto foi criado, ele é passado como um parâmetro como o [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) ou [GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) métodos. O `MoveCamera` método atualiza o mapa instantaneamente durante o `AnimateCamera` método fornece uma transição suave e animada.

Este trecho de código é um exemplo simples de como usar o `CameraUpdateFactory` para criar um `CameraUpdate` que incrementará o nível de zoom do mapa com o nível de zoom de um:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

A API de mapas fornece um [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que irá agregar todos os valores possíveis para a posição da câmera. Uma instância dessa classe pode ser fornecida para o [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) método que retornará um `CameraUpdate` objeto. A API de mapas também inclui o [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) classe que fornece uma API fluente para a criação de `CameraPosition` objetos.
O trecho de código a seguir mostra um exemplo de criação de um `CameraUpdate` de um `CameraPosition` e usá-la para alterar a posição da câmera em um `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);
    
    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);
    
    CameraPosition cameraPosition = builder.Build();
    
    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

No trecho de código anterior, um local específico no mapa é representado pela [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) classe. O nível de zoom é definido para 18, que é uma medida arbitrária de zoom usado pelo Google Maps. A relevância é a medida de bússola no sentido horário do Norte. A propriedade de inclinação controla o ângulo de exibição e especifica um ângulo de graus de 25 de vertical. A captura de tela a seguir mostra o `GoogleMap` depois de executar o código anterior:

[![Ângulo de visualização de mapa do Google de exemplo mostrando um local especificado com um inclinada](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Desenho do mapa

API de mapas do Android fornece da API para desenhar os itens a seguir em um mapa:

-  **Marcadores de** -esses são os ícones especiais que são usados para identificar um único local em um mapa.

-  **Sobreposições** -esta é uma imagem que pode ser usada para identificar uma coleção de locais ou área no mapa.

-  **Linhas, polígonos e círculos** -esses são APIs que permitem que as atividades adicionar formas a um mapa.


#### <a name="markers"></a>Marcadores

A API de mapas fornece um [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) classe que encapsula todos os dados sobre um único local em um mapa. Por padrão, o marcador de classe usa um ícone padrão fornecido pelo Google Maps. É possível personalizar a aparência de um marcador e responder a cliques do usuário.


##### <a name="adding-a-marker"></a>Adicionando um marcador

Para adicionar um marcador em um mapa, é necessário criar um novo [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) do objeto e, em seguida, chame o [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) método em um `GoogleMap` instância. Esse método retornará um [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) objeto.

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    map.AddMarker(markerOpt1);
}
```

O título do marcador será exibido em uma *janela informações* quando o usuário toca no marcador de. Captura de tela a seguir mostra a aparência desse marcador:

[![Mapa do Google de exemplo com um marcador e uma janela de informações para Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Personalizando um marcador

É possível personalizar o ícone usado pelo marcador chamando o `MarkerOptions.InvokeIcon` método ao adicionar o marcador no mapa.
Esse método usa um [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) objeto que contém os dados necessários para renderizar o ícone. O [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) classe fornece alguns métodos auxiliares para simplificar a criação de um `BitmapDescriptor`. A lista a seguir apresenta alguns desses métodos:

-   `DefaultMarker(float colour)` &ndash; Use o marcador do Google Maps padrão, mas alterar a cor.

-   `FromAsset(string assetName)` &ndash; Use um ícone personalizado do arquivo especificado na pasta ativos.

-   `FromBitmap(Bitmap image)` &ndash; Use o bitmap especificado como o ícone.

-   `FromFile(string fileName)` &ndash; Crie o ícone personalizado a partir do arquivo no caminho especificado.

-   `FromResource(int resourceId)` &ndash; Crie um ícone personalizado do recurso especificado.

O trecho de código a seguir mostra um exemplo de criação de um marcador de ciano padrão colorido no local:

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);
    
    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Windows de informações

*Windows informações* são janelas especiais que pop-up para exibir informações para o usuário quando eles tocam em um marcador específico. Por padrão, a janela de informações exibirá o conteúdo de título do marcador. Se o título não tiver sido atribuído, nenhuma janela de informações será exibida. Apenas uma janela de informações pode ser mostrada por vez.

É possível personalizar a janela de informações com a implementação de [GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interface. Há dois métodos importantes na interface em questão:

-  `public View GetInfoWindow(Marker marker)` &ndash; Esse método é chamado para obter uma janela de informações personalizadas para um marcador. Se ele retornar `null` , então a renderização de janela padrão será usada. Se esse método retorna uma exibição, essa exibição será colocada dentro do quadro de janela de informações.

-  `public View GetInfoContents(Marker marker)` &ndash; Esse método só será chamado se retorna GetInfoWindow `null` . Esse método pode retornar um `null` valor se a renderização padrão do conteúdo da janela de informações deve ser usada. Caso contrário, esse método deve retornar uma exibição com o conteúdo da janela de informações.

Uma janela de informações não é uma exibição ao vivo - em vez disso, Android converterá o modo de exibição para um bitmap estático e exibir que na imagem. Isso significa que uma janela de informações não pode responder a quaisquer eventos de toque ou gestos, nem ele atualizará automaticamente em si. Para atualizar uma janela de informações, é necessário chamar o [GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) método.

A imagem a seguir mostra alguns exemplos de algumas janelas de informações personalizadas. A imagem à esquerda tem seu conteúdo personalizado, enquanto a imagem à direita tem sua janela e o conteúdo personalizado com cantos arredondados:

![Janelas de marcador de exemplo para Melbourne, incluindo o ícone e população. Janela à direita tem cantos arredondados.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

Ao contrário de marcadores, que identifica um local específico em um mapa, uma [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) é uma imagem que é usada para identificar uma coleção de locais ou em uma área do mapa.

##### <a name="adding-a-groundoverlay"></a>Adicionando um GroundOverlay

Adicionando uma sobreposição de zero a um mapa é semelhante à adição de um marcador para um mapa. Primeiro, uma [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) objeto é criado. Esse objeto é então passado como um parâmetro para o [ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) método, que retornará um `GroundOverlay` objeto. Este trecho de código é um exemplo da adição de uma sobreposição de zero a um mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

Captura de tela a seguir mostra essa sobreposição em um mapa:

[![Exemplo de mapa com uma imagem sobreposto de um urso polar](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Linhas, círculos e polígonos

Há três tipos simples de geométricos figuras que podem ser adicionados a um mapa:

-  **Polyline** -esta é uma série de segmentos de linha conectados. Ele pode marcar um caminho em um mapa ou criar uma forma geométrica.

-  **Círculo** -isso desenhará um círculo no mapa.

-  **Polígono** -isso é uma forma fechada para marcar as áreas em um mapa.


##### <a name="polylines"></a>Polilinhas

Um [polilinha](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) é uma lista de consecutivas `LatLng` objetos que especificam os vértices de cada segmento de linha. Uma polilinha é criada criando primeiro um `PolylineOptions` objeto e adicionando os pontos a ele. O `PolylineOption` objeto é passado para um `GoogleMap` objeto chamando o `AddPolyline` método.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Círculos

Círculos são criados pelo primeiro instanciar um [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) objeto que especificará o centro e o raio do círculo em metres. O círculo é desenhado no mapa, chamando [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
O trecho de código a seguir mostra como desenhar um círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>Polígonos

`Polygon`s são semelhantes às `Polyline`s, no entanto, eles não estiverem abertos terminou. `Polygon`s são um loop fechado e têm seu interior preenchido.
`Polygon`s são criados da mesma maneira exata como uma `Polyline`, exceto o [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) método invocado.

Ao contrário de um `Polyline`, um `Polygon` é de fechamento automático. O polígono será fechado desativado pelo `AddPolygon` método desenhando uma linha que conecta o primeiro e último pontos. O trecho de código a seguir cria um retângulo sólido pela mesma área do trecho de código anterior no `Polyline` exemplo.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```


## <a name="responding-to-user-events"></a>Respondendo a eventos de usuário

Há três tipos de interações que um usuário pode ter um mapa:

-  **Clique em de marcador** -o usuário clica em um marcador.

-  **Arraste do marcador** -o usuário longa-clicou em um mparger

-  **Janela de informações clique** -o usuário clica em uma janela de informações.

Cada um desses eventos será discutida em mais detalhes abaixo.


### <a name="marker-click-events"></a>Eventos de clique do marcador

O `MarkerClicked` é gerado quando o usuário toca em um marcador. Esse evento aceita um `GoogleMap.MarkerClickEventArgs` objeto como um parâmetro. Essa classe contém duas propriedades:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Essa propriedade deve ser definida como `true` para indicar que o manipulador de eventos consumiu o evento. Se isso for definido como `false` e em seguida, ocorrerá o comportamento padrão, além do comportamento personalizado do manipulador de eventos.

-  `Marker` &ndash; Essa propriedade é uma referência para o marcador que gerou o `MarkerClick` eventos.


Este trecho de código mostra um exemplo de um `MarkerClick` que irá alterar a posição da câmera para um novo local no mapa:

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);
    
        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>Eventos de marcador de arrastar

Esse evento é gerado quando o usuário deseja arraste o marcador. Por padrão, os marcadores não são arrastáveis. Um marcador pode ser definido como arrastável definindo a `Marker.Draggable` propriedade para `true` ou invocando a `MarkerOptions.Draggable` método com `true` como um parâmetro.

Para arrastar o marcador, o usuário deve primeiro longa clique no marcador de e, em seguida, seu dedo deve permanecer no mapa. Quando o dedo do usuário é arrastado na tela, o marcador será movido. Quando levanta o dedo do usuário fora da tela, o marcador permanecerá em vigor.

A lista a seguir descreve os vários eventos que serão gerados para um marcador arrastável:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Esse evento é gerado quando o usuário arrasta primeiro o marcador.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Esse evento é gerado como o marcador está sendo arrastado.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Esse evento é gerado quando o usuário arrastar o marcador.

Cada um dos `EventArgs` contém uma única propriedade chamada `P0` que é uma referência para o `Marker` do objeto que está sendo arrastado.


### <a name="info-window-click-events"></a>Eventos de clique da janela de informações

Apenas uma janela de informações pode ser exibida por vez. Quando o usuário clica em uma janela de informações em um mapa, o objeto de mapa irá gerar um `InfoWindowClick` eventos. O trecho de código a seguir mostra como conectar um manipulador ao evento:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

Lembre-se de que uma janela de informações é um estático `View` que é renderizado como uma imagem do mapa. Qualquer widgets, como botões, caixas de seleção ou modos de exibição de texto que são colocados dentro da janela de informações serão inertes e não podem responder a qualquer um dos seus eventos de usuário integral.


## <a name="related-links"></a>Links relacionados

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Mapas de Google Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtenção de uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [biblioteca de usos](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [usa o recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element)
