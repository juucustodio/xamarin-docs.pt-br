---
title: Realçar uma área Circular em um mapa
description: Este artigo explica como adicionar uma sobreposição circular a um mapa, para realçar uma área circular do mapa. Enquanto o iOS e Android fornecem APIs para adicionar a sobreposição circular no mapa, na UWP, a sobreposição é renderizada como um polígono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 3064296d4c78a3342fb27afc971c37a029987e5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998551"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Realçar uma área Circular em um mapa

_Este artigo explica como adicionar uma sobreposição circular a um mapa, para realçar uma área circular do mapa._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. Sobreposições de dar suporte a conteúdo de desenho gráfico que pode ser dimensionado com o mapa conforme ele aparece ampliado. As capturas de tela a seguir mostram o resultado da adição de uma sobreposição circular a um mapa:

![](circle-map-overlay-images/screenshots.png)

Quando um [ `Map` ](xref:Xamarin.Forms.Maps.Map) controle é processado por um aplicativo xamarin. Forms, no iOS o `MapRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Na Universal Windows Platform (UWP), o `MapRenderer` classe instancia um nativo `MapControl`. O processo de renderização pode ser aproveitado para implementar as personalizações do mapa específicas da plataforma, criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Personalizar](#Customizing_the_Map) o mapa, criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, consulte [Personalizando um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Criar uma `CustomCircle` classe que tem `Position` e `Radius` propriedades:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Em seguida, crie uma subclasse do [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe, que adiciona uma propriedade de tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

Consumir o `CustomMap` controle declarando uma instância na instância de página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

Como alternativa, consumir o `CustomMap` controle declarando uma instância na instância de página do c#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

Inicializar o `CustomMap` controlar conforme necessário:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Essa inicialização adiciona [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) e `CustomCircle` instâncias para o mapa personalizado e posiciona a exibição do mapa com o [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que altera a posição e o zoom nível do mapa com a criação de um [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) de uma [ `Position` ](xref:Xamarin.Forms.Maps.Position) e um [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar o mapa

Agora deve ser adicionado a um renderizador personalizado para cada projeto de aplicativo para adicionar a sobreposição circular no mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` método para adicionar a sobreposição circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Esse método executa a configuração a seguir, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- O `MKMapView.OverlayRenderer` estiver definida como um delegado correspondente.
- O círculo é criado, definindo um estático `MKCircle` objeto que especifica o centro do círculo e o raio do círculo em metros.
- O círculo é adicionado ao mapa chamando o `MKMapView.AddOverlay` método.

Em seguida, implemente o `GetOverlayRenderer` método para personalizar a renderização da sobreposição:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` e `OnMapReady` métodos para adicionar a sobreposição circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

O `OnElementChanged` chamadas de método de `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculada à exibição, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Uma vez a `GoogleMap` instância está disponível, o `OnMapReady` método será invocado, em que o círculo é criar uma instância de um `CircleOptions` objeto que especifica o centro do círculo e o raio do círculo em metros. O círculo é adicionado ao mapa chamando o `NativeMap.AddCircle` método.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Windows Universal

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` método para adicionar a sobreposição circular:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Esse método executa as seguintes operações, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- A posição do círculo e radius são recuperados do `CustomMap.Circle` propriedade e passado para o `GenerateCircleCoordinates` método, que gera a latitude e longitude coordenadas para o perímetro do círculo. O código para esse método auxiliar é mostrado abaixo.
- As coordenadas do perímetro de circle são convertidas em um `List` de `BasicGeoposition` coordenadas.
- O círculo é criar uma instância de um `MapPolygon` objeto. O `MapPolygon` classe é usada para exibir uma forma de vários pontos no mapa, definindo seu `Path` propriedade para um `Geopath` objeto que contém as coordenadas da forma.
- O polígono é renderizado no mapa, adicionando-o para o `MapControl.MapElements` coleção.


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar uma sobreposição circular a um mapa, para realçar uma área circular do mapa.


## <a name="related-links"></a>Links relacionados

- [Ovlerlay de mapa circular (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
