---
title: 'Xamarin.Essentials: Informações do dispositivo'
description: Este documento descreve a classe DeviceInfo no Xamarin.Essentials, que fornece informações sobre o dispositivo no qual o aplicativo está em execução.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1cab4ea8ea3f98def4830e101783db1554efa69c
ms.sourcegitcommit: 099b06e311a40c00eeea85465ff9b97867a5c5de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295411"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Informações do dispositivo

A classe **DeviceInfo** fornece informações sobre o dispositivo no qual o aplicativo está em execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Como usar DeviceInfo

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

As informações a seguir são expostas por meio da API:

```csharp
// Device Model (SMG-950U, iPhone10,6)
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

## <a name="platforms"></a>Plataformas

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) correlaciona com uma cadeia de caracteres constante que mapeia para o sistema operacional. Os valores podem ser verificados com a classe `DevicePlatform`:

- **DevicePlatform.iOS** – iOS
- **DevicePlatform.Android** – Android
- **DevicePlatform.UWP** – UWP
- **DevicePlatform.Unknown** – Desconhecido

## <a name="idioms"></a>Linguagens

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) correlaciona uma cadeia de caracteres constante que mapeia para o tipo de dispositivo em que o aplicativo está sendo executado. Os valores podem ser verificados com a classe `DeviceIdiom`:

- **DeviceIdiom.Phone** – Telefone
- **DeviceIdiom.Tablet** – Tablet
- **DeviceIdiom.Desktop** – Área de trabalho
- **DeviceIdiom.TV** – TV
- **DeviceIdiom.Watch** – Observação
- **DeviceIdiom.Unknown** – Desconhecido

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` correlaciona uma enumeração para determinar se o aplicativo está em execução em um dispositivo físico ou virtual. Um dispositivo virtual é um simulador ou emulador.

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="ios"></a>[iOS](#tab/ios)

o iOS não expõe uma API para que os desenvolvedores obtenham o modelo do dispositivo iOS específico. Em vez disso, um identificador de hardware é retornado como _iPhone10, 6,_ que se refere ao iPhone X. Um mapeamento desses identificadores não é fornecido pela Apple, mas pode ser encontrado nessas (fontes não oficiais) [o wiki do iPhone](https://www.theiphonewiki.com/wiki/Models) e [obter o modelo do IOS](https://github.com/dannycabrera/Get-iOS-Model).

--------------

## <a name="api"></a>API

- [Código-fonte de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentação da API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
