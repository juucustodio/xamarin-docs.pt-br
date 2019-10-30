---
title: Usando a API do Google Maps em seu aplicativo
description: Como implementar recursos da API do Google Maps V2 em seu aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027144"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Usando a API do Google Maps em seu aplicativo

Usar o aplicativo Maps é ótimo, mas às vezes você deseja incluir mapas diretamente em seu aplicativo. Além do aplicativo de mapas interno, o Google também oferece uma [API de mapeamento nativo para Android](https://developers.google.com/maps/documentation/android-sdk/intro).
A API de mapas é adequada para casos em que você deseja manter mais controle sobre a experiência de mapeamento. As coisas que são possíveis com a API Maps são:

- Alterando programaticamente o ponto de vista do mapa.
- Adicionar e personalizar marcadores.
- Anotando um mapa com sobreposições.

Ao contrário da API do Android do Google Maps v1, a API do Android v2 do Google Maps é parte do [Google Play Services](https://developers.google.com/android/guides/overview).
Um aplicativo Xamarin. Android deve atender A alguns pré-requisitos obrigatórios antes que seja possível usar a API do Google Maps Android.

## <a name="google-maps-api-prerequisites"></a>Pré-requisitos da API do Google Maps

Várias etapas precisam ser executadas antes que você possa usar a API Maps, incluindo:

- [Obter uma chave de API de mapas](#obtain-maps-key)
- [Instalar o SDK do Google Play Services](#install-gps-sdk)
- [Instalar o pacote Xamarin. GooglePlayServices. Maps do NuGet](#install-gpsmaps-nuget)
- [Especificar as permissões necessárias](#declare-permissions)
- [Opcionalmente, crie um emulador com as APIs do Google](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />obter uma chave de API do Google Maps

A primeira etapa é obter uma chave de API do Google Maps (Observe que não é possível reutilizar uma chave de API da API herdada do Google Maps v1). Para obter informações sobre como obter e usar a chave de API com Xamarin. Android, consulte [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> instalar o SDK do Google Play Services

Google Play Services é uma tecnologia do Google que permite que aplicativos Android tirem proveito de vários recursos do Google, como Google +, In-App Billing e Maps. Esses recursos podem ser acessados em dispositivos Android como serviços em segundo plano, que estão contidos no [Google Play Services apk](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Os aplicativos Android interagem com Google Play Services por meio da biblioteca de cliente do Google Play Services. Essa biblioteca contém as interfaces e classes para os serviços individuais, como mapas. O diagrama a seguir mostra a relação entre um aplicativo Android e Google Play Services:

![Diagrama ilustrando o Google Play Store atualizando o Google Play Services APK](maps-api-images/play-services-diagram.png)

A API de mapas do Android é fornecida como parte do Google Play Services.
Antes que um aplicativo Xamarin. Android possa usar a API Maps, o SDK do Google Play Services deve ser instalado usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md). A captura de tela a seguir mostra onde no SDK do Android Manager o cliente do Google Play Services pode ser encontrado:

![Google Play Services aparece em extras no Gerenciador de SDK do Android](maps-api-images/image01.png)

> [!NOTE]
> O APK dos serviços de Google Play é um produto licenciado que pode não estar presente em todos os dispositivos. Se não estiver instalado, o Google Maps não funcionará no dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> instalar o pacote Xamarin. GooglePlayServices. Maps do NuGet

O [pacote xamarin. GooglePlayServices. Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contém as associações do Xamarin. Android para a API do Google Play Services Maps.
Para adicionar o pacote de mapa do Google Play Services, clique com o botão direito do mouse na pasta **referências** do seu projeto no Gerenciador de soluções e clique em **gerenciar pacotes NuGet...** :

![Gerenciador de Soluções mostrando o item de menu de contexto gerenciar pacotes NuGet em referências](maps-api-images/image02.png)

Isso abre o **Gerenciador de pacotes NuGet**. Clique em **procurar** e insira o **Xamarin Google Play Services mapas** no campo de pesquisa. Selecione **Xamarin. GooglePlayServices. Maps** e clique em **instalar**. (Se esse pacote tiver sido instalado anteriormente, clique em **Atualizar**.):

[![o Gerenciador de pacotes NuGet com o pacote Xamarin. GooglePlayServices. Maps selecionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Observe que os seguintes pacotes de dependência também estão instalados:

- **Xamarin. GooglePlayServices. base**
- **Xamarin. GooglePlayServices. porão**
- **Xamarin. GooglePlayServices. Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> especificar as permissões necessárias

Os aplicativos devem identificar os requisitos de hardware e permissão para usar a API do Google Maps.  Algumas permissões são concedidas automaticamente pelo SDK do Google Play Services, e não é necessário para um desenvolvedor adicioná-las explicitamente ao **AndroidManfest. xml**:

- **Acesso ao estado da rede** &ndash; a API do Maps deve ser capaz de verificar se pode baixar os blocos do mapa.

- **Acesso à internet** &ndash; acesso à Internet é necessário para baixar os blocos de mapa e se comunicar com os servidores de Google Play para acesso à API.

As seguintes permissões e recursos devem ser especificados no **AndroidManifest. xml** para a API do Google Maps Android:

- **OpenGL ES v2** &ndash; o aplicativo deve declarar o requisito para OpenGL ES v2.

- **Chave de API do Google Maps** &ndash; a chave de API é usada para confirmar se o aplicativo está registrado e autorizado a usar Google Play Services. Consulte [obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obter detalhes sobre essa chave.

- **Solicite os aplicativos de &ndash; de cliente http herdados** que têm como destino o Android 9,0 (nível de API 28) ou acima devem especificar que o cliente Apache http herdado é uma biblioteca opcional a ser usada.

- **Acesso aos serviços do Google baseados na Web** &ndash; o aplicativo precisa de permissões para acessar os serviços Web do Google que retornam a API de mapas do Android.

- **Permissões para notificações de Google Play Services** &ndash; o aplicativo deve ter permissão para receber notificações remotas de Google Play Services.

- O **acesso a provedores de localização** &ndash; são permissões opcionais.
   Eles permitirão que a classe `GoogleMap` exiba o local do dispositivo no mapa.

Além disso, o Android 9 removeu a biblioteca de cliente HTTP do Apache do bootclasspath e, portanto, não está disponível para aplicativos destinados à API 28 ou superior. A linha a seguir deve ser adicionada ao nó `application` do seu arquivo **AndroidManifest. xml** para continuar usando o cliente Apache HTTP em aplicativos que têm a API de destino 28 ou superior:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Versões muito antigas do SDK do Google Play exigiam que um aplicativo solicitasse a permissão `WRITE_EXTERNAL_STORAGE`. Esse requisito não é mais necessário com as associações recentes do Xamarin para Google Play Services.

O trecho a seguir é um exemplo das configurações que devem ser adicionadas ao **AndroidManifest. xml**:

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
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

Além de solicitar as permissões **AndroidManifest. xml**, um aplicativo também deve executar verificações de permissão de tempo de execução para o `ACCESS_COARSE_LOCATION` e as permissões de `ACCESS_FINE_LOCATION`. Consulte o guia de [permissões do Xamarin. Android](~/android/app-fundamentals/permissions.md) para obter mais informações sobre como executar verificações de permissão em tempo de execução.

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />criar um emulador com APIs do Google

Caso um dispositivo Android físico com serviços de Google Play não esteja instalado, é possível criar uma imagem de emulador para desenvolvimento. Para obter mais informações, consulte a [Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>A classe GoogleMap

Depois que os pré-requisitos são satisfeitos, é hora de começar a desenvolver o aplicativo e usar a API de mapas do Android. A classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) é a API principal que um aplicativo Xamarin. Android usará para exibir e interagir com um Google Maps para Android. Essa classe tem as seguintes responsabilidades:

- Interagir com serviços de Google Play para autorizar o aplicativo com o serviço Web do Google.

- Baixando, armazenando em cache e exibindo os blocos de mapa.

- Exibindo controles de interface do usuário, como panorâmica e zoom para ele.

- Marcadores de desenho e formas geométricas em mapas.

O `GoogleMap` é adicionado a uma atividade de uma das duas maneiras:

- **MapFragment** -o [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) é um fragmento especializado que atua como host para o objeto `GoogleMap`. A `MapFragment` requer a API do Android nível 12 ou superior.
   As versões mais antigas do Android podem usar o [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Este guia se concentrará no uso da classe `MapFragment`.

- **MapView** -o [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) é uma subclasse de exibição especializada, que pode atuar como um host para um objeto de `GoogleMap`. Os usuários dessa classe devem encaminhar todos os métodos de ciclo de vida da atividade para a classe `MapView`.

Cada um desses contêineres expõe uma propriedade `Map` que retorna uma instância de `GoogleMap`. A preferência deve ser dada à classe [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) , pois é uma API mais simples que reduz a quantidade de código clichê que um desenvolvedor deve implementar manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Adicionando um MapFragment a uma atividade

A captura de tela a seguir é um exemplo de `MapFragment`simples:

[![captura de tela de um dispositivo que exibe um fragmento do mapa do Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Semelhante a outras classes de fragmento, há duas maneiras de adicionar um `MapFragment` a uma atividade:

- **Declarativamente** -a `MapFragment` pode ser adicionada por meio do arquivo de layout XML para a atividade. O trecho de código XML a seguir mostra um exemplo de como usar o elemento `fragment`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **Programaticamente** -o `MapFragment` pode ser instanciado programaticamente usando o método [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) e, em seguida, adicionado a uma atividade. Este trecho de código mostra a maneira mais simples de instanciar um objeto `MapFragment` e adicionar a uma atividade:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    É possível configurar o objeto `MapFragment` passando um objeto [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) para `NewInstance`. Isso é discutido na seção [Propriedades do GoogleMap](#googlemap_object) que aparece mais adiante neste guia.

O método `MapFragment.GetMapAsync` é usado para inicializar o [`GoogleMap`](#googlemap_object) que é hospedado pelo fragmento e obter uma referência ao objeto de mapa que é hospedado pelo `MapFragment`. Esse método usa um objeto que implementa a interface `IOnMapReadyCallback`.

Essa interface tem um único método, `IMapReadyCallback.OnMapReady(MapFragment map)` que será invocado quando for possível que o aplicativo interaja com o objeto `GoogleMap`. O trecho de código a seguir mostra como uma atividade do Android pode inicializar um `MapFragment` e implementar a interface `IOnMapReadyCallback`:

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

Há cinco tipos diferentes de mapas disponíveis na API do Google Maps:

- **Normal** – esse é o tipo de mapa padrão. Ele mostra estradas e recursos naturais importantes, juntamente com alguns pontos de interesse artificial (como edifícios e pontes).

- **Satélite** -este mapa mostra a fotografia satélite.

- **Híbrido** – este mapa mostra fotografias satélite e mapas de estrada.

- **Terreno** – isso mostra principalmente os recursos de topografia com algumas estradas.

- **Nenhum** – este mapa não carrega nenhum bloco, ele é renderizado como uma grade vazia.

A imagem abaixo mostra três dos diferentes tipos de mapas, da esquerda para a direita (normal, híbrida, terreno):

[![três capturas de tela de exemplo de mapa: normal, híbrida e terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

A propriedade `GoogleMap.MapType` é usada para definir ou alterar o tipo de mapa que é exibido. O trecho de código a seguir mostra como exibir um mapa satélite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a>Propriedades de <a name="googlemap_object" />GoogleMap

`GoogleMap` define várias propriedades que podem controlar a funcionalidade e a aparência do mapa. Uma maneira de configurar o estado inicial de uma `GoogleMap` é passar um objeto [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) ao criar um `MapFragment`. O trecho de código a seguir é um exemplo de uso de um objeto `GoogleMapOptions` ao criar uma `MapFragment`:

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

A outra maneira de configurar uma `GoogleMap` é por meio da manipulação de propriedades no [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) do objeto de mapa. O exemplo de código a seguir mostra como configurar um `GoogleMap` para exibir os controles de zoom e uma bússola:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interagindo com o GoogleMap

A API do Maps do Android fornece APIs que permitem que uma atividade altere o ponto de vista, adicione marcadores, coloque sobreposições personalizadas ou desenhe formas geométricas. Esta seção explicará como realizar algumas dessas tarefas no Xamarin. Android.

### <a name="changing-the-viewpoint"></a>Alterando o ponto de vista

Os mapas são modeladas como um plano simples na tela, com base na projeção Mercator. A exibição de mapa é a de uma *câmera* que está olhando para baixo neste plano. A posição da câmera pode ser controlada com a alteração do local, do zoom, da inclinação e da localização. A classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) é usada para mover o local da câmera. `CameraUpdate` objetos não são instanciados diretamente, em vez disso, a API do MAPS fornece a classe [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) .

Depois que um objeto `CameraUpdate` tiver sido criado, ele será passado como um parâmetro para os métodos [googleMap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) ou [googleMap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) . O método `MoveCamera` atualiza o mapa instantaneamente enquanto o método `AnimateCamera` fornece uma transição suave e animada.

Este trecho de código é um exemplo simples de como usar o `CameraUpdateFactory` para criar uma `CameraUpdate` que incrementará o nível de zoom do mapa por um nível de zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

A API do MAPS fornece um [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que agregará todos os valores possíveis para a posição da câmera. Uma instância dessa classe pode ser fornecida para o método [CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) que retornará um objeto `CameraUpdate`. A API de mapas também inclui a classe [CameraPosition. Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) que fornece uma API fluente para criar `CameraPosition` objetos.
O trecho de código a seguir mostra um exemplo de como criar um `CameraUpdate` de um `CameraPosition` e usá-lo para alterar a posição da câmera em um `GoogleMap`:

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

No trecho de código anterior, um local específico no mapa é representado pela classe [latlng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) . O nível de zoom é definido como 18, que é uma medida arbitrária de zoom usada pelo Google Maps. A base é a medida da bússola no sentido horário do Norte. A propriedade inclinação controla o ângulo de exibição e especifica um ângulo de 25 graus a partir da vertical. A captura de tela a seguir mostra a `GoogleMap` depois de executar o código anterior:

[![exemplo do mapa do Google mostrando um local especificado com um ângulo de exibição inclinado](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Desenho no mapa

A API de mapas do Android fornece APIs para desenhar os seguintes itens em um mapa:

- **Marcadores** -são ícones especiais que são usados para identificar um único local em um mapa.

- **Sobreposições** – essa é uma imagem que pode ser usada para identificar uma coleção de locais ou área no mapa.

- **Linhas, polígonos e círculos** -são APIs que permitem que as atividades adicionem formas a um mapa.

#### <a name="markers"></a>Marcadores

A API do MAPS fornece uma classe de [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) que encapsula todos os dados sobre um único local em um mapa. Por padrão, a classe Marker usa um ícone padrão fornecido pelo Google Maps. É possível personalizar a aparência de um marcador e responder aos cliques do usuário.

##### <a name="adding-a-marker"></a>Adicionando um marcador

Para adicionar um marcador a um mapa, é necessário criar um novo objeto [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) e, em seguida, chamar o método [addmarkr](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) em uma instância de `GoogleMap`. Esse método retornará um objeto de [marcador](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) .

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

O título do marcador será exibido em uma janela de *informações* quando o usuário tocar no marcador. A captura de tela a seguir mostra a aparência deste marcador:

[![exemplo de mapa do Google com um marcador e uma janela de informações para a saliência Vimy](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personalizando um marcador

É possível personalizar o ícone usado pelo marcador chamando o método `MarkerOptions.InvokeIcon` ao adicionar o marcador ao mapa.
Esse método usa um objeto [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) que contém os dados necessários para processar o ícone. A classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fornece alguns métodos auxiliares para simplificar a criação de um `BitmapDescriptor`. A lista a seguir apresenta alguns destes métodos:

- `DefaultMarker(float colour)` &ndash; use o marcador padrão do Google Maps, mas altere a cor.

- `FromAsset(string assetName)` &ndash; usar um ícone personalizado do arquivo especificado na pasta ativos.

- `FromBitmap(Bitmap image)` &ndash; use o bitmap especificado como o ícone.

- `FromFile(string fileName)` &ndash; criar o ícone personalizado do arquivo no caminho especificado.

- `FromResource(int resourceId)` &ndash; criar um ícone personalizado a partir do recurso especificado.

O trecho de código a seguir mostra um exemplo da criação de um marcador padrão de cor ciano:

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

#### <a name="info-windows"></a>Janelas de informações

As *janelas de informações* são janelas especiais que pop-up exibem informações para o usuário quando tocam em um marcador específico. Por padrão, a janela informações exibirá o conteúdo do título do marcador. Se o título não tiver sido atribuído, nenhuma janela de informações será exibida. Somente uma janela de informações pode ser mostrada de cada vez.

É possível personalizar a janela de informações implementando a interface [googleMap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) . Há dois métodos importantes nesta interface:

- `public View GetInfoWindow(Marker marker)` &ndash; esse método é chamado para obter uma janela de informações personalizadas para um marcador. Se ele retornar `null`, a renderização de janela padrão será usada. Se esse método retornar uma exibição, essa exibição será colocada dentro do quadro da janela informações.

- `public View GetInfoContents(Marker marker)` &ndash; esse método só será chamado se GetInfoWindow retornar `null`. Esse método pode retornar um valor `null` se o processamento padrão do conteúdo da janela de informações for ser usado. Caso contrário, esse método deve retornar um modo de exibição com o conteúdo da janela informações.

Uma janela de informações não é uma exibição ao vivo. em vez disso, o Android converterá a exibição em um bitmap estático e a exibirá na imagem. Isso significa que uma janela de informações não pode responder a nenhum evento ou gestos de toque, nem será atualizada automaticamente. Para atualizar uma janela de informações, é necessário chamar o método [googleMap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) .

A imagem a seguir mostra alguns exemplos de algumas janelas de informações personalizadas. A imagem à esquerda tem seu conteúdo personalizado, enquanto a imagem à direita tem sua janela e conteúdo personalizados com cantos arredondados:

![Exemplo de janelas de marcador para Melbourne, incluindo o ícone e a população. A janela direita tem cantos arredondados.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

Ao contrário dos marcadores, que identificam um local específico em um mapa, um [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) é uma imagem usada para identificar uma coleção de locais ou uma área no mapa.

##### <a name="adding-a-groundoverlay"></a>Adicionando um GroundOverlay

A adição de uma sobreposição de aterramento a um mapa é semelhante à adição de um marcador a um mapa. Primeiro, um objeto [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) é criado. Esse objeto é passado como um parâmetro para o método [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) , que retornará um objeto `GroundOverlay`. Este trecho de código é um exemplo de adição de uma sobreposição de aterramento a um mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

A captura de tela a seguir mostra essa sobreposição em um mapa:

[![exemplo de mapa com uma imagem sobreposta de um urso polar](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Linhas, círculos e polígonos

Há três tipos simples de valores geométricos que podem ser adicionados a um mapa:

- **Polilinha** -esta é uma série de segmentos de linha conectados. Ele pode marcar um caminho em um mapa ou criar uma forma geométrica.

- **Círculo** -isso desenhará um círculo no mapa.

- **Polygon** -esta é uma forma fechada para marcar áreas em um mapa.

##### <a name="polylines"></a>Polilinhas

Uma [polilinha](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) é uma lista de objetos de `LatLng` consecutivos que especificam os vértices de cada segmento de linha. Uma polilinha é criada pela primeira vez criando um objeto `PolylineOptions` e adicionando os pontos a ele. O objeto `PolylineOption` é passado para um objeto `GoogleMap` chamando o método `AddPolyline`.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Indicam

Os círculos são criados pela primeira instância de um objeto [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) , que especificará o centro e o raio do círculo em metres. O círculo é desenhado no mapa chamando [googleMap. addcircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
O trecho de código a seguir mostra como desenhar um círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Polígonos

`Polygon`s são semelhantes a `Polyline`s, no entanto, elas não são abertas. `Polygon`s são um loop fechado e têm seu interior preenchido.
os `Polygon`s são criados exatamente da mesma maneira que um `Polyline`, exceto pelo método [googleMap. AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) invocado.

Ao contrário de um `Polyline`, um `Polygon` é o autofechamento. O polígono será fechado pelo método `AddPolygon` desenhando uma linha que conecta o primeiro e o último pontos. O trecho de código a seguir criará um retângulo sólido sobre a mesma área que o trecho de código anterior no exemplo de `Polyline`.

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

Há três tipos de interações que um usuário pode ter com um mapa:

- **Marcador de clique** – o usuário clica em um marcador.

- **Arrastar marcador** -o usuário clicou com um clique longo em um mparger

- **Janela de informações clique** -o usuário clicou em uma janela de informações.

Cada um desses eventos será discutido em mais detalhes abaixo.

### <a name="marker-click-events"></a>Eventos de clique de marcador

O evento `MarkerClicked` é gerado quando o usuário toca em um marcador. Esse evento aceita um objeto `GoogleMap.MarkerClickEventArgs` como um parâmetro. Essa classe contém duas propriedades:

- `GoogleMap.MarkerClickEventArgs.Handled` &ndash; essa propriedade deve ser definida como `true` para indicar que o manipulador de eventos consumiu o evento. Se estiver definido como `false`, o comportamento padrão ocorrerá além do comportamento personalizado do manipulador de eventos.

- `Marker` &ndash; essa propriedade é uma referência ao marcador que gerou o evento `MarkerClick`.

Este trecho de código mostra um exemplo de um `MarkerClick` que alterará a posição da câmera para um novo local no mapa:

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

### <a name="marker-drag-events"></a>Eventos de arrastar marcador

Esse evento é gerado quando o usuário deseja arrastar o marcador. Por padrão, os marcadores não são arrastáveis. Um marcador pode ser definido como arrastável definindo a propriedade `Marker.Draggable` como `true` ou invocando o método `MarkerOptions.Draggable` com `true` como um parâmetro.

Para arrastar o marcador, o usuário deve primeiro clicar longo no marcador e, em seguida, o dedo deve permanecer no mapa. Quando o dedo do usuário é arrastado pela tela, o marcador será movido. Quando o dedo do usuário se levantar da tela, o marcador permanecerá em vigor.

A lista a seguir descreve os vários eventos que serão gerados para um marcador arrastável:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; esse evento é gerado quando o usuário arrasta o marcador pela primeira vez.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; esse evento é gerado como o marcador está sendo arrastado.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; esse evento é gerado quando o usuário termina de arrastar o marcador.

Cada um dos `EventArgs` contém uma única propriedade chamada `P0` que é uma referência ao objeto `Marker` que está sendo arrastado.

### <a name="info-window-click-events"></a>Eventos de clique da janela de informações

Somente uma janela de informações pode ser exibida de cada vez. Quando o usuário clica em uma janela de informações em um mapa, o objeto de mapa gera um evento de `InfoWindowClick`. O trecho de código a seguir mostra como conectar um manipulador ao evento:

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

Lembre-se de que uma janela de informações é um `View` estático que é renderizado como uma imagem no mapa. Qualquer widget, como botões, caixas de seleção ou exibições de texto posicionadas dentro da janela de informações, será inertdo e não poderá responder a nenhum dos seus eventos de usuário integral.

## <a name="related-links"></a>Links relacionados

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [API do Android do Google Maps v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [usa-biblioteca](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [usa-recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element)
