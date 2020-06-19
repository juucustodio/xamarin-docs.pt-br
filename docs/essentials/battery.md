---
title: 'Xamarin.Essentials: Bateria'
description: Este documento descreve a classe Battery no Xamarin.Essentials , que permite que você verifique as informações da bateria do dispositivo e monitore as alterações.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 01/22/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3138db09c2a3d343f36aa48f561930df47f48ae6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802479"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Bateria

A classe **Battery** permite que você verifique as informações sobre a bateria do dispositivo e monitore as alterações, além de fornecer informações sobre o status de economia de energia do dispositivo, o que indica se o dispositivo está em execução em um modo de baixa energia. Os aplicativos devem evitar o processamento em segundo plano se o status da economia de energia do dispositivo estiver ativado.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Battery**, a configuração específica da plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

A permissão `Battery` é necessária e deve ser configurada no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte no nó do **manifesto** .

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque a permissão **Bateria**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

## <a name="using-battery"></a>Como usar Battery

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Verifique as informações atuais da bateria:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

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
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

Os dispositivos que funcionam com baterias podem ser colocados em um modo de baixo consumo de energia. Por vezes, os dispositivos passam para este modo automaticamente, por exemplo, quando a bateria fica abaixo dos 20% de capacidade. O sistema operacional responde ao modo de economia de energia reduzindo atividades que tendem a esgotar a bateria. Os aplicativos podem ajudar evitando o processamento em segundo plano ou outras atividades que consomem muita energia quando o modo de economia de energia está ativado.

Obtenha o status atual da economia de energia do dispositivo usando a propriedade `Battery.EnergySaverStatus` estática:

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

Essa propriedade retorna um membro da enumeração `EnergySaverStatus`, que é `On`, `Off` ou `Unknown`. Se a propriedade retornar `On`, o aplicativo deverá evitar o processamento em segundo plano ou outras atividades que possam consumir muita energia.

O aplicativo também deve instalar um manipulador de eventos. A classe **Power** expõe um evento que é acionado quando o status da economia de energia é alterado:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se o status da economia de energia for alterado para `On`, o aplicativo deverá interromper a execução do processamento em segundo plano. Se o status mudar para `Unknown` ou `Off`, o aplicativo poderá retomar o processamento em segundo plano.

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

Sem diferenças entre plataformas.

# <a name="ios"></a>[iOS](#tab/ios)

- O dispositivo deve ser usado para testar APIs.
- Só retornará `AC` ou `Battery` para `PowerSource`.

# <a name="uwp"></a>[UWP](#tab/uwp)

- Só retornará `AC` ou `Battery` para `PowerSource`.

-----

## <a name="api"></a>API

- [Código-fonte de Bateria](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Battery)
- [Documentação da API de Bateria](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
