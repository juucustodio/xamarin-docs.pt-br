---
title: 'Xamarin.Essentials: Status de economia de energia de energia'
description: A classe de energia permite que um programa para obter o status de economia de energia para determinar se o dispositivo está operando em um modo de baixa energia.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080432"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Status de economia de energia de energia

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **Power** classe fornece informações sobre o status de economia de energia do dispositivo, que indica se o dispositivo está em execução em um modo de baixa energia. Aplicativos devem evitar o processamento em segundo plano se o status de economia de energia do dispositivo.

## <a name="background"></a>Informações preliminares

Dispositivos que executam em baterias podem ser colocados em um modo de economia de energia de baixa energia. Às vezes, dispositivos são alternados para este modo automaticamente, por exemplo, quando a bateria cai abaixo de 20% da capacidade. O sistema operacional responde para o modo de economia de energia, reduzindo as atividades que tendem a depauperam a bateria. Os aplicativos podem ajudar, evitando o processamento em segundo plano ou outras atividades alta quando o modo de economia de energia está ativado.

Para dispositivos Android, o **Power** classe retorne informações significativas somente para Android versão 5.0 (pirulito) e posterior.

## <a name="using-the-power-class"></a>Usando a classe de energia

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Obter o status atual de economia de energia do dispositivo usando o estático `Power.EnergySaverStatus` propriedade:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Essa propriedade retorna um membro do `EnergySaverStatus` enumeração, que é `On`, `Off`, ou `Unknown`. Se a propriedade retornar `On`, o aplicativo deve evitar o processamento em segundo plano ou outras atividades que podem consumir uma grande quantidade de energia.

O aplicativo também deve instalar um manipulador de eventos. O **Power** classe expõe um evento que é disparado quando muda o status de economia de energia:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se o status de economia de energia for alterado para `On`, o aplicativo deve parar de executar o processamento em segundo plano. Se o status é alterado para `Unknown` ou `Off`, o aplicativo pode retomar o processamento em segundo plano.

## <a name="api"></a>API

- [Código de fonte de energia](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentação da API de energia](xref:Xamarin.Essentials.Power)
