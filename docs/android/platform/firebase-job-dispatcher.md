---
title: Dispatcher de trabalho do Firebase
description: Este guia discute como agendar o trabalho em segundo plano usando a biblioteca do Dispatcher de trabalhos do firebase do Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: cb7e8aaca13405aedd422288421d497653ddbfe8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761198"
---
# <a name="firebase-job-dispatcher"></a>Dispatcher de trabalho do Firebase

_Este guia discute como agendar o trabalho em segundo plano usando a biblioteca do Dispatcher de trabalhos do firebase do Google._

## <a name="overview"></a>Visão geral

Uma das melhores maneiras de manter um aplicativo Android responsivo ao usuário é garantir que o trabalho complexo ou de longa execução seja executado em segundo plano. No entanto, é importante que o trabalho em segundo plano não afete negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode sondar um site a cada três ou quatro minutos para consultar alterações em um determinado conjunto de dado. Isso parece benigno, mas teria um impacto desastroso na vida útil da bateria. O aplicativo ativará repetidamente o dispositivo, elevará a CPU a um estado de energia mais alto, ligará as rádios, fará as solicitações de rede e, em seguida, processando os resultados. Isso é pior porque o dispositivo não será imediatamente desligado e retornará ao estado ocioso de baixa energia. Um trabalho em segundo plano agendado incorretamente pode manter o dispositivo inadvertidamente em um estado com requisitos de energia excessivas e desnecessários. Essa atividade aparentemente inocente (sondando um site) tornará o dispositivo inutilizável em um período de tempo relativamente curto.

O Android fornece as seguintes APIs para ajudar na execução do trabalho em segundo plano, mas por si só eles não são suficientes para o agendamento inteligente de trabalhos. 

