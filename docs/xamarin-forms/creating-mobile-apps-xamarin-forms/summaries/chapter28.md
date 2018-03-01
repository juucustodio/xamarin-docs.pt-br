---
title: "Resumo do capítulo 28. Local e mapas"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7361f65fecfed9d61b9df7088f9021ffa0192ad8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumo do capítulo 28. Local e mapas

Xamarin. Forms oferece suporte a um [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) elemento derivado de `View`. Por causa dos requisitos de plataforma especiais envolvidos no uso de mapas, eles são implementados em um assembly separado, **Xamarin.Forms.Maps**e envolvem um namespace diferente: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>O sistema de coordenadas geográfico

Um sistema de coordenadas geográfico identifica posições em um objeto esférico (ou quase Esférico) como a Terra. Uma coordenada consiste em dois um *latitude* e *longitude* expresso em ângulos.

Um círculo ótimo chamado o `equator` esteja entre os dois polos por meio do qual o eixo da Terra estende conceitualmente.

### <a name="parallels-and-latitude"></a>Paralelos e latitude

Um ângulo medido Norte ou Sul, do Equador do Centro de marcas de terra linhas de latitude igual, conhecido como *paralelos*. Esses variam de 0 graus no Equador a 90 graus no Norte e Sul polos. Por convenção, latitudes ao norte Equador são valores positivos e aqueles Sul, do Equador são valores negativos.

### <a name="longitude-and-meridians"></a>Longitude e os meridianos

Metades de círculos ótimo do polo Norte ao polo Sul são linhas de longitude igual, também conhecido como *meridianos*. Esses são relativas do Meridiano de Greenwich, Inglaterra. Por convenção, longitudes Leste do Meridiano são valores positivos de 0 graus a 180 graus e longitudes Oeste do Meridiano valores negativos de 0 graus para & #x 2013; 180 graus.

### <a name="the-equirectangular-projection"></a>A projeção equirretangular

Qualquer mapa simples da Terra apresenta distorções. Se todas as linhas de latitude e longitude são simples e se igual diferenças em ângulos de latitude e longitude corresponderem a mesma distância no mapa, o resultado será um *projeção equirretangular*. Este mapa distorcido áreas mais próximo para os polos porque eles são esticados horizontalmente.

### <a name="the-mercator-projection"></a>A projeção de mercador

O popular *projeção mercador* tentativas compensar o alongamento horizontal alongando também essas áreas verticalmente. Isso resulta em um mapa onde áreas próximo os polos aparecem muito maiores do que realmente são, mas qualquer área local bem de perto compatível com a área real.

### <a name="map-services-and-tiles"></a>Serviços de mapa e lado a lado

Serviços de mapa usam uma variação da projeção mercador chamada `Web Mercator`. Os serviços de mapa fornecem os blocos de bitmap para um cliente com base no local e nível de zoom.

## <a name="getting-the-users-location"></a>Obtendo a localização do usuário

O xamarin. Forms `Map` classes não incluem um recurso para obter a localização geográfica do usuário, mas isso geralmente é desejável quando trabalhando com mapas, portanto, uma dependência de serviço deve tratá-la.

### <a name="the-location-tracker-api"></a>O controlador de local de API

