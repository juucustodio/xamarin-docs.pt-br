---
title: Introdução ao Backgrounding no iOS
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 3a82a34a37e53e0c6922ef47717a4100576c2277
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introdução ao Backgrounding no iOS

iOS regula rigidamente processamento em segundo plano e oferece três abordagens para implementá-la:

-  **Registrar uma tarefa em segundo plano** -se um aplicativo precisa concluir uma tarefa importante, ele pode pedir iOS não para interromper a tarefa quando o aplicativo é movido para o plano de fundo. Por exemplo, um aplicativo, talvez seja necessário concluir o registro em log em um usuário ou a conclusão do download de um arquivo grande.
-  **Registrar como um aplicativo necessário do plano de fundo** -um aplicativo pode registrar como um tipo específico de aplicativo que tem conhecido, requisitos de backgrounding específicos, tais como *áudio* , *VoIP* ,  *Acessório externo* , *Newsstand* , e *local* . Esses aplicativos são permitidos em segundo plano contínua processamento privilégios enquanto eles estão executando tarefas que estão dentro dos parâmetros do tipo de aplicativo registrado.
-  **Habilitar atualizações de plano de fundo** -aplicativos podem disparar atualizações em segundo plano com *região monitoramento* ou aguardando *alterações significativas de local* . A partir do iOS 7, os aplicativos também podem registrar para atualizar o conteúdo usando o plano de fundo *buscar plano de fundo* ou *notificações remoto* .


## <a name="application-states-and-application-delegate-methods"></a>Estados de aplicativo e os métodos de representante do aplicativo

Antes de mergulhar no código de processamento no iOS em segundo plano, é necessário entender como backgrounding afeta o ciclo de vida de um aplicativo iOS.

O ciclo de vida do aplicativo do iOS é uma coleção de estados do aplicativo e os métodos para mover entre elas. Um aplicativo faz a transição entre estados com base no comportamento do usuário e os requisitos de backgrounding do aplicativo. A movimentação é ilustrada o diagrama a seguir:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama de estados de aplicativo e os métodos de representante do aplicativo")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **Não está em execução** -o aplicativo ainda não foi iniciado no dispositivo.
-  **Execução/ativo** -o aplicativo está na tela e está executando código em primeiro plano.
-  **Inativo** -o aplicativo é interrompido por uma chamada telefônica, texto ou outra interrupção.
-  **Backgrounded** -o aplicativo é movido para o plano de fundo e continua executando código em segundo plano.
-  **Suspended** - se o aplicativo não tem nenhum código para ser executado em segundo plano, ou se todo o código foi concluída, o aplicativo será *suspenso* pelo sistema operacional. Processo do aplicativo um suspenso é mantido ativo, mas o aplicativo não é possível executar qualquer código nesse estado.
-  **Retornar a não execução/encerramento (Rare)** - às vezes, o processo do aplicativo é destruído e o aplicativo retorna para o *não está em execução* estado. Isto acontece em situações de memória baixa, ou se o usuário termina manualmente o aplicativo.


