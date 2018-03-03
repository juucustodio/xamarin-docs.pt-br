---
title: Recursos de plataforma
description: "Recursos de específicos de Apple Watch para incluir em aplicativos de watchOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 16e779761aa164ea9890547e3baca527a3592021
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="platform-features"></a>Recursos de plataforma

_Recursos de específicos de Apple Watch para incluir em aplicativos de watchOS._

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Aprimoramentos de pagamento da Apple](~/ios/watchos/platform/apple-pay.md)

WatchOS 3, a estrutura de PassKit foi expandida para permitir que o suporte para pagamentos de seguros, no aplicativo (de bens físicos e serviços) para os aplicativos em execução no Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações de garantir que ele tenha o conteúdo, o usuário precisa antes de abri-lo.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introdução ao watchOS 4](introduction-to-watchos4.md)

Novos recursos do watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introdução ao watchOS 3](introduction-to-watchos3/index.md)

Este artigo apresenta todas as APIs de novas e modificadas disponíveis no watchOS 3 para desenvolvedores do Xamarin.

##  <a name="notificationsnotificationsmd"></a>[Notificações](notifications.md)

Saiba como fornecer tratamento em seu aplicativo de inspeção de notificação personalizada.

##  <a name="complicationscomplicationsmd"></a>[Complicações](complications.md)

Adicione suporte complicativo para exibir os dados atualizados na face do relógio.


## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugestões proativos](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite que o aplicativo proativamente apresentar informações ao usuário em contextos de dado. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui o `MapItem` propriedade que permite que o aplicativo forneça informações de local para uso posterior por outros aplicativos.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interação rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornecendo interação do usuário rápido é essenciais para a criação de aplicativos Apple Watch atraentes e complicações. Novo para watchOS 3, Apple adicionou suporte para reconhecedores de gestos, acesso às novas técnicas de notificação do usuário e a navegação e coroa Digital. Isso, juntamente com o suporte adicionado para SceneKit e SpriteKit, permitem que o desenvolvedor crie facilmente interfaces avançadas e rápidas, rápida e responsivo.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Aprimoramentos de aplicativo de ginástica](~/ios/watchos/platform/workout-apps.md)

Novo watchOS 3, treinamento relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch. Para habilitar esse recurso (e obter acesso aos dados HealthKit), o aplicativo deve incluir o `WKBackgroundModes` chave no `Info.plist` arquivo com o valor `workout-processing`.
