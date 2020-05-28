---
title: Xamarin.FormsMapear geocodificação
description: Este artigo explica como geocodificar e reverter dados de mapa de código, usando o Xamarin.Forms . Mapeia a classe geocodificadora.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fe099235857f6bd0531539e3aa84e41bf59b50ba
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139861"
---
# <a name="xamarinforms-map-geocoding"></a>Xamarin.FormsMapear geocodificação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

O [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) namespace fornece uma [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe, que converte entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em [`Position`](xref:Xamarin.Forms.Maps.Position) objetos. Para obter mais informações sobre a [`Position`](xref:Xamarin.Forms.Maps.Position) estrutura, consulte [posição e distância do mapa](position-distance.md).

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

- [Exemplo de mapas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.FormsPosição e distância do mapa](position-distance.md)
- [API geocodificadora](xref:Xamarin.Forms.Maps.Geocoder)
