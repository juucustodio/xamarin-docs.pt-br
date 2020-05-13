---
title: 'Xamarin.Essentials: Informações do dispositivo'
description: Este documento descreve a classe DeviceInfo no Xamarin.Essentials, que fornece informações sobre o dispositivo no qual o aplicativo está em execução.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1790e950dfccddcca84adf97bcff64f905ee59f6
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150084"
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

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform)correlaciona com uma cadeia de caracteres constante que mapeia para o sistema operacional. Os valores podem ser verificados com a classe `DevicePlatform`:

- **DevicePlatform.iOS** – iOS
- **DevicePlatform.Android** – Android
- **DevicePlatform.UWP** – UWP
- **DevicePlatform.Unknown** – Desconhecido

## <a name="idioms"></a>Linguagens

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom)correlaciona uma cadeia de caracteres constante que mapeia para o tipo de dispositivo no qual o aplicativo está sendo executado. Os valores podem ser verificados com a classe `DeviceIdiom`:

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

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
