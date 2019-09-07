---
title: Resumo do capítulo 28. Localização e mapas
description: 'Criando aplicativos móveis com Xamarin. Forms: Resumo do capítulo 28. Localização e mapas'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 8c0b60a5064bbcf605f66a7b9dcce5da1f84a015
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770895"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Resumo do capítulo 28. Localização e mapas

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Xamarin. Forms dá suporte a um [ `Map` ](xref:Xamarin.Forms.Maps.Map) elemento derivado de `View`. Por causa dos requisitos de plataforma especial envolvidos no uso de mapas, eles são implementados em um assembly separado, **Xamarin.Forms.Maps**e envolvem um namespace diferente: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>O sistema de coordenadas geográficas

Um sistema de coordenadas geográficas identifica posições em um objeto esférico (ou quase esférica), como a Terra. Uma coordenada consiste em dois uma *latitude* e *longitude* expressa em ângulos.

Um círculo grande chamado o `equator` esteja entre os dois polos por meio do qual o eixo da Terra estende conceitualmente.

### <a name="parallels-and-latitude"></a>Parallels e latitude

Um ângulo medido Norte ou com o Equador no centro das marcas de terra da linhas de latitude igual, conhecido como *parallels*. Esses variam de 0 graus no Equador a 90 graus no Centro-Norte e Sul polos. Por convenção, as latitudes ao norte do Equador são valores positivos e aqueles com Equador são valores negativos.

### <a name="longitude-and-meridians"></a>A longitude e meridianos

Metades do círculos de excelente do polo Norte ao polo Sul são linhas de longitude igual, também conhecido como *meridianos*. Eles são relativos do Meridiano de Greenwich, Inglaterra. Por convenção, as longitudes Leste do Meridiano principal são valores positivos de 0 graus a 180 graus e as longitudes Oeste do Meridiano são valores negativos de 0 graus para &ndash;180 graus.

### <a name="the-equirectangular-projection"></a>A projeção equirretangular

Qualquer mapa simples da Terra apresenta distorções. Se todas as linhas de latitude e longitude são retas, e se as diferenças iguais em ângulos de latitude e longitude corresponderem a mesma distância no mapa, o resultado é um *projeção equirretangular*. Este mapa distorce áreas mais próximo para os polos porque eles são esticados horizontalmente.

### <a name="the-mercator-projection"></a>A projeção Mercator

A popular *projeção Mercator* tenta compensar o alongamento horizontal, também dessas áreas de ampliando verticalmente. Isso resulta em um mapa em que áreas de perto os polos aparecem muito maiores do que realmente estão, mas nenhuma área local está em conformidade com bastante proximidade com a área real.

### <a name="map-services-and-tiles"></a>Serviços de mapa e blocos

Serviços de mapa usam uma variação da projeção mercador chamada `Web Mercator`. Os serviços de mapa fornecem os blocos de bitmap para um cliente com base no local e nível de zoom.

## <a name="getting-the-users-location"></a>Introdução a localização do usuário

O xamarin. Forms `Map` classes não incluem um recurso para obter a localização geográfica de usuário, mas isso geralmente é desejável quando trabalhar com os mapas, portanto, um serviço de dependência deve manipulá-lo.

> [!NOTE]
> Aplicativos xamarin. Forms em vez disso, podem usar o [ `Geolocation` ](~/essentials/geolocation.md) classe incluída no Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>O controlador de local de API

