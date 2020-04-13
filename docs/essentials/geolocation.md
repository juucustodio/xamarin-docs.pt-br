---
title: 'Xamarin.Essentials: Geolocalização'
description: Este documento descreve a classe Geolocation no Xamarin.Essentials, que fornece APIs para recuperar as coordenadas atuais de geolocalização do dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 840aadcafea88ef08f53e16f535439be0862fee9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80070352"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Geolocalização

A classe **Geolocation** fornece APIs para recuperar as coordenadas atuais de geolocalização do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade de **Geolocalização**, a seguinte configuração específica da plataforma é necessária:

# <a name="android"></a>[Android](#tab/android)

As permissões Coarse Location e Fine Location são necessárias e devem ser configuradas no projeto do Android. Além disso, se o seu aplicativo for destinado ao Android 5.0 (API nível 21) ou superior, você deverá declarar que ele usa os recursos de hardware no arquivo de manifesto. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Ou atualize o manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Ou clique com o botão direito no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque as permissões **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

O **Info.plist** do seu aplicativo deve conter a chave `NSLocationWhenInUseUsageDescription` para acessar o local do dispositivo.

Abra o editor de plist e adicione a propriedade **Privacy - Location When In Use Usage Description** e preencha um valor a ser exibido ao usuário.

Ou edite manualmente o arquivo, adicione o seguinte e atualize a justificativa:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Você deve definir a permissão `Location` para o aplicativo. Isso pode ser feito abrindo o **Package.appxmanifest**, selecionando a guia **Funcionalidades** e marcando **Local**.

-----

## <a name="using-geolocation"></a>Usar a geolocalização

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A API de Geolocalização também solicitará ao usuário permissões quando for necessário.

Você pode obter o último [local](xref:Xamarin.Essentials.Location) conhecido do dispositivo chamando o método `GetLastKnownLocationAsync`. Isso geralmente é mais rápido do que fazer uma consulta completa, mas pode ser menos precisa e pode retornar `null` se não houver nenhum local armazenado em cache.

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
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
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

A altitude nem sempre está disponível. Se não estiver disponível, a propriedade `Altitude` poderá ser `null` ou o valor poderá ser zero. Se a altitude estiver disponível, o valor estará em metros acima do nível do mar. 

Para consultar as coordenadas do [local](xref:Xamarin.Essentials.Location) atual do dispositivo, use `GetLocationAsync`. É melhor passar um `GeolocationRequest` e `CancellationToken` completo, pois pode demorar um pouco para obter o local do dispositivo.

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
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
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

## <a name="geolocation-accuracy"></a>Precisão da geolocalização

A tabela a seguir descreve a precisão por plataforma:

### <a name="lowest"></a>O menor

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1.000–5.000 |

### <a name="low"></a>Baixo

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300–3.000 |

### <a name="medium-default"></a>Médio (padrão)

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 100–500 |
| iOS | 100 |
| UWP | 30–500 |

### <a name="high"></a>Alta

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 0–100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>Melhor

| Plataforma | Distância (em metros) |
| --- | --- |
| Android | 0–100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance" />

## <a name="detecting-mock-locations"></a>Detectando locais fictícios
Alguns dispositivos podem retornar um local fictício do provedor ou por um aplicativo que fornece locais fictícios. Você pode detectar isso `IsFromMockProvider` usando [`Location`](xref:Xamarin.Essentials.Location)o em qualquer .

```csharp
var request = new GeolocationRequest(GeolocationAccuracy.Medium);
var location = await Geolocation.GetLocationAsync(request);

if (location != null)
{
    if(location.IsFromMockProvider)
    {
        // location is from a mock provider
    }
}
```

## <a name="distance-between-two-locations"></a>Distância entre dois locais

As [`Location`](xref:Xamarin.Essentials.Location) [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) classes `CalculateDistance` definem métodos que permitem calcular a distância entre dois locais geográficos. Essa distância calculada não considera estradas ou outros caminhos e é simplesmente a distância mais curta entre os dois pontos ao longo da superfície da Terra, também conhecido como _ortodromia_ ou, coloquialmente, a distância "em linha reta".

Aqui está um exemplo:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

O construtor `Location` tem argumentos de latitude e longitude, nessa ordem. Os valores de latitude positiva estão ao norte do Equador, e os valores de longitude positiva estão a leste do Meridiano primário. Use o argumento final para `CalculateDistance` a fim de especificar milhas ou quilômetros. A classe `UnitConverters` também define os métodos `KilometersToMiles` e `MilesToKilometers` para conversão entre as duas unidades.

## <a name="platform-differences"></a>Diferenças entre plataformas

A altitude é calculada de forma diferente em cada plataforma.

# <a name="android"></a>[Android](#tab/android)

No Android, [a altitude](https://developer.android.com/reference/android/location/Location#getAltitude()), se disponível, é devolvida em metros acima do elipsóide de referência WGS 84. Se este local não tiver uma altitude, então 0.0 é devolvido.

# <a name="ios"></a>[iOS](#tab/ios)

No iOS, [a altitude](https://developer.apple.com/documentation/corelocation/cllocation/1423820-altitude) é medida em metros. Valores positivos indicam altitudes acima do nível do mar, enquanto valores negativos indicam altitudes abaixo do nível do mar.

# <a name="uwp"></a>[UWP](#tab/uwp)

Na UWP, a altitude é devolvida em metros. Consulte a documentação [altitudeReferenceSystem](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint.altitudereferencesystem#Windows_Devices_Geolocation_Geopoint_AltitudeReferenceSystem) para obter mais informações.

-----

## <a name="api"></a>API

- [Código-fonte de geolocalização](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentação da API de Geolocalização](xref:Xamarin.Essentials.Geolocation)
