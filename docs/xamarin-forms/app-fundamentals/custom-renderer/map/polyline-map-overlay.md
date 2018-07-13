---
title: Realçar uma rota em um mapa
description: Este artigo explica como adicionar uma sobreposição de polilinha em um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectados que normalmente são usados para mostrar uma rota em um mapa, ou formulário de qualquer forma que é necessário.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998713"
---
# <a name="highlighting-a-route-on-a-map"></a>Realçar uma rota em um mapa

_Este artigo explica como adicionar uma sobreposição de polilinha em um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectados que normalmente são usados para mostrar uma rota em um mapa, ou formulário de qualquer forma que é necessário._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. Sobreposições de dar suporte a conteúdo de desenho gráfico que pode ser dimensionado com o mapa conforme ele aparece ampliado. As capturas de tela a seguir mostram o resultado da adição de uma sobreposição de polilinha em um mapa:

![](polyline-map-overlay-images/screenshots.png)

Quando um [ `Map` ](xref:Xamarin.Forms.Maps.Map) controle é processado por um aplicativo xamarin. Forms, no iOS o `MapRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Na Universal Windows Platform (UWP), o `MapRenderer` classe instancia um nativo `MapControl`. O processo de renderização pode ser aproveitado para implementar as personalizações do mapa específicas da plataforma, criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Personalizar](#Customizing_the_Map) o mapa, criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, consulte [Personalizando um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Criar uma subclasse do [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe, que adiciona um `RouteCoordinates` propriedade:

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

O `RouteCoordinates` propriedade irá armazenar uma coleção de coordenadas que definem a rota seja realçado.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Essa inicialização especifica uma série de coordenadas de latitude e longitude, para definir a rota no mapa seja realçado. Ele, em seguida, posiciona a exibição do mapa com o [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) método, que altera a posição e o nível de zoom do mapa com a criação de uma [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) de uma [ `Position` ](xref:Xamarin.Forms.Maps.Position) e uma [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizar o mapa

Agora deve ser adicionado a um renderizador personalizado para cada projeto de aplicativo para adicionar a sobreposição de polilinha ao mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polilinha:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Esse método executa a configuração a seguir, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- O `MKMapView.OverlayRenderer` estiver definida como um delegado correspondente.
- A coleção de coordenadas de latitude e longitude são recuperados do `CustomMap.RouteCoordinates` propriedade e armazenados como uma matriz de `CLLocationCoordinate2D` instâncias.
- A polyline é criado chamando estático `MKPolyline.FromCoordinates` método, que especifica a latitude e longitude de cada ponto.
- A polyline é adicionado ao mapa chamando o `MKMapView.AddOverlay` método.

Em seguida, implemente o `GetOverlayRenderer` método para personalizar a renderização da sobreposição:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` e `OnMapReady` métodos para adicionar a sobreposição de polilinha:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

O `OnElementChanged` método recupera a coleção de coordenadas de latitude e longitude do `CustomMap.RouteCoordinates` propriedade e os armazena em uma variável de membro. Em seguida, ele chama o `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculada à exibição, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Uma vez a `GoogleMap` instância está disponível, o `OnMapReady` método será invocado, onde a polyline é criar uma instância de um `PolylineOptions` objeto que especifica a latitude e longitude de cada ponto. A polyline, em seguida, é adicionado ao mapa chamando o `NativeMap.AddPolyline` método.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Windows Universal

Criar uma subclasse do `MapRenderer` de classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polilinha:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Esse método executa as seguintes operações, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- A coleção de coordenadas de latitude e longitude são recuperados do `CustomMap.RouteCoordinates` propriedade e convertido em um `List` de `BasicGeoposition` coordenadas.
- A polyline é criar uma instância de um `MapPolyline` objeto. O `MapPolygon` classe é usada para exibir uma linha do mapa, definindo seu `Path` propriedade para um `Geopath` objeto que contém as coordenadas de linha.
- A polyline é renderizado no mapa, adicionando-o para o `MapControl.MapElements` coleção.

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar um mapa, para mostrar uma rota em um mapa, ou formulário de qualquer forma que é necessário uma sobreposição de polilinha.


## <a name="related-links"></a>Links relacionados

- [Mapa de polilinha Ovlerlay (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
