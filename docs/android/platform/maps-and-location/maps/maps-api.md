---
title: Usando a API do Google Maps em seu aplicativo
description: Como implementar os recursos do Google Maps API v2 em seu aplicativo Xamarin.Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027144"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Usando a API do Google Maps em seu aplicativo

Usar o aplicativo Maps é ótimo, mas às vezes você quer incluir mapas diretamente em seu aplicativo. Além do aplicativo de mapas incorporados, o Google também oferece uma [API de mapeamento nativo para Android](https://developers.google.com/maps/documentation/android-sdk/intro).
A API do Maps é adequada para casos em que você deseja manter mais controle sobre a experiência de mapeamento. As coisas que são possíveis com a API do Maps incluem:

- Mudando programáticamente o ponto de vista do mapa.
- Adicionando e personalizando marcadores.
- Anotando um mapa com sobreposições.

Ao contrário do agora depreciado Google Maps Android API v1, o Google Maps Android API v2 faz parte do [Google Play Services](https://developers.google.com/android/guides/overview).
Um aplicativo Xamarin.Android deve atender a alguns pré-requisitos obrigatórios antes que seja possível usar a API Android do Google Maps.

## <a name="google-maps-api-prerequisites"></a>Pré-requisitos de API do Google Maps

Várias etapas precisam ser tomadas antes que você possa usar a API do Maps, incluindo:

- [Obtenha uma chave de API do Maps](#obtain-maps-key)
- [Instale o Google Play Services SDK](#install-gps-sdk)
- [Instale o pacote Xamarin.GooglePlayServices.Maps da NuGet](#install-gpsmaps-nuget)
- [Especifique as permissões necessárias](#declare-permissions)
- [Opcionalmente, crie um emulador com as APIs do Google](#create-emulator-with-google-api)

### <a name="obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Obtenha uma chave de API do Google Maps

O primeiro passo é obter uma chave de API do Google Maps (observe que você não pode reutilizar uma chave de API da API v1 do Google Maps) herdada). Para obter informações sobre como obter e usar a chave API com Xamarin.Android, consulte [Obtendo uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="install-the-google-play-services-sdk"></a><a name="install-gps-sdk" />Instale o Google Play Services SDK

O Google Play Services é uma tecnologia do Google que permite que os aplicativos Android aproveitem vários recursos do Google, como Google+, In-App Billing e Maps. Esses recursos são acessíveis em dispositivos Android como serviços em segundo plano, que estão contidos no [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Os aplicativos para Android interagem com o Google Play Services através da biblioteca cliente do Google Play Services. Esta biblioteca contém as interfaces e as classes para os serviços individuais, como mapas. O diagrama a seguir mostra a relação entre um aplicativo Android e o Google Play Services:

![Diagrama ilustrando a Google Play Store atualizando o Google Play Services APK](maps-api-images/play-services-diagram.png)

A API do Android Maps é fornecida como parte do Google Play Services.
Antes que um aplicativo Xamarin.Android possa usar a API do Maps, o Google Play Services SDK deve ser instalado usando o [Android SDK Manager](~/android/get-started/installation/android-sdk.md). A captura de tela a seguir mostra onde no Android SDK Manager o cliente de serviços do Google Play pode ser encontrado:

![Google Play Services aparece em Extras no Android SDK Manager](maps-api-images/image01.png)

> [!NOTE]
> O APK dos serviços do Google Play é um produto licenciado que pode não estar presente em todos os dispositivos. Se não estiver instalado, o Google Maps não funcionará no dispositivo.

### <a name="install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" />Instale o pacote Xamarin.GooglePlayServices.Maps da NuGet

O [pacote Xamarin.GooglePlayServices.Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contém as ligações Xamarin.Android para a API do Google Play Services Maps.
Para adicionar o pacote Google Play Services Map, clique com o botão direito do mouse na pasta **Referências** do seu projeto no Solution Explorer e clique em **Gerenciar pacotes NuGet...**:

![Solution Explorer mostrando gerenciar o item de menu de contexto do NuGet Packages em Referências](maps-api-images/image02.png)

Isso abre o **NuGet Package Manager**. Clique **em Procurar** e digite **Xamarin Google Play Services Maps** no campo de pesquisa. Selecione **Xamarin.GooglePlayServices.Maps** e clique **em Instalar**. (Se este pacote tivesse sido instalado anteriormente, clique em **Atualizar**.):

[![NuGet Package Manager com Xamarin.GooglePlayServices.Maps pacote selecionado](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Observe que os seguintes pacotes de dependência também estão instalados:

- **Xamarin.GooglePlayServices.Base**
- **Xamarin.GooglePlayServices.Porão**
- **Xamarin.GooglePlayServices.Tarefas**

### <a name="specify-the-required-permissions"></a><a name="declare-permissions" />Especifique as permissões necessárias

Os aplicativos devem identificar os requisitos de hardware e permissão para usar a API do Google Maps.  Algumas permissões são concedidas automaticamente pelo Google Play Services SDK, e não é necessário que um desenvolvedor as adicione explicitamente ao **AndroidManfest.XML**:

- **Acesso ao Estado** &ndash; de Rede A API do Maps deve ser capaz de verificar se pode baixar as telhas do mapa.

- **Acesso à** &ndash; Internet O acesso à Internet é necessário para baixar as telhas do mapa e se comunicar com os Servidores do Google Play para acesso à API.

As seguintes permissões e recursos devem ser especificados no **AndroidManifest.XML** para a API Android do Google Maps:

- **OpenGL ES v2** &ndash; O aplicativo deve declarar o requisito para OpenGL ES v2.

- **Chave** &ndash; da API do Google Maps A chave da API é usada para confirmar que o aplicativo está registrado e autorizado a usar o Google Play Services. Consulte [Obter uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) para obter detalhes sobre esta chave.

- **Solicite o cliente** &ndash; Apache HTTP legado Os aplicativos que têm como alvo o Android 9.0 (nível 28 da API) ou superior devem especificar que o cliente Apache HTTP legado é uma biblioteca opcional para usar.

- **Acesso aos Serviços baseados** &ndash; na Web do Google O aplicativo precisa de permissões para acessar os serviços web do Google que ressuadem a API do Android Maps.

- **Permissões para notificações** &ndash; do Google Play Services O aplicativo deve receber permissão para receber notificações remotas do Google Play Services.

- **Acesso a provedores de** &ndash; localização Estas são permissões opcionais.
   Eles permitirão `GoogleMap` que a classe exiba a localização do dispositivo no mapa.

Além disso, o Android 9 removeu a biblioteca de clientes Apache HTTP do bootclasspath e, portanto, não está disponível para aplicativos que visam a API 28 ou superior. A seguinte linha deve `application` ser adicionada ao nó do seu arquivo **AndroidManifest.xml** para continuar usando o cliente Apache HTTP em aplicativos que visam a API 28 ou superior:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Versões muito antigas do Google Play SDK exigiam um aplicativo para solicitar a `WRITE_EXTERNAL_STORAGE` permissão. Essa exigência não é mais necessária com as recentes ligações xamarin para o Google Play Services.

O trecho a seguir é um exemplo das configurações que devem ser adicionadas ao **AndroidManifest.XML**:

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

Além de solicitar as permissões **AndroidManifest.XML,** um aplicativo também deve `ACCESS_COARSE_LOCATION` realizar `ACCESS_FINE_LOCATION` verificações de permissão de tempo de execução para as permissões. Consulte o guia [de permissões xamarin.android](~/android/app-fundamentals/permissions.md) para obter mais informações sobre a realização de verificações de permissões em tempo de execução.

### <a name="create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Crie um emulador com APIs do Google

Caso um dispositivo Android físico com serviços Google Play não esteja instalado, é possível criar uma imagem de emulador para desenvolvimento. Para obter mais informações, consulte o [Gerenciador de dispositivos](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>A classe GoogleMap

Uma vez que os pré-requisitos estejam satisfeitos, é hora de começar a desenvolver o aplicativo e usar a API do Android Maps. A classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) é a principal API que um aplicativo Xamarin.Android usará para exibir e interagir com um Google Maps para Android. Esta classe tem as seguintes responsabilidades:

- Interagindo com os serviços do Google Play para autorizar o aplicativo com o serviço web do Google.

- Baixando, cacheando e exibindo as telhas do mapa.

- Exibindo controles de ida e primeira, como panela e zoom para o usuário.

- Marcadores de desenho e formas geométricas em mapas.

O `GoogleMap` é adicionado a uma atividade de duas maneiras:

- **MapFragment** - O [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) é um Fragmento especializado `GoogleMap` que atua como host para o objeto. A `MapFragment` API android nível 12 ou superior.
   Versões mais antigas do Android podem usar o [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Este guia se concentrará em usar a `MapFragment` classe.

- **MapView** - O [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) é uma subclasse de exibição `GoogleMap` especializada, que pode atuar como um host para um objeto. Os usuários desta classe devem encaminhar todos os `MapView` métodos do ciclo de vida da Atividade para a classe.

Cada um desses contêineres `Map` expõe uma propriedade `GoogleMap`que retorna uma instância de . A preferência deve ser dada à classe [MapFragment,](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) pois é uma API mais simples que reduz a quantidade de código de caldeira que um desenvolvedor deve implementar manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Adicionando um MapFragment a uma atividade

A captura de tela a `MapFragment`seguir é um exemplo de um simples :

[![Captura de tela de um dispositivo exibindo um fragmento do Google Map](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Semelhante a outras classes fragmentadas, `MapFragment` existem duas maneiras de adicionar a a a uma atividade:

- **Declarativamente** - `MapFragment` O pode ser adicionado através do arquivo de layout XML para a Atividade. O seguinte trecho xml mostra um exemplo `fragment` de como usar o elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **Programática** - `MapFragment` O pode ser programaticamente instanciado usando o [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) método e, em seguida, adicionado a uma Atividade. Este trecho mostra a maneira mais simples `MapFragment` de instanciar um objeto e adicionar a uma atividade:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    É possível configurar o `MapFragment` objeto passando [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) um `NewInstance`objeto para . Isso é discutido na seção propriedades do [GoogleMap](#googlemap_object) que aparece mais tarde neste guia.

O `MapFragment.GetMapAsync` método é usado [`GoogleMap`](#googlemap_object) para inicializar o que é hospedado pelo fragmento e obter `MapFragment`uma referência ao objeto de mapa que está hospedado pelo . Este método pega um objeto `IOnMapReadyCallback` que implementa a interface.

Esta interface tem um `IMapReadyCallback.OnMapReady(MapFragment map)` único método, que será invocado quando for `GoogleMap` possível para o aplicativo interagir com o objeto. O seguinte trecho de código mostra como uma `MapFragment` atividade `IOnMapReadyCallback` do Android pode inicializar a e implementar a interface:

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

### <a name="map-types"></a>Tipos de mapas

Existem cinco tipos diferentes de mapas disponíveis na API do Google Maps:

- **Normal** - Este é o tipo de mapa padrão. Mostra estradas e importantes características naturais, juntamente com alguns pontos artificiais de interesse (como edifícios e pontes).

- **Satélite** - Este mapa mostra a fotografia de satélite.

- **Híbrido** - Este mapa mostra fotografia de satélite e mapas rodoviários.

- **Terreno** - Isso mostra principalmente características topográficas com algumas estradas.

- **Nenhum** - Este mapa não carrega nenhuma telhas, é renderizado como uma grade vazia.

A imagem abaixo mostra três dos diferentes tipos de mapas, da esquerda para a direita (normal, híbrido, terreno):

[![Três capturas de tela de exemplo de mapa: Normal, Híbrido e Terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

A `GoogleMap.MapType` propriedade é usada para definir ou alterar qual tipo de mapa é exibido. O trecho de código a seguir mostra como exibir um mapa de satélite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="googlemap-properties"></a><a name="googlemap_object" />Propriedades do GoogleMap

`GoogleMap`define várias propriedades que podem controlar a funcionalidade e a aparência do mapa. Uma maneira de configurar o `GoogleMap` estado inicial de a é passar `MapFragment`um objeto [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) ao criar um . O seguinte trecho de código é `GoogleMapOptions` um exemplo `MapFragment`de usar um objeto ao criar um:

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

A outra maneira de `GoogleMap` configurar um é manipulando propriedades nas [Configurações uidodo](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) do objeto do mapa. A próxima amostra de código `GoogleMap` mostra como configurar um para exibir os controles de zoom e uma bússola:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interagindo com o GoogleMap

A API do Android Maps fornece APIs que permitem que uma Atividade altere o ponto de vista, adicione marcadores, coloque sobreposições personalizadas ou desenhe formas geométricas. Esta seção discutirá como realizar algumas dessas tarefas no Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Mudando o ponto de vista

Mapas são modelados como um plano plano na tela, baseado na projeção do Mercator. A visão do mapa é a de uma *câmera* olhando diretamente para este avião. A posição da câmera pode ser controlada alterando a localização, zoom, inclinação e rolamento. A classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) é usada para mover a localização da câmera. `CameraUpdate`objetos não são diretamente instanciados, em vez disso, a API do Maps fornece a classe [CameraUpdateFactory.](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)

Uma `CameraUpdate` vez criado um objeto, ele é passado como um parâmetro para os métodos [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) ou [GoogleMap.AnimateCamera.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) O `MoveCamera` método atualiza o mapa `AnimateCamera` instantaneamente enquanto o método fornece uma transição suave e animada.

Este trecho de código é um exemplo `CameraUpdateFactory` simples de `CameraUpdate` como usar o para criar um que irá incrementar o nível de zoom do mapa pelo nível de um zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

A API do Maps fornece uma [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) que agregará todos os valores possíveis para a posição da câmera. Uma instância desta classe pode ser fornecida ao método [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) que retornará um `CameraUpdate` objeto. A API do Maps também inclui a classe [CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) que fornece uma API fluente para a criação de `CameraPosition` objetos.
O trecho de código a seguir `CameraUpdate` mostra `CameraPosition` um exemplo de criar a `GoogleMap`a e usá-lo para alterar a posição da câmera em um :

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

No trecho de código anterior, um local específico no mapa é representado pela classe [LatLng.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) O nível de zoom é definido para 18, que é uma medida arbitrária de zoom usada pelo Google Maps. O rolamento é a medição da bússola no sentido horário do Norte. A propriedade Tilt controla o ângulo de visão e especifica um ângulo de 25 graus da vertical. A captura de `GoogleMap` tela a seguir mostra o após a execução do código anterior:

[![Exemplo Google Map mostrando um local especificado com um ângulo de visão inclinado](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Desenho no Mapa

A API do Android Maps fornece API's para desenhar os seguintes itens em um mapa:

- **Marcadores** - Estes são ícones especiais que são usados para identificar um único local em um mapa.

- **Sobreposições** - Esta é uma imagem que pode ser usada para identificar uma coleção de locais ou área no mapa.

- **Linhas, Polígonos e Círculos** - São APIs que permitem que as atividades adicionem formas a um mapa.

#### <a name="markers"></a>Marcadores

A API do Maps fornece uma classe [Marker](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) que encapsula todos os dados sobre um único local em um mapa. Por padrão, a classe Marker usa um ícone padrão fornecido pelo Google Maps. É possível personalizar a aparência de um marcador e responder aos cliques do usuário.

##### <a name="adding-a-marker"></a>Adicionando um marcador

Para adicionar um marcador a um mapa, é necessário criar um novo objeto `GoogleMap` [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) e, em seguida, chamar o método [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) em uma instância. Este método retornará um objeto [Marker.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

O título do marcador será exibido em uma *janela de informações* quando o usuário tocar no marcador. A captura de tela a seguir mostra como este marcador se parece:

[![Exemplo Google Map com um marcador e uma janela de informações para Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personalizando um marcador

É possível personalizar o ícone usado pelo `MarkerOptions.InvokeIcon` marcador chamando o método ao adicionar o marcador ao mapa.
Este método leva um objeto [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) contendo os dados necessários para renderizar o ícone. A classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fornece alguns métodos auxiliares para simplificar a criação de um `BitmapDescriptor`. A lista a seguir introduz alguns desses métodos:

- `DefaultMarker(float colour)`&ndash; Use o marcador padrão do Google Maps, mas altere a cor.

- `FromAsset(string assetName)`&ndash; Use um ícone personalizado do arquivo especificado na pasta Ativos.

- `FromBitmap(Bitmap image)`&ndash; Use o bitmap especificado como ícone.

- `FromFile(string fileName)`&ndash; Crie o ícone personalizado do arquivo no caminho especificado.

- `FromResource(int resourceId)`&ndash; Crie um ícone personalizado a partir do recurso especificado.

O seguinte trecho de código mostra um exemplo de criação de um marcador padrão colorido de ciano:

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

*As janelas de informações* são janelas especiais que surgem para exibir informações ao usuário quando eles tocam em um marcador específico. Por padrão, a janela de informações exibirá o conteúdo do título do marcador. Se o título não tiver sido atribuído, nenhuma janela de informações aparecerá. Apenas uma janela de informações pode ser mostrada de cada vez.

É possível personalizar a janela de informações implementando a interface [GoogleMap.IInfoWindowAdapter.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) Existem dois métodos importantes nesta interface:

- `public View GetInfoWindow(Marker marker)`&ndash; Este método é chamado para obter uma janela de informações personalizada para um marcador. Se ele `null` retornar, então a renderização da janela padrão será usada. Se este método retornar uma exibição, então essa exibição será colocada dentro do quadro da janela de informações.

- `public View GetInfoContents(Marker marker)`&ndash; Este método só será chamado se `null` o GetInfoWindow retornar . Este método pode `null` retornar um valor se a renderização padrão do conteúdo da janela de informações for usada. Caso contrário, este método deve retornar uma exibição com o conteúdo da janela de informações.

Uma janela de informações não é uma exibição ao vivo - em vez disso, o Android converterá a exibição em um bitmap estático e exibirá isso na imagem. Isso significa que uma janela de informações não pode responder a nenhum evento de toque ou gestos, nem se atualizará automaticamente. Para atualizar uma janela de informações, é necessário chamar o método [GoogleMap.ShowInfoWindow.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())

A imagem a seguir mostra alguns exemplos de algumas janelas de informações personalizadas. A imagem à esquerda tem seu conteúdo personalizado, enquanto a imagem à direita tem sua janela e conteúdo personalizados com cantos arredondados:

![Exemplo de janelas de marcação para Melbourne, incluindo ícone e população. A janela direita tem cantos arredondados.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

Ao contrário dos marcadores, que identificam um local específico em um mapa, um [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) é uma imagem que é usada para identificar uma coleção de locais ou uma área no mapa.

##### <a name="adding-a-groundoverlay"></a>Adicionando um GroundOverlay

Adicionar uma sobreposição de solo a um mapa é semelhante à adição de um marcador a um mapa. Primeiro, um objeto [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) é criado. Esse objeto é então passado como [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) um parâmetro para `GroundOverlay` o método, que retornará um objeto. Este trecho de código é um exemplo de adicionar uma sobreposição de solo a um mapa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

A captura de tela a seguir mostra essa sobreposição em um mapa:

[![Mapa de exemplo com uma imagem sobrevassamente de um urso polar](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Linhas, Círculos e Polígonos

Existem três tipos simples de figuras geométricas que podem ser adicionados a um mapa:

- **Polilina** - Esta é uma série de segmentos de linha conectada. Ele pode marcar um caminho em um mapa ou criar uma forma geométrica.

- **Círculo** - Isso vai desenhar um círculo no mapa.

- **Polígono** - Esta é uma forma fechada para marcar áreas em um mapa.

##### <a name="polylines"></a>Polilinhas

A [Polyline](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) é uma `LatLng` lista de objetos consecutivos que especificam os vértices de cada segmento de linha. Uma polilina é criada primeiro `PolylineOptions` criando um objeto e adicionando os pontos a ele. O `PolylineOption` objeto é então `GoogleMap` passado para `AddPolyline` um objeto chamando o método.

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

Os círculos são criados primeiro instanciando um objeto [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) que especificará o centro e o raio do círculo em metros. O círculo é desenhado no mapa ligando para [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
O seguinte trecho de código mostra como desenhar um círculo:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Polígonos

`Polygon`s são `Polyline`semelhantes a s, no entanto eles não estão abertos terminou. `Polygon`s são um loop fechado e ter seu interior preenchido.
`Polygon`s são criados exatamente da `Polyline`mesma maneira que a , exceto o método [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) invocado.

Ao `Polyline`contrário `Polygon` de um , um é auto-fechamento. O polígono será fechado `AddPolygon` pelo método desenhando uma linha que conecta o primeiro e o último pontos. O trecho de código a seguir criará um retângulo sólido sobre a `Polyline` mesma área que o trecho de código anterior no exemplo.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```

## <a name="responding-to-user-events"></a>Respondendo a eventos de usuários

Existem três tipos de interações que um usuário pode ter com um mapa:

- **Click Marker** - O usuário clica em um marcador.

- **Marker Drag** - O usuário clicou há muito tempo em um mparger

- **Janela de informações Clique** - O usuário clicou em uma janela de informações.

Cada um desses eventos será discutido com mais detalhes abaixo.

### <a name="marker-click-events"></a>Eventos de clique marcador

O `MarkerClicked` evento é levantado quando o usuário toca em um marcador. Este evento aceita `GoogleMap.MarkerClickEventArgs` um objeto como parâmetro. Esta classe contém duas propriedades:

- `GoogleMap.MarkerClickEventArgs.Handled`&ndash; Esta propriedade deve `true` ser definida para indicar que o manipulador de eventos consumiu o evento. Se isso for `false` definido para então, o comportamento padrão ocorrerá, além do comportamento personalizado do manipulador de eventos.

- `Marker`&ndash; Esta propriedade é uma referência ao `MarkerClick` marcador que levantou o evento.

Este trecho de código mostra `MarkerClick` um exemplo de um que mudará a posição da câmera para um novo local no mapa:

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

### <a name="marker-drag-events"></a>Eventos de arrasto de marcadores

Este evento é levantado quando o usuário deseja arrastar o marcador. Por padrão, os marcadores não são arrastadores. Um marcador pode ser definido como `Marker.Draggable` arrastado `true` definindo a `MarkerOptions.Draggable` propriedade `true` para ou invocando o método com como parâmetro.

Para arrastar o marcador, o usuário deve primeiro clicar no marcador e, em seguida, seu dedo deve permanecer no mapa. Quando o dedo do usuário é arrastado pela tela, o marcador se moverá. Quando o dedo do usuário levantar da tela, o marcador permanecerá no lugar.

A lista a seguir descreve os vários eventos que serão levantados para um marcador arrastavel:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash; Este evento é levantado quando o usuário arrasta o marcador pela primeira vez.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`&ndash; Este evento é levantado como o marcador está sendo arrastado.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash; Este evento é levantado quando o usuário termina de arrastar o marcador.

Cada um `EventArgs` deles contém `P0` uma única propriedade `Marker` chamada que é uma referência ao objeto que está sendo arrastado.

### <a name="info-window-click-events"></a>Eventos do Click da Janela de Informações

Apenas uma janela de informações pode ser exibida de cada vez. Quando o usuário clica em uma janela de informações `InfoWindowClick` em um mapa, o objeto do mapa levantará um evento. O seguinte trecho de código mostra como conectar um manipulador ao evento:

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

Lembre-se que uma `View` janela de informações é uma estática que é renderizada como uma imagem no mapa. Quaisquer widgets, como botões, caixas de seleção ou visualizações de texto que forem colocados dentro da janela de informações, ficarão inertes e não poderão responder a nenhum de seus eventos integrais do usuário.

## <a name="related-links"></a>Links relacionados

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Google Maps Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtenção de uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [usa-biblioteca](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [usa-recurso](https://developer.android.com/guide/topics/manifest/uses-feature-element)