O [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução contém o código para um controlador de local de API. O [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estrutura encapsula uma latitude e longitude. O [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface define dois métodos para iniciar e pausar o rastreador de local e um evento quando um novo local está disponível.

#### <a name="the-ios-location-manager"></a>O Gerenciador de localização do iOS

A implementação do iOS dos `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe que faz uso de iOS [ `CLLocationManager` ](xref:CoreLocation.CLLocationManager).

#### <a name="the-android-location-manager"></a>O Gerenciador local Android

A implementação de Android da `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe que faz uso do Android [ `LocationManager` ](xref:Android.Locations.LocationManager) classe.

#### <a name="the-uwp-geo-locator"></a>O localizador de geográfica UWP

A implementação da plataforma Universal do Windows do `ILocationTracker` é um [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe que faz uso da UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Exibir a localização do telefone

O [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) exemplo usa o controlador de local para exibir o local do telefone, em texto e em um mapa cilíndrica equidistante.

### <a name="the-required-overhead"></a>O necessária sobrecarga

Alguma sobrecarga é necessária para **WhereAmI** usar o controlador de local. Primeiro, todos os projetos na **WhereAmI** solução deve ter referências em projetos correspondentes **Xamarin.FormsBook.Platform**e cada **WhereAmI** projeto deve chamar o `Toolkit.Init` método.

Alguma sobrecarga adicional de específico da plataforma, na forma de permissões de localização, é necessária.

#### <a name="location-permission-for-ios"></a>Permissão de localização para iOS

Para iOS, o **Info. plist** arquivo deve incluir os itens que contêm o texto de uma pergunta perguntando ao usuário para permitir obtendo local do usuário.

#### <a name="location-permissions-for-android"></a>Permissões de localização para Android

Aplicativos Android que obter a localização do usuário devem ter uma permissão de ACCESS_FILE_LOCATION no arquivo androidmanifest. XML.

#### <a name="location-permissions-for-the-uwp"></a>Permissões de localização para a UWP

Um aplicativo da plataforma Universal do Windows deve ter um `location` marcado de funcionalidade do dispositivo no arquivo Package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabalhando com Xamarin.Forms.Maps

Vários requisitos que estão envolvidos no uso de `Map` classe.

### <a name="the-nuget-package"></a>O pacote do NuGet

O **Xamarin.Forms.Maps** NuGet biblioteca deve ser adicionada à solução do aplicativo. O número de versão deve ser igual a **xamarin. Forms** pacote atualmente instalado.

### <a name="initializing-the-maps-package"></a>Inicializando o pacote de mapas

Os projetos de aplicativo devem chamar o `Xamarin.FormsMaps.Init` método depois de fazer uma chamada para `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Habilitando os serviços de mapa

Porque o `Map` pode obter a localização do usuário, o aplicativo deve obter permissão para o usuário da maneira descrita anteriormente neste capítulo:

#### <a name="enabling-ios-maps"></a>Habilitar iOS mapeia

Um aplicativo iOS usando `Map` precisa de duas linhas no arquivo Info. plist.

#### <a name="enabling-android-maps"></a>Habilitando o Android mapeia

Uma chave de autorização é necessária para usar os serviços do Google Maps. Essa chave é inserida na **androidmanifest. XML** arquivo. Além disso, o **androidmanifest. XML** requer o arquivo `manifest` marcas envolvidas na obtenção do local do usuário.

#### <a name="enabling-uwp-maps"></a>Habilitar o UWP é mapeado

Um aplicativo de plataforma Universal do Windows requer uma chave de autorização para usar o Bing Maps. Essa chave é passada como um argumento para o `Xamarin.FormsMaps.Init` método. O aplicativo também deve ser habilitado para serviços de localização.

### <a name="the-unadorned-map"></a>O mapa não adornado

O [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) exemplo consiste em um [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) arquivo e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) arquivo code-behind que permite navegar para vários programas de demonstração.

O [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) arquivo mostra como exibir o [ `Map` ](xref:Xamarin.Forms.Maps.Map) exibição. Por padrão, ele exibe a cidade de Roma, mas o mapa pode ser manipulado pelo usuário.

Para desativar a rolagem horizontal e vertical, defina as [ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propriedade `false`. Para desabilitar a aplicação de zoom, defina [ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) para `false`. Essas propriedades podem não funcionar em todas as plataformas.

### <a name="streets-and-terrain"></a>Ruas e terreno

Você pode exibir diferentes tipos de mapas, definindo o `Map` propriedade [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) do tipo [ `MapType` ](xref:Xamarin.Forms.Maps.MapType), uma enumeração com três membros:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), o padrão
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

O [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) arquivo mostra como usar um botão de opção para selecionar o tipo de mapa. Ele faz uso do [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca e uma classe com base no [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) arquivo.

### <a name="map-coordinates"></a>Coordenadas do mapa

Um programa pode obter área atual que o `Map` está exibindo por meio de [ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriedade. Esta propriedade é *não* apoiada por uma propriedade vinculável, e não há nenhum mecanismo de notificação para indicar quando ele foi alterado, portanto, um programa que quiser monitorar a propriedade provavelmente deve usar um timer para essa finalidade.

`VisibleRegion` é do tipo [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan), uma classe com quatro propriedades somente leitura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) do tipo [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) do tipo `double`, que indica a altura da área do mapa exibida
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) do tipo `double`, que indica a largura da área do mapa exibida
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) do tipo [ `Distance` ](xref:Xamarin.Forms.Maps.Distance), que indica o tamanho da maior área circular visível no mapa

`Position` e `Distance` são as duas estruturas. `Position` define duas propriedades somente leitura definidas por meio de [ `Position` construtor](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` destina-se a fornecer uma unidade independente distância com a conversão entre unidades inglesas e métrica. Um `Distance` valor pode ser criado de várias maneiras:

- [`Distance` construtor](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) com uma distância em metros
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) método estático
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) método estático
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) método estático

O valor está disponível em três propriedades:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) do tipo `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) do tipo `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) do tipo `double`

