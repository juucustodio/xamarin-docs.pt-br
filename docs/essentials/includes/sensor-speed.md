---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 676c5a6795ab4896ccd1b288424bf2040b1208aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2018
ms.locfileid: "52294799"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obtenha os dados do sensor o mais rápido possível (não é garantido retornar no thread de interface do usuário).
- **Jogo** – taxa adequada para jogos (não é garantido retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequada para a interface do usuário geral.

Se o manipulador de eventos não tiver a garantia de ser executado no thread da interface do usuário e se precisar acessar os elementos de interface do usuário, use o método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para executar esse código no thread da interface do usuário.