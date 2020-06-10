---
Título: "Resumo do capítulo 28. Local e mapas "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 28. Local e mapeia "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: F6E20077-687C-45C4-A375-31D4F49BBFA4 autor: davidbritch MS. Author: dabritch MS. Date: 07/19/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-28-location-and-maps"></a>Resumo do capítulo 28. Localização e mapas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Xamarin.Formsdá suporte a um [`Map`](xref:Xamarin.Forms.Maps.Map) elemento derivado de `View` . Devido aos requisitos de plataforma especiais envolvidos no uso de mapas, eles são implementados em um assembly separado, ** Xamarin.Forms . Mapeia**e envolve um namespace diferente: `Xamarin.Forms.Maps` .

## <a name="the-geographic-coordinate-system"></a>O sistema de coordenadas geográficas

Um sistema de coordenadas geográficas identifica posições em um objeto esférico (ou quase esférico) como a terra. Uma coordenada consiste em uma *latitude* e *longitude* expressas em ângulos.

Um bom círculo chamado `equator` é o Midway entre os dois polos por meio dos quais o eixo da terra se estende conceitualmente.

### <a name="parallels-and-latitude"></a>Paralelos e latitude

Um ângulo medido norte ou sul do Equador a partir do centro da terra marca as linhas de latitude igual, conhecidas como *paralelos*. Elas variam de 0 graus no Equador a 90 graus no norte e no polos do Sul. Por convenção, o norte da latitude do Equador são valores positivos e aqueles sul do Equador são valores negativos.

### <a name="longitude-and-meridians"></a>Longitude e meridianos

As metades dos grandes círculos do pólo norte para o Pólo Sul são linhas de longitude igual, também conhecidas como *meridianos*. Eles são relativos ao meridiano principal da Greenwich, Inglaterra. Por convenção, o leste da longitude do meridiano principal são valores positivos de 0 graus a 180 graus e as longitudes oeste do meridiano principal são valores negativos de 0 graus a &ndash; 180 graus.

### <a name="the-equirectangular-projection"></a>A projeção cilíndrica equidistante

Qualquer mapa simples da Terra apresenta distorções. Se todas as linhas de latitude e longitude forem retas e se diferenças iguais nos ângulos de latitude e longitude corresponderem a distâncias iguais no mapa, o resultado será uma *Projeção cilíndrica equidistante*. Esse mapa distorce áreas próximas à polos porque elas são alongadas horizontalmente.

### <a name="the-mercator-projection"></a>A projeção Mercator

A *projeção* popular de Mercator tenta compensar o alongamento horizontal, ampliando também essas áreas verticalmente. Isso resulta em um mapa em que as áreas próximas ao polos parecem muito maiores do que realmente são, mas qualquer área local está em conformidade com a área real.

### <a name="map-services-and-tiles"></a>Mapear serviços e blocos

Os serviços de mapa usam uma variação da projeção Mercator chamada `Web Mercator` . Os serviços de mapa entregam blocos de bitmap para um cliente com base no local e no nível de zoom.

## <a name="getting-the-users-location"></a>Obtendo o local do usuário

As Xamarin.Forms `Map` classes não incluem um recurso para obter a localização geográfica do usuário, mas isso geralmente é desejável ao trabalhar com mapas, de modo que um serviço de dependência deve tratá-lo.

> [!NOTE]
> Xamarin.Formsem vez disso, os aplicativos podem usar a [`Geolocation`](~/essentials/geolocation.md) classe incluída no Xamarin.Essentials .

### <a name="the-location-tracker-api"></a>A API do controlador de localização

