---
title: Introdução à operação em segundo plano no iOS
description: 'Este documento descreve o plano de fundo em iOS: Estados de aplicativo, métodos de ciclo de vida do aplicativo e atualização de aplicativo em segundo plano.'
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/24/2018
ms.openlocfilehash: 1899ee225d0ea285a38ba7b4d341e5d3a4452639
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521392"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introdução à operação em segundo plano no iOS

o iOS regula o processamento em segundo plano de forma muito rígida e oferece três abordagens para implementá-lo:

- **Registrar uma tarefa em segundo plano** – se um aplicativo precisar concluir uma tarefa importante, ele poderá pedir ao Ios para não interromper a tarefa quando o aplicativo mudar para o plano de fundo. Por exemplo, um aplicativo pode precisar concluir o registro em log de um usuário ou concluir o download de um arquivo grande.
- **Registrar-se como um aplicativo necessário** para o plano de fundo – um aplicativo pode ser registrado como um tipo específico de aplicativo que tem requisitos de plano de fundo específicos, como *áudio* , *VoIP* , *acessório externo* , *Newsstand* , e o *local* . Esses aplicativos recebem privilégios contínuos de processamento em segundo plano, contanto que estejam executando tarefas que estejam dentro dos parâmetros do tipo de aplicativo registrado.
- **Habilitar atualizações em segundo plano** – os aplicativos podem disparar atualizações em segundo plano com *monitoramento de região* ou ouvindo *alterações significativas no local* . A partir do iOS 7, os aplicativos também podem se registrar para atualizar o conteúdo em segundo plano usando *busca em segundo plano* ou *notificações remotas* .


## <a name="application-states-and-application-delegate-methods"></a>Estados do aplicativo e métodos de representante do aplicativo

Antes de nos aprofundarmos no código para processamento em segundo plano no iOS, precisamos entender como o plano de fundo afeta o ciclo de vida de um aplicativo iOS.

O ciclo de vida do aplicativo iOS é uma coleção de Estados e métodos do aplicativo para se mover entre eles. Um aplicativo faz a transição entre Estados com base no comportamento do usuário e nos requisitos de plano de fundo do aplicativo. O movimento é ilustrado pelo seguinte diagrama:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama dos Estados do aplicativo e dos métodos de representante do aplicativo")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **Não está em execução** -o aplicativo ainda não foi iniciado no dispositivo.
- **Running/active** -o aplicativo está na tela e está executando o código em primeiro plano.
- **Inativo** -o aplicativo é interrompido por uma chamada telefônica, texto ou outra interrupção de entrada.
- **Em segundo plano** -o aplicativo passa para o plano de fundo e continua executando o código em segundo plano.
- **Suspenso** – se o aplicativo não tiver nenhum código para ser executado em segundo plano ou se todo o código tiver sido concluído, o aplicativo será *suspenso* pelo sistema operacional. Um processo do aplicativo suspenso é mantido ativo, mas o aplicativo não pode executar nenhum código nesse estado.
- **Retornar para não em execução/término (raro)** – ocasionalmente, o processo do aplicativo é destruído e o aplicativo retorna para o estado *não em execução* . Isso ocorre em situações de pouca memória ou se o usuário finalizar o aplicativo manualmente.


