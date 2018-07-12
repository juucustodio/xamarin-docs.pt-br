---
title: 'Xamarin.Essentials: Status de economia de energia de energia'
description: A classe de energia permite que um programa para obter o status de economia de energia para determinar se o dispositivo está operando no modo de baixa energia.
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831514"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials: Status de economia de energia de energia

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **energia** classe fornece informações sobre o status de economia de energia do dispositivo, que indica se o dispositivo está em execução em um modo de baixa energia. Aplicativos devem evitar o processamento em segundo plano se o status de economia de energia do dispositivo.

## <a name="background"></a>Informações preliminares

Dispositivos que executam o com baterias podem ser colocados em um modo de proteção de energia de baixa energia. Às vezes, dispositivos são alternados esse modo automaticamente, por exemplo, quando a bateria está abaixo de 20% da capacidade. O sistema operacional responde ao modo de economia de energia, reduzindo as atividades que tendem a depauperam a bateria. Os aplicativos podem ajudar, evitando o processamento em segundo plano ou outras atividades alta quando o modo de economia de energia está ativado.

Para dispositivos Android, o **energia** classe retornará informações significativas somente para a versão do Android 5.0 (Lollipop) e acima.

## <a name="using-the-power-class"></a>Usando a classe de energia

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Obter o status atual de economia de energia do dispositivo usando estático `Power.EnergySaverStatus` propriedade:

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

Essa propriedade retorna um membro de `EnergySaverStatus` enumeração, que é `On`, `Off`, ou `Unknown`. Se a propriedade retornar `On`, o aplicativo deve evitar o processamento em segundo plano ou outras atividades que podem consumir uma grande quantidade de energia.

O aplicativo também deve instalar um manipulador de eventos. O **energia** classe expõe um evento que é disparado quando muda o status de economia de energia:

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

Se o status de economia de energia for alterado para `On`, o aplicativo deve parar de executar o processamento em segundo plano. Se o status muda para `Unknown` ou `Off`, o aplicativo possa retomar o processamento em segundo plano.

## <a name="api"></a>API

- [Código-fonte de energia](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Documentação da API de energia](xref:Xamarin.Essentials.Power)