A solução [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contém o código para uma API de controlador de local. A [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) estrutura encapsula uma latitude e longitude. A [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface define dois métodos para iniciar e pausar o controlador de local e um evento quando um novo local está disponível.

#### <a name="the-ios-location-manager"></a>O Gerenciador de local do iOS

A implementação do iOS do `ILocationTracker` é uma [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe que utiliza o Ios [`CLLocationManager`](xref:CoreLocation.CLLocationManager) .

#### <a name="the-android-location-manager"></a>O Gerenciador de local do Android

A implementação do Android do `ILocationTracker` é uma [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe que usa a classe do Android [`LocationManager`](xref:Android.Locations.LocationManager) .

#### <a name="the-uwp-geo-locator"></a>O localizador de localização geográfica UWP

A implementação Plataforma Universal do Windows do `ILocationTracker` é uma [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe que usa o UWP [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator) .

### <a name="display-the-phones-location"></a>Exibir o local do telefone

O exemplo [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) usa o controlador de local para exibir o local do telefone, em texto e em um mapa do cilíndrica equidistante.

### <a name="the-required-overhead"></a>A sobrecarga necessária

Uma sobrecarga é necessária para **WhereAmI** usar o controlador de localização. Primeiro, todos os projetos na solução **WhereAmI** devem ter referências aos projetos correspondentes no **Xamarin. FormsBook. Platform**e cada projeto **WhereAmI** deve chamar o `Toolkit.Init` método.

Uma sobrecarga adicional específica da plataforma, na forma de permissões de local, é necessária.

#### <a name="location-permission-for-ios"></a>Permissão de local para iOS

Para o iOS, o arquivo **info. plist** deve incluir itens que contenham o texto de uma pergunta solicitando que o usuário permita obter o local desse usuário.

#### <a name="location-permissions-for-android"></a>Permissões de local para Android

Os aplicativos Android que obtêm o local do usuário devem ter uma permissão ACCESS_FILE_LOCATION no arquivo AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Permissões de local para o UWP

Um aplicativo Plataforma Universal do Windows deve ter uma `location` funcionalidade de dispositivo marcada no arquivo Package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Trabalhando com Xamarin.Forms . Los

Vários requisitos estão envolvidos no uso da `Map` classe.

### <a name="the-nuget-package"></a>O pacote NuGet

O ** Xamarin.Forms . **A biblioteca NuGet de mapas deve ser adicionada à solução de aplicativo. O número de versão deve ser igual ao **Xamarin.Forms** pacote atualmente instalado.

### <a name="initializing-the-maps-package"></a>Inicializando o pacote de mapas

Os projetos de aplicativo devem chamar o `Xamarin.FormsMaps.Init` método depois de fazer uma chamada para `Xamarin.Forms.Forms.Init` .

### <a name="enabling-map-services"></a>Habilitando serviços de mapa

Como o `Map` pode obter o local do usuário, o aplicativo deve obter permissão para o usuário da maneira descrita anteriormente neste capítulo:

#### <a name="enabling-ios-maps"></a>Habilitando mapas do iOS

Um aplicativo iOS usando `Map` precisa de duas linhas no arquivo info. plist.

#### <a name="enabling-android-maps"></a>Habilitando mapas do Android

Uma chave de autorização é necessária para usar os serviços do Google Map. Essa chave é inserida no arquivo **AndroidManifest.xml** . Além disso, o arquivo **AndroidManifest.xml** requer `manifest` marcas envolvidas na obtenção do local do usuário.

#### <a name="enabling-uwp-maps"></a>Habilitando mapas UWP

Um aplicativo Plataforma Universal do Windows requer uma chave de autorização para usar o Bing Maps. Essa chave é passada como um argumento para o `Xamarin.FormsMaps.Init` método. O aplicativo também deve ser habilitado para serviços de localização.

### <a name="the-unadorned-map"></a>O mapa não adornado

O exemplo [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) consiste em um arquivo [MapsDemoHomePage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) e um arquivo de código-behind [MapsDemoHomePage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) que permite navegar para vários programas de demonstração.

O arquivo [BasicMapPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) mostra como exibir o [`Map`](xref:Xamarin.Forms.Maps.Map) modo de exibição. Por padrão, ele exibe a cidade de Roma, mas o mapa pode ser manipulado pelo usuário.

Para desabilitar a rolagem horizontal e vertical, defina a [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propriedade como `false` . Para desabilitar o zoom, defina [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) como `false` . Essas propriedades podem não funcionar em todas as plataformas.

### <a name="streets-and-terrain"></a>Ruas e terrenos

Você pode exibir diferentes tipos de mapas definindo a `Map` propriedade [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) do tipo [`MapType`](xref:Xamarin.Forms.Maps.MapType) , uma enumeração com três membros:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), o padrão
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

O arquivo [MapTypesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) mostra como usar um botão de opção para selecionar o tipo de mapa. Ele faz uso da [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e uma classe baseada no arquivo [MapTypeRadioButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) .

### <a name="map-coordinates"></a>Coordenadas do mapa

Um programa pode obter a área atual que o `Map` está exibindo por meio da [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriedade. Essa propriedade *não* é apoiada por uma propriedade vinculável e não há nenhum mecanismo de notificação para indicar quando ela foi alterada, de modo que um programa que deseja monitorar a propriedade provavelmente deve usar um temporizador para essa finalidade.

`VisibleRegion`é do tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , uma classe com quatro propriedades somente leitura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)do tipo[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)do tipo `double` , indicando a altura da área exibida do mapa
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)do tipo `double` , indicando a largura da área exibida do mapa
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)do tipo [`Distance`](xref:Xamarin.Forms.Maps.Distance) , indicando o tamanho da maior área circular visível no mapa

`Position`e `Distance` são ambas estruturas. `Position`define duas propriedades somente leitura definidas por meio do [ `Position` Construtor](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`o destina-se a fornecer uma distância independente de unidade convertendo entre unidades métricas e em inglês. Um `Distance` valor pode ser criado de várias maneiras:

- [ `Distance` Construtor](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) com uma distância em metros
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))método estático
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))método estático
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))método estático

O valor está disponível em três propriedades:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)do tipo`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)do tipo`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)do tipo`double`

O arquivo [MapCoordinatesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contém vários `Label` elementos para exibir as `MapSpan` informações. O arquivo code-behind [MapCoordinatesPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) usa um temporizador para manter as informações atualizadas à medida que o usuário manipula o mapa.

### <a name="position-extensions"></a>Extensões de posição

Uma nova biblioteca para este livro chamada [**Xamarin. FormsBook. Toolkit. Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contém tipos específicos de mapa, mas independentes de plataforma. A [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe tem um `ToString` método para `Position` , e um método para calcular a distância entre dois `Position` valores.

### <a name="setting-an-initial-location"></a>Configurando um local inicial

Você pode chamar o [ `MoveToRegion` ] (xref: Xamarin.Forms . Maps. map. MoveToRegion ( Xamarin.Forms . Maps. MapSpan)) de `Map` para definir programaticamente um local e nível de zoom no mapa. O argumento é do tipo `MapSpan` . Você pode criar um `MapSpan` objeto usando um dos seguintes:

- [ `MapSpan` Construtor] (xref: Xamarin.Forms . Maps. MapSpan .% 23ctor ( Xamarin.Forms . Maps. Position, System. Double, System. Double)) com um `Position` e latitude e longitude span
- [ `MapSpan.FromCenterAndRadius` ] (xref: Xamarin.Forms . Maps. MapSpan. FromCenterAndRadius ( Xamarin.Forms . Maps. Position, Xamarin.Forms . Mapas. distância)) com um `Position` e RADIUS

Também é possível criar um novo a `MapSpan` partir de um existente usando os métodos [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) ou [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)) .

O arquivo [WyomingPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) e o arquivo code-behind [WyomingPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) demonstra como usar o `MoveToRegion` método para exibir o estado de Wyoming.

Como alternativa, você pode usar o [ `Map` constructor] (xref: Xamarin.Forms . Maps. map .% 23ctor ( Xamarin.Forms . Maps. MapSpan)) com um `MapSpan` objeto para inicializar o local do mapa. O arquivo [XamarinHQPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) mostra como fazer isso inteiramente em XAML para exibir a sede da Xamarin em San Francisco.

### <a name="dynamic-zooming"></a>Zoom dinâmico

Você pode usar um `Slider` para aplicar um zoom de forma dinâmica a um mapa. O arquivo [RadiusZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) e o arquivo code-behind [RadiusZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) mostram como alterar o raio de um mapa com base no `Slider` valor.

O arquivo [LongitudeZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) e o arquivo code-behind [LongitudeZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) mostram uma abordagem alternativa que funciona melhor no Android, mas nenhuma abordagem funciona bem nas plataformas do Windows.

### <a name="the-phones-location"></a>O local do telefone

A [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriedade de `Map` funciona um pouco diferente em cada plataforma, uma vez que o arquivo [ShowLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) demonstra:

- No iOS, um ponto azul indica o local do telefone, mas você deve navegar manualmente
- No Android, é exibido um ícone que, quando enviado por push, move o mapa para o local do telefone
- O UWP é semelhante ao iOS, mas às vezes navega automaticamente para o local

O projeto **MapDemos** tenta imitar a abordagem do Android definindo primeiro um botão baseado em ícone com base no arquivo [MyLocationButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) e no arquivo code-behind [MyLocationButton.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) .

O arquivo [GoToLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) e o arquivo code-behind [GoToLocationPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) usam esse botão para navegar até o local do telefone.

### <a name="pins-and-science-museums"></a>Museus e ciência

Por fim, a `Map` classe define uma [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) Propriedade do tipo `IList<Pin>` . A [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe define quatro propriedades:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)do tipo `string` , uma propriedade necessária
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)do tipo `string` , um endereço opcional legível por humanos
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)do tipo `Position` , indicando onde o PIN é exibido no mapa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)do tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType) , uma enumeração, que não é usada

O projeto **MapDemos** contém o arquivo [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), que lista os museus de ciência na Estados Unidos e [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) as [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classes para desserializar esses dados.

O arquivo [ScienceMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) e os pinos de exibição de arquivo [ScienceMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) code-behind para esses museuss de ciência no mapa. Quando o usuário toca em um PIN, ele exibe o endereço e um site para o Museu.

### <a name="the-distance-between-two-points"></a>A distância entre dois pontos

A [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contém um [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) método com um cálculo simplificado da distância entre duas localizações geográficas.

Isso é usado no arquivo [LocalMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) e no arquivo de código-behind [LocalMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) para exibir também a distância para o Museu a partir do local do usuário:

[![Captura de tela tripla da página de museus locais](images/ch28fg28-small.png "Distância para um local")](images/ch28fg28-large.png#lightbox "Distância para um local")

O programa também demonstra como restringir dinamicamente o número de Pins com base no local do mapa.

## <a name="geocoding-and-back-again"></a>Geocodificação e voltar novamente

O [** Xamarin.Forms . **](xref:Xamarin.Forms.Maps)O assembly de mapas também contém uma [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe com um [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) método que converte um endereço de texto em zero ou mais posições geográficas possíveis e outro método [ `GetAddressesForPositionAsync` ] (xref: Xamarin.Forms . Maps. geocodificador. GetAddressesForPositionAsync ( Xamarin.Forms . Maps. Position)) que converte na outra direção.

O arquivo [GeocoderRoundTrip. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) e o arquivo code-behind [GeocoderRoundTrip.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) demonstram esse recurso.

## <a name="related-links"></a>Links relacionados

- [CAPÍTULO 28 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [CAPÍTULO 28 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.FormsMapeada](~/xamarin-forms/user-interface/map/index.md)
