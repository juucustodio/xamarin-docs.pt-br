---
title: Anotações e sobreposições no Xamarin. iOS
description: Este artigo apresenta uma explicação passo a passo mostrando como trabalhar com os recursos de anotação e sobreposição do MAP Kit. Ele mostra como adicionar um mapa a um aplicativo que exibe uma anotação e uma sobreposição no local da conferência do Xamarin evolua 2013.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 59ad6a11eecf629fc2a815e21a29493f4a1a1397
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932217"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Anotações e sobreposições no Xamarin. iOS

O aplicativo que vamos criar neste passo a passo é mostrado abaixo:

 [![Um aplicativo MapKit de exemplo](ios-maps-walkthrough-images/00-map-overlay.png)](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)

Você pode encontrar o código concluído no [exemplo do Maps de mapas](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough).

Vamos começar criando um novo projeto do **Ios vazio**e dando a ele um nome relevante. Vamos começar adicionando o código ao nosso controlador de exibição para exibir o MapView e, em seguida, criará novas classes para nosso MapDelegate e as anotações personalizadas. Siga as etapas abaixo para fazer isso:

## <a name="viewcontroller"></a>ViewController

1. Adicione os seguintes namespaces ao `ViewController` :

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Adicione uma `MKMapView` variável de instância à classe, junto com uma `MapDelegate` instância. Vamos criar o em `MapDelegate` breve:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. No método do controlador `LoadView` , adicione um `MKMapView` e defina-o como a `View` Propriedade do controlador:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Em seguida, adicionaremos o código para inicializar o mapa no método ' ViewDidLoad ' '.

1. Em `ViewDidLoad` Adicionar código para definir o tipo de mapa, mostre o local do usuário e permitir zoom e panorâmica:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;

    ```

1. Em seguida, adicione o código para centralizar o mapa e definir sua região:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;

    ```

1. Crie uma nova instância do `MapDelegate` e atribua-a ao `Delegate` do `MKMapView` . Novamente, implementaremos o em `MapDelegate` breve:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;
    ```

1. A partir do iOS 8, você deve solicitar autorização do usuário para usar seu local, então vamos adicioná-lo ao nosso exemplo. Primeiro, defina uma `CLLocationManager` variável em nível de classe:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. No `ViewDidLoad` método, queremos verificar se o dispositivo que está executando o aplicativo está usando o Ios 8 e, se for, solicitaremos autorização quando o aplicativo estiver em uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
        locationManager.RequestWhenInUseAuthorization ();
    }
    ```

1. Por fim, precisamos editar o arquivo **info. plist** para avisar os usuários do motivo da solicitação de seu local. No menu de **origem** do **info. plist**, adicione a seguinte chave:

    `NSLocationWhenInUseUsageDescription`

    e cadeia de caracteres:

    `Maps Walkthrough Docs Sample`.

## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs – uma classe para anotações personalizadas

1. Vamos usar uma classe personalizada para a anotação chamada `ConferenceAnnotation` . Adicione a seguinte classe ao projeto:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;

    namespace MapsWalkthrough
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

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-adicionando a anotação e a sobreposição

1. Com o in-loco `ConferenceAnnotation` , podemos adicioná-lo ao mapa. De volta ao `ViewDidLoad` método do `ViewController` , adicione a anotação na coordenada do centro do mapa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter));
    ```

1. Também queremos ter uma sobreposição do hotel. Adicione o código a seguir para criar o `MKPolygon` usando as coordenadas do Hotel fornecido e adicione-o ao mapa por chamada `AddOverlay` :

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

Isso conclui o código em `ViewDidLoad` . Agora precisamos implementar nossa `MapDelegate` classe para tratar da criação das exibições de anotação e de sobreposição, respectivamente.

## <a name="mapdelegate"></a>MapDelegate

1. Crie uma classe chamada `MapDelegate` que herde de `MKMapViewDelegate` e inclua uma `annotationId` variável para usar como um identificador de reutilização para a anotação:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```

    Temos apenas uma anotação aqui para que o código de reutilização não seja estritamente necessário, mas é uma boa prática incluí-lo.

1. Implemente o `GetViewForAnnotation` método para retornar uma exibição para o `ConferenceAnnotation` usando a imagem de **conference.png** incluída com este passo a passos:

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

1. Quando o usuário toca na anotação, queremos exibir uma imagem mostrando a cidade de Austin. Adicione as seguintes variáveis ao `MapDelegate` para a imagem e a exibição para exibi-la:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Em seguida, para mostrar a imagem quando a anotação for tocada, implemente o `DidSelectAnnotation` método da seguinte maneira:

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

1. Para ocultar a imagem quando o usuário desmarcar a anotação tocando em qualquer outro lugar do mapa, implemente o `DidDeselectAnnotationView` método da seguinte maneira:

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

    Agora temos o código para a anotação em vigor. Tudo o que resta é adicionar código ao `MapDelegate` para criar a exibição para a sobreposição do hotel.

1. Adicione a seguinte implementação do `GetViewForOverlay` ao `MapDelegate` :

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

 [![Toque na anotação e a imagem de Austin é exibida](ios-maps-walkthrough-images/01-map-image.png)](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Resumo

Neste artigo, examinamos como adicionar uma anotação a um mapa, bem como adicionar uma sobreposição para um polígono especificado. Também demonstramos como adicionar suporte de toque à anotação para animar uma imagem em um mapa.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de mapa de instruções](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)
- [Exemplo de demonstração de mapa](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
- [Mapas do iOS](~/ios/user-interface/controls/ios-maps/index.md)
