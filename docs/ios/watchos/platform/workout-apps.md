---
title: Aplicativos de treinamento do watchOS no Xamarin
description: Este artigo aborda os aprimoramentos que a Apple fez para testar aplicativos no watchOS 3 e como implementá-los no Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f5a2b17491b026e08abf2262a998576cbb4356c5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767826"
---
# <a name="watchos-workout-apps-in-xamarin"></a>Aplicativos de treinamento do watchOS no Xamarin

_Este artigo aborda os aprimoramentos que a Apple fez para testar aplicativos no watchOS 3 e como implementá-los no Xamarin._

Novidade no watchOS 3, os aplicativos relacionados ao treinamento têm a capacidade de executar em segundo plano no Apple Watch e obter acesso aos dados do HealthKit. Seu aplicativo pai baseado em iOS 10 também tem a capacidade de iniciar o aplicativo baseado no watchOS 3 sem intervenção do usuário.

Os tópicos a seguir serão abordados em detalhes:

## <a name="about-workout-apps"></a>Sobre os aplicativos de treinamento

Os usuários de aplicativos de ADEQÜAÇÃO e treinamento podem ser altamente dedicados, o que pode levar várias horas do dia até suas metas de saúde e adequação. Como resultado, eles esperam aplicativos responsivos e fáceis de usar que coletam e exibem dados com precisão e integram-se perfeitamente com a integridade da Apple.

Um aplicativo bem projetado de ADEQÜAÇÃO ou treinamento ajuda os usuários a revisar suas atividades para alcançar suas metas de ADEQÜAÇÃO. Usando os Apple Watch, os aplicativos de adequação e treinamento têm acesso instantâneo à taxa de coração, gravação de calorias e detecção de atividades.

