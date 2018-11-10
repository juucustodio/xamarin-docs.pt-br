---
title: 'Xamarin.Essentials: Informações do dispositivo'
description: Este documento descreve a classe DeviceInfo no Xamarin.Essentials, que fornece informações sobre o dispositivo no qual o aplicativo está em execução.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530b04446703d78452357b2c9f9089e59ebf6e6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674807"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Informações do dispositivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

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

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[Plataformas](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` correlaciona com uma cadeia de caracteres constante que mapeia para o sistema operacional. Os valores podem ser verificados com a classe `Platforms`:

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** – Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – Sem suporte

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idioms](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` correlaciona uma cadeia de caracteres constante que mapeia para o tipo de dispositivo em que o aplicativo está sendo executado. Os valores podem ser verificados com a classe `Idioms`:

- **DeviceInfo.Idioms.Phone** – Telefone
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** – Desktop
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** – Sem suporte

## <a name="device-type"></a>Tipo de dispositivo

`DeviceInfo.DeviceType` correlaciona uma enumeração para determinar se o aplicativo está em execução em um dispositivo físico ou virtual. Um dispositivo virtual é um simulador ou emulador.

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="iostabios"></a>[iOS](#tab/ios)

O iOS não expõe uma API para os desenvolvedores obterem o nome do dispositivo iOS específico. Em vez disso, um identificador de hardware retorna, como _iPhone10.6_, que se refere ao iPhone X. A Apple não fornece um mapeamento desses identificadores, mas isso pode ser encontrado no [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (uma fonte não oficial).

--------------

## <a name="api"></a>API

- [Código-fonte de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentação da API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
