---
title: Registrar aplicativos do xamarin. IOS para execução em segundo plano
description: Este documento descreve como registrar um aplicativo xamarin. IOS para ser executado em segundo plano. Ele aborda áudio aplicativos, VoIP aplicativos, Acessórios externos e bluetooth e muito mais.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a0a66571d0249ef6fd65ff382f14c38f48a8af37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61393684"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Registrar aplicativos do xamarin. IOS para execução em segundo plano

Registrando tarefas individuais do plano de fundo privilégios funciona para alguns aplicativos, mas o que acontece se um aplicativo constantemente é chamado para executar tarefas importantes de longa, como obter direções para o usuário por meio do GPS? Aplicativos como esses em vez disso, devem ser registrados como aplicativos conhecidos do plano de fundo necessário.

Ao registrar um aplicativo de sinais para iOS que o aplicativo deve ser emitido privilégios especiais necessários para executar tarefas em segundo plano.

## <a name="application-registration-categories"></a>Categorias de registro de aplicativo

Aplicativos registrados podem se enquadrar em várias categorias:

-  **Áudio** -players de música e outros aplicativos que funcionam com o conteúdo de áudio podem ser registrados para continuar a reprodução de áudio, mesmo quando o aplicativo não está mais em primeiro plano. Se um aplicativo nessa categoria tenta fazer algo diferente de reproduzir áudio ou baixar enquanto estiver no plano de fundo, o iOS a encerrará.
-  **VoIP** -aplicativos de voz sobre IP (VoIP) get do mesmos privilégios concedidos a aplicativos de áudio ao manter processamento de áudio em segundo plano. Eles também têm permissão para responder conforme necessário para os serviços de VoIP que ativá-las para manter suas conexões ativas.
-  **Bluetooth e Acessórios externos** -reservada para aplicativos que precisam se comunicar com dispositivos Bluetooth e outros acessórios de hardware externo, registro sob essas categorias permite que o aplicativo permanecer conectado ao hardware.
-  **Newsstand** -aplicativo de um Newsstand pode continuar a sincronizar o conteúdo em segundo plano.
-  **Local** – aplicativos que fazem usam do GPS ou dados de local de rede podem enviar e receber atualizações de local em segundo plano.
-  **Busca (iOS 7 +)** : um aplicativo registrado para privilégios de busca em segundo plano podem verificar um provedor para o novo conteúdo em intervalos regulares, apresentando ao usuário a conteúdo atualizado quando eles retornarem ao aplicativo.
-  **Notificações remotas (iOS 7 +)** -aplicativos podem se registrar para receber notificações de um provedor e usar a notificação para disparar uma atualização antes do usuário abre o aplicativo. As notificações podem vêm na forma de notificações por push ou optar por ativar o aplicativo silenciosamente.


Aplicativos podem ser registrados, definindo o **modos de segundo plano necessários** propriedade na caixa de diálogo *Info. plist*. Um aplicativo pode registrar no tantas categorias quantas requer:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Definir os modos em segundo plano")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obter um guia passo a passo para registrar um aplicativo para atualizações de localização em segundo plano, consulte o [instruções passo a passo de localização do plano de fundo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Aplicativo não é executado na propriedade de plano de fundo

Outra propriedade que pode ser definida em *Info. plist* é o *aplicativo não é executado em segundo plano*, ou `UIApplicationExitsOnSuspend` propriedade:

 [![](registering-applications-to-run-in-background-images/plist.png "Desabilitando em segundo plano em execução")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Isso tem exatamente o mesmo efeito que definir a configuração de atualização de aplicativo em segundo plano a desativado no iOS 7 +, exceto que ele só pode ser alterado no lado do desenvolvedor e está disponível para iOS 4 e acima. O aplicativo será suspensa imediatamente depois de inserir o plano de fundo e não será capaz de fazer qualquer processamento.

Use essa propriedade se seu aplicativo não foi projetado para lidar com processamento em segundo plano, pois ela ajuda a evitar um comportamento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Busca em segundo plano e notificações remotas

Busca em segundo plano e notificações remotas são categorias de registro especial introduzidas no iOS 7. Essas categorias permitem que os aplicativos receber o novo conteúdo de um provedor e de atualização em segundo plano. A próxima seção explora a busca e notificações remotas mais detalhadamente e também apresenta reconhecimento de locais como meio para atualizar um aplicativo em segundo plano no iOS 6.
