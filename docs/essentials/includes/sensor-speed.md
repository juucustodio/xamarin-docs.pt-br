---
ms.topic: include
ms.openlocfilehash: b82ebeaeb28195e3ec0f5a0200c0448b6b76196a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259467"
---
## <a name="sensor-speed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obtenha os dados do sensor o mais rápido possível (não é garantido retornar no thread de interface do usuário).
- **Jogo** – taxa adequada para jogos (não é garantido retornar no thread de interface do usuário).
- **Padrão** – Taxa padrão adequada para alterações de orientação de tela.
- **Interface do usuário** – taxa adequada para a interface do usuário geral.

Se o manipulador de eventos não tiver garantia de ser executado no segmento de interface [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) do usuário e se o manipulador de eventos precisar acessar elementos de interface do usuário, use o método para executar esse código no segmento de interface do usuário.
