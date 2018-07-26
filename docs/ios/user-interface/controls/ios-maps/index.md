---
title: Mapas no xamarin. IOS
description: Este documento descreve a estrutura de MapKit do iOS e como ele é usado com o xamarin. IOS. Ele aborda como adicionar um mapa, estilo, Panorâmica e zoom, trabalhar com o local do usuário, adicionar anotações, trabalhar com textos explicativos e sobreposições e executar a pesquisa local.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5343f53b77319b08424263103834ffcf10e261a0
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242060"
---
# <a name="maps-in-xamarinios"></a>Mapas no xamarin. IOS

Mapas são um recurso comum em todos os sistemas operacionais móveis modernos. iOS oferece suporte a mapeamento nativamente por meio da estrutura de Kit de mapa. Com o Kit de mapa de aplicativos podem adicionar facilmente mapas sofisticados e interativos. Esses mapas podem ser personalizados em uma variedade de formas, como adicionar anotações para marcar locais em um mapa e sobreposição de elementos gráficos de formas arbitrárias. O Map Kit ainda tem suporte interno para mostrando o local atual de um dispositivo.

## <a name="adding-a-map"></a>Adicionando um mapa

Adicionando um mapa a um aplicativo é realizado pela adição de um `MKMapView` de instância para a hierarquia de exibição, conforme mostrado abaixo:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` é um `UIView` subclasse que exibe um mapa. Simplesmente adicionando o mapa usando o código acima gera um mapa interativo:

 ![](images/00-map.png "Um exemplo de mapa")

## <a name="map-style"></a>Estilo de mapa

 `MKMapView` dá suporte a 3 estilos diferentes de mapas. Para aplicar um estilo de mapa, simplesmente defina a `MapType` propriedade para um valor da `MKMapType` enumeração:
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
-  Movimento panorâmico por meio de um gesto de Panorâmica


Esses recursos podem ser habilitados ou desabilitados, simplesmente definindo a `ZoomEnabled` e `ScrollEnabled` propriedades do `MKMapView` instância, em que o valor padrão é verdadeiro para ambos. Por exemplo, para exibir um mapa estático, basta defina as propriedades adequadas para false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Local do usuário

Além de interação do usuário, `MKMapView` também tem suporte interno para exibir o local do dispositivo. Ele faz isso usando o *Core local* framework. Antes de poder acessar o local do usuário, você deve solicitar o usuário. Para fazer isso, crie uma instância do `CLLocationManager` e chamar `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Observe que nas versões do iOS anterior 8.0, tentando chamar `RequestWhenInUseAuthorization` resultará em erro. Certifique-se de verificar a versão do iOS antes de fazer essa chamada, se você pretende dar suporte a versões anteriores a 8.

Acessar o local do usuário também requer modificações **Info. plist**. As seguintes chaves relacionadas a dados locais devem ser definidas:

- **NSLocationWhenInUseUsageDescription** – para acessar o local do usuário enquanto eles estiverem interagindo com seu aplicativo.
- **NSLocationAlwaysUsageDescription** – para quando o aplicativo acessa o local do usuário em segundo plano.

Você pode adicionar essas chaves, abrindo **Info. plist** e selecionando *origem* na parte inferior do editor.

Depois que você atualizou **Info. plist** e solicitado ao usuário permissão para acessar sua localização, você pode mostrar a localização do usuário no mapa, definindo o `ShowsUserLocation` propriedade como true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "O alerta de acesso local permitir")
 
## <a name="annotations"></a>Anotações

 `MKMapView` também dá suporte à exibição de imagens, conhecido como anotações em um mapa. Eles podem ser imagens personalizadas ou definida pelo sistema pins de várias cores. Por exemplo, a captura de tela a seguir mostra um mapa com um dois um pin e uma imagem personalizada:

 ![](images/03-annotations.png "Esta captura de tela mostra um mapa com um dois um pin e uma imagem personalizada")

### <a name="adding-an-annotation"></a>Adicionar uma anotação

Uma anotação em si tem duas partes:

-  O `MKAnnotation` objeto, que inclui dados de modelo sobre a anotação, como o título e o local da anotação.
-  O `MKAnnotationView` , que contém a imagem para exibição e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca a anotação.


Kit de mapa usa o padrão de delegação do iOS para adicionar anotações a um mapa, onde o `Delegate` propriedade do `MKMapView` é definido como uma instância de um `MKMapViewDelegate`. É a implementação desse delegado que é responsável por retornar a `MKAnnotationView` de uma anotação.

Para adicionar uma anotação, primeiro a anotação é adicionada chamando `AddAnnotations` sobre o `MKMapView` instância:

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

### <a name="reusing-annotations"></a>Reutilização de anotações

Para conservar memória, `MKMapView` permite que os a visualização de anotação a ser agrupadas em pool para reutilização, semelhante à forma como as células da tabela são reutilizadas. Como obter uma exibição de anotação do pool é feito com uma chamada para `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Mostrando os textos explicativos

Como mencionado anteriormente, uma anotação, opcionalmente, pode mostrar um texto explicativo. Para mostrar um texto explicativo simplesmente definido `CanShowCallout` como true no `MKAnnotationView`. Isso resulta no título da anotação que está sendo exibido quando a anotação é tocada, conforme mostrado:

 ![](images/04-callout.png "O título de anotações que está sendo exibido")

### <a name="customizing-the-callout"></a>Personalizando o texto explicativo

O texto explicativo também pode ser personalizado para mostrar exibições de esquerda e direita Acessórios, conforme mostrado abaixo:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Esse código resulta no seguinte texto explicativo:

 ![](images/05-callout-accessories.png "Um texto explicativo de exemplo")

Para lidar com o usuário tocar o acessório certo, basta implementar o `CalloutAccessoryControlTapped` método no `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Sobreposições

