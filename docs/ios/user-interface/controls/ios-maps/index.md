---
title: Mapas no Xamarin. iOS
description: Este documento descreve a estrutura MapKit do iOS e como ela é usada com o Xamarin. iOS. Ele aborda como adicionar um mapa, estilizar, deslocar e aplicar zoom, trabalhar com o local do usuário, adicionar anotações, trabalhar com textos explicativos e sobreposições e executar pesquisa local.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3eb50c97521d11944e6d549018e057416b9dc2b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022028"
---
# <a name="maps-in-xamarinios"></a>Mapas no Xamarin. iOS

Os mapas são um recurso comum em todos os sistemas operacionais móveis modernos. o iOS oferece suporte de mapeamento nativamente por meio da estrutura do MAP Kit. Com o MAP Kit, os aplicativos podem adicionar facilmente mapas sofisticados e interativos. Esses mapas podem ser personalizados de várias maneiras, como adicionar anotações para marcar locais em um mapa e sobrepor elementos gráficos de formas arbitrárias. O MAP kit até mesmo tem suporte interno para mostrar o local atual de um dispositivo.

## <a name="adding-a-map"></a>Adicionando um mapa

Adicionar um mapa a um aplicativo é realizado adicionando uma instância de `MKMapView` à hierarquia de exibição, conforme mostrado abaixo:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

`MKMapView` é uma subclasse `UIView` que exibe um mapa. Simplesmente adicionar o mapa usando o código acima produz um mapa interativo:

![](images/00-map.png "A sample map")

## <a name="map-style"></a>Estilo do mapa

o `MKMapView` dá suporte a três estilos diferentes de mapas. Para aplicar um estilo de mapa, basta definir a propriedade `MapType` como um valor da enumeração `MKMapType`:

```csharp
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
```

A captura de tela a seguir mostra os diferentes estilos de mapa disponíveis:

![](images/01-mapstyles.png "This screenshot show the different map styles that are available")

## <a name="panning-and-zooming"></a>Panorâmica e zoom

o `MKMapView` inclui suporte para recursos de interatividade do mapa, como:

- Zoom por meio de um gesto de pinçar
- Movimento panorâmico por meio de um gesto panorâmico

Esses recursos podem ser habilitados ou desabilitados simplesmente definindo as propriedades `ZoomEnabled` e `ScrollEnabled` da instância `MKMapView`, em que o valor padrão é true para ambos. Por exemplo, para exibir um mapa estático, basta definir as propriedades apropriadas como false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Local do usuário

Além da interação do usuário, `MKMapView` também tem suporte interno para exibir o local do dispositivo. Ele faz isso usando a estrutura de *localização principal* . Antes de poder acessar o local do usuário, você deve solicitar o usuário. Para fazer isso, crie uma instância do `CLLocationManager` e chame `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Observe que, em versões do iOS anteriores a 8,0, a tentativa de chamar `RequestWhenInUseAuthorization` resultará em um erro. Certifique-se de verificar a versão do iOS antes de fazer essa chamada se você pretende dar suporte a versões anteriores a 8.

O acesso ao local do usuário também requer modificações no **info. plist**. As seguintes chaves relacionadas a dados locais devem ser definidas:

- **NSLocationWhenInUseUsageDescription** – para acessar o local do usuário enquanto eles estiverem interagindo com seu aplicativo.
- **NSLocationAlwaysUsageDescription** – para quando o aplicativo acessa o local do usuário em segundo plano.

Você pode adicionar essas chaves abrindo **info. plist** e selecionando *Source* na parte inferior do editor.

Depois de atualizar o **info. plist** e solicitar ao usuário permissão para acessar seu local, você pode mostrar o local do usuário no mapa definindo a propriedade `ShowsUserLocation` como true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "The allow location access alert")

## <a name="annotations"></a>Anotações

 o `MKMapView` também dá suporte à exibição de imagens, conhecidas como anotações, em um mapa. Elas podem ser imagens personalizadas ou Pins definidos pelo sistema de várias cores. Por exemplo, a captura de tela a seguir mostra um mapa com um PIN e uma imagem personalizada:

 ![](images/03-annotations.png "This screenshot shows a map with a both a pin and a custom image")

### <a name="adding-an-annotation"></a>Adicionando uma anotação

Uma anotação em si tem duas partes:

- O objeto `MKAnnotation`, que inclui dados de modelo sobre a anotação, como o título e o local da anotação.
- O `MKAnnotationView`, que contém a imagem a ser exibida e, opcionalmente, um texto explicativo que é mostrado quando o usuário toca na anotação.

O MAP Kit usa o padrão de delegação do iOS para adicionar anotações a um mapa, em que a propriedade `Delegate` da `MKMapView` é definida como uma instância de um `MKMapViewDelegate`. É a implementação desse delegado que é responsável por retornar o `MKAnnotationView` de uma anotação.

Para adicionar uma anotação, primeiro a anotação é adicionada chamando `AddAnnotations` na instância `MKMapView`:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Quando o local da anotação se torna visível no mapa, o `MKMapView` chamará o método `GetViewForAnnotation` do seu delegado para obter a `MKAnnotationView` para exibição.

Por exemplo, o código a seguir retorna um `MKPinAnnotationView`fornecido pelo sistema:

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

Para conservar a memória, `MKMapView` permite que a exibição de anotações seja agrupada para reutilização, semelhante à forma como as células da tabela são reutilizadas. A obtenção de uma exibição de anotação do pool é feita com uma chamada para `DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Mostrando textos explicativos

