---
title: Atualizando um aplicativo xamarin. IOS em segundo plano
description: Este documento descreve várias maneiras de atualizar um aplicativo xamarin. IOS que está em segundo plano, como monitoramento de região, busca em segundo plano e notificações remotas.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 835dccaea79467582f56fd4b8b6b3b8f42acd632
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103225"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Atualizando um aplicativo xamarin. IOS em segundo plano

Atualização em segundo plano é o processo de despertar um aplicativo que está suspenso ou não está em execução e atualizá-lo com novo conteúdo. iOS fornece três opções para atualizar conteúdo em segundo plano:

1.  *Monitoramento de região* e *serviço significativo de alterações local* -atualizações de tela de fundo de gatilho de APIs com reconhecimento de local com base nas alterações no local do usuário. Essas APIs podem ser usadas com critério para atualizar conteúdo em aplicativos baseados na localização iOS 6, onde outras opções não estão disponíveis.
1.  *Em segundo plano (iOS 7 +) de busca* -uma abordagem temporal para Atualizando *não críticos* conteúdo que atualiza *frequentemente* .
1.  *Notificações remotas (iOS 7 +)* -aplicativos que recebem notificações por push podem usar as notificações para disparar atualizações de conteúdo em segundo plano. Esse método pode ser usado para atualizar com *importante, detecção de hora* conteúdo atualiza *esporadicamente* .


As seções a seguir abordam as Noções básicas dessas opções.

## <a name="region-monitoring-and-significant-location-changes"></a>Monitoramento de região e as alterações de localização significativa

iOS fornece duas APIs com reconhecimento de local com recursos de backgrounding:

