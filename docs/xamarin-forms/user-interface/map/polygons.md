---
title: Polígonos e polilinhas do mapa do Xamarin. Forms
description: Este artigo explica como criar polígonos e polilinhas em uma instância de mapa do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 42c00a060e0477aff4ea02f6213fa751b2adf4dd
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810489"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Polígonos e polilinhas do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[![demonstração de polígono e polilinha no iOS e no Android "](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

os elementos `Polygon` e `Polyline` permitem realçar áreas específicas em um mapa. Uma `Polygon` é uma forma totalmente fechada que pode ter uma cor de traço e preenchimento. Uma `Polyline` é uma linha que não coloca totalmente uma área.

> [!NOTE]
> Exemplos de `Polygon` e `Polyline` são encontrados no **PolygonsPage** no projeto de exemplo.

As classes `Polygon` e `Polyline` derivam de `MapElement`, que expõe as seguintes propriedades [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

- `StrokeColor` é uma propriedade `Color` que determina a cor da linha.
- `StrokeWidth` é uma propriedade `float` que determina a largura da linha.
- `Geopath` é definido em `Polygon` e `Polyline`e é uma lista de objetos [`Position`](xref:Xamarin.Forms.Maps.Position) que especificam os pontos da forma.

A classe `Polygon` define uma propriedade adicional:

- `FillColor` é uma propriedade `Color` que determina a cor do plano de fundo do polígono.

> [!NOTE]
> Se a propriedade `StrokeColor` não for especificada, o traço padrão será preto. Se a propriedade `FillColor` não for especificada, o preenchimento padrão será transparente. Portanto, se nenhuma propriedade for especificada, a forma terá uma estrutura de tópicos preta sem preenchimento.

## <a name="create-a-polygon"></a>Criar um polígono

Um objeto `Polygon` pode ser adicionado a um mapa instanciando-o e adicionando-o à coleção de `MapElements` do mapa. Isso pode ser feito no XAML da seguinte maneira:

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

As propriedades `StrokeColor` e `StrokeWidth` são especificadas para personalizar o contorno do polígono. O valor da propriedade `FillColor` corresponde ao valor da propriedade `StrokeColor`, mas tem um valor alfa especificado para torná-lo transparente, permitindo que o mapa subjacente fique visível por meio da forma. A propriedade `GeoPath` contém uma lista de objetos `Position` que definem as coordenadas geográficas dos pontos do polígono. Um objeto `Polygon` é renderizado no mapa depois que ele é adicionado à coleção `MapElements` do `Map`.

> [!NOTE]
> Uma `Polygon` é uma forma totalmente incluída. O primeiro e o último pontos serão automaticamente conectados se não corresponderem.

## <a name="create-a-polyline"></a>Criar uma polilinha

Um objeto `Polyline` pode ser adicionado a um mapa instanciando-o e adicionando-o à coleção de `MapElements` do mapa. Isso pode ser feito no XAML da seguinte maneira:

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

As propriedades `StrokeColor` e `StrokeWidth` são especificadas para personalizar a linha. A propriedade `GeoPath` contém uma lista de objetos `Position` que definem as coordenadas geográficas dos pontos de polilinha. Um objeto `Polyline` é renderizado no mapa depois que ele é adicionado à coleção `MapElements` do `Map`.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
