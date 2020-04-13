---
title: Agendador de trabalhos do Android
description: Este guia discute como agendar o trabalho em segundo plano usando a API do Agendador de Empregos do Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 10d2ae6ac35f02d75ef6e04a0531ec3f5dafd668
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292610"
---
# <a name="android-job-scheduler"></a>Agendador de trabalhos do Android

_Este guia discute como agendar o trabalho em segundo plano usando a API do Programador de Trabalho android, que está disponível em dispositivos Android rodando Android 5.0 (Nível 21 da API) e superior._

## <a name="overview"></a>Visão geral 

Uma das melhores maneiras de manter um aplicativo Android responsivo ao usuário é garantir que o trabalho complexo ou de longa duração seja realizado em segundo plano. No entanto, é importante que o trabalho de fundo não impacte negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode fazer uma enquete em um site a cada três ou quatro minutos para consultar alterações em um determinado conjunto de dados. Isso parece benigno, no entanto, teria um impacto desastroso na vida útil da bateria. O aplicativo acordará repetidamente o dispositivo, elevará a CPU a um estado de potência mais alto, ligará os rádios, fará as solicitações de rede e, em seguida, processará os resultados. Piora porque o dispositivo não desliga imediatamente e retorna ao estado ocioso de baixa potência. O trabalho de fundo mal programado pode inadvertidamente manter o dispositivo em um estado com requisitos de energia desnecessários e excessivos. Esta atividade aparentemente inocente (pesquisando um site) tornará o dispositivo inutilizável em um período relativamente curto de tempo.

O Android fornece as seguintes APIs para ajudar a realizar o trabalho em segundo plano, mas por si só elas não são suficientes para agendamento inteligente de trabalho. 

