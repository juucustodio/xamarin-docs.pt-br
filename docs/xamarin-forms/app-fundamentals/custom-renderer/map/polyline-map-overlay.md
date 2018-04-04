---
title: Realce uma rota em um mapa
description: Este artigo explica como adicionar uma sobreposição de polilinha a um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectada que normalmente são usados para mostrar uma rota em um mapa, formulário ou qualquer forma que é necessário.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f781a472a63d97c8859aff36b28e0fd4fa0c7756
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="highlighting-a-route-on-a-map"></a>Realce uma rota em um mapa

_Este artigo explica como adicionar uma sobreposição de polilinha a um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectada que normalmente são usados para mostrar uma rota em um mapa, formulário ou qualquer forma que é necessário._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. Sobreposições de suporte para desenho conteúdo gráfico com o mapa é dimensionado conforme ele aparece ampliado. As capturas de tela a seguir mostram o resultado da adição de uma sobreposição de polilinha a um mapa:

![](polyline-map-overlay-images/screenshots.png)

Quando um [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) controle é processado por um aplicativo xamarin. Forms, no iOS a `MapRenderer` classe é instanciada, que por sua vez instancia um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Sobre o Windows UWP (plataforma Universal), o `MapRenderer` classe instancia um nativo `MapControl`. O processo de renderização pode ser tomado aproveitar para implementar as personalizações do mapa específico da plataforma, criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Personalizar](#Customizing_the_Map) o mapa, criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve ser inicializada e configurada antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, consulte [Personalizando um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Criar uma subclasse do [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) classe, que adiciona um `RouteCoordinates` propriedade:

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

O `RouteCoordinates` propriedade armazenará uma coleção de coordenadas que definem a rota para ser realçado.

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consumindo o mapa personalizado

Consumir o `CustomMap` controle declarando uma instância na instância de página do XAML:

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

Como alternativa, consumir o `CustomMap` controle declarando uma instância na instância de página c#:

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

Essa inicialização especifica uma série de coordenadas de latitude e longitude, para definir a rota no mapa a ser realçado. Ele, em seguida, posiciona o modo de exibição do mapa com o [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, o que altera a posição e o nível de zoom do mapa, criando um [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) de um [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e um [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizando o mapa

Agora deve ser adicionado a um renderizador personalizado para cada projeto de aplicativo para adicionar a sobreposição de polilinha ao mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polilinha:

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

- O `MKMapView.OverlayRenderer` está definida como um delegado correspondente.
- A coleção de coordenadas de latitude e longitude são recuperadas do `CustomMap.RouteCoordinates` propriedade e armazenada como uma matriz de `CLLocationCoordinate2D` instâncias.
- A polilinha é criada chamando estático `MKPolyline.FromCoordinates` método, que especifica a latitude e longitude de cada ponto.
- A polilinha é adicionada ao mapa chamando o `MKMapView.AddOverlay` método.

Em seguida, implementar a `GetOverlayRenderer` método para personalizar a renderização da sobreposição de:

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

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` e `OnMapReady` métodos para adicionar a sobreposição de polilinha:

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

O `OnElementChanged` método recupera a coleção de coordenadas de latitude e longitude do `CustomMap.RouteCoordinates` propriedade e os armazena em uma variável de membro. Depois, ele chama o `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculado à exibição, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Uma vez o `GoogleMap` instância estiver disponível, o `OnMapReady` método será chamado, onde a polilinha é criada por instanciar uma `PolylineOptions` objeto que especifica a latitude e longitude de cada ponto. A polilinha é adicionada ao mapa chamando o `NativeMap.AddPolyline` método.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Universal do Windows

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polilinha:

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

Esse método executa as operações a seguir, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- A coleção de coordenadas de latitude e longitude são recuperadas do `CustomMap.RouteCoordinates` propriedade e convertido em um `List` de `BasicGeoposition` coordenadas.
- A polilinha é criada pela instanciação de um `MapPolyline` objeto. O `MapPolygon` classe é usada para exibir uma linha do mapa, definindo seu `Path` propriedade para um `Geopath` objeto que contém as coordenadas de linha.
- A polilinha é renderizada no mapa, adicionando-o para o `MapControl.MapElements` coleção.

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar uma sobreposição de polilinha a um mapa, para mostrar uma rota em um mapa, ou qualquer forma necessária de formulário.


## <a name="related-links"></a>Links relacionados

- [Mapa de polilinha Ovlerlay (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