O [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução contém código para um controlador de local de API. O [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estrutura encapsula uma latitude e longitude. O [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface define dois métodos para iniciar e interromper o controlador de local e um evento quando um novo local está disponível.

#### <a name="the-ios-location-manager"></a>O Gerenciador de localização do iOS

A implementação do iOS de `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe que faz uso do iOS [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/).

#### <a name="the-android-location-manager"></a>O Gerenciador local Android

A implementação do Android do `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe que usa o Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) classe.

#### <a name="the-windows-runtime-geo-locator"></a>O localizador de replicação geográfica do tempo de execução do Windows

A implementação de tempo de execução do Windows de `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe que usa o UWP [ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534).

### <a name="display-the-phones-location"></a>Exibir a localização do telefone

O [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) exemplo usa o controlador de local para exibir o local do telefone, tanto em texto em um mapa cilíndrica equidistante.

### <a name="the-required-overhead"></a>O necessária sobrecarga

Alguma sobrecarga é necessária para **WhereAmI** para usar o controlador de local. Primeiro, todos os projetos no **WhereAmI** solução deve ter referências para os projetos correspondentes em **Xamarin.FormsBook.Platform**e cada **WhereAmI** projeto deve chamar o `Toolkit.Init` método.

Alguma sobrecarga adicional de plataforma específica, na forma de permissões de local, é necessária.

#### <a name="location-permission-for-ios"></a>Permissão de local para iOS

Para iOS, o **Info. plist** arquivo deve incluir itens que contém o texto de uma pergunta pedindo que o usuário possa obter o local do usuário.

#### <a name="location-permissions-for-android"></a>Permissões de local para Android

Aplicativos Android que obter a localização do usuário devem ter uma permissão de ACCESS_FILE_LOCATION no arquivo AndroidManifest.xml.

#### <a name="location-permissions-for-the-windows-runtime"></a>Permissões de local para o tempo de execução do Windows

Um aplicativo do Windows ou Windows Phone deve ter um `location` marcado como a funcionalidade do dispositivo em que o arquivo de manifesto.

## <a name="working-with-xamarinformsmaps"></a>Trabalhando com Xamarin.Forms.Maps

Vários requisitos envolvidos usando o `Map` classe.

### <a name="the-nuget-package"></a>O pacote do NuGet

O **Xamarin.Forms.Maps** NuGet biblioteca deve ser adicionada à solução do aplicativo. O número de versão deve ser o mesmo que o **xamarin. Forms** pacote atualmente instalado.

### <a name="initializing-the-maps-package"></a>Inicializando o pacote de mapas

Os projetos de aplicativo devem chamar o `Xamarin.FormsMaps.Init` método depois de fazer uma chamada para `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Habilitar serviços de mapa

Porque o `Map` pode obter o local do usuário, o aplicativo deve obter permissão para o usuário da maneira descrita neste capítulo:

#### <a name="enabling-ios-maps"></a>Habilitar iOS mapeia

Um aplicativo iOS usando `Map` precisa de duas linhas no arquivo. plist.

#### <a name="enabling-android-maps"></a>Habilitar Android mapeia

Uma chave de autorização é necessária para usar serviços de mapa do Google. Essa chave é inserida no **AndroidManifest.xml** arquivo. Além disso, o **AndroidManifest.xml** arquivo requer `manifest` marcas envolvidas na obtenção de local do usuário.

#### <a name="enabling-windows-runtime-maps"></a>Habilitar o tempo de execução do Windows mapeado

Um aplicativo de tempo de execução do Windows requer uma chave de autorização para usar o Bing Maps. Essa chave é passada como um argumento para o `Xamarin.FormsMaps.Init` método. O aplicativo também deve ser habilitado para serviços de localização.

### <a name="the-unadorned-map"></a>O mapa acrescido

O [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) exemplo consiste em uma [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) arquivo e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) arquivo code-behind que permite navegar para vários programas de demonstração.

O [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) arquivo mostra como exibir o [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) exibição. Por padrão, ele exibe a cidade de Roma, mas o mapa pode ser manipulado pelo usuário.

Para desabilitar a rolagem horizontal e vertical, defina o [ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/) propriedade `false`. Para desabilitar a ampliação, defina [ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/) para `false`. Essas propriedades podem não funcionar em todas as plataformas.

### <a name="streets-and-terrain"></a>Ruas e tipos de terrenos

Você pode exibir diferentes tipos de mapas, definindo o `Map` propriedade [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) do tipo [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/), uma enumeração com três membros:

- [`Street`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Street/), o padrão
- [`Satellite`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Satellite/)
- [`Hybrid`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Hybrid/)

O [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) arquivo mostra como usar um botão de opção para selecionar o tipo de mapa. Ele usa o [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca e uma classe com base no [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) arquivo.

### <a name="map-coordinates"></a>Coordenadas do mapa

Um programa pode obter área atual que o `Map` está exibindo por meio de [ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/) propriedade. Esta propriedade é *não* apoiada por uma propriedade ligável, e não há nenhum mecanismo de notificação para indicar quando ele foi alterado, portanto, um programa que deseja monitorar a propriedade provavelmente deve usar um timer para essa finalidade.

`VisibleRegion` é do tipo [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/), uma classe com quatro propriedades somente leitura:

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) do tipo [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) tipo `double`, que indica a altura da área do mapa exibida
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) tipo `double`, indicando a largura da área do mapa exibida
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) tipo [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/), que indica o tamanho do maior área circular visível no mapa

