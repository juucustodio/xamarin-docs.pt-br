---
title: Introdução ao Backgrounding no iOS
description: 'Este documento descreve backgrounding no iOS: Estados de aplicativo, os métodos de ciclo de vida de aplicativos e atualização de aplicativo em segundo plano.'
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 804a99817f664989bbac67a4c662357f4ee628c5
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242271"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introdução ao Backgrounding no iOS

iOS regula rigidamente de processamento em segundo plano e oferece três abordagens para implementá-lo:

-  **Registrar uma tarefa em segundo plano** -se um aplicativo precisa concluir uma tarefa importante, ele poderá pedir iOS para não interromper a tarefa quando o aplicativo é movido na tela de fundo. Por exemplo, um aplicativo pode precisar concluir o registro em log em um usuário ou concluir o download de um arquivo grande.
-  **Registrar como um aplicativo necessário do plano de fundo** -um aplicativo pode se registrar como um tipo específico de aplicativo que tem conhecidos, requisitos de backgrounding específicos, tais como *áudio* , *VoIP* ,  *Acessório externo* , *Newsstand* , e *local* . Esses aplicativos são permitidos em segundo plano contínua privilégios de processamento, desde que eles estão realizando tarefas que estão dentro dos parâmetros do tipo de aplicativo registrado.
-  **Habilitar as atualizações do plano de fundo** -aplicativos podem disparar atualizações em segundo plano com *monitoramento de região* ou através da escuta para *alterações significativas do local* . A partir do iOS 7, os aplicativos também podem registrar para atualizar o conteúdo usando o plano de fundo *busca em segundo plano* ou *notificações remotas* .


## <a name="application-states-and-application-delegate-methods"></a>Estados de aplicativo e os métodos de representante de aplicativo

Antes de mergulhar no código de processamento no iOS em segundo plano, precisamos entender como backgrounding afeta o ciclo de vida de um aplicativo iOS.

O ciclo de vida do aplicativo iOS é uma coleção de estados do aplicativo e métodos para mover entre elas. Um aplicativo faz a transição entre estados com base em como o comportamento do usuário e os requisitos de backgrounding do aplicativo. O movimento é ilustrado pelo diagrama a seguir:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama de estados de aplicativo e os métodos de representante de aplicativo")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **Não está em execução** -o aplicativo ainda não foi iniciado no dispositivo.
-  **Execução/ativo** -o aplicativo está na tela e está executando o código em primeiro plano.
-  **Inativo** -o aplicativo é interrompido por uma chamada telefônica, texto ou outra interrupção.
-  **Colocado em segundo plano** -o aplicativo passa para o plano de fundo e continua executando código em segundo plano.
-  **Suspended** : se o aplicativo não tem qualquer código seja executado em segundo plano, ou se todo o código tiver sido concluída, o aplicativo será *Suspended* pelo sistema operacional. Processo do aplicativo um suspenso é mantido ativo, mas o aplicativo não é possível executar qualquer código nesse estado.
-  **Retornar à não execução/término (Rare)** - às vezes, o processo do aplicativo é destruído e o aplicativo retorna para o *não está em execução* estado. Isso acontece em situações de memória baixa, ou se o usuário encerra manualmente o aplicativo.


