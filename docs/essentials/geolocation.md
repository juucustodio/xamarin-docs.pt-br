---
title: 'Xamarin.Essentials: localização geográfica'
description: Este documento descreve a classe de localização geográfica Xamarin.Essentials, que fornece APIs para recuperar as coordenadas de localização geográfica do dispositivo atual.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 11749107403fc99e1d49b63ee3b50ff105abaa57
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848745"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: localização geográfica

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **geolocalização** classe fornece APIs para recuperar as coordenadas de localização geográfica do dispositivo atual.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **geolocalização** funcionalidade, a seguinte configuração específica da plataforma é necessária:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Permissões de grande e um bom local são necessárias e devem ser configuradas no projeto do Android. Além disso, se seu aplicativo for destinado ao Android 5.0 (API nível 21) ou superior, você deve declarar que seu aplicativo usa os recursos de hardware no arquivo de manifesto. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** do arquivo sob o **propriedades** pasta e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Ou atualize o manifesto do Android:

Abra o **androidmanifest. XML** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Ou clique com botão direito no projeto do Android e abra as propriedades do projeto. Sob **manifesto do Android** localizar o **permissões necessárias:** área e verifique se o **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**permissões. Isso atualizará automaticamente a **androidmanifest. XML** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Seu aplicativo **Info. plist** deve conter o `NSLocationWhenInUseUsageDescription` chave para acessar o local do dispositivo.

Abra o editor de plist e adicione a **privacidade - local quando em uso descrição de uso** propriedade e um valor para exibir o usuário de preenchimento.

Ou edite o arquivo-o manualmente e adicione o seguinte:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Você deve definir o `Location` permissão para o aplicativo. Isso pode ser feito abrindo o **Package. appxmanifest** e selecing a **funcionalidades** guia e verificando **local**.

-----

## <a name="using-geolocation"></a>Usando a localização geográfica

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A API Geoloation também solicitará ao usuário para permissões quando necessário.

Você pode obter o último conhecido [local](xref:Xamarin.Essentials.Location) do dispositivo chamando o `GetLastKnownLocationAsync` método. Isso geralmente é mais rápido, em seguida, fazer uma consulta completa, mas pode ser menos preciso.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

A altitude nem sempre está disponível. Se não estiver disponível, o `Altitude` propriedade pode ser `null` ou o valor pode ser zero. Se a altitude estiver disponível, o valor é em metros acima acima do nível do mar. 

Para consultar o dispositivo atual [local](xref:Xamarin.Essentials.Location) coordenadas, a `GetLocationAsync` pode ser usado. É melhor transmitir uma completa `GeolocationRequest` e `CancellationToken` , pois ele pode levar algum tempo para obter o local do dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

A tabela a seguir descreve a precisão por plataforma:

### <a name="lowest"></a>O menor

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | -1000 a 5000 |

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

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>Distância entre dois locais

O [ `Location` ](xref:Xamarin.Essentials.Location) e [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) classes definem `CalculateDistance` métodos que permitem que você calcular a distância entre duas localizações geográficas. Isso calculado distância não consideram estradas ou outros caminhos e é simplesmente a distância mais curta entre os dois pontos ao longo de superfície da Terra, também conhecido como o _distância de grande círculo_ ou coloquialmente, o distância "como os arquivos de galinha."

Veja um exemplo:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

O `Location` construtor tem argumentos de latitude e longitude, nessa ordem. Os valores de latitude positivos são ao norte do Equador e valores de longitude positivos são Leste do Meridiano principal. Use o argumento final para `CalculateDistance` especificar milhas ou quilômetros. O `Location` classe define também `KilometersToMiles` e `MilesToKilometers` métodos para converter entre as duas unidades.

## <a name="api"></a>API

- [Código-fonte de localização geográfica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentação da API de localização geográfica](xref:Xamarin.Essentials.Geolocation)