`Position` e `Distance` são ambas as estruturas. `Position` define duas propriedades somente leitura definidas por meio de [ `Position` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` destina-se a fornecer uma distância de unidade independente convertendo entre unidades em inglês e métrica. Um `Distance` valor pode ser criado de várias maneiras:

- [`Distance` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/) com uma distância em metros
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) método estático
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) método estático
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) método estático

O valor está disponível em três propriedades:

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) do tipo `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) do tipo `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) do tipo `double`

O [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) arquivo contém vários `Label` elementos para exibir o `MapSpan` informações. O [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) arquivo code-behind usa um temporizador para manter as informações atualizadas à medida que o usuário manipula o mapa.

### <a name="position-extensions"></a>Extensões de posição

Uma nova biblioteca para este catálogo denominado [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contém tipos específicos de mapa mas independente de plataforma. O [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe tiver um `ToString` método `Position`e um método para calcular a distância entre dois `Position` valores.

### <a name="setting-an-initial-location"></a>Configurando um local inicial

Você pode chamar o [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/) método `Map` para definir um local e nível de zoom do mapa. O argumento é do tipo `MapSpan`. Você pode criar um `MapSpan` objeto usando um dos seguintes:

- [`MapSpan` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/) com um `Position`e o intervalo de latitude e longitude
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) com um `Position` e radius

Também é possível criar um novo `MapSpan` de uma existente, usando os métodos [ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/) ou [ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/).

O [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) arquivo e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) arquivo code-behind demonstra como usar o `MoveToRegion` método para exibir o estado do Wyoming.

Como alternativa, você pode usar o [ `Map` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/) com um `MapSpan` objeto inicializar o local do mapa. O [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) arquivo mostra como fazer isso inteiramente em XAML para exibir a matriz do Xamarin em são Francisco.

### <a name="dynamic-zooming"></a>Zoom dinâmico

Você pode usar um `Slider` para ampliar dinamicamente um mapa. O [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) arquivo e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) arquivo code-behind mostram como alterar o raio de um mapa com base no `Slider` valor.

O [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) arquivo e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) arquivo code-behind mostram uma abordagem alternativa que funciona melhor no Android, mas uma abordagem não funciona bem no Windows plataformas.

### <a name="the-phones-location"></a>Localização do telefone

O [ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/) propriedade `Map` funciona um pouco diferente nas três plataformas como o [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) arquivo demonstra:

- No iOS, um ponto azul indica a localização do telefone, mas você deve navegar manualmente existe
- No Android, é exibido um ícone que, quando enviada por push move o mapa de localização do telefone
- O UWP é semelhante ao iOS, mas às vezes automaticamente navega até o local

O **MapDemos** tentativas de projeto simular a abordagem Android primeiro definindo um botão de ícone com base no [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) arquivo e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) arquivo code-behind.

O [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) arquivo e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) arquivo code-behind use esse botão para navegar até o local do telefone.

### <a name="pins-and-science-museums"></a>PINs e museus de ciências

Por fim, o `Map` classe define um [ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/) propriedade do tipo `IList<Pin>`. O [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) classe define quatro propriedades:

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) tipo `string`, uma propriedade necessária
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) tipo `string`, um endereço legível opcional
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) tipo `Position`, que indica onde o pin é exibido no mapa
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) tipo [ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/), uma enumeração, que não é usada

O **MapDemos** projeto contém o arquivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que lista museus ciência nos Estados Unidos, e [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classes para desserializar dados.

O [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) arquivo e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pins de exibição de arquivo por trás do código para essas museus ciência no mapa. Quando o usuário toca um pin, ele exibe o endereço e um site para o Museu.

### <a name="the-distance-between-two-points"></a>A distância entre dois pontos

O [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contém um [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) método com um cálculo simplificado da distância entre dois locais geográficos.

Isso é usado no [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) arquivo e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) arquivo code-behind para exibir a distância também Museu do local do usuário:

[![Tripla captura de tela da página museus Local](images/ch28fg28-small.png "distância até um local")](images/ch28fg28-large.png "distância para um local")

O programa também demonstra como restringir dinamicamente o número de pins com base no local do mapa.

## <a name="geocoding-and-back-again"></a>Geocodificação e voltar novamente

O [ **Xamarin.Forms.Maps** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) conjunto também contém um [ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/) classe com um [ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/) método converte um endereço de texto em zero ou mais posições geográficas e outro método [ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/) que converte na outra direção.

O [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) arquivo e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) arquivo code-behind demonstram esse recurso.



## <a name="related-links"></a>Links relacionados

- [28 de capítulo de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Exemplos de capítulo 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Controle de mapa](~/xamarin-forms/user-interface/map.md)
