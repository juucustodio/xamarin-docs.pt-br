---
title: Atualizando um aplicativo Xamarin. iOS em segundo plano
description: Este documento descreve várias maneiras de atualizar um aplicativo Xamarin. iOS que está em segundo plano, como monitoramento de região, busca em segundo plano e notificações remotas.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 2e0bb4fc0468f938e7a4403513fe101db2282561
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286984"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Atualizando um aplicativo Xamarin. iOS em segundo plano

A atualização em segundo plano é o processo de ativar um aplicativo que está suspenso ou não está em execução e atualizá-lo com o novo conteúdo. o iOS fornece três opções para atualizar o conteúdo em segundo plano:

1. *Monitoramento de região* e *alterações significativas de local as* APIs com reconhecimento de local de serviço disparam atualizações em segundo plano com base nas alterações no local do usuário. Essas APIs podem ser usadas com critério para atualizar o conteúdo em aplicativos iOS 6 sem base no local, em que outras opções não estão disponíveis.
1. *Busca em segundo plano (Ios 7 +)* – uma abordagem temporal para atualizar conteúdo *não crítico* que é atualizada *com frequência* .
1. *Notificações remotas (Ios 7 +)* -os aplicativos que recebem notificações por push podem usar as notificações para disparar atualizações de conteúdo em segundo plano. Esse método pode ser usado para atualizar com conteúdo *importante e sensível ao tempo* que é atualizado *esporadicamente* .


As seções a seguir abordam as noções básicas dessas opções.

## <a name="region-monitoring-and-significant-location-changes"></a>Monitoramento de região e alterações de local significativas

o iOS fornece duas APIs com reconhecimento de local com recursos de plano de fundo:

