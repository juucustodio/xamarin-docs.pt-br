---
title: 'Xamarin.Essentials: geocodificação'
description: A classe geocodificação Xamarin.Essentials fornece APIs para ambos os geocode um placemark para uma posição coordenadas e reverter geocode coordenadas para um placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 063adba82d96e7fcc64d7ec49a0c0133e1cef8ef
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080320"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: geocodificação

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **geocodificação** classe fornece APIs para geocode um placemark para uma posição coordenadas e reverter geocode coordincates um placemark.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **geocodificação** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nenhuma configuração adicional necessária.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Uma chave de API do Bing maps é necessário para usar funcationality geocodificação. Inscreva-se para uma livre [Bing Maps](https://www.bingmapsportal.com/) conta. Em **minha conta > Minhas chaves** criar uma nova chave e preencha as informações com base em seu tipo de aplicativo (que deve ser **pública aplicativo do Windows (UWP, 8. x e anteriores)** para aplicativos UWP).

Desde o início de vida do aplicativo antes de chamar qualquer **geocodificação** métodos definir a chave de API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Usando geocodificação

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Obtendo [local](xref:Xamarin.Essentials.Location) coordenadas de um endereço:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

A altitude não está sempre disponível. Se não estiver disponível, o `Altitude` propriedade pode ser `null` ou o valor pode ser zero. Se a altitude estiver disponível, o valor é em metros acima acima do nível do mar. 

Obtendo [placemarks](xref:Xamarin.Essentials.Placemark) para um conjunto existente de coordenadas:

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>Distância entre dois locais

O [ `Location` ](xref:Xamarin.Essentials.Location) e [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) classes definem métodos para calcular a distância entre dois locais. Consulte o artigo [ **Xamarin.Essentials: localização geográfica** ](geolocation.md#calculate-distance) para obter um exemplo.

## <a name="api"></a>API

- [Código-fonte geocodificação](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentação da API de geocodificação](xref:Xamarin.Essentials.Geocoding)
