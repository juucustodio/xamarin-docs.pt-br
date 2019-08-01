---
title: Realçando uma região em um mapa
description: Este artigo explica como adicionar uma sobreposição poligonal a um mapa a fim de realçar uma determinada região dele. Polígonos são uma forma fechada e têm seus interiores preenchidos.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 45a85c8ac2f40e92078d46f5765aaf2af1518b77
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650736"
---
# <a name="highlighting-a-region-on-a-map"></a>Realçando uma região em um mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon)

_Este artigo explicou como adicionar uma sobreposição poligonal a um mapa a fim de realçar uma determinada região. Polígonos são uma forma fechada e têm seus interiores preenchidos._

## <a name="overview"></a>Visão geral

Uma sobreposição é um gráfico em camadas em um mapa. As sobreposições são suporte para elaborar conteúdos gráficos que são dimensionados com o mapa conforme ele é ampliado e reduzido. As capturas de tela a seguir mostram o resultado do acréscimo de uma sobreposição de polígono a um mapa:

![](polygon-map-overlay-images/screenshots.png)

Quando um controle [`Map`](xref:Xamarin.Forms.Maps.Map) é renderizado por um aplicativo Xamarin.Forms, no iOS é criada uma instância da classe `MapRenderer`, o que por sua vez cria uma instância de um controle `MKMapView` nativo. Na plataforma Android, a classe `MapRenderer` cria uma instância de um controle `MapView` nativo. Na UWP (Plataforma Universal do Windows), a classe `MapRenderer` cria uma instância de um `MapControl` nativo. E possível aproveitar o processo de renderização para implementar personalizações de mapa específicas da plataforma criando um renderizador personalizado para um `Map` em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Map) um mapa personalizado do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Map) o mapa personalizado do Xamarin.Forms.
1. [Personalizar](#Customizing_the_Map) o mapa criando um renderizador personalizado para o mapa em cada plataforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) precisa ser inicializado e configurado antes do uso. Para obter mais informações, consulte [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Para obter informações sobre como personalizar um mapa usando um renderizador personalizado, confira [Personalizando um marcador de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Criando o mapa personalizado

Crie uma subclasse da classe [`Map`](xref:Xamarin.Forms.Maps.Map), que adiciona uma propriedade `ShapeCoordinates`:

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

A propriedade `ShapeCoordinates` armazenará uma coleção de coordenadas que definem a região a ser realçada.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Essa inicialização especifica uma série de coordenadas de latitude e longitude para definir a região do mapa a ser realçada. Em seguida, ela posiciona a exibição do mapa com o método [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), que altera a posição e o nível de zoom do mapa criando um [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) de um [`Position`](xref:Xamarin.Forms.Maps.Position) e um [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizando o mapa

Agora, é necessário adicionar um renderizador personalizado a cada projeto de aplicativo para adicionar a sobreposição de polígono ao mapa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição de polígono:

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

Esse método executa a seguinte configuração, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A propriedade `MKMapView.OverlayRenderer` é definida como um delegado correspondente.
- A coleção de coordenadas de latitude e longitude é recuperada da propriedade `CustomMap.ShapeCoordinates` e armazenada como uma matriz de instâncias de `CLLocationCoordinate2D`.
- O polígono é criado chamando o método `MKPolygon.FromCoordinates` estático, que especifica a latitude e a longitude de cada ponto.
- O polígono é adicionado ao mapa chamando o método `MKMapView.AddOverlay`. Esse método fecha automaticamente o polígono desenhando uma linha que conecta o primeiro e último pontos.

Em seguida, implemente o método `GetOverlayRenderer` para personalizar a renderização da sobreposição:

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

Crie uma subclasse da classe `MapRenderer` e substitua seus métodos `OnElementChanged` e `OnMapReady` para adicionar a sobreposição de polígono:

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

O método `OnElementChanged` recupera a coleção de coordenadas de latitude e longitude da propriedade `CustomMap.ShapeCoordinates` e a armazena em uma variável de membro. Em seguida, ele chama o método `MapView.GetMapAsync`, que obtém o `GoogleMap` subjacente que está vinculado à exibição, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms. Quando a instância de `GoogleMap` estiver disponível, o método `OnMapReady` será invocado e o polígono será criado instanciando um objeto `PolygonOptions` que especifica a latitude e a longitude de cada ponto. Em seguida, o polígono será adicionado ao mapa chamando o método `NativeMap.AddPolygon`. Esse método fecha automaticamente o polígono desenhando uma linha que conecta o primeiro e último pontos.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Criando o renderizador personalizado na Plataforma Universal do Windows

Crie uma subclasse da classe `MapRenderer` e substitua seu método `OnElementChanged` para adicionar a sobreposição de polígono:

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

Esse método executa as seguintes operações, desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:

- A coleção de coordenadas de latitude e longitude é recuperada da propriedade `CustomMap.ShapeCoordinates` e convertida em uma `List` de coordenadas `BasicGeoposition`.
- O polígono é criado instanciando um objeto `MapPolygon`. A classe `MapPolygon` é usada para exibir uma forma de vários pontos no mapa, definindo sua propriedade `Path` como um objeto `Geopath` que contém as coordenadas da forma.
- O polígono é renderizado no mapa adicionando-o à coleção `MapControl.MapElements`. Observe que o polígono será fechado automaticamente desenhando uma linha que conecta o primeiro e último pontos.

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar uma sobreposição poligonal a um mapa a fim de realçar uma determinada região. Polígonos são uma forma fechada e têm seus interiores preenchidos.


## <a name="related-links"></a>Links relacionados

- [Sobreposição de mapa com polígono (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon)
- [Personalizar um Pin de mapa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
