---
title: watchOS tarefas em segundo plano em Xamarin
description: Este documento descreve como usar tarefas em segundo plano com watchOS em Xamarin, dê uma olhada em tipos de tarefas em segundo plano, o uso de recursos, a implementação de tarefas em segundo plano, agendamento, melhores práticas e muito mais.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/13/2017
ms.openlocfilehash: 5ab53d4aea32cf41c492e286c18cbe85a619889a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792034"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS tarefas em segundo plano em Xamarin

Com watchOS 3, há três maneiras principais que um aplicativo de inspeção pode manter as informações atualizadas: 

- Usando uma das várias novas tarefas em segundo plano. 
- Ter um dos seus complicações na face do relógio (dando a ele muito tempo para atualizar). 
- Ter o pin do usuário ao aplicativo de novo encaixe (onde seu mantidas na memória e atualizadas com frequência). 

## <a name="keeping-an-app-up-to-date"></a>Manter um aplicativo atualizado

Antes de abordar todas as maneiras que um desenvolvedor pode manter os dados de um aplicativo de watchOS e Interface do usuário atualizada, esta seção dar uma olhada em um conjunto comum de padrões de uso e como um usuário pode mover entre seu iPhone e seu Apple Watch ao longo do dia com base em  a hora do dia e a atividade eles estão fazendo no momento (por exemplo, orientando).

Veja o exemplo a seguir:

