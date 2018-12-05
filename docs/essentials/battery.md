---
title: 'Xamarin.Essentials: Bateria'
description: Este documento descreve a classe Battery no Xamarin.Essentials, que permite que você verifique as informações da bateria do dispositivo e monitore a ocorrência de alterações.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6a14c939064538a405a1fe64061e0bb2e903fedd
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675426"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Bateria

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Battery** permite que você verifique informações da bateria do dispositivo e monitore a ocorrência de alterações.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Battery**, a configuração específica da plataforma a seguir é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

A permissão `Battery` é necessária e deve ser configurada no projeto do Android. Ela pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque a permissão **Bateria**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

## <a name="using-battery"></a>Como usar Battery

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Verifique as informações atuais da bateria:

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

Sempre que qualquer uma das propriedades da bateria for alterada, um evento será disparado:

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

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sem diferenças entre plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

* O dispositivo deve ser usado para testar APIs. 
* Só retornará `AC` ou `Battery` para `PowerSource`.
* Não é possível cancelar a vibração.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* Só retornará `AC` ou `Battery` para `PowerSource`.

-----

## <a name="api"></a>API

- [Código-fonte de Bateria](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentação da API de Bateria](xref:Xamarin.Essentials.Battery)