1. O *monitoramento de região* é o processo de configurar regiões com limites e ativar o dispositivo quando o usuário insere ou sai de uma região. As regiões são circulares e podem ter tamanho variável. Quando o usuário ultrapassar um limite de região, o dispositivo será ativado para lidar com o evento, normalmente acionando uma notificação ou iniciarndo uma tarefa. O monitoramento de região requer GPS e aumenta o uso de bateria e de dados.
1. O *serviço de alterações de local significativo* é uma opção mais simples, que reserva a energia disponível para dispositivos com rádios de celular. Um aplicativo que escuta alterações significativas no local será notificado quando o dispositivo alternar a célula Towers. Esse serviço pode ser usado para ativar um aplicativo suspenso ou encerrado e fornece uma oportunidade para verificar o novo conteúdo em segundo plano. A atividade em segundo plano é limitada a cerca de 10 segundos, a menos que emparelhada com uma [tarefa em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Um aplicativo não precisa do local `UIBackgroundMode` para usar essas APIs com reconhecimento de local. Como o iOS não rastreia os tipos de tarefas que podem ser executados quando o dispositivo é ativadosdo por alterações no local do usuário, essas APIs fornecem uma solução alternativa para atualizar o conteúdo em segundo plano no iOS 6. *Lembre-se de que o disparo de atualizações em segundo plano com APIs baseadas em local será desenhado nos recursos do dispositivo e pode confundir os usuários que não entendem por que um aplicativo requer acesso ao local*. Use o critério ao implementar o monitoramento de região ou alterações significativas no local para processamento em segundo plano em aplicativos que ainda não estão usando as APIs de localização.

Os aplicativos que usam o monitoramento de local para processamento em segundo plano expõem uma falha no iOS 6: se as necessidades de um aplicativo não se ajustarem a uma categoria necessária para o plano de fundo, ela terá opções limitadas de fundo. Com a introdução de duas novas APIs, *busca em segundo plano* e *notificações remotas*, o Ios 7 (e superior) fornece oportunidades em segundo plano para mais aplicativos. As duas próximas seções introduzem essas novas APIs.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Busca em segundo plano (iOS 7 e superior)

No iOS 6, um aplicativo que insere o tempo de primeiro plano necessário para carregar novo conteúdo, apresentando brevemente os usuários com o conteúdo que já vimos. A busca em segundo plano permite que os aplicativos carreguem novos dados *antes que* um usuário inicie o aplicativo e forneça ao usuário o conteúdo mais atualizado.

Para implementar a busca em segundo plano, edite *info. plist* e marque as caixas de seleção **habilitar modos de segundo plano** e **busca em segundo plano** :

 [![](updating-an-application-in-the-background-images/fetch.png "Edite o info. plist e marque as caixas de seleção Habilitar modos de segundo plano e busca em segundo plano")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Em seguida, no `AppDelegate`, substitua o `FinishedLaunching` método para definir o intervalo mínimo de busca. Neste exemplo, permitimos que o sistema operacional decida com que frequência obter o novo conteúdo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por fim, execute a busca substituindo `PerformFetch` o método `AppDelegate`no e passando um manipulador de *conclusão*. O manipulador de conclusão é um delegado que usa `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Quando concluímos a atualização do conteúdo, permitimos que o sistema operacional saiba chamando o manipulador de conclusão com o status apropriado. o iOS oferece três opções para o status do manipulador de conclusão:

1. `UIBackgroundFetchResult.NewData`– Chamado quando um novo conteúdo tiver sido obtido e o aplicativo tiver sido atualizado.
1. `UIBackgroundFetchResult.NoData`-Chamado quando a busca de novo conteúdo foi enviada, mas nenhum conteúdo está disponível.
1. `UIBackgroundFetchResult.Failed`-Útil para tratamento de erros, isso é chamado quando a busca não conseguiu passar.


Os aplicativos que usam a busca em segundo plano podem fazer chamadas para atualizar a interface do usuário em segundo plano. Quando o usuário abrir o aplicativo, a interface do usuário estará atualizada e exibirá o novo conteúdo. Isso também atualizará o instantâneo do seletor de aplicativo do aplicativo, para que o usuário possa ver quando o aplicativo tem conteúdo novo.

> [!IMPORTANT]
> Uma `PerformFetch` vez chamado, o aplicativo tem aproximadamente 30 segundos para iniciar o download do novo conteúdo e chamar o bloco do manipulador de conclusão. Se isso levar muito tempo, o aplicativo será encerrado. Considere o uso da busca em segundo plano com o _serviço de transferência em segundo plano_ ao baixar mídia ou outros arquivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

No código de exemplo acima, permitimos que o sistema operacional decida com que frequência obter novo conteúdo definindo o intervalo mínimo de `BackgroundFetchIntervalMinimum`busca como. o iOS oferece três opções para o intervalo de busca:

1. `BackgroundFetchIntervalNever`-Informe o sistema para nunca buscar um novo conteúdo. Use isso para desativar a busca em determinadas situações, como quando o usuário não estiver conectado. Esse é o valor padrão para o intervalo de busca. 
1. `BackgroundFetchIntervalMinimum`-Permita que o sistema decida com que frequência buscar com base nos padrões do usuário, na vida útil da bateria, no uso de dados e nas necessidades de outros aplicativos.
1. `BackgroundFetchIntervalCustom`-Se você souber com que frequência o conteúdo de um aplicativo é atualizado, poderá especificar um intervalo de "suspensão" após cada busca, durante o qual o aplicativo será impedido de buscar um novo conteúdo. Quando esse intervalo estiver ativo, o sistema determinará quando buscar conteúdo.


Ambos `BackgroundFetchIntervalMinimum` e`BackgroundFetchIntervalCustom` contam com o sistema para agendar buscas. Esse intervalo é dinâmico, adaptando-se às necessidades do dispositivo, bem como aos hábitos do usuário individual. Por exemplo, se um usuário verifica um aplicativo todas as manhãs e outra verificação a cada hora, o iOS garantirá que o conteúdo seja atualizado para ambos os usuários sempre que abrirem o aplicativo.

A busca em segundo plano deve ser usada para aplicativos que são atualizados com frequência com conteúdo não crítico. Para aplicativos com atualizações críticas, devem ser usadas notificações remotas. As notificações remotas são baseadas na busca em segundo plano e compartilham o mesmo manipulador de conclusão. Vamos nos aprofundar nas notificações remotas.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificações remotas (iOS 7 e superior)

As notificações por push são mensagens JSON enviadas de um provedor para um dispositivo por meio do *serviço de notificação por push da Apple (APNs)* .

No iOS 6, uma notificação por push de entrada informa o sistema para alertar o usuário de que algo interessante aconteceu em um aplicativo. Clicar na notificação puxa o aplicativo para fora do estado suspenso ou encerrado, e o aplicativo começaria a atualizar o conteúdo. o iOS 7 (e superior) estende as notificações por push comuns, fornecendo aos aplicativos a oportunidade de atualizar o conteúdo em segundo plano *antes* de notificar o usuário, para que o usuário possa abrir o aplicativo e receber um novo conteúdo imediatamente.

Para implementar notificações remotas, edite *info. plist* e marque as caixas de seleção **habilitar modos de segundo plano** e **notificações remotas** :

 [![](updating-an-application-in-the-background-images/remote.png "Modo em segundo plano definido para habilitar modos de segundo plano e notificações remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

Em seguida, defina `content-available` o sinalizador na notificação por push para 1. Isso permite que o aplicativo saiba como buscar um novo conteúdo antes de exibir o alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

No *AppDelegate*, substitua o `DidReceiveRemoteNotification` método para verificar a carga de notificação do conteúdo disponível e chame o bloco manipulador de conclusão apropriado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

As notificações remotas devem ser usadas para atualizações frequentes com conteúdo crucial para a funcionalidade do aplicativo. Para obter mais informações sobre notificações remotas, consulte o Xamarin [Push Notifications no guia do IOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) .

> [!IMPORTANT]
> Como o mecanismo de atualização nas notificações remotas se baseia na busca em segundo plano, o aplicativo deve disparar o download do novo conteúdo e chamar o bloco do manipulador de conclusão dentro de 30 segundos após o recebimento da notificação, ou o iOS encerrará o aplicativo. Considere emparelhar notificações remotas com o _serviço de transferência em segundo plano_ ao baixar mídia ou outros arquivos grandes em segundo plano.


### <a name="silent-remote-notifications"></a>Notificações remotas silenciosas

As notificações remotas são uma maneira simples de notificar aplicativos de atualizações e iniciar a busca de novo conteúdo, mas há casos em que não precisamos notificar o usuário de que algo mudou. Por exemplo, se um usuário sinalizar um arquivo para sincronização, não precisaremos notificá-los sempre que o arquivo for atualizado. A sincronização de arquivos não é um evento surpreendente, nem exige a atenção imediata do usuário. Os usuários esperam que o arquivo seja atualizado quando o abrem.

Para casos como aquele acima, o iOS permite que notificações por push sejam enviadas silenciosamente, ou seja, sem um alerta. Para transformar uma notificação regular em um silenciosa, basta remover o alerta da carga de notificação:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limites de taxa

A maior diferença entre as notificações normais e silenciosas da perspectiva do desenvolvedor é que os Pushes silenciosos são limitados por taxa. O APNs atrasará a entrega de Pushes silenciosos para o dispositivo se a taxa de push ficar muito alta. Isso é para garantir que os aplicativos não esgotam os recursos do dispositivo com muitas notificações silenciosas.

No entanto, o APNs deixará as notificações silenciosas "acumuladas" junto com uma notificação remota normal ou uma resposta de Keep-Alive. Como as notificações regulares não são limitadas por taxa, elas podem ser usadas para enviar notificações silenciosas do APNs para o dispositivo, conforme ilustrado pelo seguinte diagrama:

 [![](updating-an-application-in-the-background-images/silent.png "Notificações regulares podem ser usadas para enviar notificações silenciosas armazenadas do APNs para o dispositivo, conforme ilustrado por este diagrama")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> A Apple incentiva os desenvolvedores a enviar notificações por push silenciosas sempre que o aplicativo exigir e permitir que o APNs agende sua entrega.


Nesta seção, abordamos as várias opções para atualizar o conteúdo em segundo plano para executar tarefas que não se ajustam a uma categoria necessária para o plano de fundo. Agora, vamos ver algumas dessas APIs em ação.

 [Avançar: Parte 4-passo a passos do iOS em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
