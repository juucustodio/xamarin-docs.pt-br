---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353263"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor com o mais rápido possível (não é garantida para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface do usuário geral.

Se seu manipulador de eventos não é garantido para executar no thread da interface do usuário e se o manipulador de eventos precisa acessar os elementos de interface do usuário, use o [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) método para executar esse código no thread da interface do usuário.