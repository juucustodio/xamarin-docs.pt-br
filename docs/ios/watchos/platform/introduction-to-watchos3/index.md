---
title: Introdução ao watchOS 3
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no watchOS 3 para desenvolvedores do Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2017
ms.openlocfilehash: ec43c98b891ee1541be9b1ac2563e280730628e3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430500"
---
# <a name="introduction-to-watchos-3"></a>Introdução ao watchOS 3

_Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no watchOS 3 para desenvolvedores do Xamarin._

Este documento abordará os seguintes tópicos:

- [O que há de novo no watchOS 3](#Whats-New-in-watchOS-3)
  - Os [aprimoramentos de Apple Pay](#Apple-Pay-Enhancements) adicionam suporte para pagamentos no aplicativo no Apple Watch.
  - As [tarefas em segundo plano](#Background-Tasks) fornecem ao aplicativo a capacidade de atualizar suas informações em segundo plano para que ela esteja pronta quando o usuário precisar.
  - [Aprimoramentos de complicações](#Complications-Enhancements) foram feitos para watchOS 3 que fornecem novos recursos para os aplicativos.
  - As [estruturas disponíveis recentemente](#Newly-Available-Frameworks) foram expostas para os aplicativos watchOS.
  - As [sugestões proativas](#Proactive-Suggestions) permitem que o aplicativo mostre informações ao usuário de forma proativa.
  - Vários [aprimoramentos de segurança e privacidade](#Security-and-Privacy-Enhancements) foram feitos no watchOS 3.
  - [Instantâneos e Dock](#Snapshots-and-Dock) fornecem ao usuário acesso rápido aos aplicativos watchOS de aplicativo.
  - As [notificações do usuário](#User-Notifications) fornecem notificações locais e remotas ao usuário.
  - Vários [aprimoramentos da estrutura de conectividade do Watch](#Watch-Connectivity-Framework-Enhancements) foram feitos no watchOS 3.
  - Vários [aprimoramentos do WatchKit Framework](#WatchKit-Framework-Enhancements) foram feitos no watchOS 3.
  - Os [aprimoramentos do aplicativo de treinamento](#Workout-App-Enhancements) oferecem novas capacidades para os aplicativos de Apple Watch relacionados ao treinamento.
- [Alterações de estrutura adicionais](#Additional-Framework-Changes) foram feitas em todo o watchOS 3.
- [APIs preteridas](#Deprecated-APIs) no watchOS 3.

<a name="Whats-New-in-watchOS-3"></a>

## <a name="whats-new-in-watchos-3"></a>O que há de novo no watchOS 3

A Apple adicionou várias novas APIs e serviços no watchOS 3, juntamente com vários aprimoramentos nos recursos existentes, incluindo:

<a name="Apple-Pay-Enhancements"></a>

## <a name="apple-pay-enhancements"></a>Aprimoramentos de Apple Pay

No watchOS 3, a estrutura PassKit foi expandida para permitir o suporte a pagamentos em aplicativo seguros (de bens e serviços físicos) para os aplicativos em execução no Apple Watch.

Use as novas classes [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) para apresentar e responder a uma interface na qual o usuário pode autorizar solicitações de pagamento.

Para saber mais, consulte nosso guia de [aprimoramentos de Apple Pay](~/ios/watchos/platform/apple-pay.md) .

<a name="Background-Tasks"></a>

## <a name="background-tasks"></a>Background Tasks

o watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações, garantindo que ele tenha o conteúdo de que o usuário precisa antes de abri-lo.

As novas tarefas em segundo plano a seguir estão disponíveis:

- **Atualização do aplicativo em segundo plano** – a tarefa [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permite que o aplicativo atualize seu estado em segundo plano. Geralmente, isso incluirá outra tarefa, como baixar novo conteúdo da Internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Atualização de instantâneo em segundo plano** – a tarefa [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permite que o aplicativo atualize seu conteúdo e a interface do usuário antes que o sistema assuma um instantâneo que será usado para popular o encaixe.
- **Conectividade de inspeção em segundo plano** – a tarefa [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) é iniciada para o aplicativo quando recebe dados em segundo plano do iPhone emparelhado.
- **Sessão de URL em segundo plano** -a tarefa [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou é concluída (com êxito ou com erro).

Para saber mais, consulte nosso guia de [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) .

<a name="Complications-Enhancements"></a>

## <a name="complications-enhancements"></a>Aprimoramentos de complicações

Complicações são elementos visuais pequenos que fornecem informações úteis em um relance. Dependendo do rosto de inspeção selecionado, o usuário tem a capacidade de personalizar um rosto de inspeção com uma ou mais complicação.

o watchOS 3 dá ao aplicativo a capacidade de criar uma ou mais complicação para o aplicativo Watch, para que o usuário possa acessar suas informações rapidamente a partir de um rosto de inspeção.

Além disso, as complicações fornecem os seguintes benefícios:

- O usuário pode iniciar rapidamente o aplicativo tocando na complicação diretamente de um rosto de inspeção.
- Ter uma das complicações do aplicativo na face de inspeção faz com que o sistema mantenha o aplicativo em um estado pronto para iniciar em que ele tenta iniciar o aplicativo em segundo plano, mantê-lo na memória e dá tempo extra para atualizar.
- As complicações são garantidas pelo menos 50 atualizações por Push por dia.
- Quando o aplicativo incluir complicações, ele será apresentado na Galeria de face Apple Watch.

No watchOS 3, a estrutura ClockKit agora inclui vários novos modelos para grandes complicações extras, como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Além disso, para criar texto localizável, use novos métodos da classe [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) .

Para saber mais, confira nossas [técnicas de interação rápida para o guia do watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Newly-Available-Frameworks"></a>

## <a name="newly-available-frameworks"></a>Estruturas recentemente disponibilizadas

o watchOS 3 inclui várias estruturas Apple existentes que anteriormente não estavam disponíveis, como:

- **SceneKit** -use o SceneKit para incluir modelos 3D na interface do usuário do aplicativo de inspeção, incluindo a maioria dos recursos disponíveis em outras plataformas, como iluminação, sombreamento, animação, física e sistemas de partículas. áudio espacial 3D, sombreadores de metal ou OpenGL personalizados, filtros de imagem de núcleo e materiais com base em físico não são suportados.
- **SpriteKit** -use SpriteKit para renderizar e animar sprites na interface do usuário do aplicativo de inspeção de aplicativos, incluindo a maioria dos recursos disponíveis em outras plataformas, como ações, física, iluminação e sistemas de partículas. Não há suporte para os filtros de áudio espacial 3D, reprodução de vídeo e imagem de núcleo.
- **AVFoundation** -para gerenciar e reproduzir áudio.
- **CloudKit** -para mover dados entre o aplicativo Watch e os contêineres do icloud.
- **Áudio principal** – para gerenciar os tipos de dados para representar fluxos de áudio, buffers complexos e valores de tempo.
- **GameKit** – criar jogos sociais.

<a name="Proactive-Suggestions"></a>

## <a name="proactive-suggestions"></a>Sugestões proativas

o watchOS 3 permite que o aplicativo apresente informações de forma proativa para o usuário nos contextos fornecidos. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui a `MapItem` propriedade que permite que o aplicativo forneça informações de local para uso posterior por outros aplicativos.

Para saber mais, consulte nosso guia [de introdução ao Proactive Suggestions](~/ios/watchos/platform/proactive-suggestions.md) .

<a name="Security-and-Privacy-Enhancements"></a>

## <a name="security-and-privacy-enhancements"></a>Aprimoramentos de segurança e privacidade

A Apple fez vários aprimoramentos na segurança e na privacidade no watchOS 3, que ajudarão o desenvolvedor a melhorar a segurança de seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos executados no watchOS 3 (ou posterior) devem declarar estaticamente sua intenção de acessar recursos específicos ou informações do usuário inserindo uma ou mais chaves de privacidade específicas em seus `Info.plist` arquivos que explicam ao usuário por que o aplicativo deseja obter acesso.

Como o watchOS 3 compartilha essas alterações com o iOS 10, consulte nosso guia de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) do IOS 10 para obter mais informações.

<a name="Snapshots-and-Dock"></a>

## <a name="snapshots-and-dock"></a>Instantâneos e encaixe

No watchOS 3, a Apple adicionou o Dock, onde os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral na Apple Watch, uma galeria de instantâneos de aplicativo afixados será exibida. O usuário pode passar o dedo para a esquerda ou direita para localizar o aplicativo desejado e, em seguida, tocar no aplicativo para iniciá-lo, substituindo o instantâneo pela interface do aplicativo em execução.

Periodicamente, o sistema usa instantâneos da interface do usuário do aplicativo e usa esses instantâneos para preencher os documentos. o watchOS dá ao aplicativo a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo seja feito.

Para obter mais informações, consulte nosso guia de [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) e a [referência de WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) da Apple.

<a name="User-Notifications"></a>

## <a name="user-notifications"></a>Notificações do usuário

A estrutura de notificação do usuário introduzida no watchOS 3 dá suporte à entrega de notificações locais e remotas para o Apple Watch. Use essa estrutura para agendar notificações com base em condições específicas, como hora do dia ou local, e para receber e manipular notificações.

Para saber mais, confira nossas [técnicas de interação rápida para o guia do watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Watch-Connectivity-Framework-Enhancements"></a>

## <a name="watch-connectivity-framework-enhancements"></a>Assista aos aprimoramentos da estrutura de conectividade

A nova `HasContentPending` propriedade da classe [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) indica que a sessão recebeu dados em segundo plano que precisam ser processados. E a `RemainingComplicationUserInfoTransfers` propriedade retorna os tempos restantes em que o aplicativo IOS pode atualizar sua complicação watchOS.

Para saber mais, consulte nosso guia de [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) .

<a name="WatchKit-Framework-Enhancements"></a>

## <a name="watchkit-framework-enhancements"></a>Aprimoramentos do WatchKit Framework

o watchOS 3 inclui vários aprimoramentos para a estrutura WatchKit, incluindo o seguinte:

- O aplicativo pode obter o estado do Digital Crown usando a nova classe [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) e receber atualizações quando o usuário girar a coroa usando a classe [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) .
- A classe [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) agora inclui o `ApplicationState` método e a constante [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) que o aplicativo pode usar para acompanhar o estado de tempo de execução do aplicativo. `WKExtension` também fornece dois novos métodos que podem ser usados para agendar tarefas em segundo plano.
- O [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) agora inclui os novos `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` e os `HandleBackgroundTasks` métodos para monitorar as alterações no estado do aplicativo e tratar as atualizações de tarefas em segundo plano.
- Uma nova classe [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) foi adicionada para fornecer os seguintes tipos de reconhecimento de gesto para os aplicativos Watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- A nova classe [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) fornece uma interface para qualquer câmera de IP anexada homekit.
- A nova classe [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) permite que o aplicativo exiba um "pôster" de filme que é substituído pelo filme em execução quando o usuário o toca.
- A nova classe [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) permite que o aplicativo apresente um botão de Apple Pay em sua interface do usuário que iniciará uma solicitação de pagamento quando tocado.
- A nova classe [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) apresenta uma interface para exibir uma cena SceneKit no Apple Watch.
- A nova classe [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) apresenta uma interface para exibir uma cena SpriteKit no Apple Watch.

Para saber mais, confira nossas [técnicas de interação rápida para o guia do watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Workout-App-Enhancements"></a>

## <a name="workout-app-enhancements"></a>Aprimoramentos de aplicativo de ginástica

Novo no watchOS 3, os aplicativos relacionados ao treinamento têm a capacidade de executar em segundo plano na Apple Watch. Para habilitar esse recurso (e obter acesso aos dados do HealthKit), o aplicativo deve incluir a `WKBackgroundModes` chave no `Info.plist` arquivo com o valor `workout-processing` .

Além disso, o desenvolvedor agora tem a capacidade de iniciar o aplicativo de treinamento do watchOS da versão do aplicativo iOS no iPhone emparelhado.

Para saber mais, consulte nosso guia de [aprimoramentos de aplicativos para treinamento](~/ios/watchos/platform/workout-apps.md) .

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações de estrutura e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais no watchOS 3.

Para saber mais, consulte nosso guia de [alterações de estrutura adicionais](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) .

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no watchOS 3:

- A `UILocalNotification` classe de UIKit foi preterida e deve ser substituída pela estrutura de notificação do usuário.

Consulte a documentação de [diferenças da API watchOS 2,2](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) da Apple para watchOS 3,0 para obter uma lista completa de reprovações e alterações.

## <a name="related-links"></a>Links Relacionados

- [Exemplos de watchOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)