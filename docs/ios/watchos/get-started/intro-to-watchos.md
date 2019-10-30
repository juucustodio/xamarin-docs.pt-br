---
title: Introdução ao watchOS 5
description: Este documento fornece uma visão geral do watchOS, que descreve o ciclo de vida do aplicativo, os tipos de interface do usuário, os tamanhos de tela, as limitações e muito mais.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: b3c2908d8ae9a68189fbff4d47afa49da21b88a5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030188"
---
# <a name="introduction-to-watchos"></a>Introdução ao watchOS 5

> [!NOTE]
> Confira a [introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para obter uma visão geral dos recursos mais recentes.

## <a name="about-watchos"></a>Sobre o watchOS

Uma solução de aplicativo watchOS tem três projetos:

- **Inspecionar extensão** – um projeto que contém o código para o aplicativo Watch.
- **Inspecionar aplicativo** – contém o storyboard e os recursos da interface do usuário.
- **aplicativo pai do IOS** – este aplicativo é um aplicativo de iPhone normal. O aplicativo Watch e a extensão são agrupados no aplicativo do iPhone para entrega ao relógio do usuário.

Nos aplicativos watchOS 1, o código na extensão é executado no iPhone – o Apple Watch é efetivamente uma exibição externa. os aplicativos watchOS 2 e 3 são executados inteiramente no Apple Watch. Essa diferença é mostrada no diagrama abaixo:

[![](intro-to-watchos-images/arch-sml.png "The difference between watchOS 1 and watchOS 2 (and greater) is shown in this diagram")](intro-to-watchos-images/arch.png#lightbox)

Independentemente de qual versão do watchOS é direcionada, em Visual Studio para Mac Painel de Soluções uma solução completa será semelhante a esta:

[![](intro-to-watchos-images/projectstructure-sml.png "The Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

O *aplicativo pai* em uma solução watchOS é um aplicativo IOS normal. Esse é o único projeto na solução que é visível **no telefone**. Os casos de uso para esse aplicativo incluem tutoriais, telas administrativas e filtragem de camada intermediária, cache, etc. No entanto, é possível que o usuário instale e execute o aplicativo/extensão Watch sem **nunca** ter aberto o aplicativo pai. portanto, se você precisar que o aplicativo pai seja executado para inicialização ou administração única, será necessário programar seu aplicativo/extensão Watch para informar o usuário que.

Embora o aplicativo pai entregue o aplicativo de inspeção e a extensão, eles são executados em áreas restritas diferentes.

No watchOS 1, eles podem compartilhar dados por meio de um grupo de aplicativos compartilhado ou por meio da função estática `WKInterfaceController.OpenParentApplication`, que disparará o método `UIApplicationDelegate.HandleWatchKitExtensionRequest` na `AppDelegate` do seu aplicativo pai (consulte [trabalhando com o aplicativo pai](~/ios/watchos/app-fundamentals/parent-app.md)).

No watchOS 2 ou posterior, a estrutura de conectividade do Watch é usada para se comunicar com o aplicativo pai, usando a classe `WCSession`.

## <a name="application-lifecycle"></a>Ciclo de vida do aplicativo

Na extensão Watch, uma subclasse da classe `WKInterfaceController` é criada para cada cena de storyboard.

Essas classes de `WKInterfaceController` são análogas aos objetos `UIViewController` na programação do iOS, mas não têm o mesmo nível de acesso à exibição.
Por exemplo, você não pode adicionar controles dinamicamente ou reestruturar sua interface do usuário.
Você pode, no entanto, ocultar e revelar controles e, com alguns controles, alterar suas opções de tamanho, transparência e aparência.

O ciclo de vida de um objeto de `WKInterfaceController` envolve as seguintes chamadas:

- [Ativo](xref:WatchKit.WKInterfaceController.Awake*) : você deve executar a maior parte da sua inicialização neste método.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : chamado logo antes de o aplicativo Watch aparecer para o usuário. Use este método para executar a inicialização do último momento, iniciar animações, etc.
- Neste ponto, o aplicativo Watch aparece e a extensão começa a responder à entrada do usuário e atualiza a exibição do aplicativo de inspeção de acordo com a lógica do aplicativo.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) Depois que o aplicativo Watch tiver sido descartado pelo usuário, esse método será chamado. Após esse método retornar, os controles de interface do usuário não poderão ser modificados até a próxima vez que `WillActivate` for chamado. Esse método também será chamado se a conexão com o iPhone for interrompida.
- Depois que a extensão for desativada, ela ficará inacessível para seu programa. As funções assíncronas pendentes **não serão** chamadas. As extensões do Watch kit não podem usar modos de processamento em segundo plano. Se o programa for reativado pelo usuário, mas o aplicativo não tiver sido encerrado pelo sistema operacional, o primeiro método chamado será `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Application Lifecycle overview")

## <a name="types-of-user-interface"></a>Tipos de interface do usuário

Há três tipos de interação que o usuário pode ter com seu aplicativo Watch.
Todos são programados usando subclasses personalizadas de `WKInterfaceController`, portanto, a sequência de ciclo de vida abordada anteriormente é aplicada universalmente (as notificações são programadas com subclasses de `WKUserNotificationController`, que é uma subclasse de `WKInterfaceController`):

### <a name="normal-interaction"></a>Interação normal

A maior parte da interação de aplicativo/extensão de inspeção será com as subclasses de `WKInterfaceController` que você escreve para corresponder às cenas na interface do seu aplicativo de inspeção **. Storyboard**. Isso é abordado em detalhes nos artigos de [instalação](~/ios/watchos/get-started/installation.md) e [introdução](~/ios/watchos/get-started/index.md) .
A imagem a seguir mostra uma parte do storyboard do exemplo do [catálogo do Watch kit](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) . Para cada cena mostrada aqui, há um `WKInterfaceController` personalizado correspondente (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) no projeto de extensão.

![](intro-to-watchos-images/scenes.png "Normal Interaction examples")

### <a name="notifications"></a>Notificações

As [notificações](~/ios/watchos/platform/notifications.md) são um caso de uso importante para o Apple Watch. Há suporte para notificações locais e remotas. A interação com as notificações ocorre em dois estágios, chamado curta e longa aparência.

As breves aparências são exibidas brevemente e mostram o ícone do aplicativo Watch, seu nome e o título (conforme especificado com `WKInterfaceController.SetTitle`).

A aparência demorada combina uma área de **caixilho** fornecida pelo sistema e ignora o botão com seu conteúdo personalizado baseado em Storyboard.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` com os métodos `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Substitua esses métodos para reagir a eventos de notificação.

Para obter mais informações sobre o design da interface do usuário de notificação, consulte as [diretrizes de interface humana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Sample notifications")

## <a name="screen-sizes"></a>Tamanhos de tela

O Apple Watch tem dois tamanhos de face: 38 mm e 42 mm, ambos com uma taxa de exibição de 5:4 e uma tela de retina. Seus tamanhos utilizáveis são:

- 38 mm: 136 x 170 pixels lógicos (272 x 340 pixels físicos)
- 42 mm: 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Use `WKInterfaceDevice.ScreenBounds` para determinar em que exibição seu aplicativo Watch está em execução.

Em geral, é mais fácil desenvolver seu design de texto e layout com a exibição de 38 mm mais restrita e, em seguida, escalar verticalmente.
Se você começar com o ambiente maior, reduzir verticalmente pode levar a uma sobreposição ruim ou truncamento de texto.

Leia mais sobre como [trabalhar com tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md).

## <a name="limitations-of-watchos"></a>Limitações do watchOS

Há algumas limitações de watchOS a serem consideradas ao desenvolver aplicativos watchOS:

- Apple Watch dispositivos têm armazenamento limitado-esteja ciente do espaço disponível antes de baixar arquivos grandes (por exemplo, arquivos de áudio ou de filme).

- Muitos [controles](~/ios/watchos/user-interface/index.md) watchOS têm análogos em UIKit, mas são classes diferentes (`WKInterfaceButton` em vez de `UIButton`, `WKInterfaceSwitch` para `UISwitch`, etc.) e têm um conjunto limitado de métodos em comparação com seus equivalentes UIKit. Além disso, o watchOS tem alguns controles, como `WKInterfaceDate` (para exibir uma data e hora) que o UIKit não tem.

  - Você não pode encaminhar notificações apenas para o relógio ou somente o iPhone (que tipo de controle o usuário tem sobre o roteamento não foi anunciado pela Apple).

Algumas outras limitações conhecidas/perguntas frequentes:

- A Apple não permitirá faces de inspeção personalizadas de terceiros.

- As APIs que permitem que a inspeção controle o iTunes no telefone conectado são privadas.

## <a name="further-reading"></a>Leitura adicional

Confira a documentação da Apple:

- [Desenvolvendo para o kit de inspeção](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Guia de programação do Watch kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Diretrizes de interface humana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>Links relacionados

- [Catálogo watchOS 3 (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Catálogo watchOS 1 (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Instalação e instalação](~/ios/watchos/get-started/installation.md)
- [Primeiro vídeo do aplicativo Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Guia de desenvolvimento do kit de inspeção da Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
