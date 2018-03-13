---
title: Mapas
description: "iOS inclui o MapKit framework, o que torna mais fácil adicionar mapeado para um aplicativo. Usando o Kit de mapa, aplicativos iOS podem adicionar mapas interativos que dão suporte a recursos como Panorâmica e zoom, mostrando o local do usuário e gráficos avançados em camadas em um mapa. Este artigo analisa em vários recursos do Kit de mapa, mostrando como tirar proveito deles para compilar geográficos recursos em um aplicativo"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 540a459be24296c8446c2136773ddde59f9d4dd7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="maps"></a>Mapas

_iOS inclui o MapKit framework, o que torna mais fácil adicionar mapeado para um aplicativo. Usando o Kit de mapa, aplicativos iOS podem adicionar mapas interativos que dão suporte a recursos como Panorâmica e zoom, mostrando o local do usuário e gráficos avançados em camadas em um mapa. Este artigo analisa em vários recursos do Kit de mapa, mostrando como tirar proveito deles para compilar geográficos recursos em um aplicativo_

Mapas são um recurso comum em todos os sistemas operacionais móveis modernos. iOS oferece suporte a mapeamento nativamente por meio da estrutura de Kit de mapa. Com o Kit de mapa, aplicativos podem facilmente adicionar mapas avançados e interativos. Esses mapas podem ser personalizados de várias maneiras, como adicionar anotações para marcar locais em um mapa e sobreposição de elementos gráficos de formas arbitrárias. Kit de mapa ainda tem suporte interno para mostrar o local atual de um dispositivo.

## <a name="adding-a-map"></a>Adicionando um mapa

Adicionando um mapa a um aplicativo é feito adicionando uma `MKMapView` instância para a hierarquia do modo de exibição, conforme mostrado abaixo:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` é um `UIView` subclasse que exibe um mapa. Simplesmente adicionando o mapa usando o código acima produz um mapa interativo:

 ![](images/00-map.png "Um exemplo de mapa")

## <a name="map-style"></a>Estilo de mapa

 `MKMapView` dá suporte a 3 estilos diferentes de mapas. Para aplicar um estilo de mapa, basta definir o `MapType` um valor da propriedade de `MKMapType` enumeração:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  Captura de tela a seguir mostram os tipos de mapas diferentes que estão disponíveis:

 ![](images/01-mapstyles.png "Esta captura de tela mostram os tipos de mapas diferentes que estão disponíveis")

## <a name="panning-and-zooming"></a>Panorâmica e zoom

 `MKMapView` inclui suporte para recursos de interatividade do mapa, como:

-  Aumentar o zoom por meio de um gesto de pinçagem
-  Panorâmica por meio de um gesto de Panorâmica


Esses recursos podem ser habilitados ou desabilitados simplesmente definindo o `ZoomEnabled` e `ScrollEnabled` propriedades da `MKMapView` instância, em que o valor padrão é true para ambos. Por exemplo, para exibir um mapa estático, basta defina as propriedades adequadas para false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Local do usuário

Além de interação do usuário, `MKMapView` também tem suporte interno para exibir o local do dispositivo. Ele faz isso usando o *principal local* framework. Antes de poder acessar o local do usuário, você deve solicitar ao usuário. Para fazer isso, crie uma instância de `CLLocationManager` e chame `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Observe que nas versões do iOS antes 8.0, tentando chamar `RequestWhenInUseAuthorization` resultará em erro. Certifique-se de verificar a versão do iOS antes de fazer essa chamada se você pretende dar suporte a versões anteriores a 8.

Acessar o local do usuário também requer modificações de **Info. plist**. As seguintes chaves relacionadas a dados locais devem ser definidas:

- **NSLocationWhenInUseUsageDescription** – para acessar o local do usuário enquanto eles estiverem interagindo com seu aplicativo.
- **NSLocationAlwaysUsageDescription** – para quando o aplicativo acessa o local do usuário em segundo plano.

Você pode adicionar essas chaves abrindo **Info. plist** e selecionando *fonte* na parte inferior do editor.

