---
title: Resumo do Capítulo 28. Localização e mapas
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 28. Localização e mapas'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697071"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumo do Capítulo 28. Localização e mapas

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Xamarin.Forms suporta [`Map`](xref:Xamarin.Forms.Maps.Map) um elemento que `View`deriva de . Devido aos requisitos especiais da plataforma envolvidos no uso de mapas, eles são implementados em `Xamarin.Forms.Maps`um conjunto separado, **Xamarin.Forms.Maps**, e envolvem um namespace diferente: .

## <a name="the-geographic-coordinate-system"></a>O sistema de coordenadas geográficas

Um sistema de coordenadas geográficas identifica posições em um objeto esférico (ou quase esférico) como a Terra. Uma coordenada consiste em uma *latitude* e *longitude* expressas em ângulos.

Um grande círculo `equator` chamado está no meio do caminho entre os dois pólos através dos quais o eixo da Terra se estende conceitualmente.

### <a name="parallels-and-latitude"></a>Paralelos e latitude

Um ângulo medido ao norte ou ao sul do equador a partir do centro da Terra marca linhas de latitude igual conhecidas como *paralelos.* Estes variam de 0 graus no equador a 90 graus nos pólos norte e sul. Por convenção, latitudes ao norte do Equador são valores positivos, e aqueles ao sul do equador são valores negativos.

### <a name="longitude-and-meridians"></a>Longitude e meridianos

Metades de grandes círculos do pólo norte ao pólo sul são linhas de igual longitude, também conhecidas como *meridianos.* Estes são relativos ao Primeiro Meridiano em Greenwich, Inglaterra. Por convenção, longitudes a leste do Primeiro Meridiano são valores positivos de 0 graus a 180 graus, e longitudes a oeste do Primeiro Meridiano são valores negativos de 0 graus a &ndash;180 graus.

### <a name="the-equirectangular-projection"></a>A projeção equirectangular

Qualquer mapa plano da Terra introduz distorções. Se todas as linhas de latitude e longitude forem retas, e se as diferenças iguais nos ângulos de latitude e longitude corresponderem a distâncias iguais no mapa, o resultado é uma *projeção equiretangular*. Este mapa distorce áreas mais próximas dos pólos porque eles estão horizontalmente esticados.

### <a name="the-mercator-projection"></a>A projeção do Mercator

A *projeção* popular do Mercator tenta compensar o alongamento horizontal, esticando também essas áreas verticalmente. Isso resulta em um mapa onde áreas próximas aos pólos parecem muito maiores do que realmente são, mas qualquer área local está em conformidade com a área real.

### <a name="map-services-and-tiles"></a>Mapear serviços e telhas

Os serviços de mapa utilizam `Web Mercator`uma variação da projeção mercator chamada . Os serviços de mapa fornecem telhas bitmap para um cliente com base na localização e no nível de zoom.

## <a name="getting-the-users-location"></a>Obtendo a localização do usuário

As classes Xamarin.Forms `Map` não incluem uma facilidade para obter a localização geográfica do usuário, mas isso é muitas vezes desejável ao trabalhar com mapas, por isso um serviço de dependência deve lidar com isso.

> [!NOTE]
> Os aplicativos Xamarin.Forms podem, em vez disso, usar a [`Geolocation`](~/essentials/geolocation.md) classe incluída no Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>A API do rastreador de localização

