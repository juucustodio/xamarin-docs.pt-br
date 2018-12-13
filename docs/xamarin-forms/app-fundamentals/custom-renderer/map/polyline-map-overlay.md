---
title: Realçando uma rota em um mapa
description: Este artigo explica como adicionar uma sobreposição de polilinha a um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectados que normalmente são usados para mostrar uma rota em um mapa ou para formar qualquer forma que seja necessária.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 786f050495d4682b719178f2723c482929544678
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998713"
---
# <a name="highlighting-a-route-on-a-map"></a>Realçando uma rota em um mapa

_Este artigo explica como adicionar uma sobreposição de polilinha a um mapa. Uma sobreposição de polilinha é uma série de segmentos de linha conectados que normalmente são usados para mostrar uma rota em um mapa ou para formar qualquer forma que seja necessária._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. As sobreposições são suporte para elaborar conteúdos gráficos que são dimensionados com o mapa conforme ele é ampliado e reduzido. As capturas de tela a seguir mostram o resultado do acréscimo de uma sobreposição de polilinha a um mapa:

![](polyline-map-overlay-images/screenshots.png)

Quando um controle [`Map`](xref:Xamarin.Forms.Maps.Map) é renderizado por um aplicativo Xamarin.Forms, no iOS é criada uma instância da classe `MapRenderer`, o que por sua vez cria uma instância de um controle `MKMapView` nativo. Na plataforma Android, a classe `MapRenderer` cria uma instância de um controle `MapView` nativo. Na UWP (Plataforma Universal do Windows), a classe `MapRenderer` cria uma instância de um `MapControl` nativo. E possível aproveitar o processo de renderização para implementar personalizações de mapa específicas da plataforma criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) o mapa criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) precisa ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, confira [Personalizando um marcador de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Crie uma subclasse da classe [`Map`](xref:Xamarin.Forms.Maps.Map), que adiciona uma propriedade `RouteCoordinates`:

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

A propriedade `RouteCoordinates` armazenará uma coleção de coordenadas que definem a rota a ser realçada.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Essa inicialização especifica uma série de coordenadas de latitude e longitude para definir a rota do mapa a ser realçada. Em seguida, ela posiciona a exibição do mapa com o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que altera a posição e o nível de aplicação de zoom do mapa criando um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) de um [`Position`](xref:Xamarin.Forms.Maps.Position) e um [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizando o mapa

Agora, é necessário adicionar um renderizador personalizado a cada projeto de aplicativo para adicionar a sobreposição de polilinha ao mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição de polilinha:

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

Esse método executa a seguinte configuração, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A propriedade `MKMapView.OverlayRenderer` é definida como um delegado correspondente.
- A coleção de coordenadas de latitude e longitude é recuperada da propriedade `CustomMap.RouteCoordinates` e armazenada como uma matriz de instâncias de `CLLocationCoordinate2D`.
- A polilinha é criada chamando o método `MKPolyline.FromCoordinates` estático, que especifica a latitude e a longitude de cada ponto.
- A polilinha é adicionada ao mapa chamando o método `MKMapView.AddOverlay`.

Em seguida, implemente o método `GetOverlayRenderer` para personalizar a renderização da sobreposição:

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

Crie uma subclasse da classe `MapRenderer` e substitua seus métodos `OnElementChanged` e `OnMapReady` para adicionar a sobreposição de polilinha:

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

O método `OnElementChanged` recupera a coleção de coordenadas de latitude e longitude da propriedade `CustomMap.RouteCoordinates` e a armazena em uma variável de membro. Em seguida, ele chama o método `MapView.GetMapAsync`, que obtém o `GoogleMap` subjacente que está vinculado à exibição, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms. Quando a instância de `GoogleMap` estiver disponível, o método `OnMapReady` será invocado e a polilinha será criada instanciando um objeto `PolylineOptions` que especifica a latitude e a longitude de cada ponto. Em seguida, a polilinha é adicionada ao mapa chamando o método `NativeMap.AddPolyline`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na Plataforma Universal do Windows

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição de polilinha:

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

Esse método executa as seguintes operações, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A coleção de coordenadas de latitude e longitude é recuperada da propriedade `CustomMap.RouteCoordinates` e convertida em uma `List` de coordenadas `BasicGeoposition`.
- A polilinha é criada instanciando um objeto `MapPolyline`. A classe `MapPolygon` é usada para exibir uma linha no mapa, definindo sua propriedade `Path` como um objeto `Geopath` que contém as coordenadas da linha.
- A polilinha é renderizada no mapa adicionando-a à coleção `MapControl.MapElements`.

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar uma sobreposição de polilinha a um mapa, a fim de mostrar uma rota em um mapa ou de criar qualquer forma necessária.


## <a name="related-links"></a>Links relacionados

- [Sobreposição de mapa de polilinha (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