Após atualizar **Info. plist** e solicitado ao usuário permissão para acessar seu local, você pode mostrar a localização do usuário no mapa, definindo o `ShowsUserLocation` propriedade como true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "O alerta permitir o acesso local")
 
## <a name="annotations"></a>Anotações

 `MKMapView` também dá suporte à exibição de imagens, conhecido como anotações em um mapa. Eles podem ser imagens personalizadas ou os pins de várias cores definidas pelo sistema. Por exemplo, a captura de tela a seguir mostra um mapa com dois um pin e uma imagem personalizada:

 ![](images/03-annotations.png "Esta captura de tela mostra um mapa com dois um pin e uma imagem personalizada")

### <a name="adding-an-annotation"></a>Adicionar uma anotação

Uma anotação em si tem duas partes:

-  O `MKAnnotation` objeto, que inclui dados de modelo sobre a anotação, como o título e o local da anotação.
-  O `MKAnnotationView` , que contém a imagem para exibição e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca a anotação.


Kit de mapa usa o padrão de delegação do iOS para adicionar anotações a um mapa, onde o `Delegate` propriedade do `MKMapView` é definido como uma instância de um `MKMapViewDelegate`. É a implementação desse representante que é responsável por retornar a `MKAnnotationView` de uma anotação.

Para adicionar uma anotação, primeiro a anotação é adicionada chamando `AddAnnotations` no `MKMapView` instância:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Quando o local da anotação fica visível no mapa, o `MKMapView` chamará seu delegado `GetViewForAnnotation` método para obter o `MKAnnotationView` para exibir.

Por exemplo, o código a seguir retorna um fornecido pelo sistema `MKPinAnnotationView`:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Reutilizando anotações

Para conservar memória, `MKMapView` permite a exibição de anotação 's seja agrupado para reutilização, semelhante à forma como as células de tabela são reutilizadas. Obter uma exibição de anotação do pool é feito com uma chamada para `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Mostrando os textos explicativos

Como mencionado anteriormente, uma anotação opcionalmente pode mostrar um texto explicativo. Para mostrar um texto explicativo simplesmente definir `CanShowCallout` como true no `MKAnnotationView`. Isso resulta no título da anotação que está sendo exibido quando a anotação é tocada, conforme mostrado:

 ![](images/04-callout.png "O título de anotações que está sendo exibido")

### <a name="customizing-the-callout"></a>Personalizando o texto explicativo

O texto explicativo também pode ser personalizado para mostrar exibições de esquerda e direita Acessórios, conforme mostrado abaixo:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Esse código resulta no seguinte texto explicativo:

 ![](images/05-callout-accessories.png "Um texto explicativo de exemplo")

Para tratar o usuário tocar o direito acessório, basta implementar o `CalloutAccessoryControlTapped` método o `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Sobreposições

Outra maneira de gráficos de camada em um mapa está usando sobreposições. Sobreposições de suporte para desenho conteúdo gráfico com o mapa é dimensionado conforme ele aparece ampliado. iOS fornece suporte para vários tipos de sobreposição, incluindo:

-  Polígonos - usados para realçar alguns região em um mapa.
-  Polilinhas - geralmente vistas quando mostrando uma rota.
-  Círculos - usados para realçar uma área circular de um mapa.


Além disso, sobreposições personalizadas podem ser criadas para mostrar geometrias arbitrárias com o código de desenho granular e personalizado. Por exemplo, radar clima seria uma boa candidata para uma sobreposição personalizada.

#### <a name="adding-an-overlay"></a>Adicionando uma sobreposição

Semelhante às anotações, adicionar uma sobreposição envolve 2 partes:

-  Criando um objeto de modelo para a sobreposição e adicioná-lo para o `MKMapView` .
-  Criar um modo de exibição para a sobreposição no `MKMapViewDelegate` .


O modelo para a sobreposição pode ser qualquer `MKShape` subclasse. Inclui o xamarin `MKShape` subclasses para polígonos, polilinhas e círculos, por meio de `MKPolygon`, `MKPolyline` e `MKCircle` classes respectivamente.

Por exemplo, o código a seguir é usado para adicionar um `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

