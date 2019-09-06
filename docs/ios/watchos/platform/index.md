---
title: recursos da plataforma watchOS
description: Este documento contém links para vários guias que descrevem os recursos da plataforma watchOS, como Apple Pay, notificações, complicações, sugestões proativas, aplicativos de treinamento e muito mais.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 2b987992bcb3dd4d2575a46e21a2302ed78d8d70
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282281"
---
# <a name="watchos-platform-features"></a>recursos da plataforma watchOS

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introdução ao watchOS 5](introduction-to-watchos5/index.md)

Este documento fornece uma visão geral de alto nível dos recursos novos e atualizados do watchOS 5 que estão disponíveis para uso na criação de aplicativos watchOS com o Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introdução ao watchOS 4](introduction-to-watchos4.md)

Este documento fornece uma visão geral de alto nível dos recursos adicionados e atualizados no watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introdução ao watchOS 3](introduction-to-watchos3/index.md)

Este artigo descreve as APIs novas e atualizadas no watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Aprimoramentos de Apple Pay](~/ios/watchos/platform/apple-pay.md)

No watchOS 3, a estrutura PassKit foi expandida para permitir o suporte a pagamentos em aplicativo seguros (de bens e serviços físicos) para os aplicativos em execução no Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md)

o watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações, garantindo que ele tenha o conteúdo de que o usuário precisa antes de abri-lo.

## <a name="notificationsnotificationsmd"></a>[Notificações](notifications.md)

Saiba como fornecer manipulação de notificação personalizada em seu aplicativo Watch.

## <a name="complicationscomplicationsmd"></a>[Complicações](complications.md)

Adicione suporte à complicação para exibir dados atualizados no rosto de inspeção.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Sugestões proativas](~/ios/watchos/platform/proactive-suggestions.md)

o watchOS 3 permite que o aplicativo apresente informações de forma proativa para o usuário nos contextos fornecidos. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora `MapItem` inclui a propriedade que permite que o aplicativo forneça informações de local para uso posterior por outros aplicativos.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Técnicas de interação rápida](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornecer interações rápidas do usuário é essencial para criar aplicativos Apple Watch atraentes e complicações. Novo no watchOS 3, a Apple adicionou suporte para reconhecedores de gestos, acesso ao Digital Crown e novas técnicas de notificação e navegação de usuário. Isso, juntamente com suporte adicional para SceneKit e SpriteKit, permitem que o desenvolvedor crie facilmente interfaces avançadas e resumidas que são rápidas e responsivas.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Aprimoramentos do aplicativo de treinamento](~/ios/watchos/platform/workout-apps.md)

Novo no watchOS 3, os aplicativos relacionados ao treinamento têm a capacidade de executar em segundo plano na Apple Watch. Para habilitar esse recurso (e obter acesso aos dados do HealthKit), o aplicativo deve incluir `WKBackgroundModes` a chave `Info.plist` no arquivo com o valor `workout-processing`.
