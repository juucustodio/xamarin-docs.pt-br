---
title: Dispatcher de trabalho do Firebase
description: Este guia discute como agendar o trabalho em segundo plano usando a biblioteca firebase Job Dispatcher do Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020240"
---
# <a name="firebase-job-dispatcher"></a>Dispatcher de trabalho do Firebase

_Este guia discute como agendar o trabalho em segundo plano usando a biblioteca firebase Job Dispatcher do Google._

## <a name="overview"></a>Visão geral

Uma das melhores maneiras de manter um aplicativo Android responsivo ao usuário é garantir que o trabalho complexo ou de longa duração seja realizado em segundo plano. No entanto, é importante que o trabalho de fundo não impacte negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode fazer uma enquete em um site a cada três ou quatro minutos para consultar alterações em um determinado conjunto de dados. Isso parece benigno, no entanto, teria um impacto desastroso na vida útil da bateria. O aplicativo acordará repetidamente o dispositivo, elevará a CPU a um estado de potência mais alto, ligará os rádios, fará as solicitações de rede e, em seguida, processará os resultados. Piora porque o dispositivo não desliga imediatamente e retorna ao estado ocioso de baixa potência. O trabalho de fundo mal programado pode inadvertidamente manter o dispositivo em um estado com requisitos de energia desnecessários e excessivos. Esta atividade aparentemente inocente (pesquisando um site) tornará o dispositivo inutilizável em um período relativamente curto de tempo.

O Android fornece as seguintes APIs para ajudar a realizar o trabalho em segundo plano, mas por si só elas não são suficientes para agendamento inteligente de trabalho. 

