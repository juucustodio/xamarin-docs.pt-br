---
title: Polígonos do mapa do Xamarin. Forms, polilinhas e círculos
description: Este artigo explica como criar polígonos, polilinhas e círculos em uma instância de mapa do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: e1edbc4d7376023c9d3051b0518c8dc7368e63a7
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517331"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Polígonos e polilinhas do mapa do Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

`Polygon`os `Polyline`elementos, `Circle` e permitem realçar áreas específicas em um mapa. Um `Polygon` é uma forma totalmente incluída que pode ter uma cor de preenchimento e de traçado. Um `Polyline` é uma linha que não coloca totalmente uma área. Um `Circle` realça uma área circular do mapa:

[!["Captura de tela de um polígono de mapa e Polyline, na](polygons-images/polygon-polyline.png "Polígono e polilinha em um mapa")](polygons-images/polygon-polyline-large.png#lightbox "Polígono e polilinha em um mapa")
[![captura de tela do IOS e do Android" de um círculo de mapa, no Ios e no Android "](polygons-images/circle.png "Círculo em um mapa")](polygons-images/circle-large.png#lightbox "Círculo em um mapa")

As `Polygon`classes `Polyline`, e `Circle` derivam da `MapElement` classe, que expõe as seguintes propriedades vinculáveis:

- `StrokeColor`é um `Color` objeto que determina a cor da linha.
- `StrokeWidth`é um `float` objeto que determina a largura da linha.

A `Polygon` classe define uma propriedade ligável adicional:

- `FillColor`é um `Color` objeto que determina a cor do plano de fundo do polígono.

Além disso, as `Polygon` classes `Polyline` e definem uma `GeoPath` Propriedade, que é uma lista de [`Position`](xref:Xamarin.Forms.Maps.Position) objetos que especificam os pontos da forma.

A `Circle` classe define as seguintes propriedades vinculáveis:

- `Center`é um [`Position`](xref:Xamarin.Forms.Maps.Position) objeto que define o centro do círculo, em latitude e longitude.
- `Radius`é um [`Distance`](xref:Xamarin.Forms.Maps.Distance) objeto que define o raio do círculo em metros, quilômetros ou milhas.
- `FillColor`é uma `Color` propriedade que determina a cor dentro do perímetro do círculo.

> [!NOTE]
> Se a `StrokeColor` propriedade não for especificada, o traço padrão será preto. Se a `FillColor` propriedade não for especificada, o preenchimento padrão será transparente. Portanto, se nenhuma propriedade for especificada, a forma terá uma estrutura de tópicos preta sem preenchimento.

## <a name="create-a-polygon"></a>Criar um polígono

Um `Polygon` objeto pode ser adicionado a um mapa, instanciando-o e adicionando-o à coleção `MapElements` do mapa. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

As `StrokeColor` propriedades `StrokeWidth` e são especificadas para personalizar o contorno do polígono. O `FillColor` valor da propriedade corresponde `StrokeColor` ao valor da propriedade, mas tem um valor alfa especificado para torná-lo transparente, permitindo que o mapa subjacente fique visível por meio da forma. A `GeoPath` propriedade contém uma lista de `Position` objetos que definem as coordenadas geográficas dos pontos do polígono. Um `Polygon` objeto é renderizado no mapa depois que ele é adicionado à `MapElements` coleção do. `Map`

> [!NOTE]
> Um `Polygon` é uma forma totalmente incluída. O primeiro e o último pontos serão automaticamente conectados se não corresponderem.

## <a name="create-a-polyline"></a>Criar uma polilinha

Um `Polyline` objeto pode ser adicionado a um mapa, instanciando-o e adicionando-o à coleção `MapElements` do mapa. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

As `StrokeColor` propriedades `StrokeWidth` e são especificadas para personalizar a linha. A `GeoPath` propriedade contém uma lista de `Position` objetos que definem as coordenadas geográficas dos pontos de polilinha. Um `Polyline` objeto é renderizado no mapa depois que ele é adicionado à `MapElements` coleção do. `Map`

## <a name="create-a-circle"></a>Criar um círculo

Um `Circle` objeto pode ser adicionado a um mapa, instanciando-o e adicionando-o à coleção `MapElements` do mapa. Isso pode ser feito no XAML da seguinte maneira:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
      <maps:Map>
          <maps:Map.MapElements>
              <maps:Circle StrokeColor="#88FF0000"
                           StrokeWidth="8"
                           FillColor="#88FFC0CB">
                  <maps:Circle.Center>
                      <maps:Position>
                          <x:Arguments>
                              <x:Double>37.79752</x:Double>
                              <x:Double>-122.40183</x:Double>
                          </x:Arguments>
                      </maps:Position>
                  </maps:Circle.Center>
                  <maps:Circle.Radius>
                      <maps:Distance>
                          <x:Arguments>
                              <x:Double>250</x:Double>
                          </x:Arguments>
                      </maps:Distance>
                  </maps:Circle.Radius>
              </maps:Circle>             
          </maps:Map.MapElements>
          ...
      </maps:Map>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map();

// Instantiate a Circle
Circle circle = new Circle
{
    Center = new Position(37.79752, -122.40183);,
    Radius = new Distance(250),
    StrokeColor = Color.FromHex("#88FF0000"),
    StrokeWidth = 8,
    FillColor = Color.FromHex("#88FFC0CB")
};

// Add the Circle to the map's MapElements collection
map.MapElements.Add(circle);
```

O local do `Circle` no mapa é determinado pelo valor das propriedades `Center` e. `Radius` A `Center` propriedade define o centro do círculo, na latitude e longitude, enquanto a `Radius` propriedade define o raio do círculo em metros. As `StrokeColor` propriedades `StrokeWidth` e são especificadas para personalizar o contorno do círculo. O `FillColor` valor da propriedade especifica a cor dentro do perímetro do círculo. Os dois valores de cor especificam um canal alfa, permitindo que o mapa subjacente fique visível por meio do círculo. O `Circle` objeto é renderizado no mapa depois que ele é adicionado à `MapElements` coleção do. `Map`

> [!NOTE]
> A `GeographyUtils` classe tem um `ToCircumferencePositions` método de extensão que converte `Circle` um objeto (que `Center` define `Radius` e valores de propriedade) em uma `Position` lista de objetos que compõem as coordenadas de latitude e longitude do perímetro do círculo.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
