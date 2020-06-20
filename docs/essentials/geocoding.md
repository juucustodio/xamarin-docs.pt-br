---
title: 'Xamarin.Essentials: Geocodificação'
description: A classe geocodification no Xamarin.Essentials fornece APIs para geocodificar um placemark em coordenadas posicionais e reverter coordenadas geocódigo a um placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 66383e441435b5f4bdb48224c9ab602f28072b3d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802335"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Geocodificação

A classe **Geocoding** fornece APIs para definir o código geográfico de um marcador de local para coordenadas posicionais e reverter as coordenadas de código geográfico para um marcador de local.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Geocodificação**, a seguinte configuração específica da plataforma é necessária.

# <a name="android"></a>[Android](#tab/android)

Não exige mais configurações.

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Uma chave de API do Bing Maps é necessária para usar a funcionalidade de geocodificação. Inscreva-se em uma conta gratuita do [Bing Maps](https://www.bingmapsportal.com/). Em **Minha conta > Minhas chaves** crie uma nova chave e preencha as informações com base em seu tipo de aplicativo, que deve ser **Aplicativo Windows Público (UWP, 8.x e anteriores)** para aplicativos UWP.

No início da vida de seu aplicativo, antes de chamar qualquer método de **Geocodificação**, defina a chave de API (que está disponível somente para UWP):

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Como usar Geocoding

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Obter coordenadas de [local](xref:Xamarin.Essentials.Location) de um endereço:

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
    // Handle exception that may have occurred in geocoding
}
```

A altitude nem sempre está disponível. Se não estiver disponível, a propriedade `Altitude` poderá ser `null` ou o valor poderá ser zero. Se a altitude estiver disponível, o valor estará em metros acima do nível do mar.

## <a name="using-reverse-geocoding"></a>Uso da geocodificação reversa

Geocodificação reversa é o processo de obtenção de [marcadores de local](xref:Xamarin.Essentials.Placemark) para um conjunto existente de coordenadas:

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

As [`Location`](xref:Xamarin.Essentials.Location) [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) classes e definem métodos para calcular a distância entre dois locais. Consulte o artigo [** Xamarin.Essentials : geolocalização**](geolocation.md#calculate-distance) para obter um exemplo.

## <a name="api"></a>API

- [Código-fonte de Geocodificação](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Geocoding)
- [Documentação da API de Geocodificação](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
