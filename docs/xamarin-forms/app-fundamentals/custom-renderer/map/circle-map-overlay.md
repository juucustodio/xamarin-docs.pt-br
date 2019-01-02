---
title: Realçando uma área circular em um mapa
description: Este artigo explica como adicionar uma sobreposição circular a um mapa a fim de realçar uma área circular nele. Enquanto o iOS e o Android fornecem APIs para adicionar a sobreposição circular ao mapa, na UWP, a sobreposição é renderizada como um polígono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: ed671063aad55cb6c4730494f024a2ec2a016de1
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051925"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Realçando uma área circular em um mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)

_Este artigo explica como adicionar uma sobreposição circular a um mapa a fim de realçar uma área circular nele._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. As sobreposições são suporte para elaborar conteúdos gráficos que são dimensionados com o mapa conforme ele é ampliado e reduzido. As capturas de tela a seguir mostram o resultado do acréscimo de uma sobreposição circular a um mapa:

![](circle-map-overlay-images/screenshots.png)

Quando um controle [`Map`](xref:Xamarin.Forms.Maps.Map) é renderizado por um aplicativo Xamarin.Forms, no iOS é criada uma instância da classe `MapRenderer`, o que por sua vez cria uma instância de um controle `MKMapView` nativo. Na plataforma Android, a classe `MapRenderer` cria uma instância de um controle `MapView` nativo. Na UWP (Plataforma Universal do Windows), a classe `MapRenderer` cria uma instância de um `MapControl` nativo. E possível aproveitar o processo de renderização para implementar personalizações de mapa específicas da plataforma criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) o mapa criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) precisa ser inicializado e configurado antes do uso. Para obter mais informações, confira [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, confira [Personalizando um marcador de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Crie uma classe `CustomCircle` com as propriedades `Position` e `Radius`:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Em seguida, crie uma subclasse da classe [`Map`](xref:Xamarin.Forms.Maps.Map), que adiciona uma propriedade do tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

Consuma o controle `CustomMap` declarando uma instância dele na instância da página XAML:

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

Como alternativa, consuma o controle `CustomMap` declarando uma instância dele na instância da página em C#:

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

Inicialize o controle `CustomMap` da forma necessária:

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

Essa inicialização adiciona instâncias de [`Pin`](xref:Xamarin.Forms.Maps.Pin) e `CustomCircle` ao mapa personalizado e posiciona a exibição do mapa com o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que altera a posição e o nível de aplicação de zoom do mapa com a criação de um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) com base em uma [`Position`](xref:Xamarin.Forms.Maps.Position) e uma [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizando o mapa

Agora, é necessário adicionar um renderizador personalizado a cada projeto de aplicativo para adicionar a sobreposição circular ao mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição circular:

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

Esse método executa a seguinte configuração, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A propriedade `MKMapView.OverlayRenderer` é definida como um delegado correspondente.
- O círculo é criado definindo um objeto `MKCircle` estático que especifica o centro do círculo e seu raio em metros.
- O círculo é adicionado ao mapa chamando o método `MKMapView.AddOverlay`.

Em seguida, implemente o método `GetOverlayRenderer` para personalizar a renderização da sobreposição:

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

Crie uma subclasse da classe `MapRenderer` e substitua seus métodos `OnElementChanged` e `OnMapReady` para adicionar a sobreposição circular:

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

O método `OnElementChanged` chamará o método `MapView.GetMapAsync`, que obterá o `GoogleMap` subjacente que está vinculado à exibição, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms. Quando a instância de `GoogleMap` estiver disponível, o método `OnMapReady` será invocado e o círculo será criado instanciando um objeto `CircleOptions` que especifica o centro do círculo e seu raio em metros. Em seguida, o círculo é adicionado ao mapa chamando o método `NativeMap.AddCircle`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na Plataforma Universal do Windows

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição circular:

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

Esse método executa as seguintes operações, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A posição e o raio do círculo são recuperados da propriedade `CustomMap.Circle` e passados para o método `GenerateCircleCoordinates`, que gera coordenadas de latitude e longitude para o perímetro do círculo. O código desse método auxiliar é mostrado abaixo.
- As coordenadas de perímetro do círculo são convertidas em um `List` de coordenadas `BasicGeoposition`.
- O círculo é criado instanciando um objeto `MapPolygon`. A classe `MapPolygon` é usada para exibir uma forma de vários pontos no mapa, definindo sua propriedade `Path` como um objeto `Geopath` que contém as coordenadas da forma.
- O polígono é renderizado no mapa adicionando-o à coleção `MapControl.MapElements`.


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

Este artigo explicou como adicionar uma sobreposição circular a um mapa a fim de realçar uma área circular nele.


## <a name="related-links"></a>Links relacionados

- [Sobreposição de mapa circular (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
