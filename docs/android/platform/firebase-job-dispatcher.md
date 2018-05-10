---
title: Dispatcher de trabalho firebase
description: Este guia descreve como agendar o trabalho em segundo plano usando a biblioteca de Firebase trabalho Dispatcher do Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/08/2018
ms.openlocfilehash: a714ac55c3a49b91cb21e3ba1793b9bccd7d1be2
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="firebase-job-dispatcher"></a>Dispatcher de trabalho firebase

_Este guia descreve como agendar o trabalho em segundo plano usando a biblioteca de Firebase trabalho Dispatcher do Google._

![Firebase Dispatcher de trabalho no modo de visualização](~/media/shared/preview.png)

## <a name="overview"></a>Visão geral

Uma das melhores maneiras de manter um aplicativo do Android resposta para o usuário é garantir que o trabalho de execução longa ou complexo é executado em segundo plano. No entanto, é importante que o trabalho de plano de fundo não afetará negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode sondar um site cada três ou quatro minutos para consulta de alterações para um determinado conjunto de dados. Isso parece benigno, mas teria um grande impacto sobre a vida útil da bateria. O aplicativo será repetidamente acorde o dispositivo, elevar a CPU para um estado de energia mais alto, ligue os rádios, verifique as solicitações de rede e, em seguida, processar os resultados. Isso é pior porque o dispositivo não imediatamente será desligar e retornar para o estado ocioso de baixa energia. Trabalho em segundo plano mal agendada pode inadvertidamente manter o dispositivo em um estado com requisitos de energia desnecessária e excesso. Essa atividade aparentemente inocente (um site de sondagem) deixará o dispositivo inutilizável em um período de tempo relativamente curto.

Android fornece as seguintes APIs para ajudar a executar o trabalho em segundo plano, mas em si não são suficientes para o agendamento de trabalho inteligente. 

