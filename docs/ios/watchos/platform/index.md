---
title: recursos de plataforma do watchOS
description: Este documento leva a vários guias que descrevem os recursos da plataforma do watchOS, como Apple Pay, notificações, complicações, sugestões proativas, aplicativos de treinamento e muito mais.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 09200ba5968838edf829b30a50a8ad0f4a3ab3aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224554"
---
# <a name="watchos-platform-features"></a>recursos de plataforma do watchOS

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introdução ao watchOS 5](introduction-to-watchos5/index.md)

Este documento fornece uma visão geral dos recursos novos e atualizados no watchOS 5 que estão disponível para uso ao criar aplicativos watchOS com Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introdução ao watchOS 4](introduction-to-watchos4.md)

Este documento fornece uma visão geral dos recursos adicionados e atualizados no watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introdução ao watchOS 3](introduction-to-watchos3/index.md)

Este artigo descreve as APIs de novas e atualizadas no watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Melhorias do Apple Pay](~/ios/watchos/platform/apple-pay.md)

No watchOS 3, PassKit framework foi expandido para permitir que o suporte para pagamentos de seguros, no aplicativo (de bens físicos e serviços) para os aplicativos em execução na Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações garantindo que ele tenha o conteúdo que o usuário precisa antes de abri-lo.

## <a name="notificationsnotificationsmd"></a>[Notificações](notifications.md)

Saiba como fornecer tratamento em seu aplicativo de inspeção de notificação personalizada.

## <a name="complicationscomplicationsmd"></a>[Complicações](complications.md)

Adicione suporte a complicação para exibir dados atualizados na face do relógio.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugestões proativas](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite que o aplicativo apresente proativamente informações para o usuário dentro de contextos de dado. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui o `MapItem` propriedade que permite que o aplicativo fornecer informações de local para uso posterior por outros aplicativos.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interação rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Interações do usuário rápida fornecendo são essenciais para criar aplicativos atraentes do Apple Watch e complicações. Novo ao watchOS 3, a Apple adicionou suporte para reconhecedores de gestos, acesso aos Crown Digital e novas técnicas de notificação do usuário e a navegação. Isso, juntamente com o suporte adicionado para SceneKit e SpriteKit, permitem ao desenvolvedor criar com facilidade interfaces avançadas e glanceable que são rápidos e responsivos.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Aprimoramentos de aplicativo de ginástica](~/ios/watchos/platform/workout-apps.md)

Novo watchOS 3, ginástica relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch. Para habilitar esse recurso (e obter acesso aos dados HealthKit), o aplicativo deve incluir a `WKBackgroundModes` principais na `Info.plist` arquivo com o valor `workout-processing`.