- **[Os Serviços de Intenção](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; de Serviços são ótimos para a realização do trabalho, porém não fornecem nenhuma maneira de agendar o trabalho.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Essas APIs só permitem que o trabalho seja agendado, mas não fornecem nenhuma maneira de realmente executar o trabalho. Além disso, o AlarmManager só permite restrições baseadas no tempo, o que significa levantar um alarme em um determinado momento ou depois de um certo período de tempo. 
- **[Receptores de](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; transmissão Um aplicativo Android pode configurar receptores de transmissão para executar o trabalho em resposta a eventos ou intenções em todo o sistema. No entanto, os receptores de transmissão não fornecem qualquer controle sobre quando o trabalho deve ser executado. Além disso, as alterações no sistema operacional Android restringirão quando os receptores de transmissão funcionarem, ou os tipos de trabalho que eles podem responder. 

Existem duas características fundamentais para executar eficientemente o trabalho de fundo (às vezes referido como um _trabalho de fundo_ ou um _trabalho_):

1. **Agendamento inteligente do trabalho** &ndash; É importante que quando um aplicativo está fazendo um trabalho em segundo plano que o faça como um bom cidadão. Idealmente, o aplicativo não deve exigir que um trabalho seja executado. Em vez disso, o aplicativo deve especificar condições que devem ser atendidas para quando o trabalho pode ser executado e, em seguida, agendar esse trabalho com o sistema operacional que executará o trabalho quando as condições forem atendidas. Isso permite que o Android execute o trabalho para garantir a máxima eficiência no dispositivo. Por exemplo, as solicitações de rede podem ser executadas ao mesmo tempo para fazer o uso máximo das despesas gerais envolvidas com a rede.
2. **Encapsulamento do trabalho** &ndash; O código para executar o trabalho em segundo plano deve ser encapsulado em um componente discreto que pode ser executado independentemente da interface do usuário e será relativamente fácil de reagendar se o trabalho não for concluído por algum motivo.

O Android Job Scheduler é uma estrutura incorporada ao sistema operacional Android que fornece uma API fluente para simplificar o trabalho de plano de fundo de agendamento.  O Agendador de Empregos android consiste nos seguintes tipos:

- O `Android.App.Job.JobScheduler` é um serviço de sistema que é usado para agendar, executar e, se necessário, cancelar, trabalhos em nome de um aplicativo Android.
- Uma `Android.App.Job.JobService` é uma classe abstrata que deve ser estendida com a lógica que executará o trabalho no segmento principal da aplicação. Isso significa `JobService` que o trabalho deve ser realizado de forma assíncrona.
- Um `Android.App.Job.JobInfo` objeto detém os critérios para guiar o Android quando o trabalho deve ser executado.

Para agendar o trabalho com o Agendador de Empregos android, um aplicativo Xamarin.Android deve encapsular o código em uma classe que estende a `JobService` classe. `JobService`tem três métodos de ciclo de vida que podem ser chamados durante a vida do trabalho:

- **bool OnStartJob (parâmetros jobparameters)** &ndash; Este `JobScheduler` método é chamado pelo para executar o trabalho e é executado no segmento principal do aplicativo. É responsabilidade do `JobService` assíncrono realizar o trabalho `true` e retornar se `false` houver trabalho restante, ou se o trabalho for feito.
    
    Quando `JobScheduler` o método chamar, ele solicitará e reterá um wakelock do Android durante a duração do trabalho. Quando o trabalho é concluído, é `JobService` responsabilidade `JobScheduler` do dizer o `JobFinished` fato por chamada do método (descrito a seguir).

- **JobFinished (parâmetros jobparameters, bool needsReschedule)** &ndash; Este `JobService` método `JobScheduler` deve ser chamado pelo para dizer que o trabalho está feito. Se `JobFinished` não for `JobScheduler` chamado, o não removerá o wakelock, causando um dreno desnecessário da bateria. 

- **bool OnStopJob (parâmetros JobParameters)** &ndash; Isso é chamado quando o trabalho é interrompido prematuramente pelo Android. Ele deve `true` retornar se o trabalho deve ser reagendado com base nos critérios de repetição (discutidos abaixo com mais detalhes).

É possível especificar _restrições_ ou _gatilhos_ que controlarão quando um trabalho pode ou deve ser executado. Por exemplo, é possível restringir um trabalho para que ele só seja executado quando o dispositivo estiver carregando ou para iniciar um trabalho quando uma foto for tirada.

Este guia discutirá detalhadamente `JobService` como implementar uma `JobScheduler`aula e programá-la com o .

## <a name="requirements"></a>Requisitos

O Agendador de Trabalho android requer API Android nível 21 (Android 5.0) ou superior. 

## <a name="using-the-android-job-scheduler"></a>Usando o agendador de empregos do Android

Existem três etapas para usar a API do JobScheduler do Android:

1. Implemente um tipo jobservice para encapsular o trabalho.
2. Use `JobInfo.Builder` um objeto `JobInfo` para criar o objeto `JobScheduler` que manterá os critérios para executar o trabalho. 
3. Agende `JobScheduler.Schedule`o trabalho usando .

### <a name="implement-a-jobservice"></a>Implementar um JobService

Todo o trabalho realizado pela biblioteca Android Job Scheduler `Android.App.Job.JobService` deve ser feito em um tipo que estende a classe abstrata. Criar `JobService` um é muito `Service` semelhante à criação de um com a estrutura do Android: 

1. Estender `JobService` a aula.
2. Decore a subclasse `ServiceAttribute` com `Name` o e defina o parâmetro para uma string que é composta pelo nome do pacote e o nome da classe (veja o exemplo a seguir).
3. Defina `Permission` a `ServiceAttribute` propriedade na `android.permission.BIND_JOB_SERVICE`corda .
4. Anular o `OnStartJob` método, adicionando o código para executar o trabalho. O Android vai invocar este método no segmento principal do aplicativo para executar o trabalho. Trabalho que levará mais tempo para que alguns milissegundos sejam executados em um segmento para evitar o bloqueio da aplicação.
5. Quando o trabalho é `JobService` feito, `JobFinished` o deve chamar o método. Este método `JobService` é `JobScheduler` como diz o que o trabalho é feito. A falha `JobFinished` na chamada `JobService` resultará na colocação de exigências desnecessárias no dispositivo, diminuindo a vida útil da bateria. 
6. É uma boa ideia também `OnStopJob` substituir o método. Este método é chamado pelo Android quando o trabalho está `JobService` sendo encerrado antes de ser concluído e fornece a oportunidade de descartar adequadamente quaisquer recursos. Este método `true` deve retornar se for necessário reagendar o trabalho, ou `false` se não for desejável reexecutar o trabalho.

O código a seguir é `JobService` um exemplo do mais simples para um aplicativo, usando o TPL para executar assíncronamente alguns trabalhos:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Criando um JobInfo para agendar um trabalho

Os aplicativos Xamarin.Android não `JobService` instanciam um `JobInfo` diretamente, `JobScheduler`em vez disso eles passarão um objeto para o . O `JobScheduler` irá instanciar o objeto solicitado, `JobService` agendando e executando-o `JobService` de acordo com os metadados no `JobInfo`. Um `JobInfo` objeto deve conter as seguintes informações:

- **JobId** &ndash; este `int` é um valor que é `JobScheduler`usado para identificar um trabalho para o . A reutilização desse valor atualizará quaisquer trabalhos existentes. O valor deve ser único para a aplicação. 
- **JobService** &ndash; este parâmetro `ComponentName` é um que identifica explicitamente `JobScheduler` o tipo que o deve usar para executar um trabalho. 

Este método de extensão `JobInfo.Builder` demonstra `Context`como criar um com um Android, como uma Atividade:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Uma característica poderosa do Programador de Trabalho do Android é a capacidade de controlar quando um trabalho é executado ou sob quais condições um trabalho pode ser executado. A tabela a seguir descreve alguns dos métodos que `JobInfo.Builder` permitem que um aplicativo influencie quando um trabalho pode ser executado:  

|  Método | Descrição   |
|---|---|
| `SetMinimumLatency`  | Especifica que um atraso (em milissegundos) deve ser observado antes de um trabalho ser executado. |
| `SetOverridingDeadline`  | Declara que o trabalho deve ser executado antes que este tempo (em milissegundos) tenha transcorrido. |
| `SetRequiredNetworkType`  | Especifica os requisitos de rede para um trabalho. |
| `SetRequiresBatteryNotLow` | O trabalho só pode ser executado quando o dispositivo não estiver exibindo um aviso de "bateria fraca" para o usuário. |
| `SetRequiresCharging` | O trabalho só pode funcionar quando a bateria estiver carregando. |
| `SetDeviceIdle` | O trabalho será executado quando o dispositivo estiver ocupado. |
| `SetPeriodic` | Especifica que o trabalho deve ser executado regularmente. |
| `SetPersisted` | O trabalho deve perisist através de reinicializações de dispositivos. | 

O `SetBackoffCriteria` fornece algumas orientações `JobScheduler` sobre quanto tempo o deve esperar antes de tentar executar um trabalho novamente. Existem duas partes para os critérios de recuo: um atraso em milissegundos (valor padrão de 30 segundos) e um tipo de recuo que deve ser usado (às vezes referido como _a política de recuo_ ou a _política de repetição_). As duas políticas estão `Android.App.Job.BackoffPolicy` encapsuladas no enum:

- `BackoffPolicy.Exponential`&ndash; Uma política de recuo exponencial aumentará exponencialmente o valor inicial de recuo após cada falha. Na primeira vez que um trabalho falhar, a biblioteca aguardará o intervalo inicial especificado antes de reagendar o trabalho – exemplo 30 segundos. Na segunda vez que o trabalho falhar, a biblioteca esperará pelo menos 60 segundos antes de tentar executar o trabalho. Após a terceira tentativa fracassada, a biblioteca esperará 120 segundos, e assim por diante. Esse é o valor padrão.
- `BackoffPolicy.Linear`&ndash; Essa estratégia é um recuo linear que o trabalho deve ser reagendado para ser executado em intervalos definidos (até que ele tenha sucesso). O backoff linear é mais adequado para trabalhos que devem ser concluídos o mais rápido possível ou para problemas que se resolvam rapidamente. 

Para obter mais `JobInfo` detalhes sobre a criação de um objeto, leia [a documentação do Google para a `JobInfo.Builder` classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passando parâmetros para um trabalho através do JobInfo

Os parâmetros são passados `PersistableBundle` para um trabalho `Job.Builder.SetExtras` criando um que é passado junto com o método:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

O `PersistableBundle` é acessado `Android.App.Job.JobParameters.Extras` a partir `OnStartJob` da `JobService`propriedade no método de:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Agendar um trabalho

Para agendar um trabalho, um aplicativo Xamarin.Android receberá uma referência ao serviço do `JobScheduler` sistema e chamará o `JobScheduler.Schedule` método com o `JobInfo` objeto que foi criado na etapa anterior. `JobScheduler.Schedule`retornará imediatamente com um dos dois valores inteiros:

- **JobScheduler.ResultSuccess** &ndash; O trabalho foi agendado com sucesso. 
- **JobScheduler.ResultFailure** &ndash; O trabalho não pôde ser agendado. Isso é tipicamente causado `JobInfo` por parâmetros conflitantes.

Este código é um exemplo de agendamento de um trabalho e notificação ao usuário dos resultados da tentativa de agendamento:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>Cancelando um trabalho

É possível cancelar todos os trabalhos que foram agendados, `JobsScheduler.CancelAll()` ou `JobScheduler.Cancel(jobId)` apenas um único trabalho usando o método ou o método:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumo

Este guia discutiu como usar o Agendador de Empregos do Android para executar o trabalho de forma inteligente em segundo plano. Discutiu como encapsular o trabalho a `JobService` ser realizado como `JobScheduler` um e como usar o `JobTrigger` para agendar esse trabalho, `RetryStrategy`especificando os critérios com um e como as falhas devem ser tratadas com um .

## <a name="related-links"></a>Links relacionados

- [Agendamento inteligente de trabalho](https://developer.android.com/topic/performance/scheduling.html)
- [Referência de API do JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Agendar trabalhos como um profissional com JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Otimizações de bateria e memória do Android - Google I/O 2016 (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
