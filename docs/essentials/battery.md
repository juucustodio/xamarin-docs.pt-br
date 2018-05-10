---
title: Bateria Xamarin.Essentials
description: A classe de bateria permite verificar informações sobre a bateria do dispositivo e monitore as alterações.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e7993a8febe0cb03bbe3dec4cbd5897e7730213f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-battery"></a>Bateria Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **bateria** classe permite que você verifique as informações sobre a bateria e monitore as alterações.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **bateria** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

O `Battery` permissão é necessária e deve ser configurada no projeto Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** arquivo sob o **propriedades** pasta e adicionar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

OU atualize o manifesto do Android:

Abra o **AndroidManifest.xml** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

Ou clique com o botão direito no projeto Android e abra as propriedades do projeto. Em **manifesto do Android** encontrar o **as permissões necessárias:** área e verifique se o **bateria** permissão. Isso atualizará automaticamente o **AndroidManifest.xml** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-battery"></a>Uso da bateria

Adicione uma referência a Xamarin.Essentials em sua classe:

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
    case BatteryPowerSource.Ac:
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

Sempre que qualquer um dos chagne de propriedades da bateria um evento é acionado:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Diferenças de plataforma

| Plataforma | Diferença |
| --- | --- |
| iOS | Dispositivo deve ser usado para testar as APIs. |
| iOS | Somente retorna CA ou bateria para PowerSource. |
| UWP | Somente retorna CA ou bateria para PowerSource. |

## <a name="api"></a>API

- [Código-fonte da bateria](https://github.com/xamarin/Essentials/tree/master/Essentials/Battery)
- [Documentação da API de bateria](xref:Xamarin.Essentials.Battery)
