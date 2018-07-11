---
ms.topic: include
ms.openlocfilehash: 5c11c94956f8d56c66c50a9a480177c5b77c2643
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947426"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor com o mais rápido possível (não é garantida para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface do usuário geral.

Se seu manipulador de eventos não é garantido para executar no thread da interface do usuário e se o manipulador de eventos precisa acessar os elementos de interface do usuário, use o [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) método para executar esse código no thread da interface do usuário.