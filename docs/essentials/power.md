---
title: 'Xamarin.Essentials: Power Energy Saver Status'
description: A classe Power permite que um programa obtenha o status da economia de energia para determinar se o dispositivo está operando em um modo de baixa energia.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 96b4aef3a8df571392d43836d46b03b025c80888
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675374"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Power Energy Saver Status

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Power** oferece informações sobre o status da economia de energia do dispositivo, que indica se o dispositivo está operando em um modo de baixa energia. Os aplicativos devem evitar o processamento em segundo plano se o status da economia de energia do dispositivo estiver ativado.

## <a name="background"></a>Informações preliminares

Os dispositivos que funcionam com baterias podem ser colocados em um modo de baixo consumo de energia. Por vezes, os dispositivos passam para este modo automaticamente, por exemplo, quando a bateria fica abaixo dos 20% de capacidade. O sistema operacional responde ao modo de economia de energia reduzindo atividades que tendem a esgotar a bateria. Os aplicativos podem ajudar evitando o processamento em segundo plano ou outras atividades que consomem muita energia quando o modo de economia de energia está ativado.

Para dispositivos Android, a classe **Power** retornará informações significativas somente para a versão do Android 5.0 (Lollipop) e superior.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-the-power-class"></a>Uso da classe Power

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Obtenha o status atual da economia de energia do dispositivo usando a propriedade `Power.EnergySaverStatus` estática:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Essa propriedade retorna um membro da enumeração `EnergySaverStatus`, que é `On`, `Off` ou `Unknown`. Se a propriedade retornar `On`, o aplicativo deverá evitar o processamento em segundo plano ou outras atividades que possam consumir muita energia.

O aplicativo também deve instalar um manipulador de eventos. A classe **Power** expõe um evento que é acionado quando o status da economia de energia é alterado:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se o status da economia de energia for alterado para `On`, o aplicativo deverá interromper a execução do processamento em segundo plano. Se o status mudar para `Unknown` ou `Off`, o aplicativo poderá retomar o processamento em segundo plano.

## <a name="api"></a>API

- [Código-fonte de Power](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentação da API de Power](xref:Xamarin.Essentials.Power)
