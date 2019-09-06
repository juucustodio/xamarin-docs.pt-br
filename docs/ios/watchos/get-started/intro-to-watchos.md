---
title: Introdução ao watchOS
description: Este documento fornece uma visão geral do watchOS, que descreve o ciclo de vida do aplicativo, tipos de interface do usuário, tamanhos de tela, limitações e muito mais.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 59d02db9fa2787e93ad88e4b6f37e0fef50572a5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293109"
---
# <a name="introduction-to-watchos"></a>Introdução ao watchOS

> [!NOTE]
> Confira a [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para uma visão geral dos recursos mais recentes.

## <a name="about-watchos"></a>Sobre o watchOS

Uma solução de aplicativo watchOS tem 3 projetos:

- **Assista a extensão** – um projeto que contém o código para o aplicativo watch.
- **Assista ao aplicativo** – contém o storyboard de interface do usuário e recursos.
- **Aplicativo pai iOS** – esse aplicativo é um aplicativo de iPhone normal. O aplicativo de inspeção e a extensão são empacotadas em aplicativo do iPhone para entrega a inspeção do usuário.

Em aplicativos watchOS 1, o código na extensão é executado no iPhone – a Apple Watch é efetivamente um vídeo externo. aplicativos do watchOS 2 e 3 são executados inteiramente na Apple Watch. Essa diferença é mostrada no diagrama a seguir:

[![](intro-to-watchos-images/arch-sml.png "A diferença entre watchOS 1 e watchOS 2 (e superior) é mostrada neste diagrama")](intro-to-watchos-images/arch.png#lightbox)

Independentemente de qual versão do watchOS destina-se, no Visual Studio para painel do Mac de soluções uma solução completa será algo parecido com isso:

[![](intro-to-watchos-images/projectstructure-sml.png "O painel de soluções")](intro-to-watchos-images/projectstructure.png#lightbox)

O *aplicativo pai* um watchOS solução é um aplicativo iOS regular. Esse é o único projeto na solução que está visível **no telefone**. Casos de uso para este aplicativo inclui os tutoriais, telas administrativas e camada intermediária filtragem, cacheing, etc. No entanto, é possível que o usuário instalar e executar o aplicativo/extensão de inspeção sem **nunca** ter aberto o aplicativo pai, portanto, se você precisar que o aplicativo pai para executar inicialização única ou de administração, que precisa para programar seu relógio / extensão do aplicativo para informar ao usuário que.

Embora o aplicativo pai oferece o aplicativo de inspeção e a extensão, eles executados em diferentes áreas de segurança.

Sobre o watchOS 1, eles podem compartilhar dados por meio de um grupo de aplicativo compartilhado ou a função estática `WKInterfaceController.OpenParentApplication`, que irá disparar o `UIApplicationDelegate.HandleWatchKitExtensionRequest` método em seu aplicativo de pai `AppDelegate` (consulte [trabalhar com o aplicativo pai](~/ios/watchos/app-fundamentals/parent-app.md)).

Sobre o watchOS 2 ou posterior a estrutura de inspeção de conectividade é usada para se comunicar com o aplicativo pai, usando o `WCSession` classe.

## <a name="application-lifecycle"></a>Ciclo de vida do aplicativo

Na extensão de inspeção, uma subclasse do `WKInterfaceController` classe é criada para cada cena de Storyboard.

Eles `WKInterfaceController` classes são análogas ao `UIViewController` objetos na programação do iOS, mas não têm o mesmo nível de acesso para o modo de exibição.
Por exemplo, você não pode adicionar controles ao dinamicamente ou reestruturar sua interface do usuário.
Você pode, no entanto, ocultar e revelar os controles e, com alguns controles, alterar seu tamanho, transparência e opções de aparência.

O ciclo de vida de um `WKInterfaceController` objeto envolve as seguintes chamadas:

- [Ativo](xref:WatchKit.WKInterfaceController.Awake*) : Você deve executar a maior parte da inicialização nesse método.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : Chamado logo antes de o aplicativo Watch aparecer para o usuário. Use esse método para executar a inicialização do último momento, iniciar as animações, etc.
- Neste ponto, o aplicativo Watch é exibida e a extensão começa respondendo ao usuário de entrada e atualizando a exibição do aplicativo Watch por lógica do aplicativo.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) após o aplicativo Watch foi fechado pelo usuário, esse método é chamado. Depois que esse método retorna, controles de interface do usuário não podem ser modificados até a próxima vez `WillActivate` é chamado. Esse método também será chamado se a conexão para o iPhone é interrompida.
- Depois que a extensão foi desativada, é inacessível ao programa. Pendente funções assíncronas **não** ser chamado. Assista ao que Kit de extensões não pode usar modos de processamento em segundo plano. Se o programa é reativado pelo usuário, mas o aplicativo não foi finalizado pelo sistema operacional, o primeiro método chamado será `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Visão geral do ciclo de vida do aplicativo")

## <a name="types-of-user-interface"></a>Tipos de Interface do usuário

Há três tipos de interação com que o usuário pode ter com o aplicativo de inspeção.
Todos são programados usando classes personalizadas de subpropriedades da `WKInterfaceController`, portanto, a sequência do ciclo de vida discutido anteriormente se aplica universalmente (as notificações são programadas com classes de subpropriedades de `WKUserNotificationController`, que por si só é uma subclasse de `WKInterfaceController`):

### <a name="normal-interaction"></a>Interação normal

A maioria da interação de extensão do aplicativo watch será com classes de subpropriedades da `WKInterfaceController` que você escreve para corresponder ao plano em seu aplicativo de inspeção **Interface.storyboard**. Isso é abordado em detalhes na [instalação](~/ios/watchos/get-started/installation.md) e [Introdução](~/ios/watchos/get-started/index.md) artigos.
A imagem a seguir mostra uma parte do [Watch Kit catálogo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) Storyboard do exemplo. Para cada cena que mostrei aqui, há um personalizado correspondente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) no projeto de extensão.

![](intro-to-watchos-images/scenes.png "Exemplos de interação de normal")

### <a name="notifications"></a>Notificações

[Notificações](~/ios/watchos/platform/notifications.md) são um caso de uso principal para o Apple Watch. Há suporte para notificações locais e remotas. A interação com notificações ocorre em dois estágios, chamados Visual curto e longo.

Parece curtos são exibidos rapidamente e mostrar o ícone do aplicativo watch, seu nome e o título (conforme especificado com `WKInterfaceController.SetTitle`).

Examinar longos combina um sistema forneceu **caixilho** área e botão descartar com seu conteúdo personalizado com base no Storyboard.

`WKUserNotificationInterfaceController` estende `WKInterfaceController` com os métodos `DidReceiveLocalNotification` e `DidReceiveRemoteNotification`.
Substitua esses métodos para reagir a eventos de notificação.

Para obter mais informações sobre o design de interface do usuário de notificação, consulte o [diretrizes de Interface humana do Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notificações de exemplo")

## <a name="screen-sizes"></a>Tamanhos de tela

O Apple Watch tem dois tamanhos de face: 38 mm e 42 mm, com uma taxa de exibição de 5:4 e uma tela de retina. Seus tamanhos utilizáveis são:

- 38 mm 136 x 170 pixels lógicos (272 x 340 pixels físicos)
- 42 mm 156 x 195 pixels lógicos (312 x 390 pixels físicos).

Use `WKInterfaceDevice.ScreenBounds` para determinar em qual exibir o aplicativo de inspeção está sendo executado.

Em geral, é mais fácil desenvolver seu design de layout e de texto com a exibição de 38mm mais restrita e, em seguida, escalar verticalmente.
Se você iniciar com o ambiente maior, reduzir verticalmente pode levar a sobreposição feia ou o truncamento de texto.

Leia mais sobre [trabalhando com tamanhos de tela](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitações do watchOS

Há algumas limitações do watchOS a serem consideradas ao desenvolver aplicativos watchOS:

- Dispositivos do Apple Watch têm um armazenamento limitado – Lembre-se de que o espaço disponível antes de baixar arquivos grandes (por exemplo arquivos de áudio ou filme).

- Muitos watchOS [controles](~/ios/watchos/user-interface/index.md) apresentem semelhanças na UIKit, mas são classes diferentes (`WKInterfaceButton` vez `UIButton`, `WKInterfaceSwitch` para `UISwitch`, etc.) e ter um conjunto limitado de métodos em comparação com seu UIKit equivalentes. Além disso, o watchOS tem alguns controles, como `WKInterfaceDate` (para exibir uma data e hora) não tem que UIKit.

  - Você não pode rotear as notificações para o relógio somente ou apenas no iPhone (o tipo de controle que o usuário tem sobre o roteamento não foi anunciado pela Apple).

Outras limitações conhecidas / perguntas frequentes:

- Apple não permitirá mostradores de relógio de terceiros 3º personalizado.

- As APIs que permitem a inspeção controlar o iTunes no telefone conectado são privadas.


## <a name="further-reading"></a>Leitura adicional

Confira a documentação da Apple:

- [Desenvolvendo para o Kit de inspeção](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Assista ao guia de programação de Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Diretrizes de Interface humana do Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Links relacionados

- [watchOS 3 catálogo (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 catálogo (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Instalar e configurar](~/ios/watchos/get-started/installation.md)
- [Primeiro vídeo de aplicativo de inspeção](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Desenvolvimento de Apple para guia do Kit de inspeção](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Dicas de WatchKit da Apple](https://developer.apple.com/watchkit/tips/)
- [Introdução ao watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
