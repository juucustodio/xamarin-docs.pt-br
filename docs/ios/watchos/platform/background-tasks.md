---
title: Tarefas em segundo plano no Xamarin do watchOS
description: Este documento descreve como usar tarefas em segundo plano com o watchOS no Xamarin, dê uma olhada em tipos de tarefas em segundo plano, usando os recursos, implementar tarefas em segundo plano, agendamento, melhores práticas e muito mais.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 45886d787ecc40c9e11ce0c713ffa22819e29db2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227211"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Tarefas em segundo plano no Xamarin do watchOS

Com o watchOS 3, há três maneiras principais que um aplicativo de inspeção pode manter suas informações atualizadas: 

- Usando uma das várias novas tarefas em segundo plano. 
- Ter um de seus complicações na face do relógio (dando a ele tempo extra para atualizar). 
- Tendo o pin do usuário ao aplicativo ao Dock novo (onde seu mantidas na memória e atualizados com frequência). 

## <a name="keeping-an-app-up-to-date"></a>Mantendo um aplicativo atualizado

Antes de discutir todas as maneiras que um desenvolvedor pode manter dados de um aplicativo do watchOS e Interface do usuário atual e atualizados, esta seção será dar uma olhada em um conjunto comum de padrões de uso e como um usuário pode mover entre o iPhone e o seu Apple Watch ao longo do dia com base em  a hora do dia e a atividade eles estão fazendo no momento (por exemplo, orientando).

Veja o exemplo a seguir:

[![](background-tasks-images/update00.png "Como um usuário pode mover entre o iPhone e o seu Apple Watch ao longo do dia")](background-tasks-images/update00.png#lightbox)

1. Na parte da manhã, durante a espera na linha para um café, o usuário navega notícias atuais em seu iPhone por vários minutos.
2. Antes de deixar o café, eles verificam rapidamente o clima com uma complicação no seu Mostrador do relógio.
3. Antes do almoço, eles usam o aplicativo de mapas no iPhone para localizar um restaurante mais próximo e reservar uma reserva para atender a um cliente.
4. Quando estiverem viajando para o restaurante, eles recebem uma notificação em seu Apple Watch e uma visão rápida, eles sabem que seus almoço está atrasado.
5. Durante a noite, eles usam o aplicativo de mapas no iPhone para verificar o tráfego antes de condução home.
6. No caminho inicial, eles recebem uma notificação de iMessage em seu Apple Watch solicitando para pegar alguns leite e usam o recurso de resposta rápida para enviar a resposta "Okey".

Devido a "Visão geral" (menos de três segundos) a natureza de como um usuário é quando você usar um aplicativo de Apple Watch, normalmente há tempo suficiente para o aplicativo buscar informações desejadas e atualizar sua interface do usuário antes de exibi-los para o usuário não está.

Ao usar os novos APIs Apple está incluído no watchOS 3, o aplicativo pode agendar para um _atualização do plano de fundo_ e ter as informações desejadas prontas antes do usuário solicitar. Veja o exemplo do Complicativo clima discutidos acima:

[![](background-tasks-images/update01.png "Um exemplo de como a complicação de clima")](background-tasks-images/update01.png#lightbox)

1. As agendas de aplicativo para ser ativado pelo sistema em um momento específico. 
2. O aplicativo busca as informações que ele será necessário para gerar a atualização.
3. O aplicativo gera novamente a sua Interface do usuário para refletir os novos dados.
4. Quando o usuário resumos em complicação do aplicativo, ele tem informações atualizadas sem precisar aguardar a atualização.

Como visto acima, o sistema de watchOS desperta o aplicativo usando uma ou mais tarefas, dos quais ele tem um pool muito limitado disponível:

[![](background-tasks-images/update02.png "O sistema de watchOS desperta o aplicativo usando uma ou mais tarefas")](background-tasks-images/update02.png#lightbox)

Apple sugere aproveitando ao máximo dessa tarefa (já que é um recurso limitado para o aplicativo), mantendo nele até que o aplicativo tiver terminado o processo de atualização em si.

O sistema fornece essas tarefas chamando o novo `HandleBackgroundTasks` método da `WKExtensionDelegate` delegar. Por exemplo:

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

Quando o aplicativo tiver terminado a tarefa específica, ele retorna-lo para o sistema, marcando-concluído:

[![](background-tasks-images/update03.png "A tarefa retorna para o sistema, marcando-concluído")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Novas tarefas em segundo plano

watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações garantindo que ele tenha o conteúdo que o usuário precisa antes do aplicativo for aberto, como:

- **Atualização de aplicativo do plano de fundo** - o [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tarefa permite que o aplicativo atualize seu estado em segundo plano. Normalmente, isso incluirá outra tarefa, como baixar novo conteúdo da internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Instantâneo de atualização do plano de fundo** - o [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tarefa permite que o aplicativo atualize seu conteúdo e a interface do usuário antes do sistema tira um instantâneo que será usado para preencher o encaixe.
- **Conectividade de inspeção do plano de fundo** - o [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando ele recebe dados do plano de fundo de no iPhone emparelhado.
- **Sessão de URL do plano de fundo** - o [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tarefa é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou for concluído (com êxito ou erro).

Essas tarefas serão abordadas em detalhes nas seções a seguir.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

O `WKApplicationRefreshBackgroundTask` é uma tarefa genérica que pode ser agendada para ter o aplicativo ativado em uma data futura:

[![](background-tasks-images/update04.png "Um WKApplicationRefreshBackgroundTask ativado em uma data futura")](background-tasks-images/update04.png#lightbox)

Dentro do tempo de execução da tarefa, o aplicativo pode fazer nenhum tipo de processamento local, como atualização de uma linha do tempo complicação ou buscar certos dados necessários com um `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

O sistema enviará um `WKURLSessionRefreshBackgroundTask` quando os dados foi concluído fazendo download e pronto para ser processado pelo aplicativo:

[![](background-tasks-images/update05.png "O WKURLSessionRefreshBackgroundTask quando os dados de download for concluído")](background-tasks-images/update05.png#lightbox)

Um aplicativo não seja deixado em execução enquanto o download dos dados em segundo plano. Em vez disso, o aplicativo agenda a solicitação de dados, em seguida, está suspenso e o sistema manipula o download de dados, reawakening apenas o aplicativo quando o download for concluído.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

No watchOS 3, a Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, será exibida uma galeria de aplicativo fixo de instantâneos. O usuário pode passe o dedo para esquerda ou direita para localizar o aplicativo desejado e, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

[![](background-tasks-images/update06.png "Substituindo o instantâneo com a interface de aplicativos em execução")](background-tasks-images/update06.png#lightbox)

O sistema periodicamente gera instantâneos da interface de usuário do aplicativo (enviando um `WKSnapshotRefreshBackgroundTask`) e usa esses instantâneos para preencher o encaixe. watchOS dá a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo é tirado de aplicativo.

Instantâneos são muito importantes no watchOS 3, pois eles funcionam como imagens de visualização e de inicialização para o aplicativo. Se o usuário escolhe um aplicativo no Dock, ele será expandido para tela inteira, insira o primeiro plano e iniciar a execução, portanto, é fundamental que o instantâneo ser atualizadas:

[![](background-tasks-images/update07.png "Se o usuário escolhe um aplicativo no Dock, ela será expandida para tela inteira")](background-tasks-images/update07.png#lightbox)

Novamente, o sistema emitirá um `WKSnapshotRefreshBackgroundTask` para que o aplicativo pode preparar (com a atualização de dados e a interface do usuário) antes do instantâneo é criado:

[![](background-tasks-images/update08.png "O aplicativo pode preparar atualizando os dados e a interface do usuário antes do instantâneo é criado")](background-tasks-images/update08.png#lightbox)

Quando o aplicativo marca o `WKSnapshotRefreshBackgroundTask` concluída, o sistema automaticamente tirar um instantâneo de interface do usuário do aplicativo.

> [!IMPORTANT]
> É importante sempre agendar um ` WKSnapshotRefreshBackgroundTask` depois que o aplicativo recebeu dados novos e atualizados sua Interface do usuário ou o usuário não verá as informações modificadas.




Além disso, quando o usuário recebe uma notificação do aplicativo e toca para trazer o aplicativo em primeiro plano, o instantâneo precisa ser atualizado desde que ele está atuando como a tela de inicialização:

[![](background-tasks-images/update09.png "O usuário recebe uma notificação do aplicativo e toca para trazer o aplicativo em primeiro plano")](background-tasks-images/update09.png#lightbox)

Se passaram mais de uma hora, pois o usuário interagiu com um aplicativo do watchOS, ele será capaz de retornar ao estado padrão. O estado padrão pode ter diferentes significados para diferentes aplicativos e, com base no design de um aplicativo, ele pode não ter um estado padrão em todos os.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

No watchOS 3, a Apple integrou inspeção de conectividade com a API de atualização em segundo plano por meio do novo `WKWatchConnectivityRefreshBackgroundTask`. Usando esse novo recurso, um aplicativo de iPhone pode fornecer dados atualizados para seu equivalente do aplicativo de inspeção, enquanto o aplicativo watchOS está em execução em segundo plano:

[![](background-tasks-images/update10.png "Um aplicativo de iPhone pode fornecer dados atualizados para seu equivalente do aplicativo de inspeção, enquanto o aplicativo watchOS está em execução em segundo plano")](background-tasks-images/update10.png#lightbox)

Iniciando uma complicação enviar por Push, contexto de aplicativo, enviando um arquivo ou atualizar informações do usuário no aplicativo iPhone será ativado o aplicativo Apple Watch em segundo plano.

Quando o aplicativo watch é ativado por meio de um `WKWatchConnectivityRefreshBackgroundTask` ele será necessário usar os métodos da API padrão para receber os dados de aplicativo do iPhone.

[![](background-tasks-images/update11.png "O fluxo de dados WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Certifique-se de que a sessão foi ativado.
2. Monitorar o novo `HasContentPending` desde que o valor da propriedade `true`, o aplicativo ainda tem dados a serem processados. Como antes, o aplicativo deverá retê a tarefa até que ele terminou de processar todos os dados.
3. Quando há não há mais dados a serem processados (`HasContentPending = false`), marcar a tarefa foi concluída para retorná-lo para o sistema. Falha ao fazer isso irá esgotar o tempo de execução do plano de fundo alocado do aplicativo resultando em um relatório de falhas.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>O ciclo de vida de API de plano de fundo

Colocar todas as partes da nova API de tarefas em segundo plano em conjunto, um conjunto comum de interações pareceria com o seguinte:

[![](background-tasks-images/update12.png "O ciclo de vida de API de plano de fundo")](background-tasks-images/update12.png#lightbox)

1. Primeiro, o aplicativo watchOS agenda um plano de fundo da tarefa ativo como algum momento no futuro.
2. O aplicativo é ativado pelo sistema e enviado de uma tarefa.
3. O aplicativo processa a tarefa para concluir qualquer trabalho era necessário.
4. Como resultado do processamento da tarefa, o aplicativo pode precisar agendar o plano de fundo mais tarefas a serem concluídas mais trabalho no futuro, como baixar o conteúdo mais usando um `NSUrlSession`.
5. O aplicativo marca a tarefa foi concluída e retorna-a para o sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Usando recursos com responsabilidade

É essencial que um aplicativo watchOS se comporta com responsabilidade dentro desse ecossistema, limitando sua dreno nos recursos compartilhados do sistema.

Vejamos o cenário a seguir:

[![](background-tasks-images/update13.png "Um aplicativo watchOS limita seu consumo de recursos compartilhados do sistema")](background-tasks-images/update13.png#lightbox)

1. O usuário inicia um aplicativo watchOS às 1:00.
2. O aplicativo agenda uma tarefa para despertar e baixar novo conteúdo em uma hora às 2:00.
3. Em 1:50 PM o usuário abre novamente o aplicativo que permite atualizar seus dados e a interface do usuário no momento.
4. Em vez de deixar a ativação de tarefa o aplicativo novamente em 10 minutos, o aplicativo deve replanejar a tarefa para executar uma hora posterior às 14H: 50.

Embora cada aplicativo é diferente, a Apple sugere encontrar padrões de uso, como os exibidos acima, para ajudar a conservar recursos do sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementar tarefas em segundo plano

Por exemplo, este documento usará o aplicativo de esportes MonkeySoccer falso que relata as pontuações de futebol ao usuário. 

Vejamos o cenário de uso típico a seguir:

[![](background-tasks-images/update14.png "O cenário de uso típico")](background-tasks-images/update14.png#lightbox)

Equipe de futebol Favoritos do usuário estiver em execução a correspondência das 7:00 big 9:00 PM para que o aplicativo deve esperar que o usuário para verificar regularmente a pontuação e ele decide em um intervalo de atualização de 30 minutos.

1. O usuário abre o aplicativo e ele agenda uma tarefa de atualização em segundo plano, 30 minutos mais tarde. A API de plano de fundo permite apenas um tipo de plano de fundo a tarefa está em execução em um determinado momento.
2. O aplicativo recebe a tarefa e atualiza seus dados e a interface do usuário e agendas para outro plano de fundo de tarefa 30 minutos mais tarde. É importante que o desenvolvedor se lembra de agendar a tarefa de outro plano de fundo ou o aplicativo nunca será ativado novamente para obter mais atualizações.
3. Novamente, o aplicativo recebe a tarefa e atualiza seus dados, atualiza sua interface do usuário e agenda a outro plano de fundo de tarefa 30 minutos mais tarde.
4. O mesmo processo se repete novamente.
5. O plano de fundo última tarefa é recebida e o aplicativo atualiza novamente seus dados e a interface do usuário. Como essa é a pontuação final ele não agenda para uma nova atualização em segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Agendamento de atualização em segundo plano

Dado o cenário acima, o aplicativo MonkeySoccer pode usar o código a seguir para agendar uma atualização em segundo plano:

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

Ele cria um novo `NSDate` 30 minutos no futuro quando o aplicativo deseja ativado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleBackgroundRefresh` método da `SharedExtension` é usado para solicitar a tarefa agendada.

O sistema retornará uma `NSError` se não foi possível agendar a tarefa solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Processo de atualização

Em seguida, faça uma janela de 5 minutos, mostrando as etapas necessárias para atualizar a pontuação de examinar mais detalhadamente:

[![](background-tasks-images/update15.png "A janela de 5 minutos, mostrando as etapas necessárias para atualizar a pontuação")](background-tasks-images/update15.png#lightbox)

1. Em 19:30:02: 00 o aplicativo é ativado pelo sistema e dado a tela de fundo de atualização de tarefa. Sua primeira prioridade é obter os resultados mais recentes do servidor. Ver [Agendando um NSUrlSession](#Scheduling-a-NSUrlSession) abaixo.
2. Em 7:30:05: o aplicativo conclui a tarefa original, o sistema coloca o aplicativo no modo de suspensão e continua baixar os dados solicitados em segundo plano.
3. Quando o sistema conclui o download, ele cria uma nova tarefa para ativar o aplicativo para poder processar as informações baixadas. Ver [tratamento tarefas em segundo plano](#Handling-Background-Tasks) e [tratamento da conclusão do Download](#Handling-the-Download-Completing) abaixo. 
4. O aplicativo salva as informações atualizadas e marca a tarefa foi concluída. O desenvolvedor pode ser tentado a atualizar a Interface do usuário do aplicativo neste momento, no entanto, a Apple sugere o agendamento de uma tarefa de instantâneo para lidar com esse processo. Ver [agendar uma atualização de instantâneo](#Scheduling-a-Snapshot-Update) abaixo.
5. O aplicativo recebe a tarefa de instantâneo, atualiza a Interface do usuário e marca a tarefa foi concluída. Ver [tratamento de uma atualização de instantâneo](#Handling-a-Snapshot-Update) abaixo.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Agendando um NSUrlSession

O código a seguir pode ser usado para agendar o download das últimas pontuações:

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

## <a name="handling-background-tasks"></a>Tarefas de manipulação de plano de fundo

Substituindo o `HandleBackgroundTasks` método da `WKExtensionDelegate`, o aplicativo pode manipular as entrada tarefas em segundo plano:

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

O `HandleBackgroundTasks` método percorre todas as tarefas que o sistema enviou o aplicativo (no `backgroundTasks`) procurando um `WKUrlSessionRefreshBackgroundTask`. Se um for encontrado, ele reassocia-se a sessão e anexa um `NSUrlSessionDownloadDelegate` para lidar com a conclusão do download (consulte [tratamento da conclusão da baixar](#Handling-the-Download-Completing) abaixo):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Ele mantém um identificador da tarefa até que ela seja concluída ao adicioná-lo a uma coleção:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas as tarefas enviadas para o aplicativo precisam para ser concluída, para qualquer tarefa que não estão atualmente sendo manipulada, marcado como concluído:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Tratamento da conclusão do Download

O aplicativo MonkeySoccer usa os seguintes `NSUrlSessionDownloadDelegate` delegado para manipular a conclusão do download e processar os dados solicitados:

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

Quando inicializado, ele mantém um identificador para ambos os `ExtensionDelegate` e o `WKRefreshBackgroundTask` que o gerado. Ele substitui o `DidFinishDownloading` método para lidar com a conclusão do download. Em seguida, usa o `CompleteTask` método da `ExtensionDelegate` para informar a tarefa que ele foi concluído e removê-lo da coleção de tarefas pendentes. Ver [tratamento tarefas em segundo plano](#Handling-Background-Tasks) acima.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Agendar uma atualização de instantâneo

O código a seguir pode ser usado para agendar uma tarefa de instantâneo para atualizar a interface do usuário com os resultados mais recentes:

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

Assim como `ScheduleURLUpdateSession` método acima, ele cria um novo `NSDate` quando o aplicativo deseja ativado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleSnapshotRefresh` método da `SharedExtension` é usado para solicitar a tarefa agendada.

O sistema retornará uma `NSError` se não foi possível agendar a tarefa solicitada.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Tratamento de uma atualização de instantâneo

Para lidar com a tarefa de instantâneo, o `HandleBackgroundTasks` método (consulte [manipular tarefas de plano de fundo](#Handling-Background-Tasks) acima) é modificado para ser semelhante ao seguinte:

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

O método de testes para o tipo de tarefa que está sendo processado. Se for um `WKSnapshotRefreshBackgroundTask` ele ganha acesso para a tarefa:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

O método atualiza a Interface do usuário e em seguida, cria um `NSDate` para informar ao sistema quando o instantâneo será obsoleto. Ele cria um `NSMutableDictionary` com informações do usuário para descrever o novo instantâneo e a tarefa de instantâneo foi concluída com essas informações de marcas:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Além disso, ele também informa a tarefa de instantâneo que o aplicativo não está retornando para o estado padrão (no primeiro parâmetro). Aplicativos que não têm nenhum conceito de um estado padrão sempre devem definir essa propriedade como `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Trabalhando com eficiência

Como visto no exemplo acima da janela de cinco minutos que o aplicativo MonkeySoccer levou para atualizar suas pontuações, funcionando de maneira eficiente e usando o novo watchOS 3 tarefas em segundo plano, o aplicativo só estava ativo para um total de 15 segundos: 

[![](background-tasks-images/update16.png "O aplicativo só estava ativo para um total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Isso reduz o impacto que o aplicativo terá nos recursos disponíveis do Apple Watch e vida útil da bateria e também permite que o aplicativo funcionar melhor com outros aplicativos em execução no relógio.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Como funciona a programação

Enquanto um aplicativo watchOS 3 está em primeiro plano, ele sempre é programado para executar e pode fazer qualquer tipo de processamento necessário, como dados de atualização ou redesenhar sua interface do usuário. Quando o aplicativo passa para o plano de fundo, ele geralmente é suspenso pelo sistema e todas as operações de tempo de execução são interrompidas. 

Enquanto o aplicativo está em segundo plano, ele pode ser direcionado pelo sistema para executar rapidamente uma tarefa específica. Portanto, em watchOS 2, o sistema é ativados temporariamente um aplicativo em segundo plano para fazer coisas como manipular uma notificação Visual longo ou atualizar complicação do aplicativo. No watchOS 3, há várias novas maneiras que um aplicativo pode ser executado em segundo plano.

Enquanto um aplicativo está em segundo plano, o sistema impõe vários limites nele:

- Ele é dada apenas alguns segundos para concluir qualquer tarefa. O sistema leva em consideração, não apenas a quantidade de tempo passado, mas também quanta energia da CPU que o aplicativo está consumindo para derivar a esse limite.
- Qualquer aplicativo que excede os limites será eliminado com os seguintes códigos de erro:
    - **CPU** -0xc51bad01
    - **Tempo** -0xc51bad02
- O sistema irá impor limites diferentes com base no tipo de tarefa em segundo plano que ele solicitou que o aplicativo para executar. Por exemplo, `WKApplicationRefreshBackgroundTask` e `WKURLSessionRefreshBackgroundTask` tarefas são fornecidas tempos de execução de um pouco mais sobre outros tipos de tarefas em segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicações e atualizações de aplicativo

Além das tarefas em segundo plano novo que Apple adicionado ao watchOS 3, complicações de um aplicativo watchOS podem ter um impacto sobre como e quando o aplicativo recebe atualizações em segundo plano.

Complicações são pequenos elementos visuais que fornecem informações úteis em um relance. Dependendo do Mostrador do relógio, o usuário tem a capacidade de personalizar uma Mostrador com um ou mais complicação que pode ser fornecida por um aplicativo de inspeção no watchOS 3.

Se o usuário inclui uma das complicações do aplicativo em seu Mostrador do relógio, ele fornece ao aplicativo a seguir atualizada benefícios:

- Faz com que o sistema manter o aplicativo em um pronto para lançamento estado, onde ele tenta iniciar o aplicativo em segundo plano, mantém na memória e dá a ele tempo extra para atualizar.
- Complicações têm garantia de pelo menos 50 atualizações por push por dia.

O desenvolvedor sempre deve se esforçar para criar complicações atraentes para seus aplicativos para convencer o usuário a adicioná-los a seu Mostrador pelas razões listadas acima.

No watchOS 2, complicações foram a principal maneira de que um aplicativo recebeu o tempo de execução enquanto estiver no plano de fundo. No watchOS 3, um aplicativo de complicação será ser garantido para receber várias atualizações por hora, no entanto, ele pode usar `WKExtensions` para solicitar mais tempo de execução para atualizar seus complicações.

Examine o código a seguir usado para atualizar a complicação no aplicativo iPhone conectado:

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

Ele usa o `RemainingComplicationUserInfoTransfers` propriedade do `WCSession` ver quantos alta prioridade transfere o aplicativo tiver deixado para o dia e, em seguida, executa uma ação com base nesse número. Se o aplicativo começa a ficar sem transferências de, ele pode adiar a enviar atualizações secundárias e enviar informações apenas quando há uma alteração significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Agendamento e encaixe

No watchOS 3, a Apple adicionou o encaixe em que os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral sobre o Apple Watch, uma galeria de instantâneos de aplicativo fixo será exibida. O usuário pode passe o dedo para esquerda ou direita para localizar o aplicativo desejado e, em seguida, toque no aplicativo para iniciá-lo, substituindo o instantâneo com a interface do aplicativo em execução.

[![](background-tasks-images/dock01.png "Encaixe")](background-tasks-images/dock01.png#lightbox)

Periodicamente, o sistema usa instantâneos da interface de usuário do aplicativo e usa esses instantâneos para preencher os documentos. watchOS dá a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo é tirado de aplicativo.

Aplicativos que foram fixados ao dock podem ocorrer o seguinte:

- Eles receberão um mínimo de um atualizado por hora. Isso inclui uma tarefa de atualização de aplicativo e uma tarefa de instantâneo.
- O orçamento de atualização é distribuído entre todos os aplicativos no Dock. Portanto, quanto menos aplicativos que o usuário tenha fixado, as atualizações mais potenciais de cada aplicativo receberá.
- O aplicativo será mantido na memória para que o aplicativo continuará rapidamente quando selecionados de encaixe.

O último aplicativo executado pelo usuário será considerado o _usados recentemente_ aplicativo e ocupará o último slot no Dock. A partir daí, existe usuário pode escolher para fixá-lo permanentemente ao Dock. O mais recentemente usada será tratada como qualquer outro aplicativo favorito o usuário já foi fixada ao Dock.

> [!IMPORTANT]
> Aplicativos que só foram adicionados para a tela de início não terá qualquer agendamento regular. Para receber um agendamento regular e em segundo plano atualiza, um aplicativo _deve_ ser adicionado ao Dock.

Conforme mencionado no início deste documento, os instantâneos são muito importantes para watchOS 3, pois eles funcionam como imagens de visualização e de inicialização para o aplicativo. Se o usuário escolhe um aplicativo no Dock, ele será expandido para tela inteira, insira o primeiro plano e iniciar a execução, portanto, é imperativo que o instantâneo seja atualizado.

Pode haver vezes quando o sistema decide que precisa de um instantâneo novo de interface do usuário do aplicativo. Este situações, a solicitação de instantâneo não será contada em relação ao orçamento de tempo de execução do aplicativo. O exemplo a seguir irá disparar uma solicitação de instantâneo do sistema:

- Uma atualização de linha do tempo de complicação.
- Interação do usuário com a notificação do aplicativo.
- Alternando do primeiro plano para o estado do plano de fundo.
- Após uma hora de ter ficado no estado em segundo plano, portanto, o aplicativo pode retornar ao estado padrão.
- Quando watchOS inicializa pela primeira vez.

<a name="Best-Practices" />

## <a name="best-practices"></a>Práticas recomendadas 

A Apple sugere as seguintes práticas recomendadas ao trabalhar com tarefas em segundo plano:

- Agende sempre que o aplicativo precisa ser atualizado. Sempre que o aplicativo é executado, ele deve reavaliar suas necessidades futuras e ajuste esse agendamento conforme necessário.
- Se o sistema envia uma tarefa de atualização em segundo plano e o aplicativo não exija uma atualização, adie o trabalho até que uma atualização seja realmente necessária.
- Considere todas as oportunidades de tempo de execução disponíveis para um aplicativo:
    - Ativação de encaixe e de primeiro plano.
    - Notificações.
    - Atualizações de complicação.
    - Atualizações em segundo plano.
- Use `ScheduleBackgroundRefresh` para tempo de execução do plano de fundo para fins gerais, como:
    - O sistema para obter informações de sondagem.
    - Agendar futuro `NSURLSessions` para solicitar dados do plano de fundo. 
    - Transições de tempo conhecido.
    - Disparo de atualizações de complicação.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Práticas recomendadas do instantâneo

Ao trabalhar com atualizações de instantâneo, Apple torna as sugestões a seguir:

- Invalide instantâneos somente quando necessário, por exemplo, quando há uma alteração significativa de conteúdo.
- Evite a invalidação de instantâneo de alta frequência. Por exemplo, um aplicativo de timer não deve atualizar o instantâneo a cada segundo, ele só deve ser feito quando o temporizador foi finalizada.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Fluxo de dados de aplicativo

Apple sugere o seguinte para trabalhar com o fluxo de dados:

[![](background-tasks-images/update17.png "Diagrama de fluxo de dados do aplicativo")](background-tasks-images/update17.png#lightbox)

Um evento externo (por exemplo, a inspeção de conectividade) ativa o aplicativo. Isso força o aplicativo atualize seu modelo de dados (que representa o estado atual de aplicativos). Como resultado da alteração do modelo de dados o aplicativo precisará atualizar seus complicações, solicitar um novo instantâneo, possivelmente, iniciar um plano de fundo `NSURLSession` buscar mais dados e agendar a tela de fundo é atualizada.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>O ciclo de vida do aplicativo

Por causa de encaixe e a capacidade de fixar aplicativos favoritos a ele, pensa da Apple que mover usuários entre aplicativos muito mais, muito mais frequência, em seguida, eles faziam isso com o watchOS 2. Como resultado, o aplicativo deve estar pronto para lidar com essa alteração e mover rapidamente entre os estados de primeiro plano e plano de fundo.

Apple tem as seguintes sugestões:

- Certifique-se de que o aplicativo termina de qualquer tarefa em segundo plano mais rápido possível após a inserção de ativação de primeiro plano.
- Certifique-se de concluir todo o trabalho de primeiro plano antes de inserir o plano de fundo ao chamar `NSProcessInfo.PerformExpiringActivity`.
- Ao testar um aplicativo no simulador watchOS, nenhum dos orçamentos de tarefa serão impostas para que um aplicativo pode atualizar tanto quanto necessário para testar adequadamente um recurso.
- Sempre teste em hardwares reais do Apple Watch para garantir que o aplicativo não está em execução após seus orçamentos antes da publicação para o iTunes Connect.
- A Apple sugere mantendo o Apple Watch no carregador durante o teste e depuração.
- Certifique-se tanto frio Iniciando e retomada de um aplicativo são totalmente testadas.
- Verifique se que todas as tarefas de aplicativo estão sendo concluídas.
- Variar o número de aplicativos que estão fixados no Dock para testar o melhor e pior caso de cenários.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou as melhorias feitas pela Apple para watchOS e como eles podem ser usados para manter um aplicativo de inspeção atualizados. Primeiro, ele abordou a todos os novos Apple de tarefa em segundo plano tiver adicionado no watchOS 3. Em seguida, ele abordou o ciclo de vida de API de plano de fundo e como implementar tarefas em segundo plano em um aplicativo Xamarin. watchOS. Por fim, ele coberto funciona como a agenda e deu algumas práticas recomendadas.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
