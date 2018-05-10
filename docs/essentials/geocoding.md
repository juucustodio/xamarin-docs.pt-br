---
title: Xamarin.Essentials geocodificação
description: A classe geocodificação fornece APIs para geocode um placemark para uma posição coordenadas e reverter geocode coordincates um placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bfc463ca22d4137eadc35c02375fe41b0b99b8a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials geocodificação

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **geocodificação** classe fornece APIs para geocode um placemark para uma posição coordenadas e reverter geocode coordincates um placemark.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **geocodificação** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nenhuma configuração adicional necessária.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Uma chave de API do Bing maps é necessário para usar funcationality geocodificação. Inscreva-se para uma livre [Bing Maps](https://www.bingmapsportal.com/) conta. Em **minha conta > Minhas chaves** criar uma nova chave e preencha as informações com base em seu tipo de aplicativo.

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
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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
    // Handle exception that may have occured in geocoding
}
```

## <a name="api"></a>API

- [Código-fonte geocodificação](https://github.com/xamarin/Essentials/tree/master/Essentials/Geocoding)
- [Documentação da API de geocodificação](xref:Xamarin.Essentials.Geocoding)