Desde a introdução de suporte de multitarefa, iOS raramente encerra aplicativos ociosos e mantém em vez disso, os processos *suspenso* na memória. Manter o processo de um aplicativo de funcionamento garante que o aplicativo é iniciado rapidamente na próxima vez que o usuário o abrir. Isso também significa que os aplicativos podem mover livremente do *suspenso* estado de volta para o *Backgrounded* estado sem desenhar recursos do sistema. iOS 7 explora esse recurso com novas APIs que permitem que aplicativos pausar tarefas em segundo plano quando o dispositivo entra em suspensão, o conteúdo de atualização diretamente do plano de fundo sem interação do usuário e muito mais. Abordaremos as novas APIs no [iOS Backgrounding técnicas](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida do aplicativo

Quando um aplicativo muda de estado, o iOS notifica o aplicativo por meio de métodos de evento no `AppDelegate` classe:

-  `OnActivated` -Isso é chamado na primeira vez em que o aplicativo é iniciado, e sempre que o aplicativo de volta em primeiro plano. Este é o local para colocar o código que precisa ser executado toda vez que o aplicativo é aberto.
-  `OnResignActivation` -Se o usuário recebe uma interrupção como um texto ou chamada telefônica, esse método é chamado e o aplicativo é desativado temporariamente. O usuário deve aceitar a chamada telefônica, o aplicativo será enviado para o plano de fundo.
-  `DidEnterBackground` -Esse método chamado quando o aplicativo entra no estado backgrounded, oferece ao aplicativo cerca de cinco segundos para se preparar para encerramento possíveis. Use esse tempo para salvar os dados de usuário e tarefas e remover informações confidenciais da tela.
-  `WillEnterForeground` -Quando um usuário retorna a um aplicativo backgrounded ou suspenso e inicia o primeiro plano, `WillEnterForeground` é chamado. Esse é o momento para preparar o aplicativo seja o primeiro plano por reidratar qualquer estado salvo durante a `DidEnterBackground` .  `OnActivated` será chamado imediatamente depois que esse método é concluído.
-  `WillTerminate` -O aplicativo for encerrado, e seu processo é destruído. Esse método só será chamado se multitarefa não está disponível no dispositivo ou a versão do sistema operacional, se houver pouca memória ou se o usuário termina manualmente um aplicativo backgrounded. Observe que aplicativos suspensos encerrados não chamará `WillTerminate` .


O diagrama a seguir ilustra como o aplicativo declara e métodos de ciclo de vida se encaixam:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Este diagrama ilustra como o aplicativo declara e métodos de ciclo de vida se encaixam")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuário para Backgrounding no iOS

iOS 7 introduziu vários recursos para dar aos usuários mais controle sobre o estado backgrounded do aplicativo. O seletor de exibição do aplicativo e a configuração de atualização de aplicativo em segundo plano afetam o ciclo de vida do aplicativo.

### <a name="app-switcher"></a>Alternador de aplicativo

O seletor de exibição do aplicativo é um recurso de controle importante introduzido no iOS 7. Ele é iniciado por um toque duplo o **início** botão e mostra os aplicativos cujos processos estão ativo:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Movendo entre aplicativos usando o seletor de exibição do aplicativo")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Usando o seletor de exibição do aplicativo, os usuários podem rolar pelos instantâneos de todos os aplicativos backgrounded e suspensos. Ao tocar em um aplicativo inicia no primeiro plano. Passar o dedo para cima remove o plano de fundo, encerrando o processo do aplicativo. Nós o conduziremos detalhadamente o alternador de aplicativo no [demonstração de ciclo de vida do aplicativo do iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) na próxima seção.

> [!IMPORTANT]
> O seletor de exibição do aplicativo não mostra a diferença entre aplicativos backgrounded e foram suspensos.



### <a name="background-app-refresh-settings"></a>Configurações de atualização de aplicativo em segundo plano

iOS 7 aumenta o controle de usuário sobre o ciclo de vida do aplicativo, permitindo que os usuários recusar backgrounding para aplicativos [registrado para o processamento em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Isso não impede que aplicativos executem tarefas em segundo plano*.

Os usuários podem alterar essa configuração, navegando para <span class="uiitem">Configurações > Geral > atualização de aplicativo em segundo plano</span> e editar os privilégios backgrounding para um aplicativo selecionado. Se a atualização de aplicativo em segundo plano é definido como off, o aplicativo ser suspenso imediatamente após a inserção de plano de fundo e impedido de executar qualquer processamento em segundo plano:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Configurações de atualização de aplicativo em segundo plano")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Os desenvolvedores podem verificar o status do aplicativo de atualização em segundo plano com o `BackgroundRefreshStatus` API. Para obter um exemplo, consulte o [receita de verificação em segundo plano de atualização de configuração](https://developer.xamarin.com/recipes/ios/multitasking/check_background_refresh_setting/).

Abordamos os fundamentos do ciclo de vida do aplicativo do iOS e recursos para controlar o ciclo de vida do aplicativo. Em seguida, vamos ver o ciclo de vida do aplicativo do iOS em ação.

