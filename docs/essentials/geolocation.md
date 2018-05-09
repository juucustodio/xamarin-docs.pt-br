---
title: Localização geográfica do Xamarin.Essentials
description: A classe de localização geográfica fornece APIs para recuperar as coordenadas atuais de localização geográfica do dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 51e63245cd6959f650a0aa078cc4632bc825de5b
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials geocodificação

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **geolocalização** classe fornece APIs para recuperar as coordenadas atuais de localização geográfica do dispositivo.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **geolocalização** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Permissões de grande e um bom local são necessárias e devem ser configuradas no projeto Android. Além disso, se seu aplicativo tem como alvo Android 5.0 (API nível 21) ou superior, você deve declarar que seu aplicativo usa os recursos de hardware no arquivo de manifesto. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** arquivo sob o **propriedades** pasta e adicionar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

OU atualize o manifesto do Android:

Abra o **AndroidManifest.xml** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Ou clique com o botão direito no projeto Android e abra as propriedades do projeto. Em **manifesto do Android** encontrar o **as permissões necessárias:** área e verifique se o **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**permissões. Isso atualizará automaticamente o **AndroidManifest.xml** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Seu aplicativo é necessário ter chaves no seu **Info. plist** para NSLocationWhenInUseUsageDescription para acessar o local do dispositivo.

Abra o editor plist e adicionar o **privacidade - local quando em uso descrição do uso** propriedade e um valor para exibir o usuário de preenchimento.

OU editar o arquivo-o manualmente e adicione o seguinte:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Você deve definir o `Location` permissão para o aplicativo. Isso pode ser feito por abrir o **Package. appxmanifest** e selecing o **recursos** guia e a verificação de **local**.

-----

## <a name="using-geolocation"></a>Usando a localização geográfica

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A API Geoloation também solicitará ao usuário para permissões quando necessário.

Você pode obter o último valor conhecido [local](xref:Xamarin.Essentials.Location) do dispositivo ao chamar o `GetLastKnownLocationAsync` método. Isso geralmente é mais rápido, em seguida, fazer uma consulta completa, mas pode ser menos preciso.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

Para consultar o dispositivo atual [local](xref:Xamarin.Essentials.Location) coordenadas a `GetLocationAsync` pode ser usado. É recomendável para passar um completo `GeolocationRequest` e `CancellationToken` pois ele pode demorar algum tempo para obter o local do dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Precisão de localização geográfica

A tabela a seguir descreve a precisão por plataforma

### <a name="lowest"></a>O menor

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>Baixo

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>Médio (padrão)

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>Alta

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>Melhor

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | < = 10 |

## <a name="api"></a>API

- [Código-fonte localização geográfica](https://github.com/xamarin/Essentials/tree/master/Essentials/Geolocation)
- [Documentação da API de localização geográfica](xref:Xamarin.Essentials.Geolocation)