[![](workout-apps-images/workout01.png "Exemplo de aplicativo de adequação e treinamento")](workout-apps-images/workout01.png#lightbox)

Novo no watchOS 3, a _execução em segundo plano_ oferece aos aplicativos relacionados ao treinamento a capacidade de executar em segundo plano no Apple Watch e obter acesso aos dados do HealthKit.

Este documento apresentará o recurso de execução em segundo plano, abordará o ciclo de vida do aplicativo de treinamento e mostrará como um aplicativo de treinamento pode contribuir para os _anéis de atividade_ do usuário na Apple Watch.

## <a name="about-workout-sessions"></a>Sobre as sessões de treinamento

O coração de cada aplicativo de treinamento é uma sessão de`HKWorkoutSession` _treinamento_ () que o usuário pode iniciar e parar. A API da sessão de treinamento é fácil de implementar e fornece vários benefícios para um aplicativo de treinamento, como:

- Detecção de gravação de movimento e calorias com base no tipo de atividade.
- Contribuição automática para os anéis de atividade do usuário.
- Enquanto estiver em uma sessão, o aplicativo será exibido automaticamente sempre que o usuário ativar o dispositivo (seja levantando o pulso ou interagindo com o Apple Watch).

## <a name="about-background-running"></a>Sobre a execução em segundo plano

Conforme mencionado acima, com o watchOS 3, um aplicativo de treinamento pode ser definido para ser executado em segundo plano. Usar o plano de fundo executando um aplicativo de treinamento pode processar dados dos sensores do Apple Watch durante a execução em segundo plano. Por exemplo, um aplicativo pode continuar a monitorar a taxa de coração do usuário, mesmo que ele não seja mais exibido na tela.

A execução em segundo plano também fornece a capacidade de apresentar comentários ao vivo para o usuário a qualquer momento durante uma sessão de ginástica ativa, como enviar um alerta do Haptic para informar o usuário sobre seu progresso atual.

Além disso, a execução em segundo plano permite que o aplicativo atualize rapidamente sua interface do usuário para que o usuário tenha os dados mais recentes ao rapidamente resumir seu Apple Watch.

Para manter o alto desempenho no Apple Watch, um aplicativo de inspeção usando a execução em segundo plano deve limitar a quantidade de trabalho em segundo plano para conservar bateria. Se um aplicativo estiver usando uma CPU excessiva enquanto estiver em segundo plano, ele poderá ser suspenso por watchOS.

### <a name="enabling-background-running"></a>Habilitando a execução em segundo plano

Para habilitar a execução em segundo plano, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes no arquivo do aplicativo do `Info.plist` iPhone complementar da extensão de inspeção para abri-lo para edição.
2. Alterne para a exibição de **origem** : 

    [![](workout-apps-images/plist01.png "A exibição da fonte")](workout-apps-images/plist01.png#lightbox)
3. Adicione uma nova chave chamada `WKBackgroundModes` e defina o **tipo** como `Array`: 

    [![](workout-apps-images/plist02.png "Adicione uma nova chave chamada WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Adicione um novo item à matriz com o **tipo** de `String` e um valor de `workout-processing`: 

    [![](workout-apps-images/plist03.png "Adicionar um novo item à matriz com o tipo de cadeia de caracteres e um valor de treinamento-processamento")](workout-apps-images/plist03.png#lightbox)
5. Salve as alterações no arquivo.

## <a name="starting-a-workout-session"></a>Iniciando uma sessão de treinamento

Há três etapas principais para iniciar uma sessão de treinamento:

[![](workout-apps-images/workout02.png "As três etapas principais para iniciar uma sessão de treinamento")](workout-apps-images/workout02.png#lightbox)

1. O aplicativo deve solicitar autorização para acessar dados no HealthKit.
2. Crie um objeto de configuração de treinamento para o tipo de treinamento que está sendo iniciado.
3. Crie e inicie uma sessão de treinamento usando a configuração de treinamento recém-criada.

### <a name="requesting-authorization"></a>Solicitando autorização

Antes que um aplicativo possa acessar os dados do HealthKit do usuário, ele deve solicitar e receber autorização do usuário. Dependendo da natureza do aplicativo de treinamento, ele pode fazer os seguintes tipos de solicitações:

- Autorização para gravar dados:
  - Treinamentos
- Autorização para ler dados:
  - Energia consumida
  - Alcance
  - Taxa de coração  

Antes que um aplicativo possa solicitar autorização, ele precisa ser configurado para acessar o HealthKit.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
2. Role até a parte inferior e marque **habilitar HealthKit**: 

    [![](workout-apps-images/auth01.png "Marque Habilitar HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Salve as alterações no arquivo.
4. Siga as instruções na [ID explícita do aplicativo e no perfil de provisionamento](~/ios/platform/healthkit.md) e [associe a ID do aplicativo e o perfil de provisionamento com suas seções do aplicativo Xamarin. Ios](~/ios/platform/healthkit.md) do artigo [introdução ao HealthKit](~/ios/platform/healthkit.md) para provisionar corretamente o aplicativo.
5. Por fim, use as instruções nas seções [Kit de integridade de programação](~/ios/platform/healthkit.md) e solicitação [de permissão das](~/ios/platform/healthkit.md) sessões de usuário do artigo [introdução ao HealthKit](~/ios/platform/healthkit.md) para solicitar autorização para acessar o armazenamento de dataHealthKit do usuário.

### <a name="setting-the-workout-configuration"></a>Definindo a configuração de ginástica

As sessões de treinamento são criadas usando um objeto de`HKWorkoutConfiguration`configuração de treinamento () que especifica o tipo `HKWorkoutActivityType.Running`de ginástica (como) e o local `HKWorkoutSessionLocationType.Outdoor`de treinamento (como):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Criando um delegado de sessão de treinamento 

Para manipular os eventos que podem ocorrer durante uma sessão de treinamento, o aplicativo precisará criar uma instância delegada de sessão de treinamento. Adicione uma nova classe ao projeto e baseie- `HKWorkoutSessionDelegate` a na classe. Para o exemplo de uma execução no ar, ele pode ser semelhante ao seguinte:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }
    #endregion
  }
}
```

Essa classe cria vários eventos que serão gerados como o estado da sessão de ginástica é alterado (`DidChangeToState`) e se a sessão de treinamento falhar`DidFail`(). 

### <a name="creating-a-workout-session"></a>Criando uma sessão de treinamento

Usando a configuração do treinamento e o delegado da sessão de treinamento criados acima para criar uma nova sessão de treinamento e iniciá-lo no repositório HealthKit padrão do usuário:

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
  // Create a workout configuration
  var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
  };

  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (configuration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

Se o aplicativo iniciar essa sessão de treinamento e o usuário voltar à sua face de inspeção, um pequeno ícone verde de "homem em execução" será exibido acima do rosto:

[![](workout-apps-images/workout03.png "Um pequeno ícone de homem em verde exibido acima do rosto")](workout-apps-images/workout03.png#lightbox)

Se o usuário tocar nesse ícone, ele será levado de volta para o aplicativo.

## <a name="data-collection-and-control"></a>Coleta e controle de dados

Depois que uma sessão de treinamento tiver sido configurada e iniciada, o aplicativo precisará coletar dados sobre a sessão (como a taxa de coração do usuário) e controlar o estado da sessão:

[![](workout-apps-images/workout04.png "Diagrama de controle e coleta de dados")](workout-apps-images/workout04.png#lightbox)

1. **Observando amostras** – o aplicativo precisará recuperar informações do HealthKit que serão aplicadas e exibidas ao usuário.
2. **Observando eventos** – o aplicativo precisará responder aos eventos gerados pelo HealthKit ou da interface do usuário do aplicativo (como o usuário fazendo o treinamento).
3. **Entrar no estado de execução** -a sessão foi iniciada e está em execução no momento.
4. **Entrar no estado de pausa** – o usuário pausou a sessão de treinamento atual e pode reiniciá-la em uma data posterior. O usuário pode alternar entre os Estados em execução e em pausa várias vezes em uma única sessão de treinamento.
5. **Encerrar sessão de treinamento** – a qualquer momento, o usuário pode encerrar a sessão de treinamento ou ele pode expirar e terminar por conta própria se fosse um treinamento limitado (como uma execução de duas milhas).

A etapa final é salvar os resultados da sessão de treinamento no repositório de armazenamento HealthKit do usuário.

### <a name="observing-healthkit-samples"></a>Observando exemplos de HealthKit

O aplicativo precisará abrir uma _consulta de objeto de âncora_ para cada um dos pontos de dados do HealthKit em que está interessado, como a taxa de coração ou a energia ativa consumida. Para cada ponto de dados sendo observado, um manipulador de atualização precisará ser criado para capturar novos dados conforme eles são enviados ao aplicativo.

A partir desses pontos de dados, o aplicativo pode acumular totais (como a distância de execução total) e atualizar a interface do usuário conforme necessário. Além disso, o aplicativo pode notificar os usuários quando eles atingiram uma meta ou uma realização específica, como a conclusão da próxima quilometragem de uma execução.

Veja o seguinte código de exemplo:

```csharp
private void ObserveHealthKitSamples ()
{
  // Get the starting date of the required samples
  var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

  // Get data from the local device
  var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
  var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

  // Assemble compound predicate
  var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

  // Get ActiveEnergyBurned
  var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
    // Valid?
    if (error == null) {
      // Yes, process all returned samples
      foreach (HKSample sample in addedObjects) {
        var quantitySample = sample as HKQuantitySample;
        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
      }
      
      // Update User Interface
      ...
    }
  });

  // Start Query
  HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                        
}
```

Ele cria um predicado para definir a data de início para a qual deseja obter dados `GetPredicateForSamples` usando o método. Ele cria um conjunto de dispositivos para efetuar pull de informações de `GetPredicateForObjectsFromDevices` HealthKit usando o método, neste caso, somente Apple Watch`HKDevice.LocalDevice`local (). Os dois predicados são combinados em um predicado composto`NSCompoundPredicate`( `CreateAndPredicate` ) usando o método.

Um novo `HKAnchoredObjectQuery` é criado para o ponto de dados desejado (nesse caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` , para o ponto de dados de energia consumido ativo), nenhum limite é imposto sobre a`HKSampleQuery.NoLimit`quantidade de dados retornados () e um manipulador de atualização é definido para lidar com os dados que estão sendo retornados para o aplicativo de HealthKit. 

O manipulador de atualização será chamado sempre que novos dados forem entregues ao aplicativo para o ponto de dados fornecido. Se nenhum erro for retornado, o aplicativo poderá ler os dados com segurança, fazer quaisquer cálculos necessários e atualizar sua interface do usuário conforme necessário.

O código percorre todos os exemplos (`HKSample`) retornados `addedObjects` na matriz e os converte em um exemplo de quantidade (`HKQuantitySample`). Em seguida, ele obtém o valor Double do exemplo como um Joule`HKUnit.Joule`() e o acumula no total em execução da energia ativa gravada para o treinamento e atualiza a interface do usuário.

### <a name="achieved-goal-notification"></a>Notificação de meta Obtida

Conforme mencionado acima, quando o usuário alcança uma meta no aplicativo de treinamento (como concluir a primeira quilometragem de uma execução), ele pode enviar comentários Haptic para o usuário por meio do Taptic Engine. O aplicativo também deve atualizar sua interface do usuário neste ponto, já que o usuário vai mais do que provavelmente acionar o pulso para ver o evento que solicitou os comentários.

Para reproduzir os comentários do Haptic, use o seguinte código:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Observando eventos

Os eventos são carimbos de data/hora que o aplicativo pode usar para realçar determinados pontos durante o treinamento do usuário. Alguns eventos serão criados diretamente pelo aplicativo e salvos no treinamento, e alguns eventos serão criados automaticamente pelo HealthKit.

Para observar os eventos que são criados pelo HealthKit, o aplicativo substituirá `DidGenerateEvent` o método `HKWorkoutSessionDelegate`do:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Save HealthKit generated event
  WorkoutEvents.Add (@event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Lap:
    break;
  case HKWorkoutEventType.Marker:
    break;
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

A Apple adicionou os seguintes novos tipos de evento no watchOS 3:

- `HKWorkoutEventType.Lap`-São para eventos que interrompem o treinamento em partes de distância igual. Por exemplo, para marcar um colo em uma faixa durante a execução.
- `HKWorkoutEventType.Marker`-São para pontos de interesse arbitrários no treinamento. Por exemplo, atingir um ponto específico na rota de uma execução ao ar.

Esses novos tipos podem ser criados pelo aplicativo e armazenados no treinamento para uso posterior na criação de gráficos e estatísticas.

Para criar um evento de marcador, faça o seguinte:

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
  // Create and save marker event
  var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
  WorkoutEvents.Add (markerEvent);

  // Notify user
  NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Esse código cria uma nova instância de um evento de marcador`HKWorkoutEvent`() e salva-a em uma coleção particular de eventos (que posteriormente será gravada na sessão de treinamento) e notifica o usuário do evento por meio de haptics.

### <a name="pausing-and-resuming-workouts"></a>Pausando e retomando exercícios

A qualquer momento em uma sessão de treinamento, o usuário pode temporariamente pausar o treinamento e retomá-lo posteriormente. Por exemplo, eles podem pausar uma execução de interno para fazer uma chamada importante e retomar a execução após a conclusão da chamada.

A interface do usuário do aplicativo deve fornecer uma maneira de pausar e retomar o treinamento (chamando HealthKit) para que o Apple Watch possa conservar o espaço de energia e dados enquanto o usuário suspendeu sua atividade. Além disso, o aplicativo deve ignorar quaisquer novos pontos de dados que possam ser recebidos quando a sessão de treinamento estiver em um estado de pausa.

HealthKit responderá a pausar e retomar chamadas gerando eventos Pause e resume. Enquanto a sessão de treinamento está em pausa, nenhum novo evento ou dado será enviado ao aplicativo por HealthKit até que a sessão seja retomada.

Use o código a seguir para pausar e retomar uma sessão de treinamento:

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
  // Pause the current workout
  HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
  // Pause the current workout
  HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Os eventos Pause e resume que serão gerados a partir de HealthKit podem ser tratados substituindo `DidGenerateEvent` o método `HKWorkoutSessionDelegate`do:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);

  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

### <a name="motion-events"></a>Eventos de movimento

Além de ser novo no watchOS 3, os eventos Motion Paused (`HKWorkoutEventType.MotionPaused`) e Motion retomated (`HKWorkoutEventType.MotionResumed`). Esses eventos são gerados automaticamente pelo HealthKit durante um treinamento em execução quando o usuário inicia e para a movimentação.

Quando o aplicativo recebe um evento de movimento em pausa, ele deve parar de coletar dados até que o usuário retome o movimento e o evento de currículos de movimento seja recebido. O aplicativo não deve pausar a sessão de treinamento em resposta a um evento de movimento em pausa.

> [!IMPORTANT]
> Os eventos de movimento em pausa e retomada de movimento têm suporte apenas para o`HKWorkoutActivityType.Running`tipo de atividade RunningWorkout ().

Novamente, esses eventos podem ser tratados substituindo o `DidGenerateEvent` método `HKWorkoutSessionDelegate`do:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Terminando e salvando a sessão de treinamento

Quando o usuário concluir o treinamento, o aplicativo precisará encerrar a sessão de treinamento atual e salvá-la no banco de dados HealthKit. Os exercícios salvos em HealthKit serão exibidos automaticamente na lista atividade de treinamento.

Novo no iOS 10, isso inclui a lista de atividades de treinamento no iPhone do usuário também. Portanto, mesmo que o Apple Watch não esteja próximo, o treinamento será apresentado no telefone.

Os exercícios que incluem exemplos de energia atualizarão o anel de movimentação do usuário no aplicativo atividades, de modo que os aplicativos de terceiros agora possam contribuir para os objetivos de movimentação diários do usuário.

As etapas a seguir são necessárias para encerrar e salvar uma sessão de treinamento:

[![](workout-apps-images/workout05.png "Como terminar e salvar o diagrama de sessão de treinamento")](workout-apps-images/workout05.png#lightbox)

1. Primeiro, o aplicativo precisará encerrar a sessão de treinamento.
2. A sessão de treinamento é salva em HealthKit.
3. Adicione quaisquer amostras (como energia consumida ou distância) à sessão de ginástica salva.

### <a name="ending-the-session"></a>Encerrando a sessão

Para encerrar a sessão de treinamento, chame `EndWorkoutSession` o método `HKHealthStore` de passagem no `HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
  // End the current workout session
  HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Isso redefinirá os sensores de dispositivos para seu modo normal. Quando o HealthKit terminar de concluir o treinamento, ele receberá um retorno `DidChangeToState` de chamada para `HKWorkoutSessionDelegate`o método do:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
  // Take action based on the change in state
  switch (toState) {
  ...
  case HKWorkoutSessionState.Ended:
    StopObservingHealthKitSamples ();
    RaiseEnded ();
    break;
  }

}
```

### <a name="saving-the-session"></a>Salvando a sessão

Depois que o aplicativo terminar a sessão de treinamento, ele precisará criar um treinamento (`HKWorkout`) e salvá-lo (junto com os eventos) para o repositório de dados`HKHealthStore`HealthKit ():

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
  // Build required workout quantities 
  var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
  var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

  // Create any required metadata
  var metadata = new NSMutableDictionary ();
  metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

  // Create workout
  var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                  WorkoutSession.StartDate, 
                                  NSDate.Now, 
                                  WorkoutEvents.ToArray (), 
                                  energyBurned, 
                                  distance, 
                                  metadata);

  // Save to HealthKit
  HealthStore.SaveObject (workout, (successful, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (successful) {

      }
    } else {
      // Report error
    }
  });

}
```

Esse código cria a quantidade total de energia consumida e distância para o treinamento `HKQuantity` como objetos. Um dicionário de metadados que define o treinamento é criado e o local do treinamento é especificado:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Um novo `HKWorkout` objeto é criado com o mesmo `HKWorkoutActivityType` que `HKWorkoutSession`o, as datas de início e término, a lista de eventos (sendo acumulados das seções acima), a energia consumida, a distância total e o dicionário de metadados. Esse objeto é salvo no repositório de integridade e os erros são tratados.  

### <a name="adding-samples"></a>Adicionando amostras

Quando o aplicativo salva um conjunto de amostras em um treinamento, o HealthKit gera uma conexão entre os exemplos e o próprio treinamento para que o aplicativo possa consultar o HealthKit em uma data posterior para todos os exemplos associados a um treinamento específico. Usando essas informações, o aplicativo pode gerar grafos a partir dos dados de treinamento e plotá-los em uma linha do tempo de treinamento.

Para que um aplicativo contribua para o anel de movimentação do aplicativo de atividade, ele deve incluir amostras de energia com o treinamento salvo. Além disso, os totais de distância e energia devem corresponder à soma de quaisquer exemplos que o aplicativo associa a um treinamento salvo.

Para adicionar amostras a um treinamento salvo, faça o seguinte:

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...

private void SaveWorkoutSamples (HKWorkout workout)
{
  // Add samples to saved workout
  HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (success) {

      }
    } else {
      // Report error
    }
  });
}
```

