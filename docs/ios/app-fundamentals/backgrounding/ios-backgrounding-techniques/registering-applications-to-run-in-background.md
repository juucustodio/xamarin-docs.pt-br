---
title: Registrando aplicativos sejam executados em segundo plano
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 58ee68f765372094d68c3cf30ed6a631a67fe313
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="registering-applications-to-run-in-the-background"></a>Registrando aplicativos sejam executados em segundo plano

Registrando tarefas individuais para o plano de fundo privilégios funciona para alguns aplicativos, mas o que acontece se um aplicativo constantemente é chamado para realizar tarefas importantes de longa duração, como na obtenção de instruções para o usuário por meio de GPS? Aplicativos como estes devem ser registrados em vez disso, como aplicativos conhecidos do plano de fundo necessário.

Registrando um aplicativo sinaliza para iOS que o aplicativo deve ser dada especiais privilégios necessários para executar tarefas em segundo plano.

## <a name="application-registration-categories"></a>Categorias de registro de aplicativo

Aplicativos registrados podem se enquadrar em várias categorias:

-  **Áudio** -players de música e outros aplicativos que funcionam com o conteúdo de áudio podem ser registrados para continuar a reproduzir áudio, mesmo quando o aplicativo não está mais em primeiro plano. Se um aplicativo nessa categoria tentar fazer algo diferente de áudio play ou download em segundo plano, o iOS a terminará.
-  **VoIP** -obtém aplicativos de voz pela Internet Protocol (VoIP) dos mesmos privilégios concedidos a aplicativos de áudio para manter processamento de áudio em segundo plano. Eles também têm permissão para responder conforme necessário para os serviços de VoIP-los, para manter a conexão com a atividade de energia.
-  **Acessórios e Bluetooth externo** -reservado para aplicativos que precisam se comunicar com dispositivos Bluetooth e outros acessórios de hardware externo, registro sob essas categorias permite que o aplicativo permaneça conectado ao hardware.
-  **Newsstand** -aplicativo de Newsstand A pode continuar a sincronização de conteúdo em segundo plano.
-  **Local** - aplicativos que fazem usam de GPS ou dados de local de rede podem enviar e receber atualizações do local no plano de fundo.
-  **Busca (iOS 7 +)** : um aplicativo registrado para privilégios de busca em segundo plano podem verificar um provedor para o conteúdo novo em intervalos regulares, apresentando o usuário com o conteúdo atualizado quando eles retornarem ao aplicativo.
-  **Notificações remotas (iOS 7 +)** -aplicativos podem se registrar para receber notificações de um provedor e use a notificação para disparar uma atualização antes do usuário abre o aplicativo. Notificações podem vir na forma de notificações por push, ou optar por ativar o aplicativo silenciosamente.


Aplicativos podem ser registrados, definindo o **modos de segundo plano necessário** propriedade do aplicativo *Info. plist*. Um aplicativo pode registrar no tantas categorias quantas requer:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Definir os modos de plano de fundo")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Para obter um guia passo a passo para registrar um aplicativo para atualizações de local do plano de fundo, consulte o [passo a passo do plano de fundo local](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Aplicativo não é executado na propriedade de plano de fundo

Outra propriedade que pode ser definida em *Info. plist* é o *aplicativo não é executado em segundo plano*, ou `UIApplicationExitsOnSuspend` propriedade:

 [![](registering-applications-to-run-in-background-images/plist.png "Desabilitar a execução do plano de fundo")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Isso tem exatamente o mesmo efeito que definir a configuração de atualização de aplicativo em segundo plano como off no iOS 7 +, exceto que ela só pode ser alterada no lado do desenvolvedor e está disponível para iOS 4 e posterior. O aplicativo será suspenso imediatamente depois de inserir o plano de fundo e não será possível fazer qualquer processamento.

Use essa propriedade se seu aplicativo não foi projetado para lidar com processamento em segundo plano, pois isso ajuda a evitar um comportamento inesperado.

## <a name="background-fetch-and-remote-notifications"></a>Busca em segundo plano e notificações remotas

Busca em segundo plano e notificações remotas são categorias de registro especial introduzidas no iOS 7. Essas categorias permitem que aplicativos receber o novo conteúdo de um provedor e atualização em segundo plano. A próxima seção explora a busca e notificações remotas mais detalhadamente e também apresenta o reconhecimento de local como significa para a atualização de um aplicativo em segundo plano no iOS 6.