O [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) arquivo contém vários `Label` elementos para exibir o `MapSpan` informações. O [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) arquivo code-behind usa um temporizador para manter as informações atualizadas à medida que o usuário manipula o mapa.

### <a name="position-extensions"></a>Extensões de posição

Uma nova biblioteca para este catálogo denominado [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contém tipos específicos do mapa, mas independente de plataforma. O [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe tem um `ToString` método para `Position`e um método para calcular a distância entre dois `Position` valores.

### <a name="setting-an-initial-location"></a>Configurar um local inicial

Você pode chamar o [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) método `Map` para definir programaticamente um local e nível de zoom do mapa. O argumento é do tipo `MapSpan`. Você pode criar um `MapSpan` objeto usando um destes procedimentos:

- [`MapSpan` construtor](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) com um `Position`e o intervalo de latitude e longitude
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) com um `Position` e radius

Também é possível criar um novo `MapSpan` de uma já existente, usando os métodos [ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) ou [ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

O [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) arquivo e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) arquivo code-behind demonstra como usar o `MoveToRegion` método para exibir o estado do Wyoming.

Como alternativa, você pode usar o [ `Map` construtor](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) com um `MapSpan` objeto para inicializar o local do mapa. O [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) arquivo mostra como fazer isso inteiramente em XAML para exibir a sede do Xamarin em são Francisco.

### <a name="dynamic-zooming"></a>Aumentar o zoom dinâmico

Você pode usar um `Slider` para ampliar dinamicamente um mapa. O [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) arquivo e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) arquivo code-behind mostram como alterar o raio de um mapa com base no `Slider` valor.

O [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) arquivo e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) arquivo code-behind mostram uma abordagem alternativa que funciona melhor em Android, mas nenhuma das abordagens funciona bem com o Windows plataformas.

### <a name="the-phones-location"></a>Localização do telefone

O [ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriedade do `Map` funciona um pouco diferente em cada plataforma como o [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) arquivo demonstra:

- No iOS, um ponto azul indica a localização do telefone, mas você deve navegar manualmente existe
- No Android, um ícone é exibido que, quando enviada por push move o mapa de localização do telefone
- A UWP é semelhante ao iOS, mas, às vezes, automaticamente navega até o local

O **MapDemos** projeto tenta imitar a abordagem Android primeiro definindo um botão de ícone com base nas [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) arquivo e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) arquivo code-behind.

O [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) arquivo e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) arquivo code-behind use esse botão para navegar até a localização do telefone.

### <a name="pins-and-science-museums"></a>PINs e museus ciência

Por fim, o `Map` classe define um [ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins) propriedade do tipo `IList<Pin>`. O [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) classe define quatro propriedades:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) do tipo `string`, uma propriedade necessária
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) do tipo `string`, um endereço legível por humanos opcional
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) do tipo `Position`, que indica onde o pin é exibido no mapa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) do tipo [ `PinType` ](xref:Xamarin.Forms.Maps.PinType), uma enumeração, que não é usada

O **MapDemos** projeto contém o arquivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que lista museus ciência dos Estados Unidos, e [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classes para desserializar dados.

O [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) arquivo e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pins de exibição de arquivo de lógica para esses museus ciência no mapa. Quando o usuário toca um pin, ele exibe o endereço e um site para o Museu.

### <a name="the-distance-between-two-points"></a>A distância entre dois pontos

O [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contém um [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) método com um cálculo simplificado da distância entre dois locais geográficos.

Isso é usado na [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) arquivo e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) arquivo code-behind também exibir a distância para o Museu de local do usuário:

[![Tripla captura de tela da página Local de museus](images/ch28fg28-small.png "distância até um local")](images/ch28fg28-large.png#lightbox "distância até um local")

O programa também demonstra como restringir dinamicamente o número de pins com base na localização do mapa.

## <a name="geocoding-and-back-again"></a>Geocodificação e voltar novamente

O [ **Xamarin.Forms.Maps** ](xref:Xamarin.Forms.Maps) assembly também contém um [ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder) classe com um [ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) método converte um endereço de texto em zero ou mais possíveis posições geográficas e outro método [ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) que converte na outra direção.

O [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) arquivo e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) arquivo code-behind demonstram esse recurso.

## <a name="related-links"></a>Links relacionados

- [Capítulo 28 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Exemplos do capítulo 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mapa do xamarin. Forms](~/xamarin-forms/user-interface/map.md)