Desde a introdução do suporte de multitarefa, iOS raramente é encerrado aplicativos ociosos e, em vez disso, mantém seus processos *Suspended* na memória. Mantém o processo de um aplicativo garante que o aplicativo é iniciado rapidamente na próxima vez que o usuário o abrir. Isso também significa que os aplicativos podem se mover livremente do *Suspended* estado de volta para o *Backgrounded* estado sem desenhar em recursos do sistema. iOS 7 explora esse recurso com novas APIs que permitem que os aplicativos pausar tarefas em segundo plano quando o dispositivo entra em suspensão, o conteúdo de atualização diretamente do segundo plano sem interação do usuário e muito mais. Abordaremos as novas APIs no [técnicas de Backgrounding do iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida do aplicativo

Quando um aplicativo muda de estado, o iOS notifica o aplicativo por meio de métodos de evento no `AppDelegate` classe:

-  `OnActivated` -Isso é chamado na primeira vez em que o aplicativo é iniciado, e sempre que o aplicativo volta a entrar em primeiro plano. Isso é o lugar para colocar o código que precisa ser executado sempre que o aplicativo é aberto.
-  `OnResignActivation` -Se o usuário recebe uma interrupção como um texto ou chamada telefônica, esse método é chamado e o aplicativo é desativado temporariamente. O usuário deve aceitar a chamada telefônica, o aplicativo será enviado para o plano de fundo.
-  `DidEnterBackground` -Chamado quando o aplicativo entra no estado backgrounded, esse método fornece um aplicativo cerca de cinco segundos para se preparar para a finalização possível. Use esse tempo para salvar dados de usuário e tarefas e remover informações confidenciais da tela.
-  `WillEnterForeground` -Quando um usuário retorna a um aplicativo suspenso ou colocado em segundo plano e ele é iniciado em primeiro plano, `WillEnterForeground` é chamado. Isso é o tempo para preparar o aplicativo realizar o primeiro plano ao reidratar qualquer estado salvo durante `DidEnterBackground` .  `OnActivated` será chamado imediatamente após a conclusão desse método.
-  `WillTerminate` -O aplicativo é desligado e seu processo é destruído. Esse método só é chamado se multitarefa não está disponível no dispositivo ou a versão do sistema operacional, se houver pouca memória ou se o usuário termina manualmente um aplicativo backgrounded. Observe que os aplicativos suspensos que seria encerrados não chamará `WillTerminate` .


O diagrama a seguir ilustra como o aplicativo declara e métodos de ciclo de vida se encaixam:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Este diagrama ilustra como o aplicativo declara e métodos de ciclo de vida se encaixam")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuário para Backgrounding no iOS

iOS 7 introduziu vários recursos para dar aos usuários mais controle sobre backgrounded estado de um aplicativo. O seletor de aplicativo e a configuração de atualização de aplicativo em segundo plano afetam o ciclo de vida do aplicativo.

### <a name="app-switcher"></a>Alternador de aplicativo

O seletor de aplicativo é um recurso de controle importante introduzido no iOS 7. Ele é iniciado tocando duas vezes. o **Home** botão e, em seguida, mostra os aplicativos cujos processos estão ativos:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Movendo entre aplicativos usando o seletor de aplicativo")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Usando o seletor de aplicativo, os usuários podem rolar pelos instantâneos de todos os aplicativos suspensos e colocado em segundo plano. Tocando em um aplicativo, ele inicia em primeiro plano. Passar o dedo para cima remove o aplicativo do plano de fundo, encerrando o seu processo. Vamos utilizar de perto o alternador de aplicativo na [demonstração do ciclo de vida do aplicativo do iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) na próxima seção.

> [!IMPORTANT]
> O seletor de aplicativo não mostram a diferença entre aplicativos suspensos e colocado em segundo plano.



### <a name="background-app-refresh-settings"></a>Configurações de atualização de aplicativo de tela de fundo

iOS 7 aumenta o controle de usuário sobre o ciclo de vida do aplicativo, permitindo que os usuários a recusar backgrounding para aplicativos [registrados para processamento em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Isso não impede que aplicativos executem tarefas em segundo plano*.

Os usuários podem alterar essa configuração, navegando até <span class="uiitem">Configurações > Geral > atualização de aplicativo em segundo plano</span> e editando os privilégios backgrounding para um aplicativo selecionado. Se a atualização de aplicativo em segundo plano é definida como off, o aplicativo será suspensa imediatamente ao inserir o plano de fundo e impedido de executar qualquer processamento em segundo plano:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Configurações de atualização de aplicativo de tela de fundo")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Os desenvolvedores podem verificar o status de aplicativo de atualização em segundo plano com o `BackgroundRefreshStatus` API. Para obter um exemplo, consulte o [receita Verificar configuração de atualização em segundo plano](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Já abordamos os conceitos básicos do ciclo de vida do aplicativo do iOS e recursos para controlar o ciclo de vida do aplicativo. Em seguida, vamos ver o ciclo de vida do aplicativo iOS em ação.

