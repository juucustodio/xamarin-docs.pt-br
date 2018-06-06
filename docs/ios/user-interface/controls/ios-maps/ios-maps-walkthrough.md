---
title: As anotações e sobreposições em xamarin
description: Este artigo apresenta instruções passo a passo mostra como trabalhar com os recursos de anotação e sobreposição do Kit de mapa. Ele mostra como adicionar um mapa a um aplicativo que exibe uma anotação e sobreposição no local da conferência Xamarin evoluir 2013.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7d224f034afc9b841bbf82b2b15b92db2d7820c7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789574"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>As anotações e sobreposições em xamarin

O aplicativo que vamos construir neste passo a passo é mostrado abaixo:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Um exemplo de aplicativo MapKit")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
Você pode encontrar o código completo no **MapsWalkthroughComplete** pasta dentro de [exemplo de demonstração do mapa](https://developer.xamarin.com/samples/monotouch/MapDemo/).

Vamos começar criando um novo **iOS projeto vazio**e dando a ele um nome relevante. Podemos começar adicionando o código para o nosso controlador de exibição para exibir o MapView e irá criar novas classes para nosso MapDelegate e as anotações personalizadas. Siga as etapas abaixo para fazer isso:

## <a name="viewcontroller"></a>ViewController


1. Adicione os seguintes namespaces para o `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Adicionar uma `MKMapView` instância variável à classe, juntamente com um `MapDelegate` instância. Vamos criar o `MapDelegate` em breve:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. No controlador de `LoadView` método, adicione um `MKMapView` e defina-a como o `View` propriedade do controlador:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Em seguida, vamos adicionar código para inicializar o map no ' ViewDidLoad ' método.

1. No `ViewDidLoad` adicionar código para definir o tipo de mapa, mostrar o local do usuário e permitir que o zoom e panorâmica:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Em seguida, adicione código para centralizar o mapa e defina sua região:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Criar uma nova instância da `MapDelegate` e atribuí-la para o `Delegate` do `MKMapView`. Novamente, ela será implcodeent o `MapDelegate` em breve:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. A partir do iOS 8, você deve ser solicitar autorização do usuário para usar seu local, portanto, vamos adicionar isso ao nosso exemplo. Primeiro, defina um `CLLocationManager` variável de nível de classe:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. No `ViewDidLoad` método, queremos verificar se o dispositivo que executa o aplicativo está usando o iOS 8 e se ele for podemos solicitar autorização quando o aplicativo está em uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Por fim, é preciso editar o **Info. plist** arquivo para informar os usuários do motivo solicitando sua localização. No **fonte** menu o **Info. plist**, adicione a seguinte chave:
    
    `NSLocationWhenInUseUsageDescription` 
    
    e a cadeia de caracteres: 

    `Maps Demo`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs – uma classe para anotações personalizadas


1. Vamos usar uma classe personalizada para a anotação chamada `ConferenceAnnotation`. Adicione a seguinte classe ao projeto:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapDemo
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController - Adicionar anotação e sobreposição

1. Com o `ConferenceAnnotation` em vigor, pode adicioná-lo ao mapa. No `ViewDidLoad` método o `ViewController`, adicionar anotação em coordenadas do centro do mapa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. Também queremos ter uma sobreposição de hotel. Adicione o seguinte código para criar o `MKPolygon` usando as coordenadas de hotel fornecido e adicioná-lo ao mapa pela chamada `AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
Isso completa o código em `ViewDidLoad`. Agora, precisamos implementar nosso `MapDelegate` classe para a anotação de criação e modos de exibição de sobreposição respectivamente.


## <a name="mapdelegate"></a>MapDelegate

1. Criar uma classe chamada `MapDelegate` que herda de `MKMapViewDelegate` e incluir um `annotationId` variável a ser usado como um identificador de reutilização para a anotação:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    Somente temos uma anotação aqui para o reutilização de código não é estritamente necessário, mas é uma boa prática para incluí-lo.

1. Implementar o `GetViewForAnnotation` método para retornar uma exibição para o `ConferenceAnnotation` usando o **conference.png** imagem incluída com este passo a passo:

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. Quando o usuário toca na anotação, desejamos exibir uma imagem mostrando a cidade de Austin. Adicione as seguintes variáveis para o `MapDelegate` para a imagem e o modo de exibição para exibi-lo:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Em seguida, para mostrar a imagem quando a anotação é tocada, implemente o `DidSelectAnnotation` método da seguinte maneira:

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. Para ocultar a imagem quando o usuário cancela a seleção de anotação, toque em qualquer lugar no mapa, implemente o `DidSelectAnnotationView` método da seguinte maneira:

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    Agora temos o código para a anotação. Tudo o que resta é adicionar código para o `MapDelegate` para criar o modo de exibição para a sobreposição de hotel.

1. Adicione a seguinte implementação de `GetViewForOverlay` para o `MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

Execute o aplicativo. Agora temos um mapa interativo com uma anotação personalizada e uma sobreposição! Toque na anotação e a imagem de Austin é exibida, conforme mostrado abaixo:

 [![](ios-maps-walkthrough-images/01-map-image.png "Toque na anotação e a imagem de Austin é exibida")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Resumo

Neste artigo vimos adicionar uma anotação a um mapa, bem como adicionar uma sobreposição de um polígono especificado. Podemos também demonstraram como adicionar suporte de toque para a anotação para animar uma imagem em um mapa.


## <a name="related-links"></a>Links relacionados

- [Demonstração de mapa (exemplo)](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [Mapas de iOS](~/ios/user-interface/controls/ios-maps/index.md)