- **[Os Serviços de Intenção](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; de Serviços são ótimos para a realização do trabalho, porém não fornecem nenhuma maneira de agendar o trabalho.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Essas APIs só permitem que o trabalho seja agendado, mas não fornecem nenhuma maneira de realmente executar o trabalho. Além disso, o AlarmManager só permite restrições baseadas no tempo, o que significa levantar um alarme em um determinado momento ou depois de um certo período de tempo. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; O JobScheduler é uma ótima API que trabalha com o sistema operacional para agendar trabalhos. No entanto, ele só está disponível para aqueles aplicativos Android que visam o nível 21 da API ou superior. 
- **[Receptores de](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; transmissão Um aplicativo Android pode configurar receptores de transmissão para executar o trabalho em resposta a eventos ou intenções em todo o sistema. No entanto, os receptores de transmissão não fornecem qualquer controle sobre quando o trabalho deve ser executado. Além disso, as alterações no sistema operacional Android restringirão quando os receptores de transmissão funcionarem, ou os tipos de trabalho que eles podem responder. 

Existem duas características fundamentais para executar eficientemente o trabalho de fundo (às vezes referido como um _trabalho de fundo_ ou um _trabalho_):

1. **Agendamento inteligente do trabalho** &ndash; É importante que quando um aplicativo está fazendo um trabalho em segundo plano que o faça como um bom cidadão. Idealmente, o aplicativo não deve exigir que um trabalho seja executado. Em vez disso, o aplicativo deve especificar condições que devem ser atendidas para quando o trabalho pode ser executado e, em seguida, agendar esse trabalho para funcionar quando as condições forem atendidas. Isso permite que o Android realize o trabalho de forma inteligente. Por exemplo, as solicitações de rede podem ser executadas ao mesmo tempo para fazer o uso máximo das despesas gerais envolvidas com a rede.
2. **Encapsulamento do trabalho** &ndash; O código para executar o trabalho em segundo plano deve ser encapsulado em um componente discreto que pode ser executado independentemente da interface do usuário e será relativamente fácil de reagendar se o trabalho não for concluído por algum motivo.

O Firebase Job Dispatcher é uma biblioteca do Google que fornece uma API fluente para simplificar o trabalho de plano de fundo de agendamento. Pretende-se substituir o Google Cloud Manager. O Firebase Job Dispatcher consiste nas seguintes APIs:

- A `Firebase.JobDispatcher.JobService` é uma classe abstrata que deve ser estendida com a lógica que será executada no trabalho de fundo.
- A `Firebase.JobDispatcher.JobTrigger` declara quando o trabalho deve ser iniciado. Isso é normalmente expresso como uma janela de tempo, por exemplo, espere pelo menos 30 segundos antes de iniciar o trabalho, mas execute o trabalho dentro de 5 minutos.
- A `Firebase.JobDispatcher.RetryStrategy` contém informações sobre o que deve ser feito quando um trabalho não é executado corretamente. A estratégia de repetição especifica quanto tempo esperar antes de tentar executar o trabalho novamente. 
- A `Firebase.JobDispatcher.Constraint` é um valor opcional que descreve uma condição que deve ser atendida antes que o trabalho possa ser executado, como o dispositivo está em uma rede não-medidorou ou carregando.
- A `Firebase.JobDispatcher.Job` é uma API que unifica as APIs anteriores em uma unidade `JobDispatcher`de trabalho que pode ser agendada pelo . A `Job.Builder` classe é usada `Job`para instanciar um .
- A `Firebase.JobDispatcher.JobDispatcher` usa as três APIs anteriores para agendar o trabalho com o sistema operacional e fornecer uma maneira de cancelar empregos, se necessário.

Para agendar o trabalho com o Firebase Job Dispatcher, um aplicativo Xamarin.Android deve encapsular o código em um tipo que estende a `JobService` classe. `JobService`tem três métodos de ciclo de vida que podem ser chamados durante a vida do trabalho:

- **`bool OnStartJob(IJobParameters parameters)`**&ndash; Este método é onde o trabalho ocorrerá e deve ser sempre implementado. Ele funciona no fio principal. Este método `true` retornará se houver `false` trabalho restante, ou se o trabalho for feito. 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash; Isso é chamado quando o trabalho é interrompido por alguma razão. Ele deve `true` retornar se o trabalho deve ser remarcado para mais tarde.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash; Este método é `JobService` chamado quando o tiver terminado qualquer trabalho assíncrono. 

Para agendar um trabalho, o `JobDispatcher` aplicativo instanciará um objeto. Em seguida, a `Job.Builder` é `Job` usado para criar um `JobDispatcher` objeto, que é fornecido para o que vai tentar agendar o trabalho para executar.

Este guia discutirá como adicionar o Firebase Job Dispatcher a um aplicativo Xamarin.Android e usá-lo para agendar o trabalho em segundo plano.

## <a name="requirements"></a>Requisitos

O Firebase Job Dispatcher requer API Android nível 9 ou superior. A biblioteca firebase Job Dispatcher conta com alguns componentes fornecidos pelo Google Play Services; o dispositivo deve ter o Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Usando a Biblioteca de Despachantes de Trabalho firebase em Xamarin.Android

Para começar com o Firebase Job Dispatcher, adicione primeiro o [pacote Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) ao projeto Xamarin.Android. Procure no NuGet Package Manager para o pacote **Xamarin.Firebase.JobDispatcher** (que ainda está em pré-lançamento).

Depois de adicionar a biblioteca Firebase `JobService` Job Dispatcher, crie uma classe `FirebaseJobDispatcher`e, em seguida, programe-a para executá-la com uma instância do .

### <a name="creating-a-jobservice"></a>Criando um JobService

Todo o trabalho realizado pela biblioteca Firebase Job Dispatcher `Firebase.JobDispatcher.JobService` deve ser feito de um tipo que estende a classe abstrata. Criar `JobService` um é muito `Service` semelhante à criação de um com a estrutura do Android: 

1. Estender `JobService` a classe
2. Decore a subclasse `ServiceAttribute`com o . Embora não seja estritamente necessário, recomenda-se definir explicitamente o `Name` parâmetro `JobService`para ajudar na depuração do . 
3. Adicionar `IntentFilter` um para `JobService` declarar o no **AndroidManifest.xml**. Isso também ajudará a biblioteca firebase Job `JobService`Dispatcher localizar e invocar o .

O código a seguir é `JobService` um exemplo do mais simples para um aplicativo, usando o TPL para executar assíncronamente alguns trabalhos:

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

Antes que qualquer trabalho possa ser agendado, é necessário criar um `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. O `FirebaseJobDispatcher` é responsável pelo `JobService`agendamento de um . O seguinte trecho de código é uma maneira `FirebaseJobDispatcher`de criar uma instância do : 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

No trecho de código anterior, a `GooglePlayDriver` classe `FirebaseJobDispatcher` é que ajuda a interagir com algumas das APIs de agendamento no Google Play Services no dispositivo. O parâmetro `context` é `Context`qualquer Android, como uma Atividade. Atualmente, `GooglePlayDriver` a `IDriver` única implementação na biblioteca firebase Job Dispatcher. 

A vinculação Xamarin.Android para o Firebase Job Dispatcher fornece um método de extensão para criar um `FirebaseJobDispatcher` a `Context`partir do : 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Uma `FirebaseJobDispatcher` vez que o tenha sido instanciado, é possível criar um `Job` e executar o código na `JobService` classe. O `Job` é criado `Job.Builder` por um objeto e será discutido na próxima seção.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Criando um Firebase.JobDispatcher.Job com o Job.Builder

A `Firebase.JobDispatcher.Job` classe é responsável por encapsular os `JobService`metadados necessários para executar um . A`Job` contém informações como qualquer restrição que deve ser cumprida `Job` antes que o trabalho possa ser executado, se o é recorrente, ou quaisquer gatilhos que farão com que o trabalho seja executado.  Como um mínimo, `Job` um deve ter uma _tag_ (uma seqüência única que identifica o trabalho para o `FirebaseJobDispatcher`) e o tipo do `JobService` que deve ser executado. O Firebase Job Dispatcher `JobService` irá instanciar quando for a hora de executar o trabalho.  A `Job` é criado usando uma `Firebase.JobDispatcher.Job.JobBuilder` instância da classe. 

O seguinte trecho de código é o exemplo `Job` mais simples de como criar um usando a vinculação Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

O `Job.Builder` realizará algumas verificações básicas de validação sobre os valores de entrada para o trabalho. Uma exceção será lançada se `Job.Builder` não for `Job`possível criar um .  O `Job.Builder` criará `Job` um com os seguintes padrões:

- A `Job` _vida útil_ de uma vida útil (quanto tempo será &ndash; programado para ser `Job` executado) é somente até que o dispositivo seja reiniciado quando o dispositivo for reiniciado.
- A `Job` não &ndash; é recorrente ele só vai correr uma vez.
- Um `Job` será programado para ser executado o mais rápido possível.
- A estratégia padrão de `Job` retentativa para a é usar um _backoff exponencial_ (discutido em mais detalhes abaixo na seção [Configuração de uma Estratégia de Retry](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Agendar um trabalho

Depois de `Job`criar o , ele `FirebaseJobDispatcher` precisa ser agendado com o antes de ser executado. Existem dois métodos para `Job`agendar um:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

O valor devolvido `FirebaseJobDispatcher.Schedule` será um dos seguintes valores inteiros:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; O `Job` foi agendado com sucesso.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash; Ocorreu algum problema desconhecido que `Job` impediu que fosse agendado.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash; Um inválido `IDriver` foi `IDriver` usado ou o estava de alguma forma indisponível. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash; O `Trigger` não foi apoiado.
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; O serviço não está configurado corretamente ou não está disponível.

### <a name="configuring-a-job"></a>Configurando um trabalho

É possível personalizar um trabalho. Exemplos de como um trabalho pode ser personalizado incluem o seguinte:

- [Passar parâmetros para um Trabalho](#Passing_Parameters_to_a_Job) &ndash; A `Job` pode exigir valores adicionais para realizar seu trabalho, por exemplo, baixar um arquivo.
- [Definir restrições](#Setting_Constraints) &ndash; Pode ser necessário apenas executar um trabalho quando determinadas condições são atendidas. Por exemplo, só `Job` execute um quando o dispositivo estiver carregando. 
- [Especifique quando um `Job` deve ser executado](#Setting_Job_Triggers) &ndash; O Firebase Job Dispatcher permite que os aplicativos especifiquem uma hora em que o trabalho deve ser executado.  
- [Declare uma estratégia de repetição para trabalhos fracassados](#Setting_a_RetryStrategy) &ndash; Uma `Jobs` estratégia de _repetição_ fornece orientação para o `FirebaseJobDispatcher` que fazer com que não se complete. 

Cada um desses tópicos será mais discutido nas seções a seguir.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passando parâmetros para um trabalho

Os parâmetros são passados `Bundle` para um trabalho `Job.Builder.SetExtras` criando um que é passado junto com o método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

O `Bundle` é acessado `IJobParameters.Extras` a partir `OnStartJob` da propriedade no método:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Configuração de restrições

As restrições podem ajudar a reduzir os custos ou o dreno da bateria no dispositivo. A `Firebase.JobDispatcher.Constraint` classe define essas restrições como valores inteiros:

- `Constraint.OnUnmeteredNetwork`&ndash; Só execute o trabalho quando o dispositivo estiver conectado a uma rede não-medidora. Isso é útil para evitar que o usuário incorra em cobranças de dados.
- `Constraint.OnAnyNetwork`&ndash; Execute o trabalho em qualquer rede que o dispositivo esteja conectado. Se especificado `Constraint.OnUnmeteredNetwork`juntamente com, este valor terá prioridade.
- `Constraint.DeviceCharging`&ndash; Execute o trabalho somente quando o dispositivo estiver carregando.

As restrições são `Job.Builder.SetConstraint` definidas com o método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

O `JobTrigger` fornece orientação ao sistema operacional sobre quando o trabalho deve começar. A `JobTrigger` tem uma _janela de execução_ que `Job` define um horário programado para quando o deve ser executado. A janela de execução tem um valor _de janela inicial_ e um valor de janela _final._ A janela de inicialidade é o número de segundos que o dispositivo deve esperar antes de `Job`executar o trabalho e o valor da janela final é o número máximo de segundos para esperar antes de executar o . 

A `JobTrigger` pode ser criado `Firebase.Jobdispatcher.Trigger.ExecutionWindow` com o método.  Por `Trigger.ExecutionWindow(15,60)` exemplo, significa que o trabalho deve funcionar entre 15 e 60 segundos a partir de quando está programado. O `Job.Builder.SetTrigger` método é usado para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

A `JobTrigger` inadimplência de um trabalho `Trigger.Now`é representada pelo valor , que especifica que um trabalho seja executado o mais rápido possível após ser agendado..

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Definindo uma estratégia de retentativa

O `Firebase.JobDispatcher.RetryStrategy` é usado para especificar quanto de um atraso um dispositivo deve usar antes de tentar executar novamente um trabalho com falha. A `RetryStrategy` tem uma _política_, que define qual algoritmo de base de tempo será usado para reagendar o trabalho falho, e uma janela de execução que especifica uma janela na qual o trabalho deve ser agendado. Esta _janela de reagendamento_ é definida por dois valores. O primeiro valor é o número de segundos para esperar antes de reagendar o trabalho (o valor _inicial de recuo),_ e o segundo número é o número máximo de segundos antes do trabalho deve ser executado (o valor _máximo de recuo)._ 

Os dois tipos de políticas de repetição são identificados por esses valores int:

- `RetryStrategy.RetryPolicyExponential`&ndash; Uma política _de recuo exponencial_ aumentará exponencialmente o valor inicial de recuo após cada falha. Na primeira vez que um trabalho falhar, a biblioteca aguardará o &ndash; intervalo de _initial especificado antes de reagendar o exemplo de trabalho de 30 segundos. Na segunda vez que o trabalho falhar, a biblioteca esperará pelo menos 60 segundos antes de tentar executar o trabalho. Após a terceira tentativa fracassada, a biblioteca esperará 120 segundos, e assim por diante. O `RetryStrategy` padrão para a biblioteca Firebase Job `RetryStrategy.DefaultExponential` Dispatcher é representado pelo objeto. Ele tem um recuo inicial de 30 segundos e um recuo máximo de 3600 segundos.
- `RetryStrategy.RetryPolicyLinear`&ndash; Essa estratégia é um _recuo linear que_ o trabalho deve ser reagendado para ser executado em intervalos definidos (até que ele tenha sucesso). O backoff linear é mais adequado para trabalhos que devem ser concluídos o mais rápido possível ou para problemas que se resolvam rapidamente. A biblioteca firebase Job Dispatcher define um `RetryStrategy.DefaultLinear` que tem uma janela de reagendamento de pelo menos 30 segundos e até 3600 segundos.

É possível definir um `RetryStrategy` costume `FirebaseJobDispatcher.NewRetryStrategy` com o método. São necessários três parâmetros:

1. `int policy`&ndash; A _política_ é um `RetryStrategy` dos `RetryStrategy.RetryPolicyLinear`valores anteriores, ou `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds`&ndash; O _recuo inicial_ é um atraso, em segundos, que é necessário antes de tentar executar o trabalho novamente. O valor padrão para isso é de 30 segundos. 
3. `int maximumBackoffSeconds`&ndash; O valor _máximo de recuo_ declara o número máximo de segundos para atrasar antes de tentar executar o trabalho novamente. O valor padrão é de 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelando um trabalho

É possível cancelar todos os trabalhos que foram agendados, `FirebaseJobDispatcher.CancelAll()` ou `FirebaseJobDispatcher.Cancel(string)` apenas um único trabalho usando o método ou o método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Qualquer método retornará um valor inteiro:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; O trabalho foi cancelado com sucesso.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Um erro impediu que o trabalho fosse cancelado.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`&ndash; O `FirebaseJobDispatcher` não pode cancelar o trabalho, `IDriver` pois não há válido disponível.

## <a name="summary"></a>Resumo

Este guia discutiu como usar o Firebase Job Dispatcher para executar o trabalho de forma inteligente em segundo plano. Discutiu como encapsular o trabalho a `JobService` ser realizado como `FirebaseJobDispatcher` um e como usar o `JobTrigger` para agendar esse trabalho, `RetryStrategy`especificando os critérios com um e como as falhas devem ser tratadas com um .

## <a name="related-links"></a>Links relacionados

- [Xamarin.Firebase.JobDispatcher no NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-dispatcher no GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Agendamento inteligente de trabalho](https://developer.android.com/topic/performance/scheduling.html)
- [Otimizações de bateria e memória do Android - Google I/O 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