Desde a introdução do suporte multitarefas, o iOS raramente encerra os aplicativos ociosos e, em vez disso, mantém seus processos suspensos na memória. Manter o Process Alive de um aplicativo garante que o aplicativo seja iniciado rapidamente na próxima vez que o usuário o abrir. Isso também significa que os aplicativos podem se mover livremente do estado *suspenso* de volta para o estado *em segundo plano* sem desenhar recursos do sistema. o iOS 7 explora esse recurso com novas APIs que permitem que os aplicativos pausem tarefas em segundo plano quando o dispositivo entra em suspensão, atualiza o conteúdo diretamente do plano de fundo sem interação do usuário e muito mais. Abordaremos as novas APIs em [técnicas de plano de fundo do IOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida do aplicativo

Quando um aplicativo muda de estado, o Ios notifica o aplicativo por meio de métodos `AppDelegate` de evento na classe:

- `OnActivated`-Isso é chamado na primeira vez em que o aplicativo é iniciado e sempre que o aplicativo volta a entrar no primeiro plano. Esse é o local para colocar o código que precisa ser executado toda vez que o aplicativo é aberto.
- `OnResignActivation`-Se o usuário receber uma interrupção, como um texto ou uma chamada telefônica, esse método será chamado e o aplicativo estará temporariamente desativado. Se o usuário aceitar a chamada telefônica, o aplicativo será enviado para o plano de fundo.
- `DidEnterBackground`– Chamado quando o aplicativo entra no estado em segundo plano, esse método dá a um aplicativo cerca de cinco segundos para se preparar para o encerramento possível. Use esse tempo para salvar dados e tarefas do usuário e remover informações confidenciais da tela.
- `WillEnterForeground`-Quando um usuário retorna para um aplicativo em segundo plano ou suspenso e o inicia em primeiro plano, `WillEnterForeground` é chamado. Esse é o momento de preparar o aplicativo para levar o primeiro plano por reidratar qualquer estado salvo `DidEnterBackground` durante.  `OnActivated`será chamado imediatamente depois que esse método for concluído.
- `WillTerminate`-O aplicativo é desligado e seu processo é destruído. Esse método só será chamado se a multitarefa não estiver disponível no dispositivo ou na versão do sistema operacional, se a memória estiver baixa ou se o usuário encerrar manualmente um aplicativo em segundo plano. Observe que os aplicativos suspensos que são encerrados `WillTerminate` não serão chamados.


O diagrama a seguir ilustra como os Estados do aplicativo e os métodos de ciclo de vida se ajustam:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Este diagrama ilustra como os Estados do aplicativo e os métodos de ciclo de vida se ajustam")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuário para o plano de fundo no iOS

o iOS 7 introduziu vários recursos para dar aos usuários mais controle sobre o estado em segundo plano de um aplicativo. O alternador de aplicativo e a configuração de atualização do aplicativo em segundo plano afetam o ciclo de vida do aplicativo.

### <a name="app-switcher"></a>Alternador de aplicativo

O alternador de aplicativo é um recurso de controle importante introduzido no iOS 7. Ele é iniciado com o toque duplo do botão **página inicial** e mostra os aplicativos cujos processos estão ativos:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Movendo entre aplicativos usando o alternador de aplicativo")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Usando o alternador de aplicativo, os usuários podem rolar por instantâneos de todos os aplicativos em segundo plano e suspensos. Tocar em um aplicativo o inicia no primeiro plano. O dedo para cima remove o aplicativo do plano de fundo, encerrando seu processo. Vamos examinar mais de perto o alternador de aplicativo na [demonstração do ciclo de vida do aplicativo IOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) na próxima seção.

> [!IMPORTANT]
> O alternador de aplicativo não mostra uma diferença entre aplicativos em segundo plano e suspensos.



### <a name="background-app-refresh-settings"></a>Configurações de atualização do aplicativo em segundo plano

o iOS 7 aumenta o controle de usuário sobre o ciclo de vida do aplicativo, permitindo que os usuários optem por aplicativos [registrados para processamento em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Isso não impede que os aplicativos executem tarefas em segundo plano*.

Os usuários podem alterar essa configuração navegando até **configurações > geral > atualização do aplicativo em segundo plano** e edição dos privilégios de plano de fundo de um aplicativo selecionado. Se a atualização do aplicativo em segundo plano estiver definida como off, o aplicativo será suspenso imediatamente após a inserção do plano de fundo e impedido de fazer qualquer processamento em segundo plano:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Configurações de atualização do aplicativo em segundo plano")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Os desenvolvedores podem verificar o status do aplicativo de atualização `BackgroundRefreshStatus` em segundo plano com a API. Para obter um exemplo, consulte a [receita verificar a configuração de atualização em segundo plano](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Nós abordamos os conceitos básicos do ciclo de vida do aplicativo iOS e os recursos para controlar o ciclo de vida do aplicativo. Em seguida, vamos ver o ciclo de vida do aplicativo iOS em ação.

