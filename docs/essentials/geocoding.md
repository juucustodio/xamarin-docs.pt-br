---
title: 'Xamarin.Essentials: Geocódigo'
description: A classe Geocoding no Xamarin.Essentials fornece APIs para definir o código geográfico de um marcador de local para coordenadas posicionais e reverter as coordenadas de código geográfico para um marcador de local.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 157eb3116f09268790036f8983543114e7a58276
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354102"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Geocódigo

A classe **Geocoding** fornece APIs para definir o código geográfico de um marcador de local para coordenadas posicionais e reverter as coordenadas de código geográfico para um marcador de local.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Geocodificação**, a seguinte configuração específica da plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Não exige mais configurações.

# <a name="iostabios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Uma chave de API do Bing Maps é necessária para usar a funcionalidade de geocodificação. Inscreva-se em uma conta gratuita do [Bing Maps](https://www.bingmapsportal.com/). Em **Minha conta > Minhas chaves** crie uma nova chave e preencha as informações com base em seu tipo de aplicativo, que deve ser **Aplicativo Windows Público (UWP, 8.x e anteriores)** para aplicativos UWP.

No início da vida de seu aplicativo, antes de chamar qualquer método de **Geocodificação**, defina a chave de API (que está disponível somente para UWP):

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Como usar Geocoding

Adicione uma referência ao Xamarin.Essentials na classe:

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

As classes [`Location`](xref:Xamarin.Essentials.Location) e [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) definem métodos para calcular a distância entre dois locais. Confira um exemplo no artigo [**Xamarin.Essentials: geolocalização**](geolocation.md#calculate-distance).

## <a name="api"></a>API

- [Código-fonte de Geocodificação](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentação da API de Geocodificação](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
