---
title: Tarefas em segundo plano do watchOS no Xamarin
description: Este documento descreve como usar tarefas em segundo plano com o watchOS no Xamarin, examinando os tipos de tarefas em segundo plano, usando recursos, implementando tarefas em segundo plano, agendamento, práticas recomendadas e muito mais.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/13/2017
ms.openlocfilehash: b01fbbe813b778d3c2e1cabeba620ed48a46ecac
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287150"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Tarefas em segundo plano do watchOS no Xamarin

Com o watchOS 3, há três maneiras principais de que um aplicativo Watch possa manter suas informações atualizadas: 

- Usando uma das várias novas tarefas em segundo plano. 
- Ter uma de suas complicações na face do relógio (dando a ela tempo extra para atualizar). 
- Fazer com que o usuário fixe o aplicativo no novo encaixe (onde ele é mantido na memória e atualizado com frequência). 

## <a name="keeping-an-app-up-to-date"></a>Mantendo um aplicativo atualizado

Antes de discutir todas as maneiras como um desenvolvedor pode manter os dados e a interface do usuário de um aplicativo watchOS atualizados, esta seção analisará um conjunto típico de padrões de uso e como um usuário pode se mover entre o iPhone e seus Apple Watch ao longo do dia com base em  a hora do dia e a atividade que eles estão fazendo no momento (como dirigir).

Veja o exemplo a seguir:

[![](background-tasks-images/update00.png "Como um usuário pode se mover entre seu iPhone e seus Apple Watch ao longo do dia")](background-tasks-images/update00.png#lightbox)

1. Na manhã, enquanto esperava em linha para um café, o usuário procura as notícias atuais em seu iPhone por vários minutos.
2. Antes de sair do café, eles verificam rapidamente o clima com a complicação de sua face de inspeção.
3. Antes de almoçar, eles usam o aplicativo Maps no iPhone para encontrar um restaurante próximo e agendar uma reserva para atender a um cliente.
4. Ao viajar para o restaurante, eles recebem uma notificação sobre seus Apple Watch e, com uma visão rápida, sabem que o seu compromisso de almoço está em execução tardiamente.
5. Na noite, eles usam o aplicativo Maps no iPhone para verificar o tráfego antes de dirigir para casa.
6. Na forma inicial, eles recebem uma notificação de iMessage em seus Apple Watch solicitando que eles peguem um leite e usem o recurso de resposta rápida para enviar a resposta "OK".

Por causa da natureza de "visão rápida" (menos de três segundos) de como um usuário está querendo usar um aplicativo Apple Watch, normalmente não há tempo suficiente para o aplicativo buscar as informações desejadas e atualizar sua interface do usuário antes de exibi-la aos usuários.

Usando as novas APIs que a Apple incluiu no watchOS 3, o aplicativo pode agendar uma _atualização em segundo plano_ e ter as informações desejadas prontas antes que o usuário a solicite. Veja o exemplo da complicação do clima discutida acima:

[![](background-tasks-images/update01.png "Um exemplo da complicação do clima")](background-tasks-images/update01.png#lightbox)

1. O aplicativo é agendado para ser ativadosdo pelo sistema em um momento específico. 
2. O aplicativo busca as informações necessárias para gerar a atualização.
3. O aplicativo regenera sua interface do usuário para refletir os novos dados.
4. Quando o usuário Resumindo a complicação do aplicativo, ele tem informações atualizadas sem que o usuário precise aguardar a atualização.

Como visto acima, o sistema watchOS ativa o aplicativo usando uma ou mais tarefas, das quais ele tem um pool muito limitado disponível:

[![](background-tasks-images/update02.png "O sistema watchOS ativa o aplicativo usando uma ou mais tarefas")](background-tasks-images/update02.png#lightbox)

A Apple sugere aproveitar ao máximo essa tarefa (já que é um recurso limitado para o aplicativo), mantendo-a até que o aplicativo tenha concluído o processo de atualização.

O sistema entrega essas tarefas chamando o novo `HandleBackgroundTasks` método `WKExtensionDelegate` do delegado. Por exemplo:

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

Quando o aplicativo concluir a tarefa determinada, ele a retornará ao sistema marcando-a como concluída:

[![](background-tasks-images/update03.png "A tarefa retorna ao sistema marcando-a como concluída")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Novas tarefas em segundo plano

o watchOS 3 apresenta várias tarefas em segundo plano que um aplicativo pode usar para atualizar suas informações, garantindo que ele tenha o conteúdo de que o usuário precisa antes de abrir o aplicativo, como:

- **Atualização do aplicativo em segundo plano** – a tarefa [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permite que o aplicativo atualize seu estado em segundo plano. Geralmente, isso incluirá outra tarefa, como baixar novo conteúdo da Internet usando um [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Atualização de instantâneo em segundo plano** – a tarefa [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permite que o aplicativo atualize seu conteúdo e a interface do usuário antes que o sistema assuma um instantâneo que será usado para popular o encaixe.
- **Conectividade de inspeção em segundo plano** – a tarefa [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) é iniciada para o aplicativo quando recebe dados em segundo plano do iPhone emparelhado.
- **Sessão de URL em segundo plano** -a tarefa [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) é iniciada para o aplicativo quando uma transferência em segundo plano requer autorização ou é concluída (com êxito ou com erro).

Essas tarefas serão abordadas em detalhes nas seções abaixo.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

O `WKApplicationRefreshBackgroundTask` é uma tarefa genérica que pode ser agendada para ter o aplicativo ativados em uma data futura:

[![](background-tasks-images/update04.png "Um WKApplicationRefreshBackgroundTask ativados em uma data futura")](background-tasks-images/update04.png#lightbox)

Dentro do tempo de execução da tarefa, o aplicativo pode fazer qualquer tipo de processamento local, como atualizar uma linha do tempo de complicação ou buscar `NSUrlSession`alguns dados necessários com um.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

O sistema enviará um `WKURLSessionRefreshBackgroundTask` quando os dados terminarem de baixar e estiverem prontos para serem processados pelo aplicativo:

[![](background-tasks-images/update05.png "O WKURLSessionRefreshBackgroundTask quando os dados concluíram o download")](background-tasks-images/update05.png#lightbox)

Um aplicativo não é deixado em execução enquanto os dados estão sendo baixados em segundo plano. Em vez disso, o aplicativo agenda a solicitação de dados e, em seguida, é suspenso e o sistema manipula o download dos dados, apenas reativando o aplicativo quando o download é concluído.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

No watchOS 3, a Apple adicionou o Dock, onde os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral na Apple Watch, uma galeria de instantâneos de aplicativo afixados será exibida. O usuário pode passar o dedo para a esquerda ou direita para localizar o aplicativo desejado e, em seguida, tocar no aplicativo para iniciá-lo, substituindo o instantâneo pela interface do aplicativo em execução.

[![](background-tasks-images/update06.png "Substituindo o instantâneo pela interface de aplicativos em execução")](background-tasks-images/update06.png#lightbox)

Periodicamente, o sistema usa instantâneos da interface do usuário do aplicativo ( `WKSnapshotRefreshBackgroundTask`enviando um) e usa esses instantâneos para popular o encaixe. o watchOS dá ao aplicativo a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo seja feito.

Os instantâneos são muito importantes no watchOS 3, já que funcionam como as imagens de versão prévia e de inicialização para o aplicativo. Se o usuário se liquidar em um aplicativo no Dock, ele será expandido para tela inteira, entrará no primeiro plano e começará a ser executado, portanto, é imperativo que o instantâneo esteja atualizado:

[![](background-tasks-images/update07.png "Se o usuário se liquidar em um aplicativo no Dock, ele será expandido para tela inteira")](background-tasks-images/update07.png#lightbox)

Novamente, o sistema emitirá um `WKSnapshotRefreshBackgroundTask` para que o aplicativo possa se preparar (atualizando os dados e a interface do usuário) antes que o instantâneo seja obtido:

[![](background-tasks-images/update08.png "O aplicativo pode se preparar atualizando os dados e a interface do usuário antes que o instantâneo seja tomado")](background-tasks-images/update08.png#lightbox)

Quando o aplicativo marcar a `WKSnapshotRefreshBackgroundTask` conclusão, o sistema tirará automaticamente um instantâneo da interface do usuário do aplicativo.

> [!IMPORTANT]
> É importante sempre agendar um `WKSnapshotRefreshBackgroundTask` depois que o aplicativo receber novos dados e atualizado sua interface do usuário, ou o usuário não verá as informações modificadas.




Além disso, quando o usuário recebe uma notificação do aplicativo e a toca para colocar o aplicativo em primeiro plano, o instantâneo precisa estar atualizado, pois também está agindo como a tela de inicialização:

[![](background-tasks-images/update09.png "O usuário recebe uma notificação do aplicativo e a toca para colocar o aplicativo em primeiro plano")](background-tasks-images/update09.png#lightbox)

Se tiver sido mais de uma hora desde que o usuário interage com um aplicativo watchOS, ele poderá retornar ao seu estado padrão. O estado padrão pode significar diferentes coisas para diferentes aplicativos e, com base no design de um aplicativo, ele pode não ter um estado padrão.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

No watchOS 3, a Apple integrou a conectividade com a API de atualização em segundo `WKWatchConnectivityRefreshBackgroundTask`plano por meio do novo. Usando esse novo recurso, um aplicativo do iPhone pode entregar dados atualizados à sua contraparte do aplicativo Watch, enquanto o aplicativo watchOS está em execução em segundo plano:

[![](background-tasks-images/update10.png "Um aplicativo do iPhone pode entregar dados atualizados à sua contraparte do aplicativo Watch, enquanto o aplicativo watchOS está em execução em segundo plano")](background-tasks-images/update10.png#lightbox)

Iniciar um push de complicação, o contexto do aplicativo, o envio de um arquivo ou a atualização das informações do usuário do aplicativo do iPhone ativará o aplicativo Apple Watch em segundo plano.

Quando o aplicativo Watch for ativados por meio `WKWatchConnectivityRefreshBackgroundTask` de, será necessário usar os métodos de API padrão para receber os dados do aplicativo do iPhone.

[![](background-tasks-images/update11.png "O fluxo de dados WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Verifique se a sessão foi ativada.
2. Monitore a `HasContentPending` nova propriedade, desde que o valor `true`seja, o aplicativo ainda tem dados a serem processados. Como antes, o aplicativo deve manter a tarefa até concluir o processamento de todos os dados.
3. Quando não houver mais dados a serem processados (`HasContentPending = false`), marque a tarefa concluído para retorná-la ao sistema. A falha em fazer isso esgotará o tempo de execução de segundo plano alocado do aplicativo, resultando em um relatório de falhas.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>O ciclo de vida da API de segundo plano

Ao colocar todas as partes da nova API de tarefas em segundo plano, um conjunto típico de interações seria semelhante ao seguinte:

[![](background-tasks-images/update12.png "O ciclo de vida da API de segundo plano")](background-tasks-images/update12.png#lightbox)

1. Primeiro, o aplicativo watchOS agenda uma tarefa em segundo plano para ser ativo como um ponto no futuro.
2. O aplicativo é ativados pelo sistema e enviou uma tarefa.
3. O aplicativo processa a tarefa para concluir qualquer trabalho necessário.
4. Como resultado do processamento da tarefa, o aplicativo pode precisar agendar mais tarefas em segundo plano para concluir mais trabalho no futuro, como baixar mais conteúdo usando um `NSUrlSession`.
5. O aplicativo marca a tarefa como concluída e a retorna para o sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Usando recursos com responsabilidade

É fundamental que um aplicativo watchOS se comporta com responsabilidade nesse ecossistema limitando seu dreno nos recursos compartilhados do sistema.

Dê uma olhada no cenário a seguir:

[![](background-tasks-images/update13.png "Um aplicativo watchOS limita seu dreno nos recursos compartilhados do sistema")](background-tasks-images/update13.png#lightbox)

1. O usuário inicia um aplicativo watchOS às 1:00 PM.
2. O aplicativo agenda uma tarefa para ativar e baixar o novo conteúdo em uma hora às 2:00 PM.
3. Às 1:50, o usuário abre novamente o aplicativo que permite que ele atualize seus dados e a interface do usuário no momento.
4. Em vez de permitir que a tarefa ative o aplicativo novamente em 10 minutos, o aplicativo deve reagendar a tarefa para ser executada uma hora depois às 2:50.

Embora cada aplicativo seja diferente, a Apple sugere encontrar padrões de uso, como os mostrados acima, para ajudar a conservar os recursos do sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementando tarefas em segundo plano

Por exemplo, este documento usará o aplicativo de esportes MonkeySoccer ilegítimo que relata pontuações de futebol para o usuário. 

Veja o seguinte cenário de uso típico:

[![](background-tasks-images/update14.png "O cenário de uso típico")](background-tasks-images/update14.png#lightbox)

A equipe de futebol favorita do usuário está reproduzindo uma grande correspondência de 7:00 às 9:00, então o aplicativo deve esperar que o usuário esteja verificando a pontuação regularmente e decide em um intervalo de atualização de 30 minutos.

1. O usuário abre o aplicativo e agenda uma tarefa para atualização em segundo plano 30 minutos depois. A API em segundo plano permite que apenas um tipo de tarefa em segundo plano seja executado em um determinado momento.
2. O aplicativo recebe a tarefa e atualiza seus dados e a interface do usuário e agenda para outra tarefa em segundo plano 30 minutos mais tarde. É importante que o desenvolvedor seja lembrado de agendar outra tarefa em segundo plano, ou o aplicativo nunca será acordado para obter mais atualizações.
3. Novamente, o aplicativo recebe a tarefa e atualiza seus dados, atualiza sua interface do usuário e agenda outra tarefa em segundo plano 30 minutos depois.
4. O mesmo processo se repete novamente.
5. A última tarefa em segundo plano é recebida e o aplicativo atualiza seus dados e a interface do usuário novamente. Como essa é a pontuação final, ela não é agendada para uma nova atualização em segundo plano. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Agendamento para atualização em segundo plano

Considerando o cenário acima, o aplicativo MonkeySoccer pode usar o seguinte código para agendar uma atualização em segundo plano:

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

Ele cria um novo `NSDate` 30 minutos no futuro quando o aplicativo deseja ser acordado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleBackgroundRefresh` método`SharedExtension` do é usado para solicitar que a tarefa seja agendada.

O sistema retornará um `NSError` se não for possível agendar a tarefa solicitada.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Processando a atualização

Em seguida, dê uma olhada mais de perto na janela de 5 minutos mostrando as etapas necessárias para atualizar a Pontuação:

[![](background-tasks-images/update15.png "A janela de 5 minutos mostrando as etapas necessárias para atualizar a Pontuação")](background-tasks-images/update15.png#lightbox)

1. Às 7:30:02 PM, o aplicativo é ativado pelo sistema e recebeu a tarefa de atualização em segundo plano. Sua primeira prioridade é obter as pontuações mais recentes do servidor. Consulte [agendando um NSUrlSession](#Scheduling-a-NSUrlSession) abaixo.
2. Às 7:30:05 o aplicativo conclui a tarefa original, o sistema coloca o aplicativo em suspensão e continua a baixar os dados solicitados em segundo plano.
3. Quando o sistema conclui o download, ele cria uma nova tarefa para ativar o aplicativo para que ele possa processar as informações baixadas. Consulte [manipulando tarefas em segundo plano](#Handling-Background-Tasks) e [manipulando a conclusão do download](#Handling-the-Download-Completing) abaixo. 
4. O aplicativo salva as informações atualizadas e marca a tarefa como concluída. O desenvolvedor pode ser tentado a atualizar a interface do usuário do aplicativo no momento, no entanto, a Apple sugere o agendamento de uma tarefa de instantâneo para lidar com esse processo. Consulte [agendando uma atualização de instantâneo](#Scheduling-a-Snapshot-Update) abaixo.
5. O aplicativo recebe a tarefa de instantâneo, atualiza sua interface do usuário e marca a tarefa como concluída. Consulte [manipulando uma atualização de instantâneo](#Handling-a-Snapshot-Update) abaixo.

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

Ele configura e cria um novo `NSUrlSession`e, em seguida, usa essa sessão para criar uma nova tarefa de download usando o `CreateDownloadTask` método. Ele chama o `Resume` método da tarefa de download para iniciar a sessão.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Manipulando tarefas em segundo plano

Ao substituir o `HandleBackgroundTasks` método `WKExtensionDelegate`do, o aplicativo pode manipular as tarefas de entrada em segundo plano:

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

O `HandleBackgroundTasks` método percorre todas as tarefas nas quais o sistema enviou o aplicativo (em `backgroundTasks`) pesquisando por um `WKUrlSessionRefreshBackgroundTask`. Se um for encontrado, ele reingressará na sessão e anexará um `NSUrlSessionDownloadDelegate` para lidar com a conclusão do download (consulte [manipulando a conclusão do download](#Handling-the-Download-Completing) abaixo):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Ele mantém um identificador da tarefa até que ela seja concluída adicionando-a a uma coleção:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Todas as tarefas enviadas para o aplicativo precisam ser concluídas, para qualquer tarefa que não esteja sendo manipulada no momento, marque-a como concluída:

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Lidando com a conclusão do download

O aplicativo MonkeySoccer usa o seguinte `NSUrlSessionDownloadDelegate` delegado para lidar com a conclusão do download e processar os dados solicitados:

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

Quando inicializado, ele mantém um identificador para o `ExtensionDelegate` e o `WKRefreshBackgroundTask` que o gerou. Ele substitui o `DidFinishDownloading` método para lidar com a conclusão do download. Em seguida, `CompleteTask` o usa o `ExtensionDelegate` método do para informar a tarefa que ela foi concluída e removê-la da coleção de tarefas pendentes. Consulte [manipulando tarefas em segundo plano](#Handling-Background-Tasks) acima.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Agendando uma atualização de instantâneo

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

Assim como `ScheduleURLUpdateSession` no método acima, ele cria um `NSDate` novo para quando o aplicativo deseja ser acordado e cria um `NSMutableDictionary` para manter os detalhes da tarefa solicitada. O `ScheduleSnapshotRefresh` método`SharedExtension` do é usado para solicitar que a tarefa seja agendada.

O sistema retornará um `NSError` se não for possível agendar a tarefa solicitada.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Manipulando uma atualização de instantâneo

Para manipular a tarefa de instantâneo, `HandleBackgroundTasks` o método (consulte [manipulando tarefas em segundo plano](#Handling-Background-Tasks) acima) é modificado para ser semelhante ao seguinte:

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

O método testa o tipo de tarefa que está sendo processada. Se for `WKSnapshotRefreshBackgroundTask` , ele obtém acesso à tarefa:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

O método atualiza a interface do usuário e, `NSDate` em seguida, cria um para informar ao sistema quando o instantâneo será obsoleto. Ele cria um `NSMutableDictionary` com informações de usuário para descrever o novo instantâneo e marca a tarefa de instantâneo concluída com estas informações:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Além disso, ele também informa à tarefa de instantâneo que o aplicativo não está retornando ao estado padrão (no primeiro parâmetro). Os aplicativos que não têm nenhum conceito de estado padrão sempre devem definir essa propriedade `true`como.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Trabalhando com eficiência

Como visto no exemplo acima da janela de cinco minutos que o aplicativo MonkeySoccer levou para atualizar suas pontuações, trabalhando de maneira eficiente e usando as novas tarefas em segundo plano do watchOS 3, o aplicativo esteve ativo apenas por um total de 15 segundos: 

[![](background-tasks-images/update16.png "O aplicativo esteve ativo somente por um total de 15 segundos")](background-tasks-images/update16.png#lightbox)

Isso reduz o impacto que o aplicativo terá nos recursos de Apple Watch disponíveis e na vida útil da bateria e também permite que o aplicativo funcione melhor com outros aplicativos em execução no relógio.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Como funciona o agendamento

Embora um aplicativo watchOS 3 esteja em primeiro plano, ele é sempre agendado para execução e pode fazer qualquer tipo de processamento necessário, como atualizar dados ou redesenhar sua interface do usuário. Quando o aplicativo passa para o plano de fundo, ele é geralmente suspenso pelo sistema e todas as operações de tempo de execução são interrompidas. 

Enquanto o aplicativo está em segundo plano, ele pode ser direcionado pelo sistema para executar rapidamente uma tarefa específica. Portanto, no watchOS 2, o sistema pode ativar temporariamente um aplicativo em segundo plano para fazer coisas como manipular uma notificação de aparência longa ou atualizar a complicação do aplicativo. No watchOS 3, há várias maneiras novas que um aplicativo pode ser executado em segundo plano.

Enquanto um aplicativo está em segundo plano, o sistema impõe vários limites:

- Apenas alguns segundos para concluir uma determinada tarefa. O sistema leva em consideração não apenas a quantidade de tempo aprovada, mas também a quantidade de energia da CPU que o aplicativo está consumindo para derivar esse limite.
- Qualquer aplicativo que exceda seus limites será eliminado com os seguintes códigos de erro:
  - **CPU** -0xc51bad01
  - **Tempo** -0xc51bad02
- O sistema imporá limites diferentes com base no tipo de tarefa em segundo plano que solicitou que o aplicativo executasse. Por exemplo, `WKApplicationRefreshBackgroundTask` `WKURLSessionRefreshBackgroundTask` as tarefas recebem tempos de execução um pouco mais longos em relação a outros tipos de tarefas em segundo plano.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicações e atualizações de aplicativos

Além das novas tarefas em segundo plano que a Apple adicionou ao watchOS 3, as complicações de um aplicativo watchOS podem afetar como e quando o aplicativo recebe atualizações em segundo plano.

Complicações são elementos visuais pequenos que fornecem informações úteis em um relance. Dependendo do rosto de inspeção selecionado, o usuário tem a capacidade de personalizar um rosto de inspeção com uma ou mais complicação que pode ser fornecida por um aplicativo Watch no watchOS 3.

Se o usuário incluir uma das complicações do aplicativo em seu rosto de inspeção, ele fornecerá ao aplicativo os seguintes benefícios atualizados:

- Faz com que o sistema mantenha o aplicativo em um estado pronto para iniciar, em que ele tenta iniciar o aplicativo em segundo plano, mantém-o na memória e dá tempo extra para atualizar.
- As complicações são garantidas pelo menos 50 atualizações por Push por dia.

O desenvolvedor sempre deve se esforçar para criar complicações atraentes para seus aplicativos para convencer o usuário a adicioná-los à sua face de observação pelos motivos listados acima.

No watchOS 2, as complicações eram a principal maneira que um aplicativo recebeu tempo de execução enquanto estava em segundo plano. No watchOS 3, um aplicativo de complicação ainda será garantido para receber várias atualizações por hora, no entanto, ele pode `WKExtensions` usar para solicitar mais tempo de execução para atualizar suas complicações.

Dê uma olhada no código a seguir usado para atualizar a complicação do aplicativo de iPhone conectado:

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

Ele usa a `RemainingComplicationUserInfoTransfers` propriedade `WCSession` do para ver quantas transferências de alta prioridade o aplicativo saiu para o dia e, em seguida, executa uma ação com base nesse número. Se o aplicativo começar a ser executado com pouca transferência, ele poderá manter o envio de atualizações secundárias e apenas enviar informações quando houver uma alteração significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Agendamento e encaixe

No watchOS 3, a Apple adicionou o Dock, onde os usuários podem fixar seus aplicativos favoritos e acessá-los rapidamente. Quando o usuário pressiona o botão lateral na Apple Watch, uma galeria de instantâneos de aplicativo afixados será exibida. O usuário pode passar o dedo para a esquerda ou direita para localizar o aplicativo desejado e, em seguida, tocar no aplicativo para iniciá-lo, substituindo o instantâneo pela interface do aplicativo em execução.

[![](background-tasks-images/dock01.png "O Dock")](background-tasks-images/dock01.png#lightbox)

Periodicamente, o sistema usa instantâneos da interface do usuário do aplicativo e usa esses instantâneos para preencher os documentos. o watchOS dá ao aplicativo a oportunidade de atualizar seu conteúdo e a interface do usuário antes que esse instantâneo seja feito.

Os aplicativos que foram fixados no Dock podem esperar o seguinte:

- Eles receberão, no mínimo, um valor atualizado por hora. Isso inclui uma tarefa de atualização de aplicativo e uma tarefa de instantâneo.
- O orçamento de atualização é distribuído entre todos os aplicativos no Dock. Portanto, quanto menos aplicativos o usuário tiver fixado, mais atualizações possíveis serão recebidas por cada aplicativo.
- O aplicativo será mantido na memória para que o aplicativo seja retomado rapidamente quando selecionado no Dock.

O último aplicativo executado pelo usuário será considerado o aplicativo _usado mais recentemente_ e ocupará o último slot no Dock. A partir daí, o usuário pode optar por fixá-lo permanentemente no Dock. O usado mais recentemente será tratado como qualquer outro aplicativo favorito que o usuário já tenha fixado no Dock.

> [!IMPORTANT]
> Os aplicativos que só foram adicionados à tela inicial não receberão nenhum agendamento regular. Para receber atualizações regulares de agendamento e em segundo plano, um aplicativo _deve_ ser adicionado ao Dock.

Como mencionado anteriormente neste documento, os instantâneos são muito importantes no watchOS 3, já que eles funcionam como imagens de visualização e inicialização para o aplicativo. Se o usuário se liquidar em um aplicativo no Dock, ele será expandido para tela inteira, entrará no primeiro plano e começará a ser executado, portanto, é imperativo que o instantâneo esteja atualizado.

Pode haver ocasiões em que o sistema decida que precisa de um novo instantâneo da interface do usuário do aplicativo. Nessas situações, a solicitação de instantâneo não será contada em relação ao orçamento de tempo de execução do aplicativo. O seguinte irá disparar uma solicitação de instantâneo do sistema:

- Uma atualização da linha do tempo de complicação.
- Interação do usuário com a notificação de um aplicativo.
- Alternando do primeiro plano para o estado do plano de fundo.
- Após uma hora de ser no estado de segundo plano, o aplicativo pode retornar ao estado padrão.
- Quando o watchOS é inicializado pela primeira vez.

<a name="Best-Practices" />

## <a name="best-practices"></a>Práticas recomendadas 

A Apple sugere as seguintes práticas recomendadas ao trabalhar com tarefas em segundo plano:

- Agendar sempre que o aplicativo precisar ser atualizado. Toda vez que o aplicativo é executado, ele deve reavaliar suas necessidades futuras e ajustar essa agenda conforme necessário.
- Se o sistema enviar uma tarefa de atualização em segundo plano e o aplicativo não exigir uma atualização, adie o trabalho até que uma atualização seja realmente necessária.
- Considere todas as oportunidades de tempo de execução disponíveis para um aplicativo:
  - Encaixe e ativação em primeiro plano.
  - Notificações.
  - Atualizações de complicação.
  - Atualizações em segundo plano.
- Use `ScheduleBackgroundRefresh` para tempo de execução em segundo plano de uso geral, como:
  - Sondando o sistema para obter informações.
  - Agendar `NSURLSessions` futuro para solicitar dados em segundo plano. 
  - Transições de tempo conhecidas.
  - Disparando atualizações de complicação.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Práticas recomendadas de instantâneo

Ao trabalhar com atualizações de instantâneo, a Apple faz as seguintes sugestões:

- Invalidar instantâneos somente quando necessário, por exemplo, quando há uma alteração significativa no conteúdo.
- Evite a invalidação de instantâneo de alta frequência. Por exemplo, um aplicativo de timer não deve atualizar o instantâneo a cada segundo, ele só deve ser feito quando o temporizador termina.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Fluxo de dados do aplicativo

A Apple sugere o seguinte para trabalhar com o fluxo de dados:

[![](background-tasks-images/update17.png "Diagrama de fluxo de dados de aplicativo")](background-tasks-images/update17.png#lightbox)

Um evento externo (como a conectividade de inspeção) ativa o aplicativo. Isso força o aplicativo a atualizar seu modelo de dados (que representa o estado atual dos aplicativos). Como resultado da alteração do modelo de dados, o aplicativo precisará atualizar suas complicações, solicitar um novo instantâneo, possivelmente iniciar um plano `NSURLSession` de fundo para extrair mais dados e agendar mais atualizações em segundo plano.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>O ciclo de vida do aplicativo

Por causa do encaixe e da capacidade de fixar aplicativos favoritos a ele, a Apple pensa que os usuários se moverão entre muito mais aplicativos, com muito mais frequência, em seguida, fizeram com watchOS 2. Como resultado, o aplicativo deve estar pronto para lidar com essa alteração e mover-se entre os Estados de primeiro e segundo plano rapidamente.

A Apple tem as seguintes sugestões:

- Verifique se o aplicativo conclui qualquer tarefa em segundo plano assim que possível na inserção da ativação em primeiro plano.
- Certifique-se de concluir todo o trabalho em primeiro plano antes `NSProcessInfo.PerformExpiringActivity`de entrar no plano de fundo chamando.
- Ao testar um aplicativo no simulador de watchOS, nenhum dos orçamentos de tarefa será aplicado para que um aplicativo possa ser atualizado tanto quanto necessário para testar corretamente um recurso.
- Sempre teste em hardware de Apple Watch real para garantir que o aplicativo não esteja em execução após seus orçamentos antes da publicação no iTunes Connect.
- A Apple sugere manter o Apple Watch no carregador durante o teste e a depuração.
- Certifique-se de que a inicialização a frio e a retomada de um aplicativo sejam exaustivamente testadas.
- Verifique se todas as tarefas do aplicativo estão sendo concluídas.
- Varie o número de aplicativos que são fixados no Dock para testar os melhores e os piores cenários de caso.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos que a Apple fez no watchOS e como eles podem ser usados para manter um aplicativo observado atualizado. Primeiro, ele abordou toda a nova tarefa em segundo plano que a Apple adicionou no watchOS 3. Em seguida, ele abordou o ciclo de vida da API de segundo plano e como implementar tarefas em segundo plano em um aplicativo Xamarin watchOS. Por fim, ele abordou como o agendamento funciona e deu algumas práticas recomendadas.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