- **[Serviços de intenção](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; Os serviços de intenção são ótimos para executar o trabalho, no entanto, eles não fornecem como agendar o trabalho.
- **[Alarmmanager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Essas APIs só permitem que o trabalho seja agendado, mas não fornecem como realmente realizar o trabalho. Além disso, o Alarmmanager permite apenas restrições baseadas em tempo, o que significa gerar um alarme em um determinado momento ou depois de um determinado período de tempo decorrido. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; O JobSchedule é uma excelente API que funciona com o sistema operacional para agendar trabalhos. No entanto, ele só está disponível para os aplicativos Android que têm como destino o nível de API 21 ou superior. 
- **[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Um aplicativo Android pode configurar receptores de difusão para executar o trabalho em resposta a eventos ou tentativas de todo o sistema. No entanto, os receptores de difusão não fornecem nenhum controle sobre quando o trabalho deve ser executado. Além disso, as alterações no sistema operacional Android serão restritas quando os receptores de transmissão funcionarem ou os tipos de trabalho aos quais eles podem responder. 

Há dois recursos principais para executar com eficiência o trabalho em segundo plano (às vezes chamado de _trabalho em segundo plano_ ou um _trabalho_):

1. **Agendando o trabalho de forma inteligente** &ndash; É importante que, quando um aplicativo estiver trabalhando em segundo plano, ele faça isso como um bom cidadão. O ideal é que o aplicativo não exija que um trabalho seja executado. Em vez disso, o aplicativo deve especificar condições que devem ser atendidas para quando o trabalho puder ser executado e, em seguida, agendar esse trabalho para ser executado quando as condições forem atendidas. Isso permite que o Android execute o trabalho de forma inteligente. Por exemplo, as solicitações de rede podem ser executadas em lotes para executar tudo ao mesmo tempo para fazer o uso máximo da sobrecarga envolvida na rede.
2. **Encapsulando o trabalho** &ndash; O código para executar o trabalho em segundo plano deve ser encapsulado em um componente discreto que pode ser executado independentemente da interface do usuário e será relativamente fácil reagendar se o trabalho não for concluído por algum motivo.

O Dispatcher do trabalho firebase é uma biblioteca do Google que fornece uma API fluente para simplificar o agendamento do trabalho em segundo plano. Ela deve ser a substituição do Google Cloud Manager. O Dispatcher do trabalho firebase consiste nas seguintes APIs:

- Uma `Firebase.JobDispatcher.JobService` é uma classe abstrata que deve ser estendida com a lógica que será executada no trabalho em segundo plano.
- Um `Firebase.JobDispatcher.JobTrigger` declara quando o trabalho deve ser iniciado. Normalmente, isso é expresso como uma janela de tempo, por exemplo, aguardar pelo menos 30 segundos antes de iniciar o trabalho, mas executar o trabalho em 5 minutos.
- Um `Firebase.JobDispatcher.RetryStrategy` contém informações sobre o que deve ser feito quando um trabalho não é executado corretamente. A estratégia de repetição especifica o tempo de espera antes de tentar executar o trabalho novamente. 
- Um `Firebase.JobDispatcher.Constraint` é um valor opcional que descreve uma condição que deve ser atendida antes que o trabalho possa ser executado, como o dispositivo está em uma rede ou um carregamento não medido.
- O `Firebase.JobDispatcher.Job` é uma API que unifica as APIs anteriores do para uma unidade de trabalho que pode ser agendada `JobDispatcher`pelo. A `Job.Builder` classe é usada para instanciar `Job`um.
- Um `Firebase.JobDispatcher.JobDispatcher` usa as três APIs anteriores para agendar o trabalho com o sistema operacional e para fornecer uma maneira de cancelar trabalhos, se necessário.

Para agendar o trabalho com o Dispatcher do trabalho firebase, um aplicativo Xamarin. Android deve encapsular o código em um tipo `JobService` que estende a classe. `JobService`tem três métodos de ciclo de vida que podem ser chamados durante o tempo de vida do trabalho:

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; Esse método é onde o trabalho ocorrerá e sempre deve ser implementado. Ele é executado no thread principal. Esse método retornará `true` se houver trabalho restante ou `false` se o trabalho for feito. 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; Isso é chamado quando o trabalho é interrompido por algum motivo. Ele deve retornar `true` se o trabalho deve ser reagendado para mais tarde.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** Esse método é chamado quando o `JobService` tiver concluído qualquer trabalho assíncrono. &ndash; 

Para agendar um trabalho, o aplicativo criará `JobDispatcher` uma instância de um objeto. Em seguida, `Job.Builder` um é usado para criar `Job` um objeto, que é fornecido para `JobDispatcher` o que tentará e agendará a execução do trabalho.

Este guia discutirá como adicionar o Dispatcher do trabalho firebase a um aplicativo Xamarin. Android e usá-lo para agendar o trabalho em segundo plano.

## <a name="requirements"></a>Requisitos

O Dispatcher do trabalho firebase exige o nível 9 ou superior da API do Android. A biblioteca do dispatcher do trabalho do firebase se baseia em alguns componentes fornecidos pelo Google Play Services; o dispositivo deve ter o Google Play Services instalado.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Usando a biblioteca do Dispatcher de trabalhos do firebase no Xamarin. Android

Para começar a usar o Dispatcher do trabalho firebase, primeiro adicione o [pacote do NuGet xamarin. firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) ao projeto Xamarin. Android. Pesquise o Gerenciador de pacotes NuGet para o pacote **Xamarin. firebase. JobDispatcher** (que ainda está em pré-lançamento).

Depois de adicionar a biblioteca do dispatcher do trabalho do `JobService` firebase, crie uma classe e, em seguida, agende- `FirebaseJobDispatcher`a para ser executada com uma instância do.

### <a name="creating-a-jobservice"></a>Criando um JobService

Todo o trabalho executado pela biblioteca do dispatcher do trabalho firebase deve ser feito em um tipo que `Firebase.JobDispatcher.JobService` estende a classe abstrata. Criar um `JobService` é muito semelhante a criar um `Service` com a estrutura do Android: 

1. Estender a `JobService` classe
2. Decorar a subclasse com o `ServiceAttribute`. Embora não seja estritamente necessário, é recomendável definir explicitamente o `Name` parâmetro para ajudar na depuração do `JobService`. 
3. Adicione um `IntentFilter` para declarar o `JobService` no **AndroidManifest. xml**. Isso também ajudará a biblioteca do Dispatcher de trabalhos do firebase a `JobService`localizar e invocar o.

O código a seguir é um exemplo do mais simples `JobService` para um aplicativo, usando a TPL para realizar um trabalho de forma assíncrona:

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

Antes que qualquer trabalho possa ser agendado, é necessário criar um `Firebase.JobDispatcher.FirebaseJobDispatcher` objeto. O `FirebaseJobDispatcher` é responsável por agendar `JobService`um. O trecho de código a seguir é uma maneira de criar uma instância `FirebaseJobDispatcher`do: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

No trecho de código anterior, a `GooglePlayDriver` classe is que ajuda a `FirebaseJobDispatcher` interagir com algumas das APIs de agendamento em Google Play Services no dispositivo. O parâmetro `context` é qualquer Android `Context`, como uma atividade. Atualmente, `GooglePlayDriver` o é a `IDriver` única implementação na biblioteca do dispatcher do trabalho do firebase. 

A associação do Xamarin. Android para o Dispatcher do trabalho firebase fornece um método `FirebaseJobDispatcher` `Context`de extensão para criar um a partir do: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Depois que `FirebaseJobDispatcher` o tiver sido instanciado, é possível criar um `Job` e `JobService` executar o código na classe. O `Job` é criado por um `Job.Builder` objeto e será discutido na próxima seção.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Criando um firebase. JobDispatcher. Job com o trabalho. Builder

A `Firebase.JobDispatcher.Job` classe é responsável por encapsular os metadados necessários para executar um `JobService`. Um`Job` contém informações como qualquer restrição que deve ser atendida antes que o trabalho possa ser executado, se `Job` o for recorrente ou qualquer gatilho que fará com que o trabalho seja executado.  Como um mínimo simples, um `Job` deve ter uma _marca_ (uma cadeia de caracteres exclusiva que identifica o trabalho `FirebaseJobDispatcher`para o) e o tipo `JobService` de que deve ser executado. O Dispatcher do trabalho firebase criará `JobService` uma instância do quando for hora de executar o trabalho.  Um `Job` é criado usando uma instância `Firebase.JobDispatcher.Job.JobBuilder` da classe. 

O trecho de código a seguir é o exemplo mais simples de como criar `Job` um usando a associação do Xamarin. Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

O `Job.Builder` executará algumas verificações de validação básicas nos valores de entrada para o trabalho. Uma exceção será gerada se não for possível para o `Job.Builder` criar um. `Job`  O `Job.Builder` criará um `Job` com os seguintes padrões:

- O `Job` _tempo de vida_ de um (quanto tempo será agendado para ser executado) é apenas até que o &ndash; dispositivo seja reinicializado depois que `Job` o dispositivo reinicializar o for perdido.
- R `Job` não está &ndash; recorrente, ele só será executado uma vez.
- Um `Job` será agendado para ser executado assim que possível.
- A estratégia de repetição padrão para `Job` um é usar uma _retirada exponencial_ (discutida em mais detalhes abaixo na seção [definindo um RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Agendando um trabalho

Depois de criar `Job`o, ele precisa ser agendado com `FirebaseJobDispatcher` o antes de ser executado. Há dois métodos para agendar um `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

O valor retornado por `FirebaseJobDispatcher.Schedule` será um dos seguintes valores inteiros:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; O`Job` foi agendado com êxito.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`Ocorreu algum problema desconhecido que impediu `Job` o de ser agendado. &ndash;
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`Um inválido `IDriver` foi usado ou o `IDriver` estava de alguma forma indisponível. &ndash; 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`Não há suporte para o `Trigger`. &ndash;
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; O serviço não está configurado corretamente ou não está disponível.

### <a name="configuring-a-job"></a>Configurando um trabalho

É possível personalizar um trabalho. Exemplos de como um trabalho pode ser personalizado incluem o seguinte:

- [Passando parâmetros para um trabalho](#Passing_Parameters_to_a_Job) &ndash; Um`Job` pode exigir valores adicionais para executar seu trabalho, por exemplo, baixar um arquivo.
- [Definir restrições](#Setting_Constraints) &ndash; Pode ser necessário apenas executar um trabalho quando determinadas condições forem atendidas. Por exemplo, só execute um `Job` quando o dispositivo estiver carregando. 
- [Especifique quando um `Job` deve executar](#Setting_Job_Triggers) &ndash; o Dispatcher de trabalho firebase permite que os aplicativos especifiquem uma hora em que o trabalho deve ser executado.  
- [Declarar uma estratégia de repetição para trabalhos com falha](#Setting_a_RetryStrategy) Uma estratégia de _repetição_ fornece orientação sobre `FirebaseJobDispatcher` o que fazer com `Jobs` o que não é concluído. &ndash; 

Cada um desses tópicos será discutido mais nas seções a seguir.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passando parâmetros para um trabalho

Os parâmetros são passados para um trabalho criando um `Bundle` que é passado junto com o `Job.Builder.SetExtras` método:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

O `Bundle` é acessado `IJobParameters.Extras` a partir da `OnStartJob` Propriedade no método:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Definindo restrições

As restrições podem ajudar a reduzir os custos ou drenagem de bateria no dispositivo. A `Firebase.JobDispatcher.Constraint` classe define essas restrições como valores inteiros:

- `Constraint.OnUnmeteredNetwork`&ndash; Só execute o trabalho quando o dispositivo estiver conectado a uma rede ilimitada. Isso é útil para impedir que o usuário incorra em encargos de dados.
- `Constraint.OnAnyNetwork`&ndash; Execute o trabalho em qualquer rede à qual o dispositivo está conectado. Se especificado junto com `Constraint.OnUnmeteredNetwork`, esse valor terá prioridade.
- `Constraint.DeviceCharging`&ndash; Execute o trabalho somente quando o dispositivo estiver carregando.

As restrições são definidas com `Job.Builder.SetConstraint` o método: 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

O `JobTrigger` fornece orientação para o sistema operacional sobre quando o trabalho deve ser iniciado. Um `JobTrigger` tem uma _janela de execução_ que define um horário agendado para `Job` quando o deve ser executado. A janela de execução tem um valor de _janela inicial_ e um valor de _janela final_ . A janela inicial é o número de segundos que o dispositivo deve aguardar antes de executar o trabalho e o valor da janela final é o número máximo de segundos a aguardar antes de executar o `Job`. 

Um `JobTrigger` pode ser criado com o `Firebase.Jobdispatcher.Trigger.ExecutionWindow` método.  Por exemplo `Trigger.ExecutionWindow(15,60)` , significa que o trabalho deve ser executado entre 15 e 60 segundos a partir de quando ele estiver agendado. O `Job.Builder.SetTrigger` método é usado para 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

O padrão `JobTrigger` para um trabalho é representado pelo valor `Trigger.Now`, que especifica que um trabalho seja executado assim que possível depois de ser agendado.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Configurando um RetryStrategy

O `Firebase.JobDispatcher.RetryStrategy` é usado para especificar a quantidade de atraso que um dispositivo deve usar antes de tentar executar novamente um trabalho com falha. Um `RetryStrategy` tem uma _política_, que define qual algoritmo de base de tempo será usado para reagendar o trabalho com falha e uma janela de execução que especifica uma janela na qual o trabalho deve ser agendado. Essa _janela de reagendamento_ é definida por dois valores. O primeiro valor é o número de segundos a aguardar antes de reagendar o trabalho (o valor _inicial de retirada_ ) e o segundo número é o número máximo de segundos antes que o trabalho deva ser executado (o valor _máximo de retirada_ ). 

Os dois tipos de políticas de repetição são identificados por esses valores int:

- `RetryStrategy.RetryPolicyExponential`Uma política de _retirada exponencial_ aumentará o valor inicial da retirada exponencialmente após cada falha. &ndash; Na primeira vez que um trabalho falhar, a biblioteca aguardará o intervalo de _initial especificado antes de reagendar &ndash; o exemplo de trabalho 30 segundos. Na segunda vez em que o trabalho falhar, a biblioteca aguardará pelo menos 60 segundos antes de tentar executar o trabalho. Após a terceira tentativa com falha, a biblioteca aguardará 120 segundos e assim por diante. O padrão `RetryStrategy` para a biblioteca do dispatcher do trabalho firebase é representado `RetryStrategy.DefaultExponential` pelo objeto. Ele tem uma retirada inicial de 30 segundos e uma retirada máxima de 3600 segundos.
- `RetryStrategy.RetryPolicyLinear`Essa estratégia é uma _retirada linear_ que o trabalho deve ser reagendado para ser executado em intervalos definidos (até que tenha sucesso). &ndash; A retirada linear é mais adequada para o trabalho que deve ser concluído assim que possível ou para problemas que serão resolvidos rapidamente. A biblioteca do dispatcher do trabalho firebase `RetryStrategy.DefaultLinear` define um que tem uma janela de reagendamento de, pelo menos, 30 segundos e até 3600 segundos.

É possível definir um personalizado `RetryStrategy` com o `FirebaseJobDispatcher.NewRetryStrategy` método. São necessários três parâmetros:

1. `int policy``RetryStrategy.RetryPolicyLinear` `RetryStrategy.RetryPolicyExponential` `RetryStrategy` A política é um dos valores anteriores, ou. &ndash;
2. `int initialBackoffSeconds`A _retirada inicial_ é um atraso, em segundos, necessário antes de tentar executar o trabalho novamente. &ndash; O valor padrão para isso é 30 segundos. 
3. `int maximumBackoffSeconds`O valor máximo de retirada declara o número máximo de segundos a serem atrasados antes de tentar executar o trabalho novamente. &ndash; O valor padrão é 3600 segundos. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Cancelando um trabalho

É possível cancelar todos os trabalhos que foram agendados ou apenas um único trabalho usando o `FirebaseJobDispatcher.CancelAll()` método ou o `FirebaseJobDispatcher.Cancel(string)` método:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Qualquer um dos métodos retornará um valor inteiro:

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; O trabalho foi cancelado com êxito.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Um erro impediu o trabalho de ser cancelado.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`O não pode cancelar o trabalho, pois não há nenhum disponível `IDriver`válido. &ndash; `FirebaseJobDispatcher`

## <a name="summary"></a>Resumo

Este guia abordou como usar o Dispatcher de trabalhos do firebase para realizar o trabalho de forma inteligente em segundo plano. Ele abordou como encapsular o trabalho a ser executado como um `JobService` e como usar o para `FirebaseJobDispatcher` agendar esse trabalho, especificando os critérios com um `JobTrigger` e como as falhas devem ser tratadas `RetryStrategy`com um.

## <a name="related-links"></a>Links relacionados

- [Xamarin. firebase. JobDispatcher no NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-Dispatcher de trabalho no GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher Binding](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Agendamento de trabalhos inteligentes](https://developer.android.com/topic/performance/scheduling.html)
- [Otimizações de memória e bateria do Android – Google I/O 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
