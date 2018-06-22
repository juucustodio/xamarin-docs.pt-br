---
title: Introdução ao watchOS 3
description: Este artigo apresenta todas as APIs e recursos disponíveis no watchOS 3 novos e modificados para desenvolvedores do Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: 506e3795538ceffc77301a608c504fc6ec2045a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780031"
---
# <a name="introduction-to-watchos-3"></a>Introdução ao watchOS 3

_Este artigo apresenta todas as APIs e recursos disponíveis no watchOS 3 novos e modificados para desenvolvedores do Xamarin._

Este documento aborda os seguintes tópicos:

- [O que há de novo no watchOS 3](#Whats-New-in-watchOS-3)
    - [Aprimoramentos de pagamento da Apple](#Apple-Pay-Enhancements) adiciona suporte para pagamentos no aplicativo no Apple Watch.
    - [Tarefas em segundo plano](#Background-Tasks) que o aplicativo tenha a capacidade de atualizar suas informações em segundo plano para que ele esteja pronto quando o usuário precisa.
    - [Aprimoramentos de complicações](#Complications-Enhancements) foram feitas para watchOS 3 que fornece novos recursos para os aplicativos.
    - [Recentemente estruturas disponíveis](#Newly-Available-Frameworks) têm expostos para os aplicativos watchOS.
    - [Sugestões pró-ativo](#Proactive-Suggestions) permite que o aplicativo de maneira proativa mostram informações para o usuário.
    * Vários [segurança e privacidade aprimoramentos](#Security-and-Privacy-Enhancements) watchOS 3 foram feitas.
    - [Instantâneos e encaixe](#Snapshots-and-Dock) fornecer ao usuário acesso rápido a aplicativos de watchOS de aplicativo.
    - [Notificações de usuário](#User-Notifications) fornece notificações de locais e remotas para o usuário.
    * Vários [Framework aprimoramentos na conectividade de inspeção](#Watch-Connectivity-Framework-Enhancements) foram feitas no watchOS 3.
    * Vários [WatchKit Framework aprimoramentos](#WatchKit-Framework-Enhancements) foram feitas no watchOS 3.
    - [Aprimoramentos de aplicativo de ginástica](#Workout-App-Enhancements) fornece novas habilidades para o treinamento relacionados aplicativos Apple Watch.
- [Alterações adicionais do Framework](#Additional-Framework-Changes) foram feitas em todo watchOS 3.
- [Preterido APIs](#Deprecated-APIs) em watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>O que há de novo no watchOS 3

Apple adicionou várias novas APIs e serviços watchOS 3 junto com muitas melhorias em recursos existentes, incluindo:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Aprimoramentos de pagamento da Apple

WatchOS 3, a estrutura de PassKit foi expandida para permitir que o suporte para pagamentos de seguros, no aplicativo (de bens físicos e serviços) para os aplicativos em execução no Apple Watch.

Use a nova [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) classes para apresentar e responder a uma interface em que o usuário pode autorizar solicitações de pagamento.

Para obter mais informações, consulte nosso [Apple pagar aprimoramentos](~/ios/watchos/platform/apple-pay.md) guia.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Tarefas em Segundo Plano

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações de garantir que ele tenha o conteúdo, o usuário precisa antes de abri-lo.

As seguintes novas tarefas em segundo plano estão disponíveis:

- **Atualização de aplicativo do plano de fundo** - o [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarefa permite que o aplicativo atualizar seu estado em segundo plano. Normalmente, isso incluirá outra tarefa como baixar o novo conteúdo da internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantâneo de atualização do plano de fundo** - o [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarefa permite que o aplicativo atualizar seu conteúdo e a interface do usuário antes do sistema tira um instantâneo que será usado para preencher o encaixe.
- **Conectividade de inspeção do plano de fundo** - o [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando ele recebe os dados do plano de fundo de iPhone emparelhado.
- **Sessão de URL do plano de fundo** - o [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou for concluído (com êxito ou erro).

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Aprimoramentos de complicações

Complicações são pequenos elementos visuais que fornecem informações úteis em um relance. Dependendo da face do relógio selecionado, o usuário tem a capacidade de personalizar uma face do relógio com um ou mais Complicativo.

watchOS 3 fornece o aplicativo a capacidade de criar um ou mais Complicativo para o aplicativo de inspeção para que o usuário pode acessar sua informações em geral de uma face do relógio.

Além disso, complicações fornecem os seguintes benefícios:

- O usuário pode iniciar rapidamente o aplicativo tocando do complicativo diretamente de uma face do relógio.
- O sistema para manter o aplicativo em um estado pronto para iniciar em que ele tenta iniciar o aplicativo em segundo plano, tendo uma complicações do aplicativo no watch face causas mantê-lo na memória e permite que ele muito tempo para atualizar.
- Complicações têm garantia de atualizações de push pelo menos 50 por dia.
- Quando o aplicativo inclui complicações, ele aparecerá na Galeria de Face do Apple Watch (consulte da Apple [complicações adicionando à galeria](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentação para obter mais informações).

Em watchOS 3, a estrutura ClockKit agora inclui vários novos modelos para complicações extra grandes como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Além disso, para criar texto localizável, use novos métodos de [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) classe.

Para obter mais informações, consulte nosso [rápido técnicas de interação para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Estruturas recentemente disponíveis

watchOS 3 inclui várias estruturas Apple existentes que estavam anteriormente indisponíveis, como:

- **SceneKit** -SceneKit de uso para incluir 3D modelos de interface de usuário do aplicativo de inspeção do incluindo a maioria dos recursos disponíveis em outras plataformas, como a iluminação, sombreamento, animação, física e sistemas de partícula. Não há suporte para o áudio espacial 3D, sombreadores de sistema operacional ou OpenGL personalizados, filtros de imagem principal e o material fisicamente.
- **SpriteKit** -SpriteKit de uso para renderizar e animar sprites na interface de usuário do aplicativo de inspeção de aplicativo incluindo a maioria dos recursos disponíveis em outras plataformas como sistemas de ações, física, iluminação e partícula. Não há suporte para o áudio espacial 3D, reprodução de vídeo e filtros de imagem principal.
- **AVFoundation** - para gerenciar e executar o áudio.
- **CloudKit** - para mover dados entre os contêineres de aplicativo e iCloud inspeção.
- **Áudio de núcleo** - para gerenciar os tipos de dados para representar os fluxos de áudio, buffers complexos e valores de tempo.
- **GameKit** - para criar jogos sociais.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Sugestões proativos

watchOS 3 permite que o aplicativo proativamente apresentar informações ao usuário em contextos de dado. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui o `MapItem` propriedade que permite que o aplicativo forneça informações de local para uso posterior por outros aplicativos.

Para obter mais informações, consulte nosso [Introdução sugestões pró-ativo](~/ios/watchos/platform/proactive-suggestions.md) guia.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Segurança e privacidade aprimoramentos

Apple fez diversos aprimoramentos de segurança e privacidade no watchOS 3 que podem ajudar o desenvolvedor de melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos em execução no watchOS 3 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específica de privacidade em seus `Info.plist` arquivos explicam para o usuário por que o aplicativo deseja acessar.

Como watchOS 3 compartilha essas alterações com iOS 10, consulte nosso iOS 10 [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Instantâneos e encaixe

Em watchOS 3, Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, será exibida uma galeria de instantâneos de aplicativo fixados. O usuário pode passar esquerda ou direita para localizar o aplicativo desejado, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

Periodicamente, o sistema usa os instantâneos de interface de usuário do aplicativo e usa esses instantâneos para preencher os documentos. watchOS o aplicativo tem a oportunidade para atualizar seu conteúdo e a interface do usuário antes desse instantâneo é criado.

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia e do Apple [WKSnapshotRefreshBackgroundTask referência](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notificações de usuário

A estrutura de notificação do usuário, introduzida no watchOS 3 oferece suporte a entrega de notificações de locais e remotas para o Apple Watch. Use essa estrutura para agendar notificações com base em condições específicas, como a hora do dia ou local e para receber e tratar notificações.

Para obter mais informações, consulte nosso [rápido técnicas de interação para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Assista a conectividade do Framework aprimoramentos

O novo `HasContentPending` propriedade o [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) classe indica que a sessão recebeu dados em segundo plano que precisa ser processada. E o `RemainingComplicationUserInfoTransfers` propriedade retorna o restante horários em que o aplicativo iOS pode atualizar seu watchOS Complicativo.

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Aprimoramentos do WatchKit Framework

watchOS 3 inclui várias melhorias para a estrutura de WatchKit, incluindo o seguinte:

- O aplicativo pode obter o estado da Crown Digital usando o novo [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) classe e receber atualizações quando o usuário gira o coroa usando o [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) classe.
- O [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) classe agora inclui o `ApplicationState` método e [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) constante que o aplicativo pode usar para controlar o estado de tempo de execução do aplicativo. `WKExtension` também fornece dois novos métodos que podem ser usados para agendar tarefas em segundo plano.
- O [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) agora inclui o novo `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` e `HandleBackgroundTasks` métodos para monitorar as alterações no estado do aplicativo e lidar com atualizações de tarefa em segundo plano.
- Um novo [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) classe foi adicionada para fornecer os seguintes tipos de reconhecimento de gesto para os aplicativos de inspeção: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer ](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- O novo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) classe fornece uma interface para qualquer HomeKit anexadas a câmera IP.
- O novo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) classe permite que o aplicativo exibir um filme "pôster" que é substituído por filme em execução quando o usuário toca-lo.
- O novo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) classe permite que o aplicativo apresentar um botão de pagamento da Apple em sua interface de usuário que irá iniciar uma solicitação de pagamento quando tocada.
- O novo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) classe apresenta uma interface para exibir uma cena SceneKit sobre o Apple Watch.
- O novo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) classe apresenta uma interface para exibir uma cena SpriteKit sobre o Apple Watch.

Para obter mais informações, consulte nosso [rápido técnicas de interação para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Aprimoramentos de aplicativo de ginástica

Novo watchOS 3, treinamento relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch. Para habilitar esse recurso (e obter acesso aos dados HealthKit), o aplicativo deve incluir o `WKBackgroundModes` chave no `Info.plist` arquivo com o valor `workout-processing`.

Além disso, o desenvolvedor agora tem a capacidade de iniciar o aplicativo de treinamento watchOS da versão do aplicativo iOS no iPhone emparelhado.

Para obter mais informações, consulte nosso [aprimoramentos de aplicativo de ginástica](~/ios/watchos/platform/workout-apps.md) guia.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais na watchOS 3.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) guia.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram substituídas no watchOS 3:

- O `UILocalNotification` classe de UIKit foi preterido e deve ser substituído com o framework de notificação do usuário.

Consulte da Apple [watchOS 2.2 para as diferenças de API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentação para obter uma lista completa de substituições e alterações.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
