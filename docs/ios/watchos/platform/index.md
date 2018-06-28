---
title: recursos da plataforma watchOS
description: Links este documento em várias guias que descrevem os recursos da plataforma watchOS como pagamento da Apple, notificações, complicações, pró-ativo sugestões, aplicativos de treinamento e muito mais.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 16d10dd69223f404aac7c933302992a1544461e9
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066608"
---
# <a name="watchos-platform-features"></a>recursos da plataforma watchOS

Links este documento em várias guias que descrevem os recursos da plataforma watchOS como pagamento da Apple, notificações, complicações, pró-ativo sugestões, aplicativos de treinamento e muito mais.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introdução ao watchOS 4](introduction-to-watchos4.md)

Este documento fornece uma visão geral dos recursos adicionados e atualizados em watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introdução ao watchOS 3](introduction-to-watchos3/index.md)

Este artigo descreve as APIs de novas e atualizadas em watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Aprimoramentos de pagamento da Apple](~/ios/watchos/platform/apple-pay.md)

WatchOS 3, a estrutura de PassKit foi expandida para permitir que o suporte para pagamentos de seguros, no aplicativo (de bens físicos e serviços) para os aplicativos em execução no Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md)

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações de garantir que ele tenha o conteúdo, o usuário precisa antes de abri-lo.

## <a name="notificationsnotificationsmd"></a>[Notificações](notifications.md)

Saiba como fornecer tratamento em seu aplicativo de inspeção de notificação personalizada.

## <a name="complicationscomplicationsmd"></a>[Complicações](complications.md)

Adicione suporte complicativo para exibir os dados atualizados na face do relógio.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugestões proativos](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 permite que o aplicativo proativamente apresentar informações ao usuário em contextos de dado. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui o `MapItem` propriedade que permite que o aplicativo forneça informações de local para uso posterior por outros aplicativos.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interação rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornecendo interação do usuário rápido é essenciais para a criação de aplicativos Apple Watch atraentes e complicações. Novo para watchOS 3, Apple adicionou suporte para reconhecedores de gestos, acesso às novas técnicas de notificação do usuário e a navegação e coroa Digital. Isso, juntamente com o suporte adicionado para SceneKit e SpriteKit, permitem que o desenvolvedor crie facilmente interfaces avançadas e rápidas, rápida e responsivo.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Aprimoramentos de aplicativo de ginástica](~/ios/watchos/platform/workout-apps.md)

Novo watchOS 3, treinamento relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch. Para habilitar esse recurso (e obter acesso aos dados HealthKit), o aplicativo deve incluir o `WKBackgroundModes` chave no `Info.plist` arquivo com o valor `workout-processing`.
