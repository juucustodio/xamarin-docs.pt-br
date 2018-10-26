---
title: Aplicativos de treinamento em Xamarin do watchOS
description: Este artigo aborda os aprimoramentos de Apple tornou-se aos aplicativos de ginástica no watchOS 3 e como implementá-los no Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d755160043191f93247fd09e99f23eb85831fa8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113892"
---
# <a name="watchos-workout-apps-in-xamarin"></a>Aplicativos de treinamento em Xamarin do watchOS

_Este artigo aborda os aprimoramentos de Apple tornou-se aos aplicativos de ginástica no watchOS 3 e como implementá-los no Xamarin._


Novo watchOS 3, ginástica relacionados a aplicativos têm a capacidade de executar em segundo plano sobre o Apple Watch e obter acesso aos dados HealthKit. Seu aplicativo do iOS 10 com base em pai também tem a capacidade de iniciar o aplicativo watchOS 3 com base sem intervenção do usuário.

Os tópicos a seguir serão abordados em detalhes:

## <a name="about-workout-apps"></a>Sobre aplicativos de ginástica

Os usuários de treinamento e adequação a aplicativos podem ser altamente dedicados, dedicar várias horas do dia em relação às suas metas de integridade e de adequação. Como resultado, eles esperam aplicativos responsivos, fácil de usar que coletam e exibem dados com precisão e integração perfeita com a integridade da Apple.

Um aplicativo bem projetado de treinamento ou de adequação a ajuda os usuários a suas atividades para alcançar suas metas de adequação do gráfico. Usando o Apple Watch, treinamento e adequação a aplicativos têm acesso instantâneo a taxa de coração, a detecção de calorias e gravar em atividade.