Opcionalmente, o aplicativo pode calcular e criar um subconjunto menor de amostras ou uma amostra de mega (abrangendo todo o intervalo do treinamento) que, em seguida, é associado ao treinamento salvo.

## <a name="workouts-and-ios-10"></a>Treinamentos e iOS 10

Todos os aplicativos de ginástica watchOS 3 tem um aplicativo-pai iOS 10 baseada em treinamento e, novo no iOS 10, este aplicativo iOS pode ser usado para iniciar um treinamento que colocar o Apple Watch no modo de treinamento (sem a intervenção do usuário) e executar o aplicativo watchOS no modo de execução do plano de fundo (consulte [sobre o plano de fundo em execução](#about-background-running) acima para obter mais detalhes).

Enquanto o aplicativo watchOS está em execução, ele pode usar WatchConnectivity para mensagens e comunicação com o aplicativo do iOS pai.

Veja como esse processo funciona:

[![](workout-apps-images/workout06.png "diagrama de comunicação do iPhone e Apple Watch")](workout-apps-images/workout06.png#lightbox)

1. O aplicativo do iPhone cria `HKWorkoutConfiguration` um objeto e define o tipo e o local do treinamento.
2. O `HKWorkoutConfiguration` objeto é enviado à versão Apple Watch do aplicativo e, se ele ainda não estiver em execução, ele será iniciado pelo sistema.
3. Usando a configuração de treinamento passada, o aplicativo watchOS 3 inicia uma nova sessão de treinamento`HKWorkoutSession`().

> [!IMPORTANT]
> Para que o aplicativo pai do iPhone inicie um treinamento na Apple Watch, o aplicativo watchOS 3 deve ter a execução em segundo plano habilitada. Consulte [habilitando a execução de segundo plano](#enabling-background-running) acima para obter mais detalhes.

Esse processo é muito semelhante ao processo de iniciar uma sessão de treinamento no aplicativo watchOS 3 diretamente. No iPhone, use o seguinte código:

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
  // Can the app communicate with the watchOS version of the app?
  if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
      ActivityType = HKWorkoutActivityType.Running,
      LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Start watch app
    HealthStore.StartWatchApp (configuration, (success, error) => {
      // Handle any errors
      if (error == null) {
        // Was the save successful
        if (success) {
          ...
        }
      } else {
        // Report error
        ...
      }
    });
  }
}
```

Esse código garante que a versão watchOS do aplicativo esteja instalada e a versão do iPhone possa se conectar a ela primeiro:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

Em seguida, ele `HKWorkoutConfiguration` cria um normalmente e usa `StartWatchApp` o método do `HKHealthStore` para enviá-lo para o Apple Watch e iniciar o aplicativo e a sessão de treinamento.

E, no aplicativo do sistema operacional Watch, use o seguinte código `WKExtensionDelegate`no:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

Ele usa `HKWorkoutConfiguration` e cria um novo `HKWorkoutSession` e anexa uma instância do personalizado `HKWorkoutSessionDelegate`. A sessão de treinamento é iniciada no repositório de integridade do HealthKit do usuário.

## <a name="bringing-all-the-pieces-together"></a>Reunindo todas as peças

Ao tirar todas as informações apresentadas neste documento, um aplicativo de treinamento baseado em watchOS 3 e seu aplicativo de treinamento baseado em iOS 10 pai podem incluir as seguintes partes:

1. **Ios 10 `ViewController.cs`**  -lida com o início de uma sessão de conectividade de inspeção e um treinamento na Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -manipula a versão watchOS 3 do aplicativo de treinamento.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -um personalizado `HKWorkoutSessionDelegate` para manipular eventos para o treinamento.

> [!IMPORTANT]
> O código mostrado nas seções a seguir inclui apenas as partes necessárias para implementar os novos recursos aprimorados fornecidos para aplicativos de treinamento no watchOS 3. Todo o código de suporte e o código para apresentar e atualizar a interface do usuário não estão incluídos, mas podem ser facilmente criados seguindo nossa outra documentação do watchOS.<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

O `ViewController.cs` arquivo na versão do aplicativo de treinamento do IOS 10 pai incluiria o seguinte código:

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
  public partial class ViewController : UIViewController
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
    public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
    #endregion

    #region Constructors
    protected ViewController (IntPtr handle) : base (handle)
    {
      // Note: this .ctor should not contain any initialization logic.
    }
    #endregion

    #region Private Methods
    private void InitializeWatchConnectivity ()
    {
      // Is Watch Connectivity supported?
      if (!WCSession.IsSupported) {
        // No, abort
        return;
      }

      // Is the session already active?
      if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
        // No, start session
        ConnectivitySession.ActivateSession ();
      }
    }

    private void StartOutdoorRun ()
    {
      // Can the app communicate with the watchOS version of the app?
      if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
          ActivityType = HKWorkoutActivityType.Running,
          LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
          // Handle any errors
          if (error == null) {
            // Was the save successful
            if (success) {
              ...
            }
          } else {
            // Report error
            ...
          }
        });
      }
    }
    #endregion

    #region Override Methods
    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Start Watch Connectivity
      InitializeWatchConnectivity ();
    }
    #endregion
  }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

O `ExtensionDelegate.cs` arquivo na versão watchOS 3 do aplicativo de treinamento incluiria o seguinte código:

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
    public OutdoorRunDelegate RunDelegate { get; set; }
    #endregion

    #region Constructors
    public ExtensionDelegate ()
    {
      
    }
    #endregion

    #region Private Methods
    private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
    {
      // Create workout session
      // Start workout session
      NSError error = null;
      var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

      // Successful?
      if (error != null) {
        // Report error to user and return
        return;
      }

      // Create workout session delegate and wire-up events
      RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

      RunDelegate.Failed += () => {
        // Handle the session failing
        ...
      };

      RunDelegate.Paused += () => {
        // Handle the session being paused
        ...
      };

      RunDelegate.Running += () => {
        // Handle the session running
        ...
      };

      RunDelegate.Ended += () => {
        // Handle the session ending
        ...
      };
      
      RunDelegate.ReachedMileGoal += (miles) => {
        // Handle the reaching a session goal
        ...
      };

      RunDelegate.HealthKitSamplesUpdated += () => {
        // Update UI as required
        ...
      };

      // Start session
      HealthStore.StartWorkoutSession (workoutSession);
    }

    private void StartOutdoorRun ()
    {
      // Create a workout configuration
      var workoutConfiguration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
      };

      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion

    #region Override Methods
    public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
    {
      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion
  }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

O `OutdoorRunDelegate.cs` arquivo na versão watchOS 3 do aplicativo de treinamento incluiria o seguinte código:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Private Variables
    private HKAnchoredObjectQuery QueryActiveEnergyBurned;
    #endregion

    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    public float MilesRun { get; set; }
    public double ActiveEnergyBurned { get; set;}
    public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
    public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;

    }
    #endregion

    #region Private Methods
    private void ObserveHealthKitSamples ()
    {
      // Get the starting date of the required samples
      var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

      // Get data from the local device
      var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
      var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

      // Assemble compound predicate
      var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

      // Get ActiveEnergyBurned
      QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
          // Yes, process all returned samples
          foreach (HKSample sample in addedObjects) {
            // Accumulate totals
            var quantitySample = sample as HKQuantitySample;
            ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

            // Save samples
            WorkoutSamples.Add (sample);
          }

          // Inform caller
          RaiseHealthKitSamplesUpdated ();
        }
      });

      // Start Query
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                            
    }

    private void StopObservingHealthKitSamples ()
    {
      // Stop query
      HealthStore.StopQuery (QueryActiveEnergyBurned);
    }

    private void ResumeObservingHealthkitSamples ()
    {
      // Resume current queries 
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
    }

    private void NotifyUserOfReachedMileGoal (float miles)
    {
      // Play haptic feedback
      WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

      // Raise event
      RaiseReachedMileGoal (miles);
    }

    private void SaveWorkoutSession ()
    {
      // Build required workout quantities
      var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
      var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

      // Create any required metadata
      var metadata = new NSMutableDictionary ();
      metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

      // Create workout
      var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                      WorkoutSession.StartDate, 
                                      NSDate.Now, 
                                      WorkoutEvents.ToArray (), 
                                      energyBurned, 
                                      distance, 
                                      metadata);

      // Save to HealthKit
      HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (successful) {
            // Add samples to workout
            SaveWorkoutSamples (workout);
          }
        } else {
          // Report error
          ...
        }
      });

    }

    private void SaveWorkoutSamples (HKWorkout workout)
    {
      // Add samples to saved workout
      HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (success) {
            ...
          }
        } else {
          // Report error
          ...
        }
      });
    }
    #endregion

    #region Public Methods
    public void PauseWorkout ()
    {
      // Pause the current workout
      HealthStore.PauseWorkoutSession (WorkoutSession);
    }

    public void ResumeWorkout ()
    {
      // Pause the current workout
      HealthStore.ResumeWorkoutSession (WorkoutSession);
    }

    public void ReachedNextMile ()
    {
      // Create and save marker event
      var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
      WorkoutEvents.Add (markerEvent);

      // Notify user
      NotifyUserOfReachedMileGoal (++MilesRun);
    }

    public void EndOutdoorRun ()
    {
      // End the current workout session
      HealthStore.EndWorkoutSession (WorkoutSession);
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        StopObservingHealthKitSamples ();
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        if (fromState == HKWorkoutSessionState.Paused) {
          ResumeObservingHealthkitSamples ();
        } else {
          ObserveHealthKitSamples ();
        }
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        SaveWorkoutSession ();
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);

      // Save HealthKit generated event
      WorkoutEvents.Add (@event);

      // Take action based on the type of event
      switch (@event.Type) {
      case HKWorkoutEventType.Lap:
        ...
        break;
      case HKWorkoutEventType.Marker:
        ...
        break;
      case HKWorkoutEventType.MotionPaused:
        ...
        break;
      case HKWorkoutEventType.MotionResumed:
        ...
        break;
      case HKWorkoutEventType.Pause:
        ...
        break;
      case HKWorkoutEventType.Resume:
        ...
        break;
      }
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();
    public delegate void OutdoorRunMileGoalDelegate (float miles);

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }

    public event OutdoorRunMileGoalDelegate ReachedMileGoal;
    internal void RaiseReachedMileGoal (float miles)
    {
      if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
    }

    public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
    internal void RaiseHealthKitSamplesUpdated ()
    {
      if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
    }
    #endregion
  }
}
```

## <a name="best-practices"></a>Práticas recomendadas

A Apple sugere o uso das seguintes práticas recomendadas ao projetar e implementar aplicativos de treinamento no watchOS 3 e iOS 10:

- Verifique se o aplicativo de treinamento watchOS 3 ainda está funcional mesmo quando não é possível se conectar ao iPhone e à versão iOS 10 do aplicativo.
- Use a distância HealthKit quando o GPS não estiver disponível, pois é capaz de gerar amostras de distância sem GPS.
- Permitir que o usuário inicie o treinamento do Apple Watch ou do iPhone.
- Permitir que o aplicativo exiba os exercícios de outras fontes (como outros aplicativos de terceiros) em suas exibições de dados históricos.
- Verifique se o aplicativo não exibe os exercícios excluídos em dados históricos.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos que a Apple fez para testar aplicativos no watchOS 3 e como implementá-los no Xamarin.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introdução ao HealthKit](~/ios/platform/healthkit.md)
