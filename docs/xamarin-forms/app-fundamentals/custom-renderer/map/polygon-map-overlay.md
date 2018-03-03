---
title: "Realce uma região em um mapa"
description: "Este artigo explicou como adicionar uma sobreposição de polígono para um mapa, para realçar uma região no mapa. Polígonos são uma forma fechada e tem seus interiores preenchidos."
ms.topic: article
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 6c116565842537f24d92a6d100ab1636f25c2e12
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="highlighting-a-region-on-a-map"></a>Realce uma região em um mapa

_Este artigo explicou como adicionar uma sobreposição de polígono para um mapa, para realçar uma região no mapa. Polígonos são uma forma fechada e tem seus interiores preenchidos._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. Sobreposições de suporte para desenho conteúdo gráfico com o mapa é dimensionado conforme ele aparece ampliado. As capturas de tela a seguir mostram o resultado da adição de uma sobreposição de polígono para um mapa:

![](polygon-map-overlay-images/screenshots.png)

Quando um [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) controle é processado por um aplicativo xamarin. Forms, no iOS a `MapRenderer` classe é instanciada, que por sua vez instancia um nativo `MKMapView` controle. Na plataforma Android, o `MapRenderer` classe instancia um nativo `MapView` controle. Sobre o Windows UWP (plataforma Universal), o `MapRenderer` classe instancia um nativo `MapControl`. O processo de renderização pode ser tomado aproveitar para implementar as personalizações do mapa específico da plataforma, criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do xamarin. Forms.
1. [Personalizar](#Customizing_the_Map) o mapa, criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve ser inicializada e configurada antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, consulte [Personalizando um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Criar uma subclasse do [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) classe, que adiciona um `ShapeCoordinates` propriedade:

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

O `ShapeCoordinates` propriedade armazenará uma coleção de coordenadas que definem a região realçada.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Essa inicialização especifica uma série de coordenadas de latitude e longitude, para definir a região do mapa a ser realçado. Ele, em seguida, posiciona o modo de exibição do mapa com o [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) método, o que altera a posição e o nível de zoom do mapa, criando um [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) de um [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e um [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizando o mapa

Agora deve ser adicionado a um renderizador personalizado para cada projeto de aplicativo para adicionar a sobreposição de polígono do mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

Esse método executa a configuração a seguir, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- O `MKMapView.OverlayRenderer` está definida como um delegado correspondente.
- A coleção de coordenadas de latitude e longitude são recuperadas do `CustomMap.ShapeCoordinates` propriedade e armazenada como uma matriz de `CLLocationCoordinate2D` instâncias.
- O polígono é criado chamando estático `MKPolygon.FromCoordinates` método, que especifica a latitude e longitude de cada ponto.
- O polígono é adicionado ao mapa chamando o `MKMapView.AddOverlay` método. Este método fecha automaticamente o polígono ao desenhar uma linha que conecta o primeiro e último pontos.

Em seguida, implementar a `GetOverlayRenderer` método para personalizar a renderização da sobreposição de:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` e `OnMapReady` métodos para adicionar a sobreposição de polígono:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

O `OnElementChanged` método recupera a coleção de coordenadas de latitude e longitude do `CustomMap.ShapeCoordinates` propriedade e os armazena em uma variável de membro. Depois, ele chama o `MapView.GetMapAsync` método, que obtém subjacente `GoogleMap` que está vinculado à exibição, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Uma vez o `GoogleMap` instância estiver disponível, o `OnMapReady` método será chamado, onde o polígono é criado por instanciar uma `PolygonOptions` objeto que especifica a latitude e longitude de cada ponto. O polígono é adicionado ao mapa chamando o `NativeMap.AddPolygon` método. Este método fecha automaticamente o polígono ao desenhar uma linha que conecta o primeiro e último pontos.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na plataforma Universal do Windows

Criar uma subclasse do `MapRenderer` classe e substituir seu `OnElementChanged` método para adicionar a sobreposição de polígono:

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

Esse método executa as operações a seguir, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:

- A coleção de coordenadas de latitude e longitude são recuperadas do `CustomMap.ShapeCoordinates` propriedade e convertido em um `List` de `BasicGeoposition` coordenadas.
- O polígono é criado pela instanciação de um `MapPolygon` objeto. O `MapPolygon` classe é usada para exibir uma forma de vários pontos no mapa, definindo seu `Path` propriedade para um `Geopath` objeto que contém as coordenadas da forma.
- O polígono é renderizado no mapa, adicionando-o para o `MapControl.MapElements` coleção. Observe que o polígono será fechado automaticamente ao desenhar uma linha que conecta o primeiro e último pontos.

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar uma sobreposição de polígono para um mapa, para realçar uma região do mapa. Polígonos são uma forma fechada e tem seus interiores preenchidos.


## <a name="related-links"></a>Links relacionados

- [Sobreposição de mapa de polígono (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Personalizando um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