[![](workout-apps-images/workout01.png "Exemplo de aplicativo de aptidão e treinamento")](workout-apps-images/workout01.png#lightbox)

Novo no watchOS 3, _em execução em segundo plano_ oferece treinamento relacionados a aplicativos a capacidade de executar em segundo plano sobre o Apple Watch e obter acesso aos dados HealthKit.

Este documento apresentará o recurso em execução em segundo plano, abrangem o ciclo de vida do aplicativo de ginástica e mostrar como um aplicativo de ginástica pode contribuir para o usuário _anéis de atividade_ sobre o Apple Watch.

## <a name="about-workout-sessions"></a>Sobre as sessões de treinamento

A essência de cada aplicativo de ginástica é um _sessão de treinamento_ (`HKWorkoutSession`) que o usuário pode iniciar e parar. A API de sessão de treinamento é fácil de implementar e fornece vários benefícios para um aplicativo de ginástica, tais como:

- Movimento e calorias gravar detecção com base no tipo de atividade.
- Contribuição automática para anéis de atividade do usuário.
- Enquanto estiver em uma sessão, o aplicativo será exibido automaticamente sempre que o usuário é ativado no dispositivo (seja por acionar seu pulso ou interagir com o Apple Watch).

## <a name="about-background-running"></a>Sobre a execução do plano de fundo

Como mencionado acima, com o watchOS 3 um aplicativo de ginástica pode ser definido para ser executado em segundo plano. Usar o plano de fundo executando um aplicativo de ginástica pode processar dados de sensores do de Apple Watch durante a execução em segundo plano. Por exemplo, um aplicativo pode continuar a monitorar a taxa de coração do usuário, mesmo que ele não será mais exibido na tela.

Em segundo plano execução também fornece a capacidade de apresentar comentários em tempo real para o usuário a qualquer momento durante uma sessão ativa de treinamento, como enviar um alerta hápticos para informar o usuário de progresso atual.

Além disso, a execução em segundo plano permite que o aplicativo atualizar rapidamente sua Interface do usuário para que o usuário tem os dados mais recentes quando eles olhei rapidamente seu Apple Watch.

Para manter o alto desempenho no Apple Watch, um aplicativo de inspeção usando em execução em segundo plano deve limitar a quantidade de trabalho em segundo plano para conservar a bateria. Se um aplicativo está usando a CPU em excesso no plano de fundo, ele pode obter suspenso pelo watchOS.

### <a name="enabling-background-running"></a>Habilitando em segundo plano em execução

Para habilitar a execução do plano de fundo, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes em do aplicativo do iPhone da extensão de inspeção complementar `Info.plist` arquivo para abri-lo para edição.
2. Alterne para o **origem** exibição: 

    [![](workout-apps-images/plist01.png "A exibição da fonte")](workout-apps-images/plist01.png#lightbox)
3. Adicionar uma nova chave chamada `WKBackgroundModes` e defina o **tipo** para `Array`: 

    [![](workout-apps-images/plist02.png "Adicionar uma nova chave chamada WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Adicionar um novo item à matriz com o **tipo** dos `String` e um valor de `workout-processing`: 

    [![](workout-apps-images/plist03.png "Adicionar um novo item à matriz com o tipo de cadeia de caracteres e um valor de processamento de ginástica")](workout-apps-images/plist03.png#lightbox)
5. Salve as alterações no arquivo.

## <a name="starting-a-workout-session"></a>Iniciar uma sessão de treinamento

Há três etapas principais para iniciar uma sessão de treinamento:

[![](workout-apps-images/workout02.png "As três etapas principais para iniciar uma sessão de treinamento")](workout-apps-images/workout02.png#lightbox)

1. O aplicativo deve solicitar autorização para acessar dados em HealthKit.
2. Crie um objeto de configuração de treinamento para o tipo de treinamento que está sendo iniciado.
3. Crie e inicie uma sessão de treinamento usando a configuração de ginástica recém-criada.

### <a name="requesting-authorization"></a>Solicitando a autorização

Antes de um aplicativo pode acessar os dados do usuário HealthKit, ele deve solicitar e receber autorização do usuário. Dependendo da natureza do aplicativo de ginástica pode fazer os seguintes tipos de solicitações:

- Autorização para gravar os dados:
    - Exercícios físicos
- Autorização para ler dados:
    - Energia consumida
    - distância
    - Taxa de coração  

Antes de um aplicativo pode solicitar autorização, ele precisa ser configurado para acessar o HealthKit.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
2. Role para baixo e marque **habilitar HealthKit**: 

    [![](workout-apps-images/auth01.png "Seleção Habilitar HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Salve as alterações no arquivo.
4. Siga as instruções na [ID do aplicativo explícita e perfil de provisionamento](~/ios/platform/healthkit.md) e [associando a ID do aplicativo e o provisionamento de perfil com seu aplicativo de xamarin. IOS](~/ios/platform/healthkit.md) seções o [Introdução ao HealthKit](~/ios/platform/healthkit.md) artigo para provisionar corretamente o aplicativo.
5. Por fim, use as instruções na [Kit de integridade de programação](~/ios/platform/healthkit.md) e [solicitando permissão do usuário](~/ios/platform/healthkit.md) seções o [Introdução ao HealthKit](~/ios/platform/healthkit.md) a solicitação do artigo autorização para acessar o repositório de dados do usuário HealthKit.

### <a name="setting-the-workout-configuration"></a>Definindo a configuração de ginástica

Sessões de treinamento são criadas usando um objeto de configuração de ginástica (`HKWorkoutConfiguration`) que especifica o tipo de treinamento (como `HKWorkoutActivityType.Running`) e o local de ginástica (como `HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Criar um delegado de sessão de treinamento 

Para manipular os eventos que podem ocorrer durante uma sessão de treinamento, o aplicativo precisará criar uma instância de delegado de sessão de treinamento. Adicione uma nova classe ao projeto e baseá-la fora do `HKWorkoutSessionDelegate` classe. Para obter o exemplo de um tempo de execução ao ar livre, ele se parece com o seguinte:

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

Essa classe cria vários eventos que serão gerados como o estado de alterações da sessão de treinamento (`DidChangeToState`) e, se a sessão de treinamento falhar (`DidFail`). 

### <a name="creating-a-workout-session"></a>Criação de uma sessão de treinamento

Usando a configuração de treinamento e o delegado de sessão de treinamento criado acima para criar uma nova sessão de treinamento e iniciá-lo no repositório de HealthKit padrão do usuário:

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

Se o aplicativo é iniciado para esta sessão de treinamento e o usuário alterna novamente para seu Mostrador do relógio, um pequeno ícone verde "running man" será exibido acima a face:

[![](workout-apps-images/workout03.png "Um pequeno em execução man ícone verde exibido acima a face")](workout-apps-images/workout03.png#lightbox)

Se o usuário toca nesse ícone, eles serão ser levados de volta para o aplicativo.

## <a name="data-collection-and-control"></a>Coleta de dados e controle

Depois que uma sessão de treinamento foi configurada e iniciada, o aplicativo precisará coletar dados sobre a sessão (por exemplo, a taxa de coração do usuário) e controlar o estado da sessão:

[![](workout-apps-images/workout04.png "Diagrama de controle e a coleta de dados")](workout-apps-images/workout04.png#lightbox)

1. **Observando exemplos** -o aplicativo precisará recuperar as informações de HealthKit que será tratado e exibido ao usuário.
2. **Observa eventos** -o aplicativo precisa responder a eventos que são gerados pela HealthKit ou em uma interface de usuário do aplicativo (por exemplo, o usuário pausa o treinamento).
3. **Insira o estado de execução** -a sessão foi iniciada e está em execução no momento.
4. **Insira o estado em pausa** -o usuário pausou a sessão de treinamento atual e reiniciá-lo em uma data posterior. O usuário pode alternar entre os estados em execução e em pausa várias vezes em uma única sessão de treinamento.
5. **Encerrar a sessão de treinamento** – a qualquer momento, o usuário pode encerrar a sessão de treinamento ou pode expirar e terminam por conta própria se fosse uma ginástica limitada (por exemplo, uma execução de duas milhas).

A etapa final é salvar os resultados da sessão de treinamento em armazenamento de dados do usuário HealthKit.

### <a name="observing-healthkit-samples"></a>Observando o HealthKit exemplos

O aplicativo precisará abrir um _consulta de objeto de âncora_ para cada um dos dados HealthKit pontos que ele está interessado, como frequência cardíaca ou energia active gravado. Para cada ponto de dados que está sendo observado, um manipulador de atualização precisa ser criado para capturar novos dados conforme eles são enviados para o aplicativo.

Partir desses pontos de dados, o aplicativo pode se acumular os totais (por exemplo, a distância total de execução) e atualizar sua Interface do usuário conforme necessário. Além disso, o aplicativo pode notificar os usuários quando eles tiverem chegado a um objetivo específico ou a realização, como concluir a próxima parte de uma execução.

Examine o código de exemplo a seguir:

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

Ele cria um predicado para definir a data de início que deseja obter dados para usar o `GetPredicateForSamples` método. Ele cria um conjunto de dispositivos para efetuar pull de informações HealthKit de usando o `GetPredicateForObjectsFromDevices` método, nesse caso, o local Apple Watch (`HKDevice.LocalDevice`). Os dois predicados são combinados em um predicado de composta (`NSCompoundPredicate`) usando o `CreateAndPredicate` método.

Uma nova `HKAnchoredObjectQuery` é criado para o ponto de dados desejado (nesse caso `HKQuantityTypeIdentifier.ActiveEnergyBurned` para o ponto de dados do Active Directory gravado de energia), nenhum limite é imposto na quantidade de dados retornados (`HKSampleQuery.NoLimit`) e um manipulador de atualização estiver definido para lidar com sendo de dados retornado para o aplicativo de HealthKit. 

O manipulador de atualização será chamado sempre que novos dados são entregues para o aplicativo para o ponto de dados específico. Se nenhum erro for retornado, o aplicativo pode com segurança ler os dados, faça quaisquer cálculos necessários e atualizar sua interface do usuário conforme necessário.

O código faz um loop sobre todas as amostras (`HKSample`) retornados em de `addedObjects` de matriz e converte-os em uma amostra de quantidade (`HKQuantitySample`). Em seguida, ele obtém o valor duplo de amostra como uma joule (`HKUnit.Joule`) e acumula-a para o total acumulado de energia ativa gravado para o treinamento e atualiza a Interface do usuário.

### <a name="achieved-goal-notification"></a>Notificação de meta obtida

Como mencionado acima, quando o usuário atinge um objetivo no aplicativo de ginástica (como concluir a primeira parte de uma execução), ele pode enviar comentários hápticos ao usuário por meio do mecanismo de Taptic. O aplicativo também deve atualizar sua interface de usuário neste ponto, já que o usuário provavelmente irá gerar seu pulso para ver o evento que gerou os comentários.

Para reproduzir os comentários hápticos, use o seguinte código:

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Observa eventos

Os eventos são os carimbos de hora que o aplicativo pode usar para realçar determinados pontos durante o treinamento do usuário. Alguns eventos serão criados diretamente pelo aplicativo e salvo para o treinamento e alguns eventos serão criados automaticamente pelo HealthKit.

Para observar os eventos que são criados por HealthKit, o aplicativo substituirá o `DidGenerateEvent` método da `HKWorkoutSessionDelegate`:

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

Apple adicionou os seguintes tipos de evento novo no watchOS 3:

- `HKWorkoutEventType.Lap` -São eventos que interromper o treinamento em partes de distância igual entre eles. Por exemplo, para marcar uma visão sobre uma faixa durante a execução.
- `HKWorkoutEventType.Marker` -São arbitrários pontos de interesse dentro de treinamento. Por exemplo, atingindo um ponto específico na rota de um tempo de execução ao ar livre.

Esses novos tipos podem ser criados pelo aplicativo e armazenados em do treinamento para uso posterior na criação de gráficos e estatísticas.

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

Esse código cria uma nova instância de um evento de marcador (`HKWorkoutEvent`) e salvá-los em uma coleção particular de eventos (que posteriormente serão gravados para a sessão de treinamento) e notifica o usuário do evento por meio de haptics.

### <a name="pausing-and-resuming-workouts"></a>Pausando e retomando exercícios físicos

Em qualquer ponto em uma sessão de treinamento, o usuário pode pausar a ginástica temporariamente e retomá-lo em um momento posterior. Por exemplo, eles podem pausar um tempo de execução interno para fazer uma chamada importante e retomar a execução após a chamada for concluída.

Interface do usuário do aplicativo deve fornecer uma maneira de pausar e retomar o treinamento (chamando o HealthKit), de modo que a Apple Watch pode conservar espaço de dados e de alimentação enquanto o usuário suspendeu sua atividade. Além disso, o aplicativo deve ignorar quaisquer novos pontos de dados que podem ser recebidos quando a sessão de treinamento está em um estado pausado.

HealthKit responderá para pausar e retomar chamadas por meio da geração de eventos de pausar e retomar. Enquanto a sessão de treinamento está em pausa, sem dados ou novos eventos serão enviados para o aplicativo por HealthKit até que a sessão seja retomada.

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

Os eventos de pausar e retomar que serão gerados a partir HealthKit podem ser manipulados, substituindo o `DidGenerateEvent` método da `HKWorkoutSessionDelegate`:

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

Novidade para watchOS 3, são o movimento em pausa (`HKWorkoutEventType.MotionPaused`) e retomada de movimento (`HKWorkoutEventType.MotionResumed`) eventos. Esses eventos são gerados automaticamente pelo HealthKit durante um treinamento em execução quando o usuário iniciar e parar de se mover.

Quando o aplicativo recebe um evento de movimento em pausa, ele deve interromper a coleta de dados até que o usuário retoma o movimento e o evento retoma de movimento é recebido. Aplicativo do aplicativo não deve pausar a sessão de treinamento em resposta a um evento de movimento em pausa.

> [!IMPORTANT]
> Somente há suporte para os eventos de movimento em pausa e retomada de movimento para o tipo de atividade RunningWorkout (`HKWorkoutActivityType.Running`).

Novamente, esses eventos podem ser manipulados, substituindo o `DidGenerateEvent` método da `HKWorkoutSessionDelegate`:

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

## <a name="ending-and-saving-the-workout-session"></a>Encerrando e salvar a sessão de treinamento

Quando o usuário tiver concluído seu treinamento, o aplicativo precisará encerrar a sessão de treinamento atual e salvá-lo ao banco de dados HealthKit. Exercícios físicos salvos HealthKit serão automaticamente exibidos na lista de atividades de treinamento.

Novo para o iOS 10, isso inclui a lista de lista de atividades de treinamento no iPhone do usuário também. Portanto, mesmo que a Apple Watch não esteja perto, verá o treinamento no telefone.

Exercícios que incluem exemplos de energia atualizará o anel de mover do usuário no aplicativo de atividades para que o 3º aplicativos de terceiros agora podem contribuir para metas de movimentação diárias do usuário.

As etapas a seguir são necessários para terminar e salvar uma sessão de treinamento:

[![](workout-apps-images/workout05.png "Encerrando e salvar o diagrama de sessão de treinamento")](workout-apps-images/workout05.png#lightbox)

1. Primeiro, o aplicativo precisará encerrar a sessão de treinamento.
2. A sessão de treinamento é salvo HealthKit.
3. Adicione todos os exemplos (por exemplo, energia consumida ou distância) para a sessão de treinamento salvo.

### <a name="ending-the-session"></a>Encerrando a sessão

Para encerrar a sessão de treinamento, chame o `EndWorkoutSession` método da `HKHealthStore` passando o `HKWorkoutSession`:

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

Isso redefinirá os sensores de dispositivos para o modo normal. Quando o HealthKit termina terminando o treinamento, ele receberá um retorno de chamada para o `DidChangeToState` método da `HKWorkoutSessionDelegate`:

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

Depois que o aplicativo encerrou a sessão de treinamento, será necessário criar uma ginástica (`HKWorkout`) e salve-o (junto com eventos de um) para o armazenamento de dados HealthKit (`HKHealthStore`):

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

Esse código cria a exigir a quantidade total de energia consumida e a distância para o treinamento como `HKQuantity` objetos. Um dicionário de metadados que definem o treinamento é criado e o local do treinamento é especificado:

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Uma nova `HKWorkout` objeto é criado com o mesmo `HKWorkoutActivityType` como o `HKWorkoutSession`, as datas inicial e finais, a lista de eventos (que está sendo acumulados de seções acima), a energia consumida, total de distância e dicionário de metadados. Esse objeto é salvo de Store de integridade e quaisquer erros manipulados.  

### <a name="adding-samples"></a>Adição de amostras

Quando o aplicativo salva um conjunto de amostras para um treinamento, o HealthKit gera uma conexão entre as amostras e o treinamento em si para que o aplicativo possa consultar HealthKit em uma data posterior para todos os exemplos associados a uma determinado ginástica. Usando essas informações, o aplicativo pode gerar gráficos dos dados de treinamento e plotá-lo em relação a uma linha do tempo de treinamento.

Para um aplicativo contribuir com a mover anel atividade do aplicativo, ele deverá incluir exemplos de energia com o treinamento salvo. Além disso, os totais de distância e energia devem coincidir com a soma de qualquer exemplo que o aplicativo associa um treinamento salvo.

Para adicionar exemplos para um treinamento salvo, faça o seguinte:

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

Opcionalmente, o aplicativo pode calcular e criar um subconjunto menor de amostras ou uma amostra de megabytes (abrangendo todo o intervalo da ginástica) que, em seguida, obtém associada o treinamento salvo.

## <a name="workouts-and-ios-10"></a>Exercícios físicos e o iOS 10

Todos os aplicativos de ginástica watchOS 3 tem um aplicativo-pai iOS 10 baseada em treinamento e, novo no iOS 10, este aplicativo iOS pode ser usado para iniciar um treinamento que colocar o Apple Watch no modo de treinamento (sem a intervenção do usuário) e executar o aplicativo watchOS no modo de execução do plano de fundo (consulte [sobre o plano de fundo em execução](#About-Background-Running) acima para obter mais detalhes).

Enquanto o aplicativo watchOS é executado, ele pode usar WatchConnectivity para mensagens e comunicação com o aplicativo iOS pai.

Dar uma olhada em como esse processo funciona:

[![](workout-apps-images/workout06.png "diagrama de comunicação do Apple Watch e iPhone")](workout-apps-images/workout06.png#lightbox)

1. O aplicativo de iPhone cria um `HKWorkoutConfiguration` do objeto e define o tipo de treinamento e o local.
2. O `HKWorkoutConfiguration` o objeto é enviado a versão do aplicativo do Apple Watch e, se ele ainda não estiver sendo executado, ele é iniciado pelo sistema.
3. Usando o que for passado na configuração de treinamento, o aplicativo watchOS 3 inicia uma nova sessão de treinamento (`HKWorkoutSession`).

> [!IMPORTANT]
> Para o aplicativo de iPhone pai iniciar uma ginástica na Apple Watch, o aplicativo watchOS 3 deve ter habilitado executando o plano de fundo. Consulte [habilitando em execução em segundo plano](#Enabling-Background-Running) acima para obter mais detalhes.

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

Esse código garante que a versão do aplicativo do watchOS está instalado e a versão para iPhone pode se conectar a ele pela primeira vez:

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

Em seguida, ele cria um `HKWorkoutConfiguration` como de costume e usa o `StartWatchApp` método da `HKHealthStore` para enviá-lo para o Apple Watch e iniciar o aplicativo e a sessão de treinamento.

E no aplicativo de inspeção do sistema operacional, use o seguinte código no `WKExtensionDelegate`:

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

Ele usa o `HKWorkoutConfiguration` e cria um novo `HKWorkoutSession` e anexa a uma instância de custom `HKWorkoutSessionDelegate`. A sessão de treinamento é iniciada em relação HealthKit Health Store do usuário.

## <a name="bringing-all-the-pieces-together"></a>Reunindo todas as peças

Levando todas as informações apresentadas neste documento, um aplicativo de ginástica com base watchOS 3 e seu aplicativo de ginástica baseados em iOS 10 pai podem incluir as seguintes partes:

1. **iOS 10 `ViewController.cs`**  -manipula a partir de uma sessão de inspeção de conectividade e um treinamento sobre o Apple Watch.
2. **watchOS 3 `ExtensionDelegate.cs`**  -lida com a versão do aplicativo ginástica do watchOS 3.
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -um personalizado `HKWorkoutSessionDelegate` para manipular eventos para o treinamento.

> [!IMPORTANT]
> O código mostrado nas seções a seguir inclui apenas as partes necessárias para implementar os recursos novos e aprimorados fornecidos aos aplicativos de ginástica no watchOS 3. Todo código de suporte e o código para apresentar e atualizar a interface do usuário não está incluídos, mas podem ser criadas facilmente, seguindo nossas outras documentações do watchOS.<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

O `ViewController.cs` arquivo na versão iOS 10 pai do aplicativo de ginástica incluiria o código a seguir:

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

O `ExtensionDelegate.cs` arquivo na versão do aplicativo de ginástica watchOS 3 incluiria o código a seguir:

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

O `OutdoorRunDelegate.cs` arquivo na versão do aplicativo de ginástica watchOS 3 incluiria o código a seguir:

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

A Apple sugere usando as seguintes práticas recomendadas ao projetar e implementar aplicativos de ginástica no watchOS 3 e no iOS 10:

- Certifique-se de que o aplicativo de ginástica watchOS 3 ainda está funcional até mesmo quando não for possível se conectar ao iPhone e a versão do aplicativo do iOS 10.
- Use o HealthKit distância quando GPS não estiver disponível, pois ele é capaz de gerar as amostras de distância sem GPS.
- Permitir que o usuário iniciar o treinamento do iPhone ou o Apple Watch.
- Permitir que o aplicativo exibir os exercícios de outras fontes (como outros aplicativos de terceiros 3ª) em seus modos de exibição de dados históricos.
- Certifique-se de que o aplicativo faz não exibição excluída exercícios físicos nos dados históricos.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos de Apple tornou-se aos aplicativos de ginástica no watchOS 3 e como implementá-los no Xamarin.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Introdução ao HealthKit](~/ios/platform/healthkit.md)