A solução [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contém código para uma API de rastreador de localização. A [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estrutura encapsula uma latitude e longitude. A [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface define dois métodos para iniciar e pausar o rastreador de localização e um evento quando um novo local estiver disponível.

#### <a name="the-ios-location-manager"></a>O gerenciador de localização do iOS

A implementação `ILocationTracker` do [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) iOS é uma classe [`CLLocationManager`](xref:CoreLocation.CLLocationManager)que faz uso do iOS .

#### <a name="the-android-location-manager"></a>O gerenciador de localização do Android

A implementação `ILocationTracker` do [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) Android é uma [`LocationManager`](xref:Android.Locations.LocationManager) classe que faz uso da classe Android.

#### <a name="the-uwp-geo-locator"></a>O geolocalizador UWP

A implementação da `ILocationTracker` Universal [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) Windows Platform é uma [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)classe que faz uso do UWP .

### <a name="display-the-phones-location"></a>Exibir a localização do telefone

A amostra [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) usa o rastreador de localização para exibir a localização do telefone, tanto em texto quanto em um mapa equiretangular.

### <a name="the-required-overhead"></a>A sobrecarga necessária

Algumas despesas gerais são necessárias para **que o WhereAmI** use o rastreador de localização. Primeiro, todos os projetos na solução **WhereAmI** devem ter referências aos projetos correspondentes em **Xamarin.FormsBook.Platform**, e cada projeto **WhereAmI** deve chamar o `Toolkit.Init` método.

Algumas despesas gerais específicas da plataforma, na forma de permissões de localização, são necessárias.

#### <a name="location-permission-for-ios"></a>Permissão de localização para iOS

Para o iOS, o arquivo **info.plist** deve incluir itens que contenham o texto de uma pergunta pedindo ao usuário para permitir a localização desse usuário.

#### <a name="location-permissions-for-android"></a>Permissões de localização para Android

Os aplicativos Android que obtêm a localização do usuário devem ter uma permissão ACCESS_FILE_LOCATION no arquivo AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Permissões de localização para o UWP

Um aplicativo universal da `location` Plataforma Windows deve ter um recurso de dispositivo marcado no arquivo Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabalhando com Xamarin.Forms.Maps

Vários requisitos estão `Map` envolvidos no uso da classe.

### <a name="the-nuget-package"></a>O pacote NuGet

A **biblioteca Xamarin.Forms.Maps** NuGet deve ser adicionada à solução do aplicativo. O número da versão deve ser o mesmo do pacote **Xamarin.Forms** atualmente instalado.

### <a name="initializing-the-maps-package"></a>Inicializando o pacote mapas

Os projetos de `Xamarin.FormsMaps.Init` aplicação devem chamar `Xamarin.Forms.Forms.Init`o método depois de fazer uma chamada para .

### <a name="enabling-map-services"></a>Habilitando serviços de mapa

Como `Map` pode obter a localização do usuário, o aplicativo deve obter permissão para o usuário da maneira descrita anteriormente neste capítulo:

#### <a name="enabling-ios-maps"></a>Ativando mapas iOS

Um aplicativo iOS usando `Map` precisa de duas linhas no arquivo info.plist.

#### <a name="enabling-android-maps"></a>Habilitando mapas Android

Uma chave de autorização é necessária para usar os serviços do Google Map. Esta chave está inserida no arquivo **AndroidManifest.xml.** Além disso, o arquivo **AndroidManifest.xml** requer `manifest` tags envolvidas na obtenção da localização do usuário.

#### <a name="enabling-uwp-maps"></a>Habilitando mapas UWP

Um aplicativo Universal Windows Platform requer uma chave de autorização para usar o Bing Maps. Esta chave é passada como `Xamarin.FormsMaps.Init` um argumento para o método. A aplicação também deve ser habilitada para serviços de localização.

### <a name="the-unadorned-map"></a>O mapa não adornado

A amostra [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) consiste em um arquivo [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) arquivo de código atrás que permite navegar para vários programas de demonstração.

O arquivo [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) mostra [`Map`](xref:Xamarin.Forms.Maps.Map) como exibir a exibição. Por padrão, ele exibe a cidade de Roma, mas o mapa pode ser manipulado pelo usuário.

Para desativar a rolagem horizontal [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) e `false`vertical, defina a propriedade como . Para desativar o zoom, defina-se [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) como `false`. Essas propriedades podem não funcionar em todas as plataformas.

### <a name="streets-and-terrain"></a>Ruas e Terrenos

Você pode exibir diferentes tipos `Map` [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) de [`MapType`](xref:Xamarin.Forms.Maps.MapType)mapas definindo a propriedade do tipo , uma enumeração com três membros:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), o padrão
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

O arquivo [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) mostra como usar um botão de rádio para selecionar o tipo de mapa. Ele faz uso [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) da classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e uma classe baseada no arquivo [MapTypeRadioButton.xaml.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)

### <a name="map-coordinates"></a>Coordenadas do mapa

Um programa pode obter a `Map` área atual [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) que o está exibindo através da propriedade. Esta propriedade *não* é apoiada por uma propriedade vinculável, e não há mecanismo de notificação para indicar quando ela foi alterada, então um programa que deseja monitorar a propriedade provavelmente deve usar um temporizador para esse fim.

`VisibleRegion`é do [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)tipo , uma classe com quatro propriedades somente leitura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)de tipo[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)do `double`tipo, indicando a altura da área exibida do mapa
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)do `double`tipo, indicando a largura da área exibida do mapa
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)do [`Distance`](xref:Xamarin.Forms.Maps.Distance)tipo, indicando o tamanho da maior área circular visível no mapa

