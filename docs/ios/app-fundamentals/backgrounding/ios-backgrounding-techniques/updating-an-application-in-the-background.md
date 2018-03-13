---
title: Atualizando um aplicativo em segundo plano
ms.topic: article
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f4a18bf8f35d1a6c615c819ea90433d1eb123422
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="updating-an-application-in-the-background"></a>Atualizando um aplicativo em segundo plano

Atualização em segundo plano é o processo de ativação de um aplicativo que está suspenso ou não está em execução e atualizá-lo com novo conteúdo. iOS fornece três opções para atualizar conteúdo em segundo plano:

1.  *Monitoramento de região* e *serviço significativo de alterações de local* -atualizações de plano de fundo de gatilho de APIs com reconhecimento de local com base nas alterações no local do usuário. Essas APIs podem ser usadas com prudência para atualizar conteúdo em aplicativos não baseados em local iOS 6, onde outras opções não estão disponíveis.
1.  *Em segundo plano (iOS 7 +) de busca* -uma abordagem temporal para atualizar *não críticos* conteúdo atualiza *frequentemente* .
1.  *Notificações remotas (iOS 7 +)* -aplicativos que recebem notificações por push podem usar as notificações para disparar atualizações de conteúdo em segundo plano. Esse método pode ser usado para atualizar com *importantes, detecção de hora* conteúdo atualiza *esporadicamente* .


As seções a seguir abordam os conceitos básicos das seguintes opções.

## <a name="region-monitoring-and-significant-location-changes"></a>Monitoramento de região e alterações significativas local

iOS fornece duas APIs com reconhecimento de local com backgrounding recursos:

1.  *Monitoramento de região* é o processo de configuração de regiões com limites e ativar o dispositivo quando o usuário entra ou sair de uma região. Regiões são circulares e podem ser de tamanho variável. Quando o usuário ultrapassar um limite de região, o dispositivo será ativada para manipular o evento, geralmente, disparar uma notificação ou iniciar uma tarefa. Monitoramento de região requer GPS e aumenta a bateria e uso de dados.
1.  O *serviço significativo de alterações de local* é uma opção mais simples, para conservar energia disponível para dispositivos com rádios celulares. Um aplicativo de escuta de alterações significativas local será notificado quando o dispositivo alterna torres de célula. Esse serviço pode ser usado para ativar um aplicativo suspenso ou encerrado e fornece uma oportunidade para verificar se há novos conteúdos no plano de fundo. Atividade em segundo plano é limitada a cerca de 10 segundos, a menos que emparelhado com um [tarefa em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Um aplicativo não é necessário para o local `UIBackgroundMode` para usar essas APIs com reconhecimento de local. Porque iOS não controla os tipos de tarefas que podem ser executados quando o dispositivo é ativado por alterações no local do usuário, essas APIs fornecem uma solução alternativa para atualizar conteúdo em segundo plano no iOS 6. *Tenha em mente disparar atualizações em segundo plano com APIs com base em localização desenhará nos recursos do dispositivo e pode confundir os usuários que não entendem por que um aplicativo requer acesso ao seu local*. Use o critério ao implementar o monitoramento de região ou alterações significativas do local de processamento em aplicativos que já não estiverem usando o local de APIs em segundo plano.

Aplicativos usando o monitoramento de local para processamento em segundo plano expor uma falha no iOS 6: se as necessidades do aplicativo não se encaixam em uma categoria de plano de fundo necessário, ele tem opções backgrounding limitadas. Com a introdução de duas novas APIs, *buscar plano de fundo* e *remoto notificações*, iOS 7 (e maior) fornece backgrounding oportunidades para mais aplicativos. As próximas duas seções apresentam essas novas APIs.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Busca em segundo plano (iOS 7 e superior)

No iOS 6, um aplicativo inserindo o primeiro plano necessário tempo para carregar o novo conteúdo, apresentando brevemente os usuários com conteúdo que você já viu. Busca em segundo plano permite que os aplicativos carregar novos dados *antes de* um usuário inicia o aplicativo e fornecer ao usuário com o conteúdo mais atualizado.

Para implementar a busca em segundo plano, editar *Info. plist* e verifique o **habilitar modos de segundo plano** e **busca em segundo plano** caixas de seleção:

 [![](updating-an-application-in-the-background-images/fetch.png "Editar o Info. plist e marque as caixas de seleção Habilitar modos de segundo plano e a busca de plano de fundo")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Em seguida, no `AppDelegate`, substituir o `FinishedLaunching` método para definir o intervalo mínimo de busca. Neste exemplo, deixamos que o sistema operacional decidir como geralmente buscar o novo conteúdo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por fim, executar a busca, substituindo o `PerformFetch` método no `AppDelegate`e passando um *processador de conclusão*. O manipulador de conclusão é um delegado que utiliza um `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Quando terminamos conteúdo de atualização, deixamos que o sistema operacional sabe chamando o manipulador de conclusão com o status apropriado. iOS oferece três opções para o status de conclusão de manipulador:

1.  `UIBackgroundFetchResult.NewData` -Chamado quando o novo conteúdo foi buscado e o aplicativo foi atualizado.
1.  `UIBackgroundFetchResult.NoData` -Chamado quando a busca de novos conteúdos fiz, mas nenhum conteúdo está disponível.
1.  `UIBackgroundFetchResult.Failed` -Útil para tratamento de erros, isso é chamado quando a busca não pôde percorrer.


Aplicativos que usam a busca em segundo plano podem fazer chamadas para atualizar a interface do usuário do plano de fundo. Quando o usuário abre o aplicativo, a interface do usuário será atualizado e exibir o conteúdo novo. Isso também atualizará o instantâneo de alternador de aplicativo do aplicativo, para que o usuário pode ver quando o aplicativo tem conteúdo novo.

> [!IMPORTANT]
> **Observação**: uma vez `PerformFetch` é chamado, o aplicativo tem aproximadamente 30 segundos para iniciar o download de conteúdo novo e, em seguida, chamar o bloco do manipulador de conclusão. Se isso levar muito tempo, o aplicativo será encerrado. Considere o uso de busca em segundo plano com o _o serviço de transferência em segundo plano_ durante o download de mídia ou outros arquivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

No código de exemplo acima, deixamos que o sistema operacional decidir como geralmente buscar o conteúdo novo definindo o intervalo mínimo de busca como `BackgroundFetchIntervalMinimum`. iOS oferece três opções para o intervalo de busca:

1.  `BackgroundFetchIntervalNever` -Informa o sistema nunca buscar novo conteúdo. Use isto para desativar a busca em determinadas situações, como quando o usuário não está conectado. Este é o valor padrão para o intervalo de busca. 
1.  `BackgroundFetchIntervalMinimum` -Permite que o sistema decidir a frequência de busca com base em padrões do usuário, vida útil da bateria, uso de dados e as necessidades de outros aplicativos.
1.  `BackgroundFetchIntervalCustom` -Se você souber quantas vezes o conteúdo de um aplicativo é atualizado, você pode especificar um intervalo de "sleep" após cada busca, durante o qual o aplicativo será impedido de busca de novo conteúdo. Após esse intervalo, o sistema determinará quando buscar o conteúdo.


Ambos `BackgroundFetchIntervalMinimum` e `BackgroundFetchIntervalCustom` depende do sistema para agendar buscas. Este intervalo é dinâmico, adaptação às necessidades do dispositivo, bem como hábitos de usuários individuais. Por exemplo, se um aplicativo de todas as manhãs verifica se um usuário e outro verificações a cada hora, iOS garantirá que o conteúdo é atualizado para os usuários sempre que abrirem o aplicativo.

Busca em segundo plano deve ser usada para aplicativos que atualizam frequentemente com conteúdo não críticos. Para aplicativos com atualizações críticas, notificações remoto deve ser usadas. Notificações remotas se baseiam no plano de fundo buscar e compartilham o mesmo manipulador de conclusão. Nos aprofundaremos nas notificações remoto em seguida.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificações remotas (iOS 7 e superior)

Notificações por push são mensagens JSON enviadas de um provedor para um dispositivo por meio do *Apple Push Notification service (APNs)*.

No iOS 6, uma entrada notificações de push informa ao sistema para alertar o usuário que algo interessante tiver ocorrido em um aplicativo. Clicando na notificação extrai o aplicativo fora do estado suspenso ou encerrado, e o aplicativo poderia começar a atualização do conteúdo. iOS 7 (e maior) estende as notificações por push comum, fornecendo uma chance de atualizar o conteúdo no plano de fundo de aplicativos *antes de* notificar o usuário, para que o usuário pode abrir o aplicativo e ser apresentado com novo conteúdo imediatamente.

Para implementar notificações remotas, editar *Info. plist* e verifique o **habilitar modos de segundo plano** e **remotas notificações** caixas de seleção:

 [![](updating-an-application-in-the-background-images/remote.png "Modo de plano de fundo definido como habilitar modos de segundo plano e notificações remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

Em seguida, defina o `content-available` sinalizador de notificação por push em si como 1. Isso permite que o aplicativo para buscar novo conteúdo antes de exibir o alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

No *AppDelegate*, substituir o `DidReceiveRemoteNotification` método para verificar se a carga de notificação para o conteúdo disponível e chamar o bloco do manipulador de conclusão apropriado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notificações remotas devem ser usadas para atualizações não frequentes com conteúdo que é essencial para a funcionalidade do aplicativo. Para obter mais informações sobre notificações remotas, consulte o Xamarin [notificações por Push no iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) guia.

> [!IMPORTANT]
> **Observação**: porque o mecanismo de atualização em notificações remoto baseia-se em busca de segundo plano, o aplicativo deve disparar o download de conteúdo novo e chamar o bloco do manipulador de conclusão dentro de 30 segundos para receber a notificação ou será de iOS Encerre o aplicativo. Considere o emparelhamento remoto notificações com _o serviço de transferência em segundo plano_ durante o download de mídia ou outros arquivos grandes em segundo plano.


### <a name="silent-remote-notifications"></a>Notificações remotas silenciosas

Notificações remotas são uma maneira simples para notificar aplicativos de atualizações e disparar buscando o novo conteúdo, mas há casos em que não precisamos para notificar o usuário que algo foi alterado. Por exemplo, se um usuário indica um arquivo para sincronização, não precisamos para notificá-lo sempre que atualiza o arquivo. Sincronização de arquivo não é um evento surpreendente nem requer atenção imediata do usuário. Os usuários esperam apenas o arquivo a ser atualizado quando eles abrirem-lo.

Em casos como mostrado acima, o iOS permite que notificações por push para ser enviado silenciosamente - ou seja, sem um alerta. Para ativar uma notificação de regular para um silenciosa, basta remova o alerta da carga de notificação:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limites de taxa

A maior diferença entre normais e silenciosas notificações de um ponto de vista do desenvolvedor é silenciosos envios por push são taxa limitada. APNs atrasa a entrega de silenciosas envios por push para o dispositivo se a taxa de envio por push ficar muito alta. Isso é para garantir que aplicativos não esgotar os recursos de dispositivo com muitos notificações silenciosas.

No entanto, APNs permitirá notificações silenciosas "se aproveitam" junto com uma notificação remoto normal ou resposta keep-alive. Como notificações regulares não são limitadas de taxa, eles podem ser usados para notificações silenciosas armazenadas em backup por push do APNs para o dispositivo, conforme ilustrado pelo diagrama a seguir:

 [![](updating-an-application-in-the-background-images/silent.png "Notificações regulares podem ser usadas para notificações silenciosas armazenadas por push do APNs para o dispositivo, conforme ilustrado pelo diagrama")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> **Observação**: Apple incentiva os desenvolvedores a enviar notificações por push silenciosa sempre que o aplicativo requer e permitem os APNs agendar sua entrega.


Nesta seção, falamos sobre as várias opções para atualizar conteúdo em segundo plano para executar tarefas que não cabem em uma categoria de plano de fundo necessário. Agora, vamos ver algumas dessas APIs em ação.

 [Em seguida: Parte 4 - iOS Backgrounding explicações passo a passo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
