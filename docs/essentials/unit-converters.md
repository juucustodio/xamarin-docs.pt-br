---
title: Xamarin.EssentialsConversores de unidade
description: A classe UnitConverters no Xamarin.Essentials fornece vários conversores de unidade para ajudar os desenvolvedores ao usarem o Xamarin.Essentials .
ms.assetid: ''
author: ''
ms.custom: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: deff6997ff921e6048ccb2497a0747c770501a04
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137131"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Conversores de unidade

A classe **UnitConverters** fornece vários conversores de unidade para ajudar os desenvolvedores ao usarem o Xamarin.Essentials .

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Usando conversores de unidade

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Todos os conversores de unidade estão disponíveis usando a `UnitConverters` classe estática no Xamarin.Essentials . Por exemplo, você pode converter facilmente Fahrenheit em Celsius.

```csharp
var celsius = UnitConverters.FahrenheitToCelsius(32.0);
```

Veja esta lista das conversões disponíveis:

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
- MetersToInternationalFeet
- InternationalFeetToMeters
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- RadiansPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToRadiansPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToPascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers
- KilogramsToPounds
- PoundsToKilograms
- StonesToPounds
- PoundsToStones

## <a name="api"></a>API

- [Código-fonte de conversores de unidade](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentação da API de conversores de unidade](xref:Xamarin.Essentials.UnitConverters)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Unit-Conversion-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
