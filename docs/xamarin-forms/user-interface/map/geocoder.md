---
title: Geocodificação de mapas do Xamarin. Forms
description: Este artigo explica como geocodificar e reverter dados de mapa de código, usando a classe geocodificadora Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: ce1f6751c0381ed41058784fbea3ebedefbdac6d
ms.sourcegitcommit: e4c23187874488ff55794d0e81a9bba30d2c2cd6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72778778"
---
# <a name="xamarinforms-map-geocoding"></a>Geocodificação de mapas do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O Xamarin. Forms. Maps fornece a classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) , que converte entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em objetos [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="geocode-an-address"></a>Geocódigo de um endereço

Um endereço de rua pode ser geocodificado em coordenadas de latitude e longitude criando uma instância de [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) e chamando o método [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) na instância `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

O método [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) usa um argumento `string` que representa o endereço e retorna de forma assíncrona uma coleção de objetos [`Position`](xref:Xamarin.Forms.Maps.Position) que pode representar o endereço.

## <a name="reverse-geocode-an-address"></a>Reverter código a um endereço

As coordenadas de latitude e longitude podem ser revertidas geocodificadas em um endereço criando uma instância de [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) e chamando o método [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) na instância `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

O método [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) usa um argumento [`Position`](xref:Xamarin.Forms.Maps.Position) composto de coordenadas de latitude e longitude e retorna de forma assíncrona uma coleção de cadeias de caracteres que representam os endereços próximos à posição.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [API geocodificadora](xref:Xamarin.Forms.Maps.Geocoder)