Como mencionado anteriormente, uma anotação pode, opcionalmente, mostrar um texto explicativo. Para mostrar um texto explicativo, basta definir `CanShowCallout` como true no `MKAnnotationView`. Isso resulta na exibição do título da anotação quando a anotação é tocada, conforme mostrado:

 ![](images/04-callout.png "The annotations title being displayed")

### <a name="customizing-the-callout"></a>Personalizando o texto explicativo

O texto explicativo também pode ser personalizado para mostrar exibições de acessório esquerdo e direito, conforme mostrado abaixo:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Esse código resulta no seguinte texto explicativo:

 ![](images/05-callout-accessories.png "An example callout")

Para manipular o usuário tocando no acessório à direita, basta implementar o método `CalloutAccessoryControlTapped` no `MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Sobreposições

Outra maneira de camadas de elementos gráficos em um mapa é usar sobreposições. As sobreposições são suporte para elaborar conteúdos gráficos que são dimensionados com o mapa conforme ele é ampliado e reduzido. o iOS oferece suporte a vários tipos de sobreposições, incluindo:

- Polígonos – comumente usados para realçar uma região em um mapa.
- Polilinhas – geralmente visto ao mostrar uma rota.
- Círculos – usados para realçar uma área circular de um mapa.

Além disso, as sobreposições personalizadas podem ser criadas para mostrar geometrias arbitrárias com código de desenho granular e personalizado. Por exemplo, o radar de clima seria um bom candidato para uma sobreposição personalizada.

#### <a name="adding-an-overlay"></a>Adicionando uma sobreposição

Semelhante às anotações, a adição de uma sobreposição envolve duas partes:

- Criar um objeto de modelo para a sobreposição e adicioná-lo ao `MKMapView`.
- Criar uma exibição para a sobreposição no `MKMapViewDelegate`.

O modelo para a sobreposição pode ser qualquer subclasse de `MKShape`. O Xamarin. iOS inclui subclasses `MKShape` para polígonos, polilinhas e círculos, por meio das classes `MKPolygon`, `MKPolyline` e `MKCircle`, respectivamente.

Por exemplo, o código a seguir é usado para adicionar um `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

A exibição de uma sobreposição é uma instância `MKOverlayView` que é retornada pelo `GetViewForOverlay` no `MKMapViewDelegate`. Cada `MKShape` tem um `MKOverlayView` correspondente que sabe como exibir a forma fornecida. Para `MKPolygon` há `MKPolygonView`. Da mesma forma, `MKPolyline` corresponde a `MKPolylineView`e, para `MKCircle`, há `MKCircleView`.

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

Isso exibe um círculo no mapa, conforme mostrado:

 ![](images/06-circle-overlay.png "A circle displayed on the map")

## <a name="local-search"></a>Pesquisa local

o iOS inclui uma API de pesquisa local com o MAP Kit, que permite pesquisas assíncronas de pontos de interesse em uma região geográfica especificada.

Para executar uma pesquisa local, um aplicativo deve seguir estas etapas:

1. Criar `MKLocalSearchRequest` objeto.
1. Crie um objeto de `MKLocalSearch` da `MKLocalSearchRequest`.
1. Chame o método `Start` no objeto `MKLocalSearch`.
1. Recupere o objeto `MKLocalSearchResponse` em um retorno de chamada.

A própria API de pesquisa local não fornece nenhuma interface do usuário. Ele nem precisa de um mapa para ser usado. No entanto, para fazer uso prático da pesquisa local, um aplicativo precisa fornecer uma maneira de especificar uma consulta de pesquisa e exibir resultados. Além disso, como os resultados conterão dados de localização, geralmente fará sentido mostrá-los em um mapa.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Adicionando uma interface do usuário de pesquisa local

Uma maneira de aceitar a entrada de pesquisa é com um `UISearchBar`, que é fornecido por um `UISearchController` e exibirá os resultados em uma tabela.

O código a seguir adiciona o `UISearchController` (que tem uma propriedade de barra de pesquisa) no método `ViewDidLoad` de `MapViewController`:

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
```

Observe que você é responsável por incorporar o objeto de barra de pesquisa na interface do usuário. Neste exemplo, atribuímos a ele o TitleView da barra de navegação, mas se você não usar um controlador de navegação em seu aplicativo, precisará encontrar outro lugar para exibi-lo.

Neste trecho de código, criamos outro controlador de exibição personalizado – `searchResultsController` – que exibe os resultados da pesquisa e usamos esse objeto para criar nosso objeto do controlador de pesquisa. Também criamos um novo atualizador de pesquisa, que se torna ativo quando o usuário interage com a barra de pesquisa. Ele recebe notificações sobre pesquisas com cada pressionamento de tecla e é responsável por atualizar a interface do usuário.
Vamos dar uma olhada em como implementar o `searchResultsController` e o `searchResultsUpdater` mais adiante neste guia.

Isso resulta em uma barra de pesquisa exibida sobre o mapa, conforme mostrado abaixo:

 ![](images/07-searchbar.png "A search bar displayed over the map")

### <a name="displaying-the-search-results"></a>Exibindo os resultados da pesquisa

Para exibir os resultados da pesquisa, precisamos criar um controlador de exibição personalizado; Normalmente, um `UITableViewController`. Como mostrado acima, o `searchResultsController` é passado para o construtor do `searchController` quando ele está sendo criado.
O código a seguir é um exemplo de como criar esse controlador de exibição personalizado:

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

O `SearchResultsUpdater` atua como um mediador entre a barra de pesquisa do `searchController`e os resultados da pesquisa.

Neste exemplo, precisamos criar primeiro o método Search no `SearchResultsViewController`. Para fazer isso, devemos criar um objeto `MKLocalSearch` e usá-lo para emitir uma pesquisa de um `MKLocalSearchRequest`, os resultados são recuperados em um retorno de chamada passado para o método `Start` do objeto `MKLocalSearch`. Os resultados são retornados em um objeto `MKLocalSearchResponse` contendo uma matriz de objetos `MKMapItem`:

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

Em seguida, em nosso `MapViewController` criaremos uma implementação personalizada de `UISearchResultsUpdating`, que é atribuída à propriedade `SearchResultsUpdater` de nosso `searchController` na seção [adicionando uma interface do usuário de pesquisa local](#Adding_a_Local_Search_UI) :

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

 ![](images/08-search-results.png "An annotation added to the map when an item is selected from the results")

> [!IMPORTANT]
> `UISearchController` foi implementado no iOS 8. Se você quiser dar suporte a dispositivos anteriores a isso, será necessário usar `UISearchDisplayController`.

## <a name="summary"></a>Resumo

Este artigo examinou a estrutura do *Kit* de *mapa* para Ios. Primeiro, ele examinou como a classe `MKMapView` permite que mapas interativos sejam incluídos em um aplicativo. Em seguida, ele demonstrou como personalizar mapas usando anotações e sobreposições. Por fim, ele examinou os recursos de pesquisa local que foram adicionados ao MAP kit com iOS 6,1, mostrando como usar as consultas de executar localização com base em pontos de interesse e adicioná-las a um mapa.

## <a name="related-links"></a>Links relacionados

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