* **[Tentativa de serviços](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; intenção de serviços são ótimos para executar o trabalho, mas eles fornecem nenhuma maneira de agendar o trabalho.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; essas APIs permitem que apenas o trabalho ser agendada, mas não fornecer nenhuma maneira de realmente executar o trabalho. Além disso, o AlarmManager só permite que restrições de tempo com base, que significa emitir um alarme em uma determinada hora ou após um determinado período de tempo decorrido. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule o é uma API grande que funciona com o sistema operacional para agendar trabalhos. No entanto, só está disponível para esses aplicativos Android que o nível de API 21 destino ou superior. 
* **[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; Android um aplicativo pode configurar destinatários difusão para executar o trabalho em resposta a eventos de todo o sistema ou propósitos. No entanto, receptores de difusão não fornecem nenhum controle sobre quando o trabalho deve ser executado. Também restringirá as alterações no sistema operacional Android quando receptores difusão funcionam ou os tipos de trabalho que eles possam responder às. 

Há dois recursos principais para executar de forma eficiente trabalho em segundo plano (também conhecido como um _trabalho em segundo plano_ ou um _trabalho_):

1. **Inteligente agendamento do trabalho** &ndash; é importante que quando um aplicativo estiver executando um trabalho em segundo plano que ele faz isso como um bom cidadão. Idealmente, o aplicativo deve não exigem que um trabalho seja executada. Em vez disso, o aplicativo deve especificar condições que devem ser atendidas para que quando o trabalho pode executar e, em seguida, agendar que funcionam para ser executado quando as condições forem atendidas. Isso permite que o Android executar o trabalho de maneira inteligente. Por exemplo, solicitações de rede podem ser divididas em lotes para executar todos ao mesmo tempo para fazer uso máximo de sobrecarga envolvida com a rede.
2. **Encapsula o trabalho** &ndash; o código para executar o trabalho de plano de fundo deve ser encapsulado em um componente distinto que pode ser executado independentemente da interface do usuário e seja relativamente fácil reagendar se o trabalho não for concluída por algum motivo.

O Dispatcher de trabalho Firebase é uma biblioteca do Google que fornece uma API fluente para simplificar o trabalho de agendamento em segundo plano. Ele é destinado a ser a substituição para o Gerenciador de nuvem do Google. O Dispatcher de trabalho Firebase consiste das seguintes APIs:

* A `Firebase.JobDispatcher.JobService` é uma classe abstrata que deve ser estendida com a lógica que irá executar o trabalho em segundo plano.
* Um `Firebase.JobDispatcher.JobTrigger` declara quando o trabalho deve ser iniciado. Isso normalmente é expressa como uma janela de tempo, por exemplo, aguarde pelo menos 30 segundos antes de iniciar o trabalho, mas executar o trabalho em 5 minutos.
* A `Firebase.JobDispatcher.RetryStrategy` contém informações sobre o que deve ser feito quando uma tarefa falhar para serem executados corretamente. A estratégia de repetição Especifica quanto tempo a aguardar antes de tentar executar o trabalho novamente. 
* Um `Firebase.JobDispatcher.Constraint` é um valor opcional que descreve uma condição que deve ser atendida antes de executar o trabalho, como o dispositivo estiver em uma rede não monitorada ou carregada.
* O `Firebase.JobDispatcher.Job` é uma API que unifica as APIs anteriores para uma unidade de trabalho que pode ser agendado, o `JobDispatcher`. O `Job.Builder` classe é usada para criar uma instância de um `Job`.
* Um `Firebasee.JobDispatcher.JobDispatcher` usa as APIs de três anteriores para agendar o trabalho com o sistema operacional e para fornecer uma maneira de cancelar trabalhos, se necessário.

Para agendar o trabalho com o Dispatcher de trabalho Firebase, um aplicativo xamarin deve encapsular o código em um tipo que estende o `JobService` classe. `JobService` tem três métodos de ciclo de vida que pode ser chamado durante o tempo de vida do trabalho:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Esse método é onde o trabalho ocorrerá e sempre deve ser implementado. Ele é executado no thread principal. Esse método retornará `true` se há trabalho restante, ou `false` se o trabalho é feito. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Isso é chamado quando o trabalho é interrompido por algum motivo. Ele deverá retornar `true` se o trabalho deve ser reagendado para mais tarde.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Este método é chamado quando o `JobService` terminou de qualquer trabalho assíncrono. 

Para agendar um trabalho, o aplicativo instanciará uma `JobDispatcher` objeto. Em seguida, uma `Job.Builder` é usado para criar um `Job` objeto, que é fornecido para o `JobDispatcher` que irá tentar e agendar o trabalho seja executado.

Este guia sobre como adicionar o Dispatcher de trabalho Firebase a um aplicativo xamarin e usá-lo para agendar o trabalho em segundo plano.

## <a name="requirements"></a>Requisitos

O Dispatcher de trabalho Firebase requer o nível de API do Android 9 ou superior. A biblioteca de Dispatcher de trabalho Firebase depende de alguns componentes fornecidos pelo Google executar serviços; o dispositivo deve ter Google executar serviços instalados.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Usando a biblioteca de Dispatcher de trabalho de Firebase em xamarin

Para começar a usar o Dispatcher de trabalho Firebase, primeiro adicione o [pacote Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) ao projeto xamarin. Pesquisar o NuGet Package Manager para o **Xamarin.Firebase.JobDispatcher** pacote (que ainda está em pré-lançamento).

Depois de adicionar a biblioteca de Firebase Dispatcher de trabalho, crie um `JobService` classe e, em seguida, agendar a execução com uma instância do `FirebaseJobDispatcher`.

> [!NOTE]
> A associação atual para o Dispatcher de trabalho Firebase tem como alvo uma versão antiga da biblioteca. O é um [bug conhecido [(https://bugzilla.xamarin.com/show_bug.cgi?id=59046)] que impede que a associação seja atualizado para uma versão mais recente do Dispatcher de trabalho Firebase de destino.


### <a name="creating-a-jobservice"></a>Criando um JobService

Todo trabalho executado pela biblioteca Firebase Dispatcher de trabalho deve ser feito em um tipo que estende o `Firebase.JobDispatcher.JobService` classe abstrata. Criando um `JobService` é muito semelhante à criação de um `Service` com o framework Android: 

1. Estender o `JobService` classe
2. Decore a subclasse com o `ServiceAttribute`. Embora não seja estritamente necessário, é recomendável definir explicitamente o `Name` parâmetro para ajudar na depuração de `JobService`. 
3. Adicionar uma `IntentFilter` para declarar o `JobService` no **AndroidManifest.xml**. Isso também ajudará a biblioteca de Dispatcher de trabalho Firebase localizar e invocar o `JobService`.

O código a seguir é um exemplo das mais simples `JobService` para um aplicativo usando a TPL assincronamente realizar algum trabalho:

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Criando um FirebaseJobDispatcher

Antes de qualquer trabalho pode ser agendado, é necessário criar um `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. O `FirebaseJobDispatcher` é responsável por agendar uma `JobService`. O trecho de código a seguir é uma maneira de criar uma instância do `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

No trecho de código anterior, o `GooglePlayDriver` é a classe que ajuda a `FirebaseJobDispatcher` interagir com algumas das APIs de agendamento nos serviços de reprodução do Google no dispositivo. O parâmetro `context` é qualquer Android `Context`, como uma atividade. Atualmente o `GooglePlayDriver` é o único `IDriver` implementação na biblioteca do Dispatcher de trabalho Firebase. 

A associação xamarin para o Dispatcher de trabalho Firebase fornece um método de extensão para criar um `FirebaseJobDispatcher` do `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Uma vez o `FirebaseJobDispatcher` foi instanciado, é possível criar um `Job` e executar o código de `JobService` classe. O `Job` é criado por um `Job.Builder` do objeto e será abordado na próxima seção.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Criando um Firebase.JobDispatcher.Job com o Job.Builder

O `Firebase.JobDispatcher.Job` classe é responsável por encapsulando os metadados necessários para executar um `JobService`. Um`Job` contém informações como qualquer restrição que deve ser atendida antes de executar o trabalho, se o `Job` for recorrente, ou gatilhos que fará com que o trabalho a ser executado.  Como mínimo, um `Job` deve ter um _marca_ (uma cadeia de caracteres exclusiva que identifica o trabalho para o `FirebaseJobDispatcher`) e o tipo do `JobService` que deve ser executado. O Dispatcher de trabalho Firebase instanciará o `JobService` quando for hora para executar o trabalho.  Um `Job` é criada usando uma instância do `Firebase.JobDispatcher.Job.JobBuilder` classe. 

O trecho de código a seguir é o exemplo mais simples de como criar um `Job` usando a associação de xamarin:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

O `Job.Builder` executará algumas verificações de validação básicos nos valores de entrada para o trabalho. Uma exceção será lançada se ele não é possível para o `Job.Builder` para criar um `Job`.  O `Job.Builder` criará um `Job` com os seguintes padrões:

* Um `Job`do _tempo de vida_ (quanto tempo ele será agendado para ser executado) é até que o dispositivo for reinicializado &ndash; depois que o dispositivo for reinicializado o `Job` será perdido.
* Um `Job` não seja recorrente &ndash; só será executado uma vez.
* Um `Job` será agendado para ser executado assim que possível.
* A estratégia de repetição padrão para um `Job` é usar um _retirada exponencial_ (discutido em mais detalhes abaixo na seção [definindo um RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Agendar um trabalho

Depois de criar o `Job`, ela precisa ser agendada com o `FirebaseJobDispatcher` antes de ser executado. Há dois métodos para agendar um `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

O valor retornado por `FirebaseJobDispatcher.Schedule` deve ser um dos seguintes valores inteiros:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; O `Job` foi agendada com êxito.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Um problema desconhecido ocorreu impediu o `Job` de ser agendado.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; Inválido `IDriver` foi usado ou `IDriver` alguma forma não estava disponível. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; O `Trigger` não tem suporte.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; O serviço não está configurado corretamente ou não está disponível.
 
### <a name="configuring-a-job"></a>Configuração de um trabalho

É possível personalizar um trabalho. Exemplos de como um trabalho pode ser personalizado incluem o seguinte:

* [Passando parâmetros para um trabalho](#Passing_Parameters_to_a_Job) &ndash; um `Job` pode exigir valores adicionais para realizar seu trabalho, por exemplo, baixar um arquivo.
* [Definir restrições](#Setting_Constraints) &ndash; pode ser necessário executar um trabalho apenas quando determinadas condições forem atendidas. Por exemplo, executar apenas uma `Job` quando o dispositivo está sendo carregada. 
* [Especifique quando um `Job` devem ser executados](#Setting_Job_Triggers) &ndash; o Dispatcher de trabalho Firebase permite que os aplicativos especificar uma hora de quando o trabalho deve ser executado.  
* [Declare uma estratégia de repetição para trabalhos com falha](#Setting_a_RetryStrategy) &ndash; um _estratégia de tentar novamente_ fornece orientação para a `FirebaseJobDispatcher` sobre o que fazer com `Jobs` que falha ao concluir. 

Os seguintes tópicos serão discutidos mais nas seções a seguir.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-jarameters-to-a-job"></a>Passando jarameters para um trabalho

Parâmetros são passados para um trabalho, criando um `Bundle` que é passado junto com o `Job.Builder.SetExtras` método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

O `Bundle` é acessado a partir de `IJobParameters.Extras` propriedade o `OnStartJob` método:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Restrições de configuração

Podem ajudar a restrições reduz os custos ou bateria esvaziar no dispositivo. O `Firebase.JobDispatcher.Constraint` classe define essas restrições, como valores inteiros:

* `Constraint.OnUnmeteredNetwork` &ndash; Execute apenas o trabalho quando o dispositivo estiver conectado a uma rede não monitorada. Isso é útil para impedir que o usuário incorrer em encargos de dados.
* `Constraint.OnAnyNetwork` &ndash; Execute o trabalho em qualquer rede que o dispositivo está conectado. Se especificado junto com `Constraint.OnUnmeteredNetwork`, esse valor tem prioridade.
* `Constraint.DeviceCharging` &ndash; Execute o trabalho somente quando o dispositivo está sendo carregada.

Restrições são definidas com o `Job.Builder.SetConstraint` método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

O `JobTrigger` fornece orientação para o sistema operacional sobre quando o trabalho deve ser iniciada. Um `JobTrigger` tem um _executar janela_ que define um horário agendado quando o `Job` deve ser executado. A janela de execução tem um _iniciar janela_ valor e um _janela final_ valor. A janela de início é o número de segundos que o dispositivo deve aguardar antes de executar o trabalho e o valor de janela final é o número máximo de segundos a aguardar antes de executar o `Job`. 

Um `JobTrigger` podem ser criados com o `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por exemplo `Trigger.ExecutionWindow(15,60)` significa que o trabalho deve ser executado entre 15 e 60 segundos de quando agendada. O `Job.Builder.SetTrigger` método é usado para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

O padrão `JobTrigger` para um trabalho é representado pelo valor `Trigger.Now`, que especifica se um trabalho ser executado mais rápido possível depois de ser agendado.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Definindo um RetryStrategy

O `Firebase.JobDispatcher.RetryStrategy` é usado para especificar a quantidade de um atraso de um dispositivo deve usar antes de tentar executar novamente um trabalho com falha. Um `RetryStrategy` tem um _política_, que define o algoritmo de base de tempo será usado para agendar novamente o trabalho com falha e uma janela de execução que especifica uma janela na qual o trabalho deve ser agendado. Isso _janela reagendar_ é definida por dois valores. O primeiro valor é o número de segundos a aguardar antes Reagendando trabalho (o _retirada inicial_ valor), e o segundo número é o número máximo de segundos antes que o trabalho deve ser executado (o _retirada máximo_valor). 
 
Os dois tipos de políticas de repetição são identificados por esses valores int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Um _retirada exponencial_ política aumenta o valor inicial de retirada exponencialmente depois de cada falha. Na primeira vez que um trabalho falhar, a biblioteca aguardará o intervalo de _initial especificado antes Reagendando trabalho &ndash; exemplo 30 segundos. Na segunda vez que o trabalho falhar, a biblioteca de espera pelo menos 60 segundos antes de tentar executar o trabalho. Após a terceira tentativa falha, a biblioteca de 120 segundos de espera e assim por diante. O padrão `RetryStrategy` para o Dispatcher de trabalho Firebase biblioteca é representada pelo `RetryStrategy.DefaultExponential` objeto. Ele tem uma retirada inicial de 30 segundos e uma máximo retirada de 3.600 segundos.
* `RetryStrategy.RetryPolicyLinear` &ndash; Essa estratégia é um _retirada linear_ que o trabalho deve ser reagendado para serem executados em definir intervalos (até obter êxito). Retirada linear é mais adequada para o trabalho que deve ser concluído assim que possível, ou para problemas que rapidamente resolvidos sozinhos. A biblioteca de Dispatcher de trabalho Firebase define um `RetryStrategy.DefaultLinear` que tem uma janela reprogramação de pelo menos 30 segundos e até 3600 segundos.

É possível definir um personalizado `RetryStrategy` com o `FirebaseJobDispatcher.NewRetryStrategy` método. Ele usa três parâmetros:

1. `int policy` &ndash; O _política_ é um dos anterior `RetryStrategy` valores, `RetryStrategy.RetryPolicyLinear`, ou `RetryStrategy.RetryPolicyExponential`.
2. `int intialBackoffSeconds` &ndash; O _inicial retirada_ um atraso, em segundos, que é necessário antes de tentar executar o trabalho novamente. O valor padrão é 30 segundos. 
3. `int maximumBackoffSeconds` &ndash; O _retirada máximo_ valor declara o número máximo de segundos de espera antes de tentar executar o trabalho novamente. O valor padrão é 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelar um trabalho

É possível cancelar todos os trabalhos que tenham sido agendados ou apenas um único trabalho usando o `FirebaseJobDispatcher.CancelAll()` método ou o `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

O método retornará um valor inteiro:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; O trabalho foi cancelado com êxito.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Um erro impediu que o trabalho está sendo cancelada.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; O `FirebaseJobDispatcher` é não é possível cancelar o trabalho for válida não `IDriver` disponíveis.

## <a name="summary"></a>Resumo

Este guia abordou como usar o Dispatcher de trabalho Firebase para executar o trabalho de maneira inteligente em segundo plano. Ele discutiu como encapsular o trabalho a ser executado como um `JobService` e como usar o `FirebaseJobDispatcher` para agendar esse trabalho, especificando os critérios com um `JobTrigger` e como as falhas devem ser tratadas com um `RetryStrategy`.


## <a name="related-links"></a>Links relacionados

- [Gerador de associações falha com erro FATAL exceção sem tratamento: System. ArgumentNullException: o valor não pode ser nulo.](https://bugzilla.xamarin.com/show_bug.cgi?id=59046)
- [Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-trabalho dispatcher no GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Associação de Xamarin.Firebase.JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Agendamento de trabalho inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Android bateria e otimizações de memória - 2016 de e/s do Google (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
