---
title: "Introdução ao watchOS"
description: "Visão geral da estrutura de solução de watchOS e limitações"
ms.topic: article
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2276b67fc29f2752e4b178168a12e6e980b788d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-watchos"></a>Introdução ao watchOS

> [!NOTE]
> **Observação:** Check-out de [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para obter uma visão geral dos recursos mais recentes.

## <a name="about-watchos"></a>Sobre watchOS

Uma solução de aplicativo watchOS tem 3 projetos:

- **Assista a extensão** – um projeto que contém o código para o aplicativo de inspeção.
- **Assista a aplicativo** – contém o storyboard de interface do usuário e recursos.
- **Aplicativo pai iOS** – esse aplicativo é um aplicativo de iPhone normal. O aplicativo de inspeção e extensão são empacotadas em aplicativo de iPhone para entrega a inspeção do usuário.

Em aplicativos watchOS 1, o código de extensão é executado no iPhone – o Apple Watch é efetivamente um vídeo externo. aplicativos de watchOS 2 e 3 são executados inteiramente no Apple Watch. Essa diferença é mostrada no diagrama a seguir:

[ ![](intro-to-watchos-images/arch-sml.png "A diferença entre watchOS 1 e 2 (e superior) de watchOS é mostrada no diagrama")](intro-to-watchos-images/arch.png#lightbox)

Independentemente de qual versão do watchOS destina-se, no Visual Studio para preenchimento de solução do Mac uma solução completa será parecida com isto:

[![](intro-to-watchos-images/projectstructure-sml.png "O preenchimento de solução")](intro-to-watchos-images/projectstructure.png#lightbox)

O *aplicativo-pai* um watchOS solução é um aplicativo iOS regular. Esse é o único projeto na solução que é visível **no telefone**. Casos de uso para este aplicativo inclui tutoriais, telas administrativas e intermediária filtragem, cacheing, etc. No entanto, é possível que o usuário instalar e executar a inspeção/extensão do aplicativo sem **nunca** ter aberto o aplicativo-pai, então se você precisar que o aplicativo-pai para executar a inicialização única ou de administração, você precisa programar seu relógio extensão do aplicativo/para informar ao usuário que.

Embora o aplicativo-pai oferece o aplicativo de inspeção e a extensão, eles executados em caixas de proteção diferentes.

Em watchOS 1, eles podem compartilhar dados por meio de um grupo de aplicativo compartilhado ou a função estática `WKInterfaceController.OpenParentApplication`, que irá disparar o `UIApplicationDelegate.HandleWatchKitExtensionRequest` método em seu aplicativo de pai `AppDelegate` (consulte [trabalhando com o aplicativo-pai](~/ios/watchos/app-fundamentals/parent-app.md)).

Em watchOS 2 ou posterior a estrutura de conectividade de inspeção é usada para se comunicar com o aplicativo-pai, usando o `WCSession` classe.

## <a name="application-lifecycle"></a>Ciclo de vida do aplicativo

Na extensão de observação, uma subclasse do `WKInterfaceController` classe é criada para cada cena de Storyboard.

Essas `WKInterfaceController` classes são análogas ao `UIViewController` objetos na programação do iOS, mas não têm o mesmo nível de acesso para o modo de exibição.
Por exemplo, você dinamicamente não é possível adicionar controles para ou reestruturar sua interface do usuário.
Você pode, no entanto, ocultar e revelar os controles e, com alguns controles, alterar seu tamanho, transparência e opções de aparência.

O ciclo de vida de um `WKInterfaceController` objeto envolve as seguintes chamadas:

- [Ativo](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/) : você deve realizar a maior parte da sua inicialização nesse método.
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) : chamado logo antes do aplicativo Watch aparece para o usuário. Use esse método para executar a inicialização do último momento, iniciar animações, etc.
- Neste ponto, o aplicativo de observação é exibida e a extensão começa a responder ao usuário de entrada e atualizando a exibição do aplicativo de inspeção por sua lógica de aplicativo.
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/) após o aplicativo Watch seja fechado pelo usuário, este método é chamado. Depois que este método retorna, controles de interface do usuário não podem ser modificados até a próxima vez `WillActivate` é chamado. Esse método também será chamado se a conexão para o iPhone é interrompida.
- Depois que a extensão foi desativada, está inacessível para o seu programa. Pendente funções assíncronas **não** ser chamado. Assista que Kit extensões não pode usar modos de processamento em segundo plano. Se o programa for reativado pelo usuário, mas o aplicativo não foi finalizado pelo sistema operacional, será o primeiro método chamado `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Visão geral do ciclo de vida do aplicativo")

## <a name="types-of-user-interface"></a>Tipos de Interface do usuário

Há três tipos de interação que do usuário pode ter com seu aplicativo de inspeção.
Todos são programados usando classes personalizadas de subgrupos de `WKInterfaceController`, portanto, a sequência do ciclo de vida abordado anteriormente aplica universalmente (notificações são programadas com classes do subsistema de `WKUserNotificationController`, que por si só é uma subclasse de `WKInterfaceController`):

### <a name="normal-interaction"></a>Interação normal

A maioria da interação de extensão do aplicativo de inspeção será com classes do subsistema de `WKInterfaceController` que você escreve corresponder ao segundo plano em seu aplicativo de inspeção **Interface.storyboard**. Isso é abordado em detalhes no [instalação](~/ios/watchos/get-started/installation.md) e [Introdução](~/ios/watchos/get-started/index.md) artigos.
A imagem a seguir mostra uma parte do [inspecionar Kit catálogo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) Storyboard do exemplo. Para cada cena mostrada aqui, há um personalizado correspondente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) no projeto de extensão.

![](intro-to-watchos-images/scenes.png "Exemplos de interação normal")

### <a name="notifications"></a>Notificações

[Notificações](~/ios/watchos/platform/notifications.md) são um caso de uso principal para a Apple Watch. Há suporte a notificações de locais e remotas. A interação com as notificações ocorre em duas fases, chamados aparência de curto e longo.

Parece curto são exibidos rapidamente e mostrar o título, seu nome e o ícone do aplicativo de inspeção (conforme especificado com `WKInterfaceController.SetTitle`).

Examinar longo combina um fornecido pelo sistema **caixilho** área e ignorar o botão com o conteúdo personalizado com base no Storyboard.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` com os métodos `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Substitua esses métodos para reagir a eventos de notificação.

Para obter mais informações sobre o design de interface do usuário de notificação, consulte o [diretrizes de Interface humana da Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notificações de exemplo")

## <a name="screen-sizes"></a>Tamanhos de tela

O Apple Watch tem dois tamanhos de face: 38mm e 42mm, com uma taxa de exibição: 4 a 5 e uma exibição de Retina. Os tamanhos utilizáveis são:

- 38 mm: 136 x 170 pixels de lógicos (272 x 340 pixels físicos)
- 42 mm: 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Use `WKInterfaceDevice.ScreenBounds` para determinar em qual exibição seu aplicativo de inspeção está sendo executado.

Em geral, é mais fácil desenvolver o design de layout e de texto com a exibição de 38mm mais restrita e, em seguida, escalar verticalmente.
Se você iniciar com o ambiente maior, redução de escala pode levar a sobreposição feio ou o truncamento de texto.

Leia mais sobre [trabalhando com tamanhos de telas](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitações de watchOS

Há algumas limitações de watchOS estar atento ao desenvolver aplicativos watchOS:

- Dispositivos da Apple Watch têm um armazenamento limitado - Lembre-se do espaço disponível antes de baixar arquivos grandes (por exemplo. arquivos de áudio ou filme).

- Muitos watchOS [controles](~/ios/watchos/user-interface/index.md) apresentem semelhanças na UIKit, mas diferentes classes (`WKInterfaceButton` em vez de `UIButton`, `WKInterfaceSwitch` para `UISwitch`, etc.) e tem um conjunto limitado de métodos em comparação com seu UIKit equivalentes. Além disso, o watchOS tem alguns controles como `WKInterfaceDate` (para exibir uma data e hora) não tem que UIKit.

  - Você não pode rotear as notificações de inspeção apenas ou apenas o iPhone (o tipo de controle que o usuário tem sobre roteamento não foi anunciado pela Apple).

Outras limitações conhecidas / perguntas frequentes:

- Apple não permitirá que as faces inspecionar personalizado 3ª parte.

- As APIs que permitem que o relógio controlar o iTunes no telefone conectado são privadas.


## <a name="further-reading"></a>Leitura adicional

Consulte a documentação da Apple:

* [Desenvolvendo para o Kit de inspeção](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [Assista a guia de programação de Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Diretrizes de Interface Humana Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Links relacionados

- [watchOS 3 catálogo (exemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 catálogo (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Instalar e configurar](~/ios/watchos/get-started/installation.md)
- [Vídeo do primeiro aplicativo Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Desenvolvimento de Apple para a guia do Kit de inspeção](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
