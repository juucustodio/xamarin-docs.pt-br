---
title: Conversores de unidade do Xamarin.Essentials
description: A classe UnitConverters no Xamarin.Essentials fornece vários conversores de unidade para ajudar os desenvolvedores ao usar o Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: c07e0c7d9645c22f0d70c75fd7d8dffdec8cde04
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545039"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Conversores de unidade

A classe **UnitConverters** fornece vários conversores de unidade para ajudar os desenvolvedores ao usar o Xamarin.Essentials.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Usando conversores de unidade

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Todos os conversores de unidade estão disponíveis com o uso da classe `UnitConverters` estática no Xamarin.Essentials. Por exemplo, você pode converter facilmente Fahrenheit em Celsius.

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
