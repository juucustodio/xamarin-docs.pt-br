---
title: 'Xamarin.Essentials: bateria'
description: Este documento descreve a classe de bateria Xamarin.Essentials, que permite que você verifique o monitor para que as alterações e informações sobre a bateria do dispositivo.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6b87625b3305d0a9ec40593d8b3fe29eb551bbf4
ms.sourcegitcommit: bf05041cc74fb05fd906746b8ca4d1403fc5cc7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2018
ms.locfileid: "39514304"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: bateria

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **bateria** classe permite que você verifique informações sobre a bateria do dispositivo e o monitor para que as alterações.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **bateria** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

O `Battery` permissão é necessária e deve ser configurada no projeto do Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** do arquivo sob o **propriedades** pasta e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

OU atualize o manifesto do Android:

Abra o **androidmanifest. XML** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Ou clique com botão direito no projeto do Android e abra as propriedades do projeto. Sob **manifesto do Android** localizar o **permissões necessárias:** área e verifique se o **bateria** permissão. Isso atualizará automaticamente a **androidmanifest. XML** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-battery"></a>Uso da bateria

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Verifique as informações sobre a bateria atual:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Sempre que qualquer uma das propriedades da bateria for alterada, um evento é disparado:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(object sender, BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Não há diferenças de plataforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Dispositivo deve ser usado para testar APIs. 
* Só retornará `AC` ou `Battery` para `PowerSource`.
* Não é possível cancelar a vibração.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Só retornará `AC` ou `Battery` para `PowerSource`.

-----

## <a name="api"></a>API

- [Código-fonte da bateria](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentação da API de bateria](xref:Xamarin.Essentials.Battery)
