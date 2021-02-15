---
title: Xamarin.Forms Posição e distância do mapa
description: O Xamarin.Forms . O namespace Maps contém uma struct de posição que é normalmente usada ao posicionar um mapa e seus Pins, e uma estrutura de distância que pode, opcionalmente, ser usada ao posicionar um mapa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ccdcfc28e1d439b390459be242b959f53d0bd915
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367706"
---
# <a name="no-locxamarinforms-map-position-and-distance"></a>Xamarin.Forms Posição e distância do mapa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) namespace contém uma [`Position`](xref:Xamarin.Forms.Maps.Position) struct que é normalmente usada ao posicionar um mapa e seus PINs e uma [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que pode, opcionalmente, ser usada ao posicionar um mapa.

## <a name="position"></a>Posição

O [`Position`](xref:Xamarin.Forms.Maps.Position) struct encapsula uma posição armazenada como valores de latitude e longitude. Este struct define duas propriedades somente leitura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), do tipo `double` , que representa a latitude da posição em graus decimais.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), do tipo `double` , que representa a longitude da posição em graus decimais.

[`Position`](xref:Xamarin.Forms.Maps.Position) os objetos são criados com o `Position` Construtor, que requer argumentos de latitude e longitude especificados como `double` valores:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Ao criar um `Position` objeto, o valor de latitude será clamped entre-90,0 e 90,0, e o valor de longitude será clamped entre-180,0 e 180,0.

> [!NOTE]
> A `GeographyUtils` classe tem um `ToRadians` método de extensão que converte um `double` valor de graus em radianos e um `ToDegrees` método de extensão que converte um `double` valor de radianos em graus.

## <a name="distance"></a>Distância

A [`Distance`](xref:Xamarin.Forms.Maps.Distance) estrutura encapsula uma distância armazenada como um `double` valor, que representa a distância em metros. Essa estrutura define três propriedades somente leitura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), do tipo `double` , que representa a distância em quilômetros que é distribuída pelo `Distance` .
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), do tipo `double` , que representa a distância em metros que é distribuída pelo `Distance` .
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), do tipo `double` , que representa a distância em milhas que é distribuída pelo `Distance` .

[`Distance`](xref:Xamarin.Forms.Maps.Distance) os objetos podem ser criados com o `Distance` Construtor, que requer um argumento de medidores especificado como um `double` :

```csharp
Distance distance = new Distance(1450.5);
```

Como alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) os objetos podem ser criados com [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) os [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) métodos de fábrica,, [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) e `BetweenPositions` :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](/samples/xamarin/xamarin-forms-samples/workingwithmaps)