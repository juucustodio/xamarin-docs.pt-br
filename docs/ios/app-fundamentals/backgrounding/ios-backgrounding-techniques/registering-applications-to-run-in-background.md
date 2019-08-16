---
title: Registrando aplicativos Xamarin. iOS para serem executados em segundo plano
description: Este documento descreve como registrar um aplicativo Xamarin. iOS para ser executado em segundo plano. Ele aborda aplicativos de áudio, aplicativos de VoIP, acessórios externos e Bluetooth e muito mais.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 67cc2028276b6e1415c88cb8957e2cd567fa5bae
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521268"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Registrando aplicativos Xamarin. iOS para serem executados em segundo plano

O registro de tarefas individuais para privilégios em segundo plano funciona para alguns aplicativos, mas o que acontece se um aplicativo é constantemente chamado para executar tarefas importantes e de execução longa, como obter direções para o usuário via GPS? Aplicativos como esses devem ser registrados como aplicativos necessários em segundo plano conhecidos.

O registro de um aplicativo sinaliza para o iOS que o aplicativo deve receber privilégios especiais necessários para executar tarefas em segundo plano.

## <a name="application-registration-categories"></a>Categorias de registro do aplicativo

Os aplicativos registrados podem se enquadrar em várias categorias:

- **Áudio** – players de música e outros aplicativos que funcionam com conteúdo de áudio podem ser registrados para continuar reproduzindo áudio mesmo quando o aplicativo não estiver mais em primeiro plano. Se um aplicativo nesta categoria tentar fazer algo diferente de reproduzir áudio ou baixar enquanto estiver em segundo plano, o iOS o encerrará.
- **VoIP** -os aplicativos VoIP (Voice over Internet Protocol) obtêm os mesmos privilégios concedidos a aplicativos de áudio para manter o processamento de áudio em segundo plano. Eles também podem responder conforme necessário para os serviços de VoIP que os alimentam, para manter suas conexões ativas.
- **Acessórios externos e Bluetooth** -reservado para aplicativos que precisam se comunicar com dispositivos Bluetooth e outros acessórios de hardware externo, o registro nessas categorias permite que o aplicativo permaneça conectado ao hardware.
- **Newsstand** -um aplicativo Newsstand pode continuar a sincronizar conteúdo em segundo plano.
- **Local** -os aplicativos que fazem uso de GPS ou dados de localização de rede podem enviar e receber atualizações de local em segundo plano.
- **Fetch (Ios 7 +)** -um aplicativo registrado para privilégios de busca em segundo plano pode verificar um provedor em busca de conteúdo novo em intervalos regulares, apresentando ao usuário o conteúdo atualizado quando eles retornam ao aplicativo.
- **Notificações remotas (Ios 7 +)** -os aplicativos podem se registrar para receber notificações de um provedor e usar a notificação para iniciar uma atualização antes que o usuário abra o aplicativo. As notificações podem vir na forma de notificações por Push ou optar por ativar o aplicativo silenciosamente.


Os aplicativos podem ser registrados definindo a propriedade de **modos de segundo plano necessária** no *info. plist*do aplicativo. Um aplicativo pode se registrar em quantas categorias forem necessárias:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Configurando os modos de segundo plano")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obter um guia passo a passo para registrar um aplicativo para atualizações de local de segundo plano, consulte o [passo a passos local do plano de fundo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>O aplicativo não é executado na propriedade de segundo plano

Outra propriedade que pode ser definida em *info. plist* é o *aplicativo não é executado em segundo plano*ou `UIApplicationExitsOnSuspend` Propriedade:

 [![](registering-applications-to-run-in-background-images/plist.png "Desabilitando a execução em segundo plano")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Isso tem exatamente o mesmo efeito que definir a configuração de atualização do aplicativo em segundo plano como off no iOS 7 +, exceto que só pode ser alterada no lado do desenvolvedor e está disponível para o iOS 4 e superior. O aplicativo será suspenso imediatamente após a inserção do plano de fundo e não poderá fazer nenhum processamento.

Use essa propriedade se seu aplicativo não for projetado para lidar com o processamento em segundo plano, pois ajuda a evitar um comportamento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Busca em segundo plano e notificações remotas

A busca em segundo plano e as notificações remotas são categorias de registro especiais introduzidas no iOS 7. Essas categorias permitem que os aplicativos recebam novo conteúdo de um provedor e atualizem em segundo plano. A próxima seção explora as notificações de busca e remota com mais detalhes e também apresenta o reconhecimento de local como meio de atualizar um aplicativo em segundo plano no iOS 6.