1.  *Monitoramento de região* é o processo de configuração de regiões com os limites e ativando o dispositivo quando o usuário entra ou sai de uma região. Regiões são circulares e podem ser de tamanhos variados. Quando o usuário cruza um limite de região, o dispositivo será ativado para manipular o evento, normalmente por disparar uma notificação ou iniciar uma tarefa. Monitoramento de região requer GPS e aumenta a bateria e o uso de dados.
1.  O *serviço significativo de alterações local* é uma opção mais simples, a preservação de energia disponíveis para dispositivos com o celulares rádios. Um aplicativo de escuta de alterações significativas local será notificado quando o dispositivo alterna torres de célula. Esse serviço pode ser usado para ativar um aplicativo suspenso ou encerrado e fornece uma oportunidade para verificar se há novo conteúdo em segundo plano. Atividade em segundo plano é limitada a cerca de 10 segundos, a menos que emparelhado com um [tarefa em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Um aplicativo não precisa o local `UIBackgroundMode` usar essas APIs com reconhecimento de local. Porque o iOS não controla os tipos de tarefas que podem ser executados quando o dispositivo é ativado por alterações no local do usuário, essas APIs fornecem uma solução alternativa para atualizar conteúdo em segundo plano no iOS 6. *Tenha em mente que disparar atualizações em segundo plano com as APIs baseadas na localização desenhará em recursos do dispositivo e pode confundir os usuários que não entendem por que um aplicativo requer acesso ao seu local de*. Use critério ao implementar o monitoramento de região ou alterações significativas do local para processamento em aplicativos que já não estiverem usando as APIs de local em segundo plano.

Os aplicativos usando o monitoramento de local para processamento em segundo plano expõem uma falha no iOS 6: se as necessidades do aplicativo não se encaixam em uma categoria de plano de fundo necessário, ele tem opções de backgrounding limitadas. Com a introdução de duas novas APIs *busca em segundo plano* e *notificações remotas*, iOS 7 (e superior) fornece oportunidades backgrounding a mais aplicativos. As próximas duas seções introduzem essas novas APIs.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Busca em segundo plano (iOS 7 e superior)

No iOS 6, um aplicativo inserindo o primeiro plano necessário tempo para carregar o novo conteúdo, apresentando brevemente os usuários com conteúdo que você já viu. Busca em segundo plano permite que os aplicativos carreguem dados novos *antes de* um usuário inicia o aplicativo e fornecer ao usuário com o conteúdo mais atualizado.

Para implementar a busca em segundo plano, edite *Info. plist* e verifique o **habilitar modos de plano de fundo** e **busca em segundo plano** caixas de seleção:

 [![](updating-an-application-in-the-background-images/fetch.png "Editar Info. plist e marque as caixas de seleção Habilitar modos de segundo plano e a busca em segundo plano")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Em seguida, no `AppDelegate`, substitua o `FinishedLaunching` método para definir o intervalo mínimo de busca. Neste exemplo, podemos permitir que o sistema operacional decida frequência para buscar o novo conteúdo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por fim, executar a busca, substituindo o `PerformFetch` método na `AppDelegate`e passando um *manipulador de conclusão*. O manipulador de conclusão é um delegado que utiliza um `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Quando terminamos a atualização do conteúdo, podemos informar o sistema operacional ao chamar o manipulador de conclusão com o status apropriado. iOS oferece três opções para o status do manipulador de conclusão:

1.  `UIBackgroundFetchResult.NewData` -Chamado quando novo conteúdo foi buscado, e o aplicativo foi atualizado.
1.  `UIBackgroundFetchResult.NoData` -Chamado quando a busca de novos conteúdos passou, mas nenhum conteúdo está disponível.
1.  `UIBackgroundFetchResult.Failed` -Útil para tratamento de erro, isso é chamado quando a busca não foi possível percorrer.


Aplicativos que usam a busca em segundo plano podem fazer chamadas para atualizar a interface do usuário do plano de fundo. Quando o usuário abre o aplicativo, a interface do usuário será até a data e a exibição de conteúdo novo. Isso também atualizará o instantâneo do alternador de aplicativo do aplicativo, para que o usuário possa ver quando o aplicativo tiver conteúdo novo.

> [!IMPORTANT]
> Uma vez `PerformFetch` é chamado, o aplicativo tem aproximadamente 30 segundos para iniciar o download do conteúdo novo e, em seguida, chame o bloco do manipulador de conclusão. Se isso leva muito tempo, o aplicativo será encerrado. Considere o uso de busca em segundo plano com o _o serviço de transferência em segundo plano_ ao fazer o download de mídia ou outros arquivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

No código de exemplo acima, podemos permitir que o sistema operacional decida frequência para buscar o novo conteúdo, definindo o intervalo mínimo de busca para `BackgroundFetchIntervalMinimum`. iOS oferece três opções para o intervalo de busca:

1.  `BackgroundFetchIntervalNever` -Informam ao sistema nunca Buscar conteúdo novo. Use isso para desativar buscando em determinadas situações, como quando o usuário não está conectado. Isso é o valor padrão para o intervalo de busca. 
1.  `BackgroundFetchIntervalMinimum` -Permite que o sistema decidir com que frequência buscar com base em padrões do usuário, vida útil da bateria, uso de dados e as necessidades de outros aplicativos.
1.  `BackgroundFetchIntervalCustom` – Se você souber que a frequência com que o conteúdo de um aplicativo é atualizado, você pode especificar um intervalo de "sleep" após cada busca, durante o qual o aplicativo será impedido de buscar conteúdo novo. Depois que o intervalo estiver em execução, o sistema determinará quando buscar o conteúdo.


Ambos `BackgroundFetchIntervalMinimum` e `BackgroundFetchIntervalCustom` depender do sistema para agendar a buscas. Este intervalo é dinâmico, a adaptação às necessidades do dispositivo, bem como os hábitos de usuário individual. Por exemplo, se um usuário verifica a um aplicativo de todas as manhãs, e outro verificações a cada hora, iOS garantirá que o conteúdo é atualizado para os usuários sempre que abrirem o aplicativo.

Busca em segundo plano deve ser usada para aplicativos que atualizam com frequência com o conteúdo não críticos. Para aplicativos com atualizações críticas, notificações remotas deve ser usadas. Notificações remotas se baseiam na busca em segundo plano e compartilham o mesmo manipulador de conclusão. Nos aprofundaremos nas notificações remotas em seguida.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificações remotas (iOS 7 e superior)

Notificações por push são mensagens JSON enviadas de um provedor para um dispositivo por meio das *Apple Push Notification service (APNs)*.

No iOS 6, notificações de push entrada informa o sistema para alertar o usuário que algo interessante aconteceu em um aplicativo. Clicar na notificação recebe o aplicativo fora do estado suspenso ou foi encerrado e o aplicativo começaria a atualização do conteúdo. iOS 7 (e superior) estende as notificações por push comum fornecendo aos aplicativos uma chance de atualizar o conteúdo na tela de fundo *antes de* notificar o usuário, para que o usuário pode abrir o aplicativo e apresentado com novo conteúdo imediatamente.

Para implementar notificações remotas, edite *Info. plist* e verifique o **habilitar modos de plano de fundo** e **notificações remotas** caixas de seleção:

 [![](updating-an-application-in-the-background-images/remote.png "Modo de tela de fundo definido como habilitar modos de segundo plano e notificações remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

Em seguida, defina o `content-available` sinalizador de notificação por push em si como 1. Isso permite que o aplicativo saiba para buscar o novo conteúdo antes de exibir o alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

No *AppDelegate*, substitua o `DidReceiveRemoteNotification` método para verificar a carga de notificação para o conteúdo disponível e, em seguida, chamar o bloco de manipulador de conclusão apropriado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notificações remotas devem ser usadas para atualizações não frequentes com conteúdo que é crucial para a funcionalidade do aplicativo. Para obter mais informações sobre notificações remotas, consulte o Xamarin [notificações por Push no iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) guia.

> [!IMPORTANT]
> Como o mecanismo de atualização em notificações remotas é baseado na busca em segundo plano, o aplicativo deve disparar o download do conteúdo novo e chamar o bloco do manipulador de conclusão dentro de 30 segundos de receber a notificação ou iOS para encerrar o aplicativo. Considere o emparelhamento remoto notificações com _o serviço de transferência em segundo plano_ ao fazer o download de mídia ou outros arquivos grandes em segundo plano.


### <a name="silent-remote-notifications"></a>Notificações remotas silenciosas

Notificações remotas são uma maneira simples para notificar aplicativos de atualizações e disparar Buscar conteúdo novo, mas há casos em que não precisamos notificar o usuário que algo foi alterado. Por exemplo, se um usuário sinaliza um arquivo para sincronização, não precisamos para notificá-los sempre que atualiza o arquivo. Sincronização de arquivo não é um evento surpreendente, nem requer atenção imediata do usuário. Os usuários esperam apenas o arquivo a ser atualizado quando eles o abrem.

Para casos como o mostrado acima, o iOS permite que as notificações por push sejam enviadas silenciosamente - ou seja, sem um alerta. Para ativar uma notificação de regular para um silenciosa, simplesmente remova o alerta da carga de notificação:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limites de taxa

A maior diferença entre as notificações de normais e silenciosas da perspectiva do desenvolvedor é que envios por push silenciosos são limitadas. APNs atrasará a entrega de envios por push silenciosos para o dispositivo se a taxa de envio por push ficar muito alta. Isso é para garantir que aplicativos não esgotar os recursos de dispositivo com um número excessivo de notificações silenciosas.

No entanto, o APNs permitirá notificações silenciosas "se aproveitam" junto com uma notificação remota normal ou resposta keep-alive. Como notificações regulares não são de limitação de taxa, eles podem ser usados para enviar por push notificações silenciosas armazenadas para cima do APNs para o dispositivo, conforme ilustrado pelo diagrama a seguir:

 [![](updating-an-application-in-the-background-images/silent.png "Notificações regulares podem ser usadas para notificações silenciosas armazenadas por push do APNs para o dispositivo, conforme ilustrado neste diagrama")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple incentiva os desenvolvedores a enviar notificações por push silenciosas, sempre que o aplicativo requer e deixe os APNs agendar sua entrega.


Nesta seção, abordamos as várias opções para atualizar conteúdo em segundo plano para executar tarefas que não se encaixam em uma categoria de plano de fundo necessário. Agora, vamos ver algumas dessas APIs em ação.

 [Em seguida: Parte 4 - instruções passo a passo de Backgrounding do iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
