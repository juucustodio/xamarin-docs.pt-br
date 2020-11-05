---
title: Xamarin.Forms Mapear geocodificação
description: Este artigo explica como geocodificar e reverter dados de mapa de código, usando o Xamarin.Forms . Mapeia a classe geocodificadora.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12ba6e8f9bb1c547ad5a15e2e79f74c29b74ad12
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375441"
---
# <a name="no-locxamarinforms-map-geocoding"></a>Xamarin.Forms Mapear geocodificação

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) namespace fornece uma [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe, que converte entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em [`Position`](xref:Xamarin.Forms.Maps.Position) objetos. Para obter mais informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md).

> [!NOTE]
> Uma API de geocodificação alternativa é avalible no Xamarin.Essentials . A Xamarin.Essentials `Geocoding` API oferece dados de endereço estruturados ao geocodificar endereços, em oposição às cadeias de caracteres retornadas por essa API. Para obter mais informações, consulte [ Xamarin.Essentials : geocodificação](~/essentials/geocoding.md).

## <a name="geocode-an-address"></a>Geocódigo de um endereço

Um endereço de rua pode ser geocodificado em coordenadas de latitude e longitude criando uma [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instância e chamando o [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) método na `Geocoder` instância:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

O [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) método usa um `string` argumento que representa o endereço e retorna de forma assíncrona uma coleção de [`Position`](xref:Xamarin.Forms.Maps.Position) objetos que podem representar o endereço.

## <a name="reverse-geocode-an-address"></a>Reverter código a um endereço

As coordenadas de latitude e longitude podem ser revertidas geocodificadas em um endereço criando uma [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instância e chamando o [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) método na `Geocoder` instância:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

O [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) método usa um [`Position`](xref:Xamarin.Forms.Maps.Position) argumento composto por coordenadas de latitude e longitude e retorna de forma assíncrona uma coleção de cadeias de caracteres que representam os endereços próximos à posição.

## <a name="related-links"></a>Links relacionados

- [Exemplo de mapas](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms Posição e distância do mapa](position-distance.md)
- [API geocodificadora](xref:Xamarin.Forms.Maps.Geocoder)