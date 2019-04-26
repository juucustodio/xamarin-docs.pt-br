---
title: Dispatcher de trabalho do Firebase
description: Este guia aborda como agendar um trabalho em segundo plano usando a biblioteca do Dispatcher de trabalho do Firebase do Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 91bafbbdaee805ad128766bf0a770cb711597a85
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023344"
---
# <a name="firebase-job-dispatcher"></a>Dispatcher de trabalho do Firebase

_Este guia aborda como agendar um trabalho em segundo plano usando a biblioteca do Dispatcher de trabalho do Firebase do Google._


## <a name="overview"></a>Visão geral

Uma das melhores maneiras de manter um aplicativo Android responsiva ao usuário é garantir que o trabalho de execução longa ou complexo é realizado em segundo plano. No entanto, é importante que o trabalho de plano de fundo não afetará negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode sondar um site cada três ou quatro minutos a consulta para alterações em um determinado conjunto de dados. Isso parece benigno, no entanto, ele teria um impacto desastroso na vida útil da bateria. O aplicativo será repetidamente acorde o dispositivo, elevar a CPU para um estado de energia mais alto, ligar os rádios, fazer as solicitações de rede e, em seguida, processar os resultados. Isso é pior, porque o dispositivo será imediatamente não desligue e retornar para o estado ocioso de baixa energia. Trabalho em segundo plano agendado mal pode inadvertidamente manter o dispositivo em um estado com os requisitos de energia excessivos e desnecessários. Essa atividade aparentemente inocente (um site de sondagem) será renderizado o dispositivo inutilizável em um período relativamente curto de tempo.

Android fornece as seguintes APIs para ajudá-lo executando o trabalho em segundo plano, mas por si só não são suficientes para o agendamento de trabalho inteligente. 