Outra maneira de gráficos de camada em um mapa está usando sobreposições. Sobreposições de dar suporte a conteúdo de desenho gráfico que pode ser dimensionado com o mapa conforme ele aparece ampliado. iOS fornece suporte para vários tipos de sobreposições, incluindo:

-  Polígonos - comumente usados para realçar alguma região em um mapa.
-  Polilinhas - que costuma ser observadas durante a exibição de uma rota.
-  Círculos - usados para realçar uma área circular de um mapa.


Além disso, sobreposições personalizadas podem ser criadas para mostrar as geometrias arbitrárias com código de desenho granular e personalizado. Por exemplo, radar clima seria um bom candidato para uma sobreposição de personalizado.

#### <a name="adding-an-overlay"></a>Adicionando uma sobreposição

Assim como as anotações, adicionar uma sobreposição envolve 2 partes:

-  Criando um objeto de modelo para a sobreposição e adicioná-lo para o `MKMapView` .
-  Criando uma exibição para a sobreposição na `MKMapViewDelegate` .


O modelo para a sobreposição pode ser qualquer `MKShape` subclasse. Inclui o xamarin. IOS `MKShape` subclasses para polígonos, polilinhas e círculos, por meio de `MKPolygon`, `MKPolyline` e `MKCircle` classes, respectivamente.

Por exemplo, o código a seguir é usado para adicionar um `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

O modo de exibição para uma sobreposição é uma `MKOverlayView` instância que é retornada pelo `GetViewForOverlay` no `MKMapViewDelegate`. Cada `MKShape` tem um correspondente `MKOverlayView` que sabe como exibir da forma especificada. Para `MKPolygon` há `MKPolygonView`. Da mesma forma, `MKPolyline` corresponde à `MKPolylineView`e para `MKCircle` há `MKCircleView`.

Por exemplo, o código a seguir retorna uma `MKCircleView` para um `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Isso exibe um círculo no mapa, conforme mostrado:

 ![](images/06-circle-overlay.png "Um círculo exibido no mapa")

## <a name="local-search"></a>Pesquisa local

iOS inclui uma API com o Kit de mapa, que permite que as pesquisas assíncronas para pontos de interesse em uma região geográfica especificada de pesquisa local.

Para executar uma pesquisa local, um aplicativo deve seguir estas etapas:

1.  Criar `MKLocalSearchRequest` objeto.
1.  Criar uma `MKLocalSearch` objeto o `MKLocalSearchRequest` .
1.  Chame o `Start` método no `MKLocalSearch` objeto.
1.  Recuperar o `MKLocalSearchResponse` objeto em um retorno de chamada.


A própria API de pesquisa local não fornece nenhuma interface do usuário. Ele sequer exige um mapa a ser usado. No entanto, para tornar o uso prático de pesquisa local, um aplicativo precisa fornecer alguma maneira de especificar uma consulta de pesquisa e exibir os resultados. Além disso, uma vez que os resultados conterão dados de localização, será geralmente sentido mostrá-los em um mapa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Adicionando uma pesquisa de Local da interface do usuário

Uma maneira para aceitar a entrada de pesquisa é com um `UISearchBar`, que fornecido por um `UISearchController` e exibirá os resultados em uma tabela.

O código a seguir adiciona o `UISearchController` (que tem uma propriedade de barra de pesquisa) na `ViewDidLoad` método `MapViewController`:

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

Neste exemplo é preciso primeiro criar o método de pesquisa no `SearchResultsViewController`. Para fazer isso, devemos criar uma `MKLocalSearch` do objeto e usá-lo para emitir uma pesquisa por um `MKLocalSearchRequest`, os resultados são recuperados em um retorno de chamada passado para o `Start` método da `MKLocalSearch` objeto. Os resultados são retornados, em seguida, em um `MKLocalSearchResponse` objeto que contém uma matriz de `MKMapItem` objetos:

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

Em seguida, no nosso `MapViewController` vamos criar uma implementação personalizada de `UISearchResultsUpdating`, que é atribuído à `SearchResultsUpdater` propriedade do nosso `searchController` no [adicionando uma interface do usuário de pesquisa Local](#Adding_a_Local_Search_UI) seção:

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

A implementação acima adiciona uma anotação ao mapa quando um item é selecionado nos resultados, conforme mostrado abaixo:

 ![](images/08-search-results.png "Uma anotação adicionada ao mapa quando um item é selecionado dos resultados")
 
> [!IMPORTANT]
> `UISearchController` foi implementado no iOS 8. Se você deseja dar suporte a dispositivos anteriores a isso, você precisará usar `UISearchDisplayController`.



## <a name="summary"></a>Resumo

Este artigo examinado os *mapa* *Kit* framework para iOS. Primeiro, ele analisei como o `MKMapView` classe permite que os mapas interativos a serem incluídos em um aplicativo. Em seguida, ele demonstrou como personalizar ainda mais usando as anotações e sobreposições de mapas. Por fim, ele examinou os recursos de pesquisa local que foram adicionados ao mapa do Kit com iOS 6.1, que mostra como usar executar consultas baseadas na localização para pontos de interesse e adicioná-los a um mapa.

## <a name="related-links"></a>Links relacionados

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (amostra)](https://developer.xamarin.com/samples/monotouch/MapDemo)
