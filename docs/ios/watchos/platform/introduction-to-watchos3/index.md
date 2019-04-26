---
title: Introdução ao watchOS 3
description: Este artigo apresenta todas as APIs e recursos disponíveis no watchOS 3 novos e modificados para desenvolvedores do Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224056"
---
# <a name="introduction-to-watchos-3"></a>Introdução ao watchOS 3

_Este artigo apresenta todas as APIs e recursos disponíveis no watchOS 3 novos e modificados para desenvolvedores do Xamarin._

Este documento abordará os seguintes tópicos:

- [O que há de novo no watchOS 3](#Whats-New-in-watchOS-3)
    - [Aprimoramentos de pagamento de Apple](#Apple-Pay-Enhancements) adiciona suporte para pagamentos no aplicativo no Apple Watch.
    - [Tarefas em segundo plano](#Background-Tasks) dar o aplicativo a capacidade de atualizar suas informações em segundo plano para que ele está pronto quando o usuário precisar delas.
    - [Aprimoramentos de complicações](#Complications-Enhancements) foram feitas para watchOS 3 que fornecem novos recursos para os aplicativos.
    - [Recentemente estruturas disponíveis](#Newly-Available-Frameworks) têm expostos para os aplicativos do watchOS.
    - [Sugestões proativas](#Proactive-Suggestions) permite que o aplicativo mostre proativamente informações para o usuário.
    * Vários [aperfeiçoamentos de segurança e privacidade](#Security-and-Privacy-Enhancements) foram feitas ao watchOS 3.
    - [Instantâneos e encaixe](#Snapshots-and-Dock) fornecer ao usuário acesso rápido para os aplicativos do aplicativo watchOS.
    - [Notificações do usuário](#User-Notifications) fornece notificações locais e remotas para o usuário.
    * Vários [aprimoramentos na estrutura de conectividade de inspeção](#Watch-Connectivity-Framework-Enhancements) foram feitas no watchOS 3.
    * Vários [WatchKit Framework aprimoramentos](#WatchKit-Framework-Enhancements) foram feitas no watchOS 3.
    - [Aprimoramentos de aplicativo de ginástica](#Workout-App-Enhancements) oferece novas habilidades para o treinamento relacionados a aplicativos do Apple Watch.
- [Alterações adicionais do Framework](#Additional-Framework-Changes) foram feitas em todo o watchOS 3.
- [APIs preteridas](#Deprecated-APIs) no watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>O que há de novo no watchOS 3

Apple adicionou várias novas APIs e serviços no watchOS 3 junto com muitos aprimoramentos aos recursos existentes, incluindo:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Aprimoramentos do Apple Pay

No watchOS 3, PassKit framework foi expandido para permitir que o suporte para pagamentos de seguros, no aplicativo (de bens físicos e serviços) para os aplicativos em execução na Apple Watch.

Use a nova [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) classes para apresentar e responder a uma interface em que o usuário pode autorizar solicitações de pagamento.

Para obter mais informações, consulte nosso [aprimoramentos de pagamento de Apple](~/ios/watchos/platform/apple-pay.md) guia.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Background Tasks

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações garantindo que ele tenha o conteúdo que o usuário precisa antes de abri-lo.

As seguintes novas tarefas em segundo plano estão disponíveis:

- **Atualização de aplicativo do plano de fundo** - o [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarefa permite que o aplicativo atualize seu estado em segundo plano. Normalmente, isso incluirá outra tarefa, como baixar novo conteúdo da internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantâneo de atualização do plano de fundo** - o [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarefa permite que o aplicativo atualize seu conteúdo e a interface do usuário antes do sistema tira um instantâneo que será usado para preencher o encaixe.
- **Conectividade de inspeção do plano de fundo** - o [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando ele recebe dados do plano de fundo de no iPhone emparelhado.
- **Sessão de URL do plano de fundo** - o [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou for concluído (com êxito ou erro).

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Aprimoramentos de complicações

Complicações são pequenos elementos visuais que fornecem informações úteis em um relance. Dependendo do Mostrador do relógio, o usuário tem a capacidade de personalizar uma Mostrador com um ou mais complicação.

watchOS 3 oferece a capacidade de criar um ou mais complicação para o aplicativo de inspeção para que o usuário possa acessar sua informações na visão de uma Mostrador do aplicativo.

Além disso, as complicações fornecem os seguintes benefícios:

- O usuário pode iniciar rapidamente o aplicativo tocando na complicação diretamente de uma Mostrador do relógio.
- O sistema para manter o aplicativo em um estado pronto para lançamento no qual ele tenta iniciar o aplicativo em segundo plano, tendo uma complicações do aplicativo sobre as causas de face de inspeção mantê-lo na memória e dá a ele tempo extra para atualizar.
- Complicações têm garantia de pelo menos 50 atualizações por push por dia.
- Quando o aplicativo inclui complicações, serão apresentado na Galeria de Face do Apple Watch (consulte da Apple [adicionando complicações na galeria](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentação para obter mais informações).

No watchOS 3, o ClockKit framework agora inclui vários novos modelos para complicações extra grandes, como [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Além disso, para criar texto localizável, usar os novos métodos do [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) classe.

Para obter mais informações, consulte nosso [técnicas de interação rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Estruturas recentemente disponíveis

watchOS 3 inclui várias estruturas de Apple existentes que estavam anteriormente indisponíveis, como:

- **SceneKit** -modelos de uso SceneKit para incluir 3D na interface de usuário do aplicativo watch incluindo a maioria dos recursos disponíveis em outras plataformas, como a iluminação, sombreamento, animação, física e sistemas de partículas. Não há suporte para áudio espacial 3D, sombreadores personalizados de Metal ou OpenGL, filtros de imagem de núcleo e material fisicamente.
- **SpriteKit** -SpriteKit de uso para processar e animar sprites na interface de usuário do aplicativo do aplicativo watch inclusive a maioria dos recursos disponíveis em outras plataformas, como sistemas de ações, física, iluminação e partícula. Não há suporte para áudio espacial 3D, reprodução de vídeo e filtros de imagem principal.
- **AVFoundation** – para gerenciar e reproduzir áudio.
- **CloudKit** - para mover dados entre os contêineres de aplicativo e o iCloud de inspeção.
- **Principais áudio** – para gerenciar os tipos de dados para representar fluxos de áudio, buffers complexos e valores de tempo.
- **GameKit** – para criar jogos sociais.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Sugestões proativas

watchOS 3 permite que o aplicativo apresente proativamente informações para o usuário dentro de contextos de dado. Para dar suporte a esse recurso, o [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) agora inclui o `MapItem` propriedade que permite que o aplicativo fornecer informações de local para uso posterior por outros aplicativos.

Para obter mais informações, consulte nosso [Introdução ao sugestões proativas](~/ios/watchos/platform/proactive-suggestions.md) guia.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Aperfeiçoamentos de segurança e privacidade

Apple fez várias melhorias de segurança e privacidade no watchOS 3 que ajudarão o desenvolvedor melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos em execução no watchOS 3 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específicas de privacidade em seus `Info.plist` arquivos explicam ao usuário por que o aplicativo deseja acessar.

Uma vez que o watchOS 3 compartilha essas alterações com iOS 10, consulte nosso iOS 10 [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Instantâneos e encaixe

No watchOS 3, a Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, uma galeria de instantâneos de aplicativo fixo será exibida. O usuário pode passe o dedo para esquerda ou direita para localizar o aplicativo desejado e, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

Periodicamente, o sistema usa instantâneos da interface de usuário do aplicativo e usa esses instantâneos para preencher os documentos. watchOS dá a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo é tirado de aplicativo.

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia e do Apple [WKSnapshotRefreshBackgroundTask referência](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notificações do usuário

A estrutura de notificação do usuário, introduzida no watchOS 3 oferece suporte a entrega de notificações locais e remotas para o Apple Watch. Use essa estrutura para agendar notificações com base em condições específicas, como a hora do dia ou local e para receber e lidar com notificações.

Para obter mais informações, consulte nosso [técnicas de interação rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Assista a aprimoramentos na estrutura de conectividade

O novo `HasContentPending` propriedade do [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) classe indica que a sessão recebeu dados em segundo plano que precisa ser processada. E o `RemainingComplicationUserInfoTransfers` propriedade retorna restantes horários em que o aplicativo iOS podem atualizar seu watchOS complicação.

Para obter mais informações, consulte nosso [tarefas em segundo plano](~/ios/watchos/platform/background-tasks.md) guia.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Aprimoramentos do Framework do WatchKit

watchOS 3 inclui várias melhorias para a estrutura de WatchKit, incluindo o seguinte:

- O aplicativo pode obter o estado da coroa a Digital usando o novo [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) classe e receber atualizações quando o usuário gira o crown usando o [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) classe.
- O [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) classe agora inclui o `ApplicationState` método e [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) constante de que o aplicativo pode usar para controlar o estado de tempo de execução do aplicativo. `WKExtension` também fornece dois novos métodos que podem ser usados para agendar tarefas em segundo plano.
- O [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) agora inclui a nova `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` e `HandleBackgroundTasks` métodos para monitorar alterações no estado do aplicativo e lidar com atualizações de tarefa em segundo plano.
- Uma nova [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) classe foi adicionada para fornecer os seguintes tipos de reconhecimento de gesto para os aplicativos do watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- O novo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) classe fornece uma interface para qualquer HomeKit anexados a câmera IP.
- O novo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) classe permite que o aplicativo exibir um filme "pôster" é substituído pelo filme em execução quando o usuário toca-lo.
- O novo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) classe permite que o aplicativo apresentar um botão de Apple Pay em sua interface do usuário que iniciará uma solicitação de pagamento quando tocada.
- O novo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) classe apresenta uma interface para exibir uma cena de SceneKit sobre o Apple Watch.
- O novo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) classe apresenta uma interface para exibir uma cena de SpriteKit sobre o Apple Watch.

Para obter mais informações, consulte nosso [técnicas de interação rápida para watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) guia.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Aprimoramentos de aplicativo de ginástica

Novo watchOS 3, ginástica relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch. Para habilitar esse recurso (e obter acesso aos dados HealthKit), o aplicativo deve incluir a `WKBackgroundModes` principais na `Info.plist` arquivo com o valor `workout-processing`.

Além disso, o desenvolvedor agora tem a capacidade de iniciar o aplicativo de ginástica watchOS da versão do aplicativo do iOS no iPhone emparelhado.

Para obter mais informações, consulte nosso [aprimoramentos de aplicativo de ginástica](~/ios/watchos/platform/workout-apps.md) guia.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais na watchOS 3.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) guia.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>APIs obsoletas

As seguintes APIs foram preteridas no watchOS 3:

- O `UILocalNotification` classe de UIKit foi preterida e deve ser substituído com o framework de notificação do usuário.

Consulte da Apple [watchOS 2.2 às diferenças de API do watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentação para obter uma lista completa de alterações e reprovações.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [O que há de novo no watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