* **[Serviços de intenção](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; intenção serviços são ótimos para execução do trabalho, no entanto, eles fornecem nenhuma maneira de agendar o trabalho.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; essas APIs permitem que somente o trabalho ser agendado, mas não fornecer nenhuma maneira de realmente executar o trabalho. Além disso, o AlarmManager só permite a restrições de tempo com base, que significa que aciona um alarme em um determinado momento ou após um determinado período de tempo decorrido. 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule o é uma API grande que funciona com o sistema operacional para agendar trabalhos. No entanto, só está disponível para os aplicativos Android que selecionar o nível de API 21 ou superior. 
* **[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; Android de um aplicativo pode configurar para executar o trabalho em resposta a eventos de todo o sistema ou propósitos de receptores de difusão. No entanto, os receptores de difusão não fornecem nenhum controle sobre quando o trabalho deve ser executado. Também restringirá as alterações no sistema operacional Android quando funcionam de receptores de difusão ou os tipos de trabalho que eles possam responder às. 

Há dois recursos principais para executar com eficiência o trabalho em segundo plano (também conhecido como um _trabalho em segundo plano_ ou um _trabalho_):

1. **Com inteligência agendamento do trabalho** &ndash; é importante que quando um aplicativo estiver executando um trabalho em segundo plano que ele faz isso como um bom cidadão. O ideal é que o aplicativo não deve exigir que um trabalho seja executada. Em vez disso, o aplicativo deve especificar as condições que devem ser atendidas para que quando o trabalho pode ser executado e, em seguida, agendar esse trabalho para ser executado quando as condições forem atendidas. Isso permite que o Android executar o trabalho de forma inteligente. Por exemplo, solicitações de rede podem ser divididas em lotes para executar tudo ao mesmo tempo utilizar ao máximo de sobrecarga envolvida com a rede.
2. **Encapsulando o trabalho** &ndash; o código para executar o trabalho em segundo plano deve ser encapsulado em um componente discreto que pode ser executado independentemente da interface do usuário e será relativamente fácil reagendar se o trabalho não for concluída por algum motivo.

O Dispatcher de trabalho do Firebase é uma biblioteca do Google que fornece uma API fluente para simplificar o trabalho de agendamento em segundo plano. Ele destina-se a ser a substituição para o Gerenciador de nuvem do Google. O Dispatcher de trabalho do Firebase consiste as seguintes APIs:

* Um `Firebase.JobDispatcher.JobService` é uma classe abstrata que deve ser estendida com a lógica que será executado no trabalho em segundo plano.
* Um `Firebase.JobDispatcher.JobTrigger` declara quando o trabalho deve ser iniciado. Isso normalmente é expresso como uma janela de tempo, por exemplo, aguarde pelo menos 30 segundos antes de iniciar o trabalho, mas executar o trabalho de dentro de 5 minutos.
* Um `Firebase.JobDispatcher.RetryStrategy` contém informações sobre o que deve ser feito quando uma tarefa falhar para serem executados corretamente. A estratégia de repetição Especifica quanto tempo esperar antes de tentar executar novamente o trabalho. 
* Um `Firebase.JobDispatcher.Constraint` é um valor opcional que descreve uma condição que deve ser atendida antes de executar o trabalho, como o dispositivo estiver em uma rede não monitorada ou carregada.
* O `Firebase.JobDispatcher.Job` é uma API que unifica as APIs anteriores para uma unidade de trabalho que pode ser agendado pelo `JobDispatcher`. O `Job.Builder` classe é usada para criar uma instância de um `Job`.
* Um `Firebase.JobDispatcher.JobDispatcher` usa as APIs de três anteriores para agendar o trabalho com o sistema operacional e fornecem uma maneira de cancelar trabalhos, se necessário.

Para agendar o trabalho com o Dispatcher de trabalho do Firebase, um aplicativo xamarin. Android deve encapsular o código em um tipo que estende o `JobService` classe. `JobService` tem três métodos de ciclo de vida que podem ser chamados durante o tempo de vida do trabalho:

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; Esse método é onde o trabalho ocorrerá e sempre deve ser implementado. Ele é executado no thread principal. Esse método retornará `true` se não houver trabalho restante, ou `false` se o trabalho é feito. 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; Isso é chamado quando o trabalho é interrompido por algum motivo. Ele deverá retornar `true` se o trabalho deve ser reagendado para uso posterior.
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; Esse método é chamado quando o `JobService` terminou de qualquer trabalho assíncrono. 

Para agendar um trabalho, o aplicativo instanciará uma `JobDispatcher` objeto. Em seguida, uma `Job.Builder` é usado para criar um `Job` objeto, que é fornecido para o `JobDispatcher` que vai tentar e agendar o trabalho seja executado.

Este guia discute como adicionar o Dispatcher de trabalho do Firebase a um aplicativo xamarin. Android e usá-la para agendar o trabalho em segundo plano.

## <a name="requirements"></a>Requisitos

O Dispatcher de trabalho do Firebase requer nível de API do Android, 9 ou superior. A biblioteca de Dispatcher de trabalho do Firebase se baseia em alguns componentes fornecidos pelo Google Play Services; o dispositivo deve ter o Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Usando a biblioteca de Dispatcher de trabalho do Firebase no xamarin. Android

Para começar com o Dispatcher de trabalho do Firebase, primeiro adicione a [pacote do Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) ao projeto xamarin. Android. Pesquisar o Gerenciador de pacotes do NuGet para o **Xamarin.Firebase.JobDispatcher** pacote (que ainda está em pré-lançamento).

Depois de adicionar a biblioteca de Dispatcher de trabalho do Firebase, criar uma `JobService` de classe e, em seguida, agendar a execução com uma instância da `FirebaseJobDispatcher`.


### <a name="creating-a-jobservice"></a>Criando um JobService

Todo o trabalho realizado pela biblioteca de Dispatcher de trabalho do Firebase deve ser feito em um tipo que estende o `Firebase.JobDispatcher.JobService` classe abstrata. Criando um `JobService` é muito semelhante à criação de um `Service` com a estrutura do Android: 

1. Estender o `JobService` classe
2. Decore a subclasse com o `ServiceAttribute`. Embora não seja estritamente necessário, é recomendável definir explicitamente o `Name` parâmetro para ajudar na depuração com o `JobService`. 
3. Adicionar um `IntentFilter` para declarar os `JobService` na **androidmanifest. XML**. Isso também ajudará a biblioteca do Dispatcher de trabalho do Firebase localizar e invocar o `JobService`.

O código a seguir é um exemplo das mais simples `JobService` para um aplicativo usando a TPL para assincronamente executar algum trabalho:

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

Antes de qualquer trabalho pode ser agendado, ele é necessário criar um `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. O `FirebaseJobDispatcher` é responsável por agendar uma `JobService`. O trecho de código a seguir é uma maneira de criar uma instância da `FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

No trecho de código anterior, o `GooglePlayDriver` é a classe que ajuda a `FirebaseJobDispatcher` interagir com algumas das APIs de agendamento no Google Play Services no dispositivo. O parâmetro `context` é qualquer Android `Context`, como uma atividade. Atualmente, o `GooglePlayDriver` é o único `IDriver` implementação na biblioteca do Dispatcher de trabalho do Firebase. 

A associação do xamarin. Android para o Dispatcher de trabalho do Firebase fornece um método de extensão para criar uma `FirebaseJobDispatcher` do `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Uma vez a `FirebaseJobDispatcher` tiver sido instanciado, é possível criar um `Job` e executar o código a `JobService` classe. O `Job` é criado por um `Job.Builder` do objeto e será discutido na próxima seção.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Criando um Firebase.JobDispatcher.Job com o Job.Builder

O `Firebase.JobDispatcher.Job` classe é responsável por encapsular os metadados necessários para executar um `JobService`. Um`Job` contém informações como qualquer restrição que deve ser atendida antes de executar o trabalho, se o `Job` for recorrente, ou gatilhos que fará com que o trabalho a ser executado.  Como mínimo, um `Job` deve ter um _marca_ (uma cadeia de caracteres exclusiva que identifica o trabalho para o `FirebaseJobDispatcher`) e o tipo dos `JobService` que deve ser executado. O Dispatcher de trabalho do Firebase instanciará o `JobService` quando é hora de executar o trabalho.  Um `Job` é criado usando uma instância da `Firebase.JobDispatcher.Job.JobBuilder` classe. 

O trecho de código a seguir está o exemplo mais simples de como criar um `Job` usando a associação do xamarin. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

O `Job.Builder` executará algumas verificações de validação básicos nos valores de entrada para o trabalho. Uma exceção será gerada se ele não é possível para o `Job.Builder` para criar um `Job`.  O `Job.Builder` criará um `Job` com os seguintes padrões:

* Um `Job`do _tempo de vida_ (quanto tempo ela será agendada para ser executado) é até que o dispositivo for reinicializado &ndash; depois que o dispositivo for reinicializado o `Job` será perdido.
* Um `Job` não for recorrente &ndash; só será executada uma vez.
* Um `Job` será agendada para ser executado assim que possível.
* A estratégia de repetição padrão para um `Job` é usar um _retirada exponencial_ (discutido em mais detalhes abaixo, na seção [definindo um RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Agendar um trabalho

Depois de criar o `Job`, ele precisa ser agendado com o `FirebaseJobDispatcher` antes de ser executado. Há dois métodos para agendar um `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

O valor retornado por `FirebaseJobDispatcher.Schedule` será um dos seguintes valores inteiros:

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; O `Job` foi agendada com êxito.
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; Houve algum problema desconhecido ocorreu que impediam a `Job` de ser agendado.
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; Inválido `IDriver` foi usado ou o `IDriver` alguma forma, não estava disponível. 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; O `Trigger` não tem suporte.
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; O serviço não está configurado corretamente ou não está disponível.
 
### <a name="configuring-a-job"></a>Configurando um trabalho

É possível personalizar um trabalho. Exemplos de como um trabalho pode ser personalizado incluem o seguinte:

* [Passando parâmetros para um trabalho](#Passing_Parameters_to_a_Job) &ndash; um `Job` pode exigir valores adicionais para realizar seu trabalho, por exemplo, baixando um arquivo.
* [Definir restrições](#Setting_Constraints) &ndash; pode ser necessário executar um trabalho apenas quando determinadas condições forem atendidas. Por exemplo, apenas executar um `Job` quando o dispositivo está sendo carregada. 
* [Especificar quando uma `Job` deve ser executado](#Setting_Job_Triggers) &ndash; o Dispatcher de trabalho do Firebase permite que os aplicativos especificar uma hora de quando o trabalho deve ser executado.  
* [Declare uma estratégia de repetição para trabalhos com falha](#Setting_a_RetryStrategy) &ndash; um _estratégia de repetição_ fornece orientação para a `FirebaseJobDispatcher` sobre o que fazer com `Jobs` que não serão concluídos. 

Cada um desses tópicos será discutida mais nas seções a seguir.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passando parâmetros para um trabalho

Parâmetros são passados para um trabalho com a criação de um `Bundle` que é passado junto com o `Job.Builder.SetExtras` método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

O `Bundle` é acessado a partir de `IJobParameters.Extras` propriedade no `OnStartJob` método:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Restrições de configuração

Restrições podem ajudar a reduz custos ou bateria esvaziar no dispositivo. O `Firebase.JobDispatcher.Constraint` classe define essas restrições como valores inteiros:

* `Constraint.OnUnmeteredNetwork` &ndash; Apenas execute o trabalho quando o dispositivo estiver conectado a uma rede não monitorada. Isso é útil para impedir que o usuário incorrer em encargos de dados.
* `Constraint.OnAnyNetwork` &ndash; Execute o trabalho em qualquer rede o dispositivo está conectado. Se for especificado junto com `Constraint.OnUnmeteredNetwork`, esse valor terá prioridade.
* `Constraint.DeviceCharging` &ndash; Execute o trabalho somente quando o dispositivo está sendo carregada.

Restrições são definidas com o `Job.Builder.SetConstraint` método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

O `JobTrigger` fornece orientação para o sistema operacional sobre quando o trabalho deve começar. Um `JobTrigger` tem um _janela de execução_ que define um horário agendado para quando o `Job` deve ser executado. A janela de execução tem um _janela de início_ valor e um _janela final_ valor. A janela de início é o número de segundos que o dispositivo deve aguardar antes de executar o trabalho e o valor final da janela é o número máximo de segundos a aguardar antes de executar o `Job`. 

Um `JobTrigger` podem ser criados com o `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por exemplo `Trigger.ExecutionWindow(15,60)` significa que o trabalho deve ser executado entre 15 e 60 segundos desde quando ele é agendado. O `Job.Builder.SetTrigger` método é usado para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

O padrão `JobTrigger` para um trabalho é representado pelo valor `Trigger.Now`, que especifica que um trabalho seja executada logo depois de ser agendado...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Definindo um RetryStrategy

O `Firebase.JobDispatcher.RetryStrategy` é usado para especificar o quanto de um atraso de um dispositivo deve usar antes de tentar executar novamente um trabalho com falha. Um `RetryStrategy` tem uma _diretiva_, que define qual algoritmo de base de tempo será usado para agendar novamente o trabalho com falha e uma janela de execução que especifica uma janela na qual o trabalho deve ser agendado. Isso _janela reagendamento_ é definido por dois valores. O primeiro valor é o número de segundos a aguardar antes de reagendamento de trabalho (o _inicial retirada_ valor), e o segundo número é o número máximo de segundos antes que o trabalho deve ser executado (o _retirada máximo_valor). 
 
Os dois tipos de políticas de repetição são identificados por esses valores int:

* `RetryStrategy.RetryPolicyExponential` &ndash; Uma _retirada exponencial_ política aumentará o valor inicial de retirada exponencialmente após cada falha. Na primeira vez que um trabalho falhar, a biblioteca aguardará o intervalo de _initial é especificado antes Reagendando trabalho &ndash; exemplo 30 segundos. Na segunda vez que o trabalho falhar, a biblioteca aguardará pelo menos 60 segundos antes de tentar executar o trabalho. Depois que a terceira tentativa falha, a biblioteca de 120 segundos de espera e assim por diante. O padrão `RetryStrategy` para o Dispatcher de trabalho do Firebase biblioteca é representada pelo `RetryStrategy.DefaultExponential` objeto. Ele tem uma retirada inicial de 30 segundos e uma máximo retirada de 3600 segundos.
* `RetryStrategy.RetryPolicyLinear` &ndash; Essa estratégia é um _retirada linear_ que o trabalho deve ser reagendado para ser executada ao definir intervalos (até obter êxito). Retirada linear é mais adequada para o trabalho que deve ser concluído assim que possível, ou para os problemas rapidamente resolver sozinho. A biblioteca de Dispatcher de trabalho do Firebase define um `RetryStrategy.DefaultLinear` que tem uma janela de reagendamento de pelo menos 30 segundos e até 3600 segundos.

É possível definir um personalizado `RetryStrategy` com o `FirebaseJobDispatcher.NewRetryStrategy` método. Ele usa três parâmetros:

1. `int policy` &ndash; O _diretiva_ é um dos anterior `RetryStrategy` valores `RetryStrategy.RetryPolicyLinear`, ou `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds` &ndash; O _retirada inicial_ é um atraso, em segundos, que é necessária antes de tentar executar novamente o trabalho. O valor padrão é 30 segundos. 
3. `int maximumBackoffSeconds` &ndash; O _retirada máximo_ valor declara o número máximo de segundos de atraso antes de tentar executar novamente o trabalho. O valor padrão é 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelar um trabalho

É possível cancelar todos os trabalhos que foram agendados, ou apenas um único trabalho usando o `FirebaseJobDispatcher.CancelAll()` método ou o `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

O método retornará um valor inteiro:

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; O trabalho foi cancelado com êxito.
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; Um erro impediu que o trabalho que está sendo cancelada.
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; O `FirebaseJobDispatcher` não é capaz de cancelar o trabalho, pois não há não válido `IDriver` disponíveis.

## <a name="summary"></a>Resumo

Este guia abordou como usar o Dispatcher de trabalho do Firebase para executar o trabalho de forma inteligente em segundo plano. Ele discutiu como encapsular o trabalho a ser executado como um `JobService` e como usar o `FirebaseJobDispatcher` agendar esse trabalho, especificando os critérios com um `JobTrigger` e como as falhas devem ser tratadas com uma `RetryStrategy`.


## <a name="related-links"></a>Links relacionados

- [Xamarin.Firebase.JobDispatcher on NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [o firebase--dispatcher de trabalho no GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Agendamento de trabalho inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Android bateria e otimizações de memória – 2016 de e/s do Google (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
