---
title: Geocodificação de mapas do Xamarin. Forms
description: Este artigo explica como geocodificar e reverter dados de mapa de código, usando a classe geocodificadora Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: 9a20618fea0091979c2ea862f417dccec565b218
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425612"
---
# <a name="xamarinforms-map-geocoding"></a>Geocodificação de mapas do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O namespace [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) fornece uma classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) , que converte entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em objetos [`Position`](xref:Xamarin.Forms.Maps.Position) . Para obter mais informações sobre a estrutura [`Position`](xref:Xamarin.Forms.Maps.Position) , consulte [posição e distância do mapa](position-distance.md).

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
- [Posição e distância do mapa do Xamarin. Forms](position-distance.md)
- [API geocodificadora](xref:Xamarin.Forms.Maps.Geocoder)
