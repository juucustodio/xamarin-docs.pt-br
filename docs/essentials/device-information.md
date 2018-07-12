---
title: 'Xamarin.Essentials: Informações do dispositivo'
description: Este documento descreve a classe DeviceInfo Xamarin.Essentials, que fornece informações sobre o dispositivo, o aplicativo está sendo executado.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7246afca19607ef2f70288d4643696f4ac35d52
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831481"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Informações do dispositivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DeviceInfo** classe fornece informações sobre o dispositivo, o aplicativo está sendo executado.

## <a name="using-deviceinfo"></a>Usando DeviceInfo

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

As informações a seguir são expostas por meio da API:

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

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Expressões](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` Correlaciona uma cadeia de caracteres constante que é mapeado para o tipo de dispositivo, o aplicativo está em execução. Os valores podem ser verificados com o `Idioms` classe:

- **DeviceInfo.Idioms.Phone** – telefone
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** – área de trabalho
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** – sem suporte

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` Correlaciona uma enumeração para determinar se o aplicativo está em execução no físico ou dispositivo virtual. Um dispositivo virtual é um simulador ou emulador.

## <a name="api"></a>API

- [Código-fonte DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentação da API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