O modo de exibição para uma sobreposição é uma `MKOverlayView` instância que é retornada pelo `GetViewForOverlay` no `MKMapViewDelegate`. Cada `MKShape` tem um correspondente `MKOverlayView` que sabe como exibir determinada forma. Para `MKPolygon` há `MKPolygonView`. Da mesma forma, `MKPolyline` corresponde à `MKPolylineView`e para `MKCircle` há `MKCircleView`.

Por exemplo, o código a seguir retorna um `MKCircleView` para um `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Isso exibe um círculo no mapa conforme mostrado:

 ![](images/06-circle-overlay.png "Um círculo exibido no mapa")

## <a name="local-search"></a>Pesquisa local

iOS inclui uma API com o Kit de mapa, que permite pesquisar assíncrona para pontos de interesse em uma região geográfica especificada de pesquisa local.

Para executar uma pesquisa local, um aplicativo deve seguir estas etapas:

1.  Criar `MKLocalSearchRequest` objeto.
1.  Criar um `MKLocalSearch` de objeto o `MKLocalSearchRequest` .
1.  Chamar o `Start` método sobre o `MKLocalSearch` objeto.
1.  Recuperar o `MKLocalSearchResponse` objeto em um retorno de chamada.


A própria API de pesquisa local não fornece nenhuma interface do usuário. Mesmo que não exige um mapa a ser usado. No entanto, para tornar o uso prático de pesquisa local, um aplicativo precisa fornecer alguma maneira de especificar uma consulta de pesquisa e exibir os resultados. Além disso, desde que os resultados conterão dados de local, geralmente fazer sentido para mostrá-los em um mapa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Adicionando uma interface do usuário de pesquisa Local

É uma maneira para aceitar a entrada de pesquisa com um `UISearchBar`, que fornecido por um `UISearchController` e exibirá os resultados em uma tabela.

O código a seguir adiciona o `UISearchController` (que tem uma propriedade de barra de pesquisa) no `ViewDidLoad` método `MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>Atualizando os resultados da pesquisa

O `SearchResultsUpdater` atua como um mediador entre o `searchController`da barra de pesquisa e os resultados da pesquisa. 

Neste exemplo, é necessário criar primeiro o método de pesquisa no `SearchResultsViewController`. Para fazer isso é necessário criar um `MKLocalSearch` de objeto e usá-lo para emitir uma pesquisa por um `MKLocalSearchRequest`, os resultados são recuperados em um retorno de chamada transmitido para o `Start` método do `MKLocalSearch` objeto. Em seguida, os resultados são retornados em uma `MKLocalSearchResponse` objeto que contém uma matriz de `MKMapItem` objetos:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

Em seguida, no nosso `MapViewController` , criaremos uma implementação personalizada de `UISearchResultsUpdating`, que é atribuída ao `SearchResultsUpdater` propriedade do nosso `searchController` no [adicionando uma interface de usuário de pesquisa Local](#Adding_a_Local_Search_UI) seção:

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

A implementação acima adiciona uma anotação ao mapa quando um item é selecionado de resultados, conforme mostrado abaixo:

 ![](images/08-search-results.png "Uma anotação adicionada ao mapa quando um item é selecionado dos resultados")
 
 > [!IMPORTANT]
> **Observação** `UISearchController` foi implementada no iOS 8. Se você deseja oferecer suporte a dispositivos anteriores a isso, você precisará usar `UISearchDisplayController`.



## <a name="summary"></a>Resumo

Este artigo examinado o *mapa* *Kit* framework para iOS. Primeiro, pesquisá-lo como o `MKMapView` classe permite interativos mapas a serem incluídos em um aplicativo. Em seguida, ele demonstrou como personalizar ainda mais usando anotações e sobreposições de mapas. Finalmente, ele examinado os recursos de pesquisa local que foram adicionados ao Kit de mapa com iOS 6.1, mostrando como usar executar consultas de localização com base para pontos de interesse e adicioná-las a um mapa.

## <a name="related-links"></a>Links relacionados

- [SearchController](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
- [MapDemo (exemplo)](https://developer.xamarin.com/samples/monotouch/MapDemo)