`Position`e `Distance` são ambas estruturas. `Position`define duas propriedades somente leitura definidas através do [ `Position` construtor:](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`destina-se a fornecer uma distância independente da unidade, convertendo entre unidades métricas e inglesas. Um `Distance` valor pode ser criado de várias maneiras:

- construtor com uma distância em metros [ `Distance` ](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))método estático
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))método estático
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))método estático

O valor está disponível em três propriedades:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)de tipo`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)de tipo`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)de tipo`double`

O arquivo [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contém vários `Label` `MapSpan` elementos para exibir as informações. O [arquivo MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) de código atrás usa um temporizador para manter as informações atualizadas à medida que o usuário manipula o mapa.

### <a name="position-extensions"></a>Extensões de posição

Uma nova biblioteca para este livro chamada [**Xamarin.FormsBook.Toolkit.Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contém tipos específicos de mapa, mas independentes da plataforma. A [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe `ToString` tem `Position`um método para , e `Position` um método para calcular a distância entre dois valores.

### <a name="setting-an-initial-location"></a>Definindo um local inicial

Você pode [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) chamar `Map` o método de definir programáticamente um local e o nível de zoom no mapa. O argumento é `MapSpan`do tipo. Você pode `MapSpan` criar um objeto usando qualquer um dos seguintes:

- construtor com `Position`um , e latitude e extensão [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance))com `Position` um raio

Também é possível criar um `MapSpan` novo a partir de [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) um [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))existente usando os métodos ou .

O arquivo [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) arquivo de `MoveToRegion` código atrás demonstram como usar o método para exibir o estado de Wyoming.

Você pode, alternativamente, usar `MapSpan` o [ `Map` construtor](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) com um objeto para inicializar a localização do mapa. O arquivo [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) mostra como fazer isso inteiramente em XAML para exibir a sede da Xamarin em São Francisco.

### <a name="dynamic-zooming"></a>Zoom dinâmico

Você pode `Slider` usar um para ampliar dinamicamente um mapa. O arquivo [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) arquivo de código atrás mostram como `Slider` alterar o raio de um mapa com base no valor.

O arquivo [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) arquivo de código atrás mostram uma abordagem alternativa que funciona melhor no Android, mas nenhuma das abordagens funciona bem nas plataformas Windows.

### <a name="the-phones-location"></a>A localização do telefone

A [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriedade `Map` funciona um pouco diferente em cada plataforma, como o arquivo [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) demonstra:

- No iOS, um ponto azul indica a localização do telefone, mas você deve navegar manualmente por lá
- No Android, um ícone é exibido que quando empurrado move o mapa para a localização do telefone
- O UWP é semelhante ao iOS, mas às vezes navega automaticamente para o local

O projeto **MapDemos** tenta imitar a abordagem do Android definindo primeiro um botão baseado em ícones baseado no arquivo [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) arquivo de código atrás.

O arquivo [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) arquivo de código atrás use este botão para navegar até a localização do telefone.

### <a name="pins-and-science-museums"></a>Pinos e museus de ciência

Finalmente, `Map` a classe [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) define uma `IList<Pin>`propriedade do tipo . A [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe define quatro propriedades:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)de `string`tipo, uma propriedade necessária
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)de `string`tipo, um endereço opcional de leitura humana
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)do `Position`tipo, indicando onde o pino é exibido no mapa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)de [`PinType`](xref:Xamarin.Forms.Maps.PinType)tipo, uma enumeração, que não é usada

O projeto **MapDemos** contém o arquivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que lista [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) museus de ciência nos Estados Unidos, e aulas para desserializar esses dados.

O arquivo [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pinos de exibição de arquivos por trás do código para esses museus de ciência no mapa. Quando o usuário toca em um pino, ele exibe o endereço e um site para o museu.

### <a name="the-distance-between-two-points"></a>A distância entre dois pontos

A [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) contém um método com um cálculo simplificado da distância entre dois locais geográficos.

Isso é usado no arquivo [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) arquivo de código para também exibir a distância até o museu da localização do usuário:

[![Captura de tela tripla da Página de Museus Locais](images/ch28fg28-small.png "Distância até um local")](images/ch28fg28-large.png#lightbox "Distância até um local")

O programa também demonstra como restringir dinamicamente o número de pinos com base na localização do mapa.

## <a name="geocoding-and-back-again"></a>Geocodificação e de volta novamente

O [**conjunto Xamarin.Forms.Maps**](xref:Xamarin.Forms.Maps) [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) também contém [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) uma classe com um método que converte um [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) endereço de texto em posições geográficas zero ou mais possíveis, e outro método que se converte na outra direção.

O arquivo [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) arquivo de código atrás demonstram essa facilidade.

## <a name="related-links"></a>Links relacionados

- [Capítulo 28 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capítulo 28 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mapa de Xamarin.Forms](~/xamarin-forms/user-interface/map/index.md)