[![](background-tasks-images/update00.png "Como um usuário pode mover entre seu iPhone e seu Apple Watch ao longo do dia")](background-tasks-images/update00.png#lightbox)

1. Da manhã, enquanto aguardava na linha para um café, o usuário navega as notícias atuais em seu iPhone por vários minutos.
2. Antes de deixar o café, eles verificam rapidamente o clima com um Complicativo em seu face do relógio.
3. Antes de almoçar, eles usam o aplicativo de mapas no iPhone para encontrar um restaurante nas proximidades e livro uma reserva para atender a um cliente.
4. Durante uma viagem para o restaurante, recebem uma notificação em seu Apple Watch e uma visão geral rápida, eles saibam que seu compromisso almoçar está atrasado.
5. Durante a noite, eles usam o aplicativo de mapas em iPhone para verificar o tráfego antes orienta a página inicial.
6. No caminho inicial, recebem uma notificação de iMessage em seu Apple Watch solicitando que eles para acompanhar alguns leite e usarem o recurso de resposta rápida para enviar a resposta "Okey".

Devido a "visão rápida" (menos de três segundos) a natureza de como um usuário desejarem usar um aplicativo Apple Watch, normalmente há tempo suficiente para o aplicativo buscar informações desejadas e atualizar sua interface do usuário antes de exibi-los para o usuário não está.

Usando as novas APIs Apple está incluído no watchOS 3, o aplicativo pode agendar para um _atualização em segundo plano_ e ter as informações desejadas prontas antes do usuário solicitar. Veja o exemplo do Complicativo clima discutidos acima:

[![](background-tasks-images/update01.png "Um exemplo do Complicativo clima")](background-tasks-images/update01.png#lightbox)

1. As agendas de aplicativo para ser ativado pelo sistema em um momento específico. 
2. O aplicativo busca as informações que será necessário para gerar a atualização.
3. O aplicativo gera novamente a sua Interface de usuário para refletir os novos dados.
4. Quando o usuário resumos em Complicativo do aplicativo, ele tem informações atualizadas sem o necessidade de aguardar a atualização do usuário.

Como mostrado acima, o sistema watchOS desperta o aplicativo usando uma ou mais tarefas, dos quais ele tem um pool de muito limitado disponível:

[![](background-tasks-images/update02.png "O sistema watchOS desperta o aplicativo usando uma ou mais tarefas")](background-tasks-images/update02.png#lightbox)

Apple sugerir aproveitando ao máximo essa tarefa (já que é um recurso limitado para o aplicativo), mantendo a ele até que o aplicativo concluiu o processo de atualização em si.

O sistema fornece essas tarefas chamando o novo `HandleBackgroundTasks` método o `WKExtensionDelegate` delegate. Por exemplo:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

Quando o aplicativo terminar a tarefa específica, ele os retorna para o sistema, marcando-concluído:

[![](background-tasks-images/update03.png "A tarefa retorna para o sistema, marcando-concluída")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Novas tarefas em segundo plano

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações de garantir que ele tenha o conteúdo, o usuário precisa antes de abrirem o aplicativo, como:

- **Atualização de aplicativo do plano de fundo** - o [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarefa permite que o aplicativo atualizar seu estado em segundo plano. Normalmente, isso incluirá outra tarefa como baixar o novo conteúdo da internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantâneo de atualização do plano de fundo** - o [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarefa permite que o aplicativo atualizar seu conteúdo e a interface do usuário antes do sistema tira um instantâneo que será usado para preencher o encaixe.
- **Conectividade de inspeção do plano de fundo** - o [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando ele recebe os dados do plano de fundo de iPhone emparelhado.
- **Sessão de URL do plano de fundo** - o [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou for concluído (com êxito ou erro).

Essas tarefas serão abordadas em detalhes nas seções a seguir.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

O `WKApplicationRefreshBackgroundTask` é uma tarefa genérica que pode ser agendada para que o aplicativo ativado em uma data futura:

[![](background-tasks-images/update04.png "Um WKApplicationRefreshBackgroundTask ativado em uma data futura")](background-tasks-images/update04.png#lightbox)

No tempo de execução da tarefa, o aplicativo pode fazer qualquer tipo de processamento local, como a atualização de uma linha do tempo do Complicativo ou buscar alguns dados necessários com um `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

O sistema enviará um `WKURLSessionRefreshBackgroundTask` quando os dados terminou de baixar e pronto para ser processado pelo aplicativo:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask quando os dados concluiu o download")](background-tasks-images/update05.png#lightbox)

Um aplicativo não é deixado em execução enquanto o download dos dados em segundo plano. Em vez disso, o aplicativo agenda a solicitação de dados, em seguida, ele é suspenso e o sistema manipula o download de dados, reawakening apenas o aplicativo quando o download for concluído.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

Em watchOS 3, Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, será exibida uma galeria de aplicativo fixados instantâneos. O usuário pode passar esquerda ou direita para localizar o aplicativo desejado, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

[![](background-tasks-images/update06.png "Substituindo o instantâneo com a interface de aplicativos em execução")](background-tasks-images/update06.png#lightbox)

O sistema periodicamente leva instantâneos de interface de usuário do aplicativo (enviando um `WKSnapshotRefreshBackgroundTask`) e usa esses instantâneos para popular o encaixe. watchOS o aplicativo tem a oportunidade para atualizar seu conteúdo e a interface do usuário antes desse instantâneo é criado.

Instantâneos são muito importantes watchOS 3 como eles funcionam como imagens de visualização e de inicialização para o aplicativo. Se o usuário estabelece em um aplicativo de encaixe, ele expandir para tela inteira, insira o primeiro plano e iniciar a execução, portanto, é fundamental que o instantâneo seja atualizado:

[![](background-tasks-images/update07.png "Se o usuário estabelece em um aplicativo de encaixe, ela será expandida para tela inteira")](background-tasks-images/update07.png#lightbox)

Novamente, o sistema emitirá um `WKSnapshotRefreshBackgroundTask` para que o aplicativo pode preparar (Atualizando os dados e a interface do usuário) antes do instantâneo é criado:

[![](background-tasks-images/update08.png "O aplicativo pode preparar atualizando os dados e a interface do usuário antes do instantâneo é criado")](background-tasks-images/update08.png#lightbox)

Quando o aplicativo marca o `WKSnapshotRefreshBackgroundTask` concluída, o sistema automaticamente tirar um instantâneo da interface de usuário do aplicativo.

> [!IMPORTANT]
> É importante sempre agendar um ` WKSnapshotRefreshBackgroundTask` depois que o aplicativo recebeu dados novos e atualizados sua Interface de usuário ou o usuário não verá as informações de modificação.




Além disso, quando o usuário recebe uma notificação do aplicativo e toca para colocar o aplicativo em primeiro plano, o instantâneo precisa ser atualizado desde que ele está atuando como tela inicial:

[![](background-tasks-images/update09.png "O usuário recebe uma notificação do aplicativo e toca para colocar o aplicativo em primeiro plano")](background-tasks-images/update09.png#lightbox)

Se mais de uma hora foram desde que o usuário interagiu com um aplicativo watchOS, ele poderá retornar ao estado padrão. O estado padrão pode significar coisas diferentes para diferentes aplicativos e, com base no design de um aplicativo, talvez ele não tenha um estado padrão em todos os.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

Em watchOS 3, Apple integrou inspecionar conectividade com a API de atualização em segundo plano por meio do novo `WKWatchConnectivityRefreshBackgroundTask`. Usando esse novo recurso, um aplicativo de iPhone pode fornecer dados atualizados para seu equivalente de aplicativo de inspeção, enquanto o aplicativo watchOS está em execução em segundo plano:

[![](background-tasks-images/update10.png "Um aplicativo de iPhone pode fornecer dados atualizados para seu equivalente de aplicativo de inspeção, enquanto o aplicativo watchOS está em execução em segundo plano")](background-tasks-images/update10.png#lightbox)

Iniciar um Complicativo Push, contexto de aplicativo, envia um arquivo ou atualizar informações do usuário do aplicativo do iPhone ativará o aplicativo Apple Watch em segundo plano.

Quando o aplicativo watch é ativado por meio de um `WKWatchConnectivityRefreshBackgroundTask` será necessário usar os métodos da API padrão para receber os dados do aplicativo do iPhone.

[![](background-tasks-images/update11.png "O fluxo de dados WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Certifique-se de que a sessão foi ativado.
2. Monitorar o novo `HasContentPending` desde que o valor da propriedade `true`, o aplicativo ainda tem dados para processar. Como antes, o aplicativo deve manter a tarefa até que terminou de processar todos os dados.
3. Quando não há mais dados a serem processados (`HasContentPending = false`), marque a tarefa foi concluída para retorná-lo para o sistema. Falha ao fazer isso irá esgotar o tempo de execução do plano de fundo alocado do aplicativo resultando em um relatório de falhas.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>O ciclo de vida de API do plano de fundo

Colocar todas as partes da nova API de tarefas em segundo plano em conjunto, um conjunto comum de interações seria semelhante ao seguinte:

[![](background-tasks-images/update12.png "O ciclo de vida de API do plano de fundo")](background-tasks-images/update12.png#lightbox)

1. Primeiro, o aplicativo watchOS agenda um plano de fundo da tarefa ativo como algum momento no futuro.
2. O aplicativo é ativado pelo sistema e enviado de uma tarefa.
3. O aplicativo processa a tarefa para concluir o trabalho foi necessário.
4. Como resultado do processamento da tarefa, o aplicativo talvez seja necessário agendar o plano de fundo mais tarefas a serem concluídas mais trabalho no futuro, como baixar o conteúdo mais usando um `NSUrlSession`.
5. O aplicativo marca a tarefa foi concluída e o retorna ao sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Usando recursos com responsabilidade

É importante que um aplicativo watchOS se comporta com responsabilidade dentro desse ecossistema, limitando o consumo de recursos compartilhados do sistema.

Observe o seguinte cenário:

[![](background-tasks-images/update13.png "Um aplicativo watchOS limita o consumo de recursos compartilhados do sistema")](background-tasks-images/update13.png#lightbox)

1. O usuário inicia um aplicativo watchOS às 13:00.
2. O aplicativo agenda uma tarefa de ativação e baixar novo conteúdo em uma hora às 2:00.
3. Às 13:50 o usuário o abrir novamente o aplicativo que permite atualizar seus dados e a interface do usuário no momento.
4. Em vez de deixar o aplicativo de ativação de tarefas novamente em 10 minutos, o aplicativo deve replanejar a tarefa para executar uma hora posterior às 2:50 PM.

Embora cada aplicativo é diferente, Apple sugere localizando padrões de uso, como os mostrados acima, para ajudar a economizar recursos do sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementando tarefas em segundo plano

Por exemplo, este documento usará o aplicativo de esportes MonkeySoccer falso que relata as pontuações de futebol para o usuário. 

Observe o seguinte cenário de uso típico:

[![](background-tasks-images/update14.png "O cenário de uso comuns")](background-tasks-images/update14.png#lightbox)

Futebol Favoritos do usuário é reproduzido uma correspondência intensa das 7:00 às 9:00, para que o aplicativo deve esperar que o usuário para verificar regularmente a pontuação e ela decide em um intervalo de atualização de 30 minutos.

1. O usuário abre o aplicativo e ele agenda uma tarefa de atualização em segundo plano 30 minutos mais tarde. A API do plano de fundo permite apenas um tipo de plano de fundo tarefa para ser executado em um determinado momento.
2. O aplicativo recebe a tarefa e atualiza seus dados e a interface do usuário e agendas para outro plano de fundo tarefas 30 minutos mais tarde. É importante que o desenvolvedor lembra agendar o plano de fundo de outra tarefa ou o aplicativo nunca será ativado novamente para obter mais atualizações.
3. Novamente, o aplicativo recebe a tarefa e atualiza seus dados, atualiza sua interface de usuário e agenda outro plano de fundo de tarefa 30 minutos mais tarde.
4. O mesmo processo é repetido novamente.
5. O plano de fundo última tarefa é recebida e o aplicativo atualiza novamente seus dados e a interface do usuário. Como essa é a pontuação final, que ele não agenda para uma nova atualização em segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Agendamento de atualização em segundo plano

Dado o cenário anterior, o aplicativo MonkeySoccer pode usar o código a seguir para agendar uma atualização em segundo plano:

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Cria um novo `NSDate` 30 minutos no futuro quando o aplicativo deseja ativado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleBackgroundRefresh` método o `SharedExtension` é usado para solicitar a tarefa agendada.

O sistema retornará uma `NSError` se não foi possível agendar a tarefa solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Processo de atualização

Em seguida, analisar mais detalhadamente na janela de 5 minutos, mostrando as etapas necessárias para atualizar a pontuação:

[![](background-tasks-images/update15.png "A janela de 5 minutos, mostrando as etapas necessárias para atualizar a pontuação")](background-tasks-images/update15.png#lightbox)

1. Em 19:30:02: 00, o aplicativo é ativado pelo sistema e recebe o plano de fundo de atualização de tarefa. Sua primeira prioridade é obter os resultados mais recentes do servidor. Consulte [Agendando um NSUrlSession](#Scheduling-a-NSUrlSession) abaixo.
2. Em 7:30:05 o aplicativo concluir a tarefa original, o sistema coloca o aplicativo no modo de suspensão e continua a baixar os dados solicitados em segundo plano.
3. Quando o sistema conclui o download, ele cria uma nova tarefa para ativar o aplicativo para que ele possa processar as informações de download. Consulte [tratamento tarefas em segundo plano](#Handling-Background-Tasks) e [concluir o Download de tratamento](#Handling-the-Download-Completing) abaixo. 
4. O aplicativo salva as informações atualizadas e marca a tarefa foi concluída. O desenvolvedor pode ser tentado para atualizar a Interface do usuário do aplicativo neste momento, Apple sugere o agendamento de uma tarefa de instantâneo para lidar com esse processo; Consulte [agendar uma atualização do instantâneo](#Scheduling-a-Snapshot-Update) abaixo.
5. O aplicativo recebe a tarefa de instantâneo, atualiza sua Interface de usuário e marca a tarefa foi concluída. Consulte [tratamento de uma atualização de instantâneo](#Handling-a-Snapshot-Update) abaixo.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Agendando um NSUrlSession

O código a seguir pode ser usado para agendar o download das pontuações mais recentes:

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

Ele configura e cria um novo `NSUrlSession`, em seguida, usa essa sessão para criar um novo download de tarefas usando o `CreateDownloadTask` método. Ele chama o `Resume` método de tarefa para iniciar a sessão de download.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Tarefas do plano de fundo de manipulação

Substituindo o `HandleBackgroundTasks` método o `WKExtensionDelegate`, o aplicativo pode manipular as entrada tarefas em segundo plano:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

O `HandleBackgroundTasks` método percorre todas as tarefas que o sistema enviou o aplicativo (em `backgroundTasks`) procurando um `WKUrlSessionRefreshBackgroundTask`. Se for encontrado, ele volta à sessão e anexa um `NSUrlSessionDownloadDelegate` para lidar com a conclusão do download (consulte [concluir o Download de tratamento](#Handling-the-Download-Completing) abaixo):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Mantém um identificador da tarefa até que ele seja concluída, adicionando-a uma coleção:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas as tarefas enviadas para o aplicativo precisam para ser concluído, para qualquer tarefa que atualmente não está sendo tratada, marcado como concluído:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Manipulando a conclusão do Download

O aplicativo MonkeySoccer usa os seguintes `NSUrlSessionDownloadDelegate` delegado para lidar com a conclusão do download e processar os dados solicitados:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

Quando inicializado, ele mantém um identificador para ambos os `ExtensionDelegate` e `WKRefreshBackgroundTask` que o gerou. Ela substitui o `DidFinishDownloading` método para lidar com a conclusão do download. Em seguida, usa o `CompleteTask` método do `ExtensionDelegate` para informar o a tarefa que ele foi concluído e removê-lo da coleção de tarefas pendentes. Consulte [tratamento tarefas em segundo plano](#Handling-Background-Tasks) acima.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Agendar uma atualização do instantâneo

O código a seguir pode ser usado para agendar uma tarefa de instantâneo para atualizar a interface do usuário com as pontuações mais recentes:

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Assim como `ScheduleURLUpdateSession` método acima, ele cria um novo `NSDate` para quando o aplicativo deseja ativado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleSnapshotRefresh` método o `SharedExtension` é usado para solicitar a tarefa agendada.

O sistema retornará uma `NSError` se não foi possível agendar a tarefa solicitada.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Tratamento de uma atualização de instantâneo

Para lidar com a tarefa de instantâneo, o `HandleBackgroundTasks` método (consulte [tratamento tarefas do plano de fundo](#Handling-Background-Tasks) acima) é modificado para ser semelhante ao seguinte:

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

O método testa para o tipo de tarefa que está sendo processada. Se for um `WKSnapshotRefreshBackgroundTask` obtiver acesso à tarefa:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

O método atualiza a Interface do usuário, em seguida, cria um `NSDate` para informar o sistema em que o instantâneo será obsoleto. Ele cria um `NSMutableDictionary` com informações do usuário para descrever o novo instantâneo e marca a tarefa de instantâneo foi concluída com essas informações:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Além disso, ele também informa a tarefa de instantâneo que o aplicativo não está retornando para o estado padrão (no primeiro parâmetro). Aplicativos que não têm conceito de um estado padrão sempre devem definir essa propriedade como `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Trabalhando com eficiência

Conforme visto no exemplo acima da janela de cinco minutos que o aplicativo MonkeySoccer levou para atualizar suas pontuações funcionando de maneira eficiente e usando o novo watchOS 3 tarefas em segundo plano, o aplicativo só estava ativo para um total de 15 segundos: 

[![](background-tasks-images/update16.png "O aplicativo só estava ativo para um total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Isso reduz o impacto que o aplicativo terá nos recursos disponíveis do Apple Watch e a vida útil da bateria e também permite que o aplicativo trabalhar melhor com outros aplicativos em execução no relógio.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Como funciona o agendamento

Enquanto um aplicativo watchOS 3 está em primeiro plano, ele sempre é agendado para executar e pode fazer qualquer tipo de processamento necessário, como atualizar dados ou redesenhar sua interface do usuário. Quando o aplicativo passa para o plano de fundo, geralmente está suspenso pelo sistema e todas as operações de tempo de execução são interrompidas. 

Enquanto o aplicativo estiver em segundo plano, ele pode ser alvo de sistema para executar rapidamente uma tarefa específica. Assim, em watchOS 2, o sistema pode ativar temporariamente um aplicativo em segundo plano para fazer coisas como manipular uma notificação de aparência longo ou atualizar o Complicativo do aplicativo. No watchOS 3, há várias novas maneiras que um aplicativo pode ser executado em segundo plano.

Com o aplicativo em segundo plano, o sistema impõe vários limites nele:

- Ela é fornecida apenas alguns segundos para concluir a tarefa. O sistema leva em consideração, não apenas a quantidade de tempo passado, mas também quanta energia da CPU que o aplicativo está consumindo para derivar a esse limite.
- Qualquer aplicativo que excede os limites será eliminado com os seguintes códigos de erro:
    - **CPU** -0xc51bad01
    - **Tempo** -0xc51bad02
- O sistema irá impor limites diferentes com base no tipo de tarefa em segundo plano que ele solicitou o aplicativo para executar. Por exemplo, `WKApplicationRefreshBackgroundTask` e `WKURLSessionRefreshBackgroundTask` tarefas são fornecidas tempos de execução de um pouco mais sobre outros tipos de tarefas em segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicações e atualizações de aplicativos

Além de novas tarefas em segundo plano Apple foi adicionado ao watchOS 3, complicações de um aplicativo watchOS podem ter um impacto sobre como e quando o aplicativo recebe atualizações em segundo plano.

Complicações são pequenos elementos visuais que fornecem informações úteis em um relance. Dependendo da face do relógio selecionado, o usuário tem a capacidade de personalizar uma face do relógio com um ou mais Complicativo que pode ser fornecido por um aplicativo de inspeção em watchOS 3.

Se o usuário inclui um complicações do aplicativo em seu face do relógio, ele fornece o aplicativo a seguir atualizada benefícios:

- Faz com que o sistema manter uma pronto para iniciar o aplicativo estado, onde ele tenta iniciar o aplicativo em segundo plano, mantém em memória e permite que ele muito tempo para atualizar.
- Complicações têm garantia de atualizações de push pelo menos 50 por dia.

O desenvolvedor deve sempre tentar criar complicações convincentes para seus aplicativos para convencer o usuário a adicioná-los aos seu face do relógio pelos motivos listados acima.

Em watchOS 2, complicações foram o principal meio de um aplicativo recebida em tempo de execução em segundo plano. WatchOS 3, um aplicativo do Complicativo ainda será ser garantido para receber várias atualizações por hora, no entanto, ele pode usar `WKExtensions` para solicitar mais tempo de execução para atualizar seus complicações.

Observe o seguinte código usado para atualizar o Complicativo do aplicativo do iPhone conectado:

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

Ele usa o `RemainingComplicationUserInfoTransfers` propriedade o `WCSession` ver quantos alta prioridade transfere o aplicativo tiver saído para o dia e, em seguida, executa uma ação com base nesse número. Se o aplicativo começa a ficar com pouca transferências, ele pode adiar a enviar atualizações secundárias e enviar informações apenas quando há uma alteração significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Agendamento e encaixe

Em watchOS 3, Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, será exibida uma galeria de instantâneos de aplicativo fixados. O usuário pode passar esquerda ou direita para localizar o aplicativo desejado, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

[![](background-tasks-images/dock01.png "Encaixe")](background-tasks-images/dock01.png#lightbox)

Periodicamente, o sistema usa os instantâneos de interface de usuário do aplicativo e usa esses instantâneos para preencher os documentos. watchOS o aplicativo tem a oportunidade para atualizar seu conteúdo e a interface do usuário antes desse instantâneo é criado.

Aplicativos que tiverem sido fixados para encaixe podem ocorrer o seguinte:

- Eles receberão um mínimo de um atualizada por hora. Isso inclui uma tarefa de atualização do aplicativo e uma tarefa de instantâneo.
- A alocação de atualização é distribuída entre todos os aplicativos no compartimento. Portanto, a menos aplicativos que de usuário foi fixado, as atualizações mais possíveis de cada aplicativo receberá.
- O aplicativo será mantido na memória para que o aplicativo continuará rapidamente quando selecionado do encaixe.

O último aplicativo executou o usuário será considerado o _usados recentemente_ aplicativo e ocupará o último slot no encaixe. A partir daí, existe usuário pode escolher para fixá-lo permanentemente o encaixe. O mais recentemente usada será tratada como qualquer outro aplicativo favorito o usuário já foi fixado de encaixe.

> [!IMPORTANT]
> Aplicativos que foram adicionados apenas para a tela inicial não terá nenhum agendamento regular. Para receber um agendamento regular e plano de fundo das atualizações, um aplicativo _deve_ ser adicionado ao encaixe.

Conforme descrito anteriormente neste documento, os instantâneos são muito importantes watchOS 3 como eles funcionam como imagens de visualização e de inicialização para o aplicativo. Se o usuário estabelece em um aplicativo de encaixe, ele expandir para tela inteira, insira o primeiro plano e iniciar a execução, portanto, é fundamental que o instantâneo seja atualizado.

Pode haver momentos em que o sistema decide um novo instantâneo de interface de usuário do aplicativo. Neste casos, a solicitação de instantâneo não afetará alocação de tempo de execução do aplicativo. O exemplo a seguir irá disparar uma solicitação de instantâneo do sistema:

- Uma atualização do Complicativo da linha do tempo.
- Interação do usuário com a notificação do aplicativo.
- Alternando do primeiro plano para o estado do plano de fundo.
- Após uma hora de sendo no estado em segundo plano, portanto o aplicativo pode retornar ao estado padrão.
- Quando watchOS inicializado pela primeira vez.

<a name="Best-Practices" />

## <a name="best-practices"></a>Práticas recomendadas 

Apple sugere práticas recomendadas a seguir ao trabalhar com tarefas em segundo plano:

- Agende sempre que o aplicativo precisa ser atualizado. Toda vez que o aplicativo for executado, ele deve reavaliar suas necessidades futuras e ajustar essa agenda conforme necessário.
- Se o sistema envia uma tarefa de atualização em segundo plano e o aplicativo não requer uma atualização, adie o trabalho até que uma atualização é realmente necessária.
- Considere todas as oportunidades de tempo de execução disponíveis para um aplicativo:
    - Ativação de primeiro plano e encaixe.
    - Notificações.
    - Atualizações do complicativo.
    - Atualizações em segundo plano.
- Use `ScheduleBackgroundRefresh` para tempo de execução do plano de fundo para fins gerais, como:
    - Sondando o sistema para obter informações.
    - Agendar futuro `NSURLSessions` para solicitar dados do plano de fundo. 
    - Transições de tempo conhecido.
    - Atualizações de disparo Complicativo.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Práticas recomendadas de instantâneo

Ao trabalhar com atualizações de instantâneo, Apple faz as seguintes sugestões:

- Invalide instantâneos somente quando necessário, por exemplo, quando há uma alteração significativa de conteúdo.
- Evite invalidação de instantâneo de alta frequência. Por exemplo, um aplicativo de timer não deve atualizar o instantâneo a cada segundo, ele só deve ser feito quando o timer terminou.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Fluxo de dados de aplicativo

Apple sugerir a seguir para trabalhar com o fluxo de dados:

[![](background-tasks-images/update17.png "Diagrama de fluxo de dados do aplicativo")](background-tasks-images/update17.png#lightbox)

Um evento externo (como inspecionar conectividade) o aplicativo é ativado. Isso força o aplicativo atualize seu modelo de dados (que representa o estado atual de aplicativos). Como resultado da alteração do modelo de dados o aplicativo precisará atualizar seus complicações, solicitar um novo instantâneo, possivelmente iniciar um plano de fundo `NSURLSession` buscar mais dados e agendar a tela de fundo é atualizada.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>O ciclo de vida do aplicativo

Por causa de encaixe e a capacidade de fixar aplicativos favoritos a ele, considera Apple que os usuários mover entre os aplicativos muito mais, muito mais frequentemente, em seguida, fez com o watchOS 2. Como resultado, o aplicativo deve estar pronto para lidar com essa alteração e mover rapidamente entre os estados de primeiro plano e plano de fundo.

Apple tem as seguintes sugestões:

- Certifique-se de que o aplicativo termina de qualquer tarefa em segundo plano assim que possível após a inserção de ativação de primeiro plano.
- Certifique-se de concluir todo o trabalho de primeiro plano antes de inserir o plano de fundo chamando `NSProcessInfo.PerformExpiringActivity`.
- Ao testar um aplicativo no simulador watchOS, nenhum dos orçamentos de tarefa serão impostas para que um aplicativo possa atualizar tanto quanto necessário para um recurso de teste corretamente.
- Sempre teste em hardware Apple Watch real para garantir que o aplicativo não está sendo executado após seu orçamento antes da publicação para conectar-se de iTunes.
- Apple sugere mantendo o Apple Watch no carregador ao teste e depuração.
- Certifique-se de que os frios Iniciando e retomada de um aplicativo são testados throughly.
- Verifique se que todas as tarefas do aplicativo estão sendo concluídas.
- Variar o número de aplicativos que estão fixadas em encaixe para testar a melhor e o pior caso de cenários.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos de que Apple fez watchOS e como eles podem ser usados para manter um aplicativo de inspeção atualizado. Primeiro, ele coberto todos o novo Apple de tarefa em segundo plano foi adicionado no watchOS 3. Em seguida, abordado o ciclo de vida de API do plano de fundo e como implementar tarefas em segundo plano em um aplicativo de watchOS Xamarin. Finalmente, ele coberto funciona como a agenda e deu algumas práticas recomendadas.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
