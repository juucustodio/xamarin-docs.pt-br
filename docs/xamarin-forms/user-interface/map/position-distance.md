---
title: Posição e distância do mapa do Xamarin. Forms
description: O namespace Xamarin. Forms. Maps contém uma estrutura de posição que é normalmente usada ao posicionar um mapa e seus PINs e uma estrutura de distância que pode, opcionalmente, ser usada ao posicionar um mapa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426149"
---
# <a name="xamarinforms-map-position-and-distance"></a>Posição e distância do mapa do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O namespace [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) contém uma struct [`Position`](xref:Xamarin.Forms.Maps.Position) que é normalmente usada ao posicionar um mapa e seus pins, e um [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que pode, opcionalmente, ser usado ao posicionar um mapa.

## <a name="position"></a>Posição

O [`Position`](xref:Xamarin.Forms.Maps.Position) struct encapsula uma posição armazenada como valores de latitude e longitude. Este struct define duas propriedades somente leitura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), do tipo `double`, que representa a latitude da posição em graus decimais.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), do tipo `double`, que representa a longitude da posição em graus decimais.

[`Position`](xref:Xamarin.Forms.Maps.Position) objetos são criados com o Construtor `Position`, que requer argumentos de latitude e longitude especificados como valores `double`:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> Ao criar um objeto de `Position`, o valor de latitude será clamped entre-90,0 e 90,0, e o valor de longitude será clamped entre-180,0 e 180,0.

## <a name="distance"></a>Alcance

O [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct encapsula uma distância armazenada como um valor de `double`, que representa a distância em metros. Essa estrutura define três propriedades somente leitura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), do tipo `double`, que representa a distância em quilômetros que é distribuída pelo `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), do tipo `double`, que representa a distância em metros que é distribuída pelo `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), do tipo `double`, que representa a distância em milhas que é distribuída pelo `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance) objetos podem ser criados com o Construtor `Distance`, que requer um argumento de medidores especificado como um `double`:

```csharp
Distance distance = new Distance(1450.5);
```

Como alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) objetos podem ser criados com os métodos de fábrica [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*), [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)e [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
