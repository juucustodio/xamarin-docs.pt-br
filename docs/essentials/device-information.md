---
title: Informações do dispositivo Xamarin.Essentials
description: A classe DeviceInfo fornece informações sobre o dispositivo, o aplicativo está sendo executado.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3a9fc352336f67375b732247560f8c1d4dd45f70
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-device-information"></a>Informações do dispositivo Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DeviceInfo** classe fornece informações sobre o dispositivo que o aplicativo está em execução no.

## <a name="using-deviceinfo"></a>Usando DeviceInfo

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

As informações a seguir são exibidas por meio da API:

```csharp
// Device Model (SMG-950U)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[Plataformas](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` se correlaciona com uma cadeia de caracteres constante que é mapeado para o sistema operacional. Os valores podem ser verificados com o `Platforms` classe:

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** – Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – sem suporte

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idiomas](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` Correlaciona uma cadeia de caracteres constante que é mapeado para o tipo de dispositivo que o aplicativo está em execução. Os valores podem ser verificados com o `Idioms` classe:

- **DeviceInfo.Idioms.Phone** – telefone
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** – área de trabalho
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** – sem suporte

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` Correlaciona uma enumeração para determinar se o aplicativo está executando em um físico ou dispositivo virtual. Um dispositivo virtual é um simulador ou emulador do Windows.

## <a name="api"></a>API

- [Código-fonte DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Essentials/DeviceInfo)
- [Documentação da API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
