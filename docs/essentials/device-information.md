---
title: 'Xamarin.Essentials: Informações sobre o dispositivo'
description: Este documento descreve a classe DeviceInfo no Xamarin.Essentials, que fornece informações sobre o dispositivo no qual o aplicativo está em execução.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 29b67e4410a3549e4e2cd63121ffa2e2205e0941
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940812"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Informações sobre o dispositivo

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

# <a name="iostabios"></a>[iOS](#tab/ios)

O iOS não expõe uma API para os desenvolvedores obterem o nome do dispositivo iOS específico. Em vez disso, um identificador de hardware retorna, como _iPhone10.6_, que se refere ao iPhone X. A Apple não fornece um mapeamento desses identificadores, mas isso pode ser encontrado no [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (uma fonte não oficial).

--------------

## <a name="api"></a>API

- [Código-fonte de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentação da API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
