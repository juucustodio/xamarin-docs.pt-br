---
title: Agendador de trabalhos do Android
description: Este guia aborda como agendar um trabalho em segundo plano usando a API do Agendador de trabalho Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: d69a96ee55b09ef9fcf1485ec34d986dd40e7662
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977981"
---
# <a name="android-job-scheduler"></a>Agendador de trabalhos do Android

_Este guia aborda como agendar um trabalho em segundo plano usando a API do Agendador de trabalho Android, que está disponível em dispositivos Android que executam o Android 5.0 (API nível 21) e superior._


## <a name="overview"></a>Visão geral 

Uma das melhores maneiras de manter um aplicativo Android responsiva ao usuário é garantir que o trabalho de execução longa ou complexo é realizado em segundo plano. No entanto, é importante que o trabalho de plano de fundo não afetará negativamente a experiência do usuário com o dispositivo. 

Por exemplo, um trabalho em segundo plano pode sondar um site cada três ou quatro minutos a consulta para alterações em um determinado conjunto de dados. Isso parece benigno, no entanto, ele teria um impacto desastroso na vida útil da bateria. O aplicativo será repetidamente acorde o dispositivo, elevar a CPU para um estado de energia mais alto, ligar os rádios, fazer as solicitações de rede e, em seguida, processar os resultados. Isso é pior, porque o dispositivo será imediatamente não desligue e retornar para o estado ocioso de baixa energia. Trabalho em segundo plano agendado mal pode inadvertidamente manter o dispositivo em um estado com os requisitos de energia excessivos e desnecessários. Essa atividade aparentemente inocente (um site de sondagem) será renderizado o dispositivo inutilizável em um período relativamente curto de tempo.

Android fornece as seguintes APIs para ajudá-lo executando o trabalho em segundo plano, mas por si só não são suficientes para o agendamento de trabalho inteligente. 

* **[Serviços de intenção](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**  &ndash; intenção serviços são ótimos para execução do trabalho, no entanto, eles fornecem nenhuma maneira de agendar o trabalho.
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash; essas APIs permitem que somente o trabalho ser agendado, mas não fornecer nenhuma maneira de realmente executar o trabalho. Além disso, o AlarmManager só permite a restrições de tempo com base, que significa que aciona um alarme em um determinado momento ou após um determinado período de tempo decorrido. 
* **[Receptores de difusão](~/android/app-fundamentals/broadcast-receivers.md)**  &ndash; Android de um aplicativo pode configurar para executar o trabalho em resposta a eventos de todo o sistema ou propósitos de receptores de difusão. No entanto, os receptores de difusão não fornecem nenhum controle sobre quando o trabalho deve ser executado. Também restringirá as alterações no sistema operacional Android quando funcionam de receptores de difusão ou os tipos de trabalho que eles possam responder às. 

Há dois recursos principais para executar com eficiência o trabalho em segundo plano (também conhecido como um _trabalho em segundo plano_ ou um _trabalho_):

1. **Com inteligência agendamento do trabalho** &ndash; é importante que quando um aplicativo estiver executando um trabalho em segundo plano que ele faz isso como um bom cidadão. O ideal é que o aplicativo não deve exigir que um trabalho seja executada. Em vez disso, o aplicativo deve especificar as condições que devem ser atendidas para que quando o trabalho pode ser executado e, em seguida, agendar o trabalho com o sistema operacional que executará o trabalho quando as condições forem atendidas. Isso permite que o Android executar o trabalho para garantir a máxima eficiência no dispositivo. Por exemplo, solicitações de rede podem ser divididas em lotes para executar tudo ao mesmo tempo utilizar ao máximo de sobrecarga envolvida com a rede.
2. **Encapsulando o trabalho** &ndash; o código para executar o trabalho em segundo plano deve ser encapsulado em um componente discreto que pode ser executado independentemente da interface do usuário e será relativamente fácil reagendar se o trabalho não for concluída por algum motivo.

O Agendador de trabalho Android é uma estrutura integrada do sistema operacional Android que fornece uma API fluente para simplificar o trabalho de agendamento em segundo plano.  O Agendador de trabalho Android consiste dos seguintes tipos:

* O `Android.App.Job.JobScheduler` é um serviço de sistema que é usado para agendar, executar e Cancelar se necessário, trabalhos em nome de um aplicativo do Android.
* Um `Android.App.Job.JobService` é uma classe abstrata que deve ser estendida com a lógica que executará o trabalho no thread principal do aplicativo. Isso significa que o `JobService` é responsável como o trabalho é para ser executada de forma assíncrona.
* Um `Android.App.Job.JobInfo` objeto contém os critérios para orientar o Android quando o trabalho deve ser executado.

Para agendar o trabalho com o Agendador de trabalho Android, um aplicativo xamarin. Android deve encapsular o código em uma classe que estende o `JobService` classe. `JobService` tem três métodos de ciclo de vida que podem ser chamados durante o tempo de vida do trabalho:

* **bool OnStartJob (parâmetros JobParameters)** &ndash; esse método é chamado pelo `JobScheduler` para executar o trabalho e é executado no thread principal do aplicativo. É responsabilidade do `JobService` assincronamente realizar o trabalho e `true` se não houver trabalho restante, ou `false` se o trabalho é feito.
    
    Quando o `JobScheduler` chama esse método, ele solicitará e manter um wakelock do Android para a duração do trabalho. Quando o trabalho for concluído, é responsabilidade do `JobService` para informar o `JobScheduler` desse fato pela chamada a `JobFinished` método (descrito a seguir).

* **JobFinished (JobParameters parâmetros, bool needsReschedule)** &ndash; esse método deve ser chamado `JobService` para informar o `JobScheduler` que o trabalho é feito. Se `JobFinished` não for chamado, o `JobScheduler` não removerá o wakelock, fazendo com que a bateria desnecessários. 

* **bool (JobParameters parâmetros) de OnStopJob** &ndash; isso é chamado quando o trabalho é interrompido prematuramente pelo Android. Ele deverá retornar `true` se o trabalho deve ser reagendado com base em critérios de repetição (discutidos em mais detalhes abaixo).

É possível especificar _restrições_ ou _disparadores_ que irá controlar quando um trabalho pode ou deve ser executado. Por exemplo, é possível restringir um trabalho de modo que ele será executado apenas quando o dispositivo está sendo carregada ou iniciar um trabalho quando uma imagem é obtido.

Este guia discute detalhadamente como implementar uma `JobService` de classe e programá-lo com o `JobScheduler`.

## <a name="requirements"></a>Requisitos

O Agendador de trabalho Android requer o nível de API do Android 21 (Android 5.0) ou superior. 

## <a name="using-the-android-job-scheduler"></a>Usando o Agendador de trabalhos do Android

Há três etapas para usar a API do Android JobScheduler:

1. Implemente um tipo JobService para encapsular o trabalho.
2. Use uma `JobInfo.Builder` objeto para criar o `JobInfo` objeto que conterá os critérios para o `JobScheduler` para executar o trabalho. 
3. Agendar o trabalho usando `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implementar um JobService

Todo o trabalho realizado pela biblioteca do Agendador de trabalho Android deve ser feito em um tipo que estende o `Android.App.Job.JobService` classe abstrata. Criando um `JobService` é muito semelhante à criação de um `Service` com a estrutura do Android: 

1. Estender o `JobService` classe.
2. Decore a subclasse com o `ServiceAttribute` e defina o `Name` parâmetro para uma cadeia de caracteres que é composto do nome do pacote e o nome da classe (consulte o exemplo a seguir).
3. Defina a `Permission` propriedade no `ServiceAttribute` na cadeia de caracteres `android.permission.BIND_JOB_SERVICE`.
4. Substituir o `OnStartJob` método, adicionando o código para executar o trabalho. Android irá chamar esse método no thread principal do aplicativo para executar o trabalho. Trabalho que levará mais tempo do que alguns milissegundos deverá ser executados em um thread para evitar o bloqueio de aplicativo.
5. Quando o trabalho for concluído, o `JobService` deve chamar o `JobFinished` método. Esse método é como `JobService` informa o `JobScheduler` este trabalho é feito. Falha ao chamar `JobFinished` resultará na `JobService` colocando demandas desnecessárias no dispositivo, diminuindo a vida útil da bateria. 
6. É recomendável também substituir o `OnStopJob` método. Este método é chamado pelo Android quando o trabalho está sendo desligado antes de ser concluído e fornece o `JobService` a oportunidade de descarta todos os recursos adequadamente. Esse método deverá retornar `true` se for necessário reagendar o trabalho, ou `false` se não for desejável para executar novamente o trabalho.
   

O código a seguir é um exemplo das mais simples `JobService` para um aplicativo usando a TPL para assincronamente executar algum trabalho:

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

Aplicativos xamarin. Android não criar uma instância de um `JobService` diretamente, em vez disso, eles serão passar uma `JobInfo` do objeto para o `JobScheduler`. O `JobScheduler` instanciará o solicitada `JobService` objeto, agendamento e execução a `JobService` acordo com os metadados no `JobInfo`. Um `JobInfo` objeto deve conter as seguintes informações:

* **JobId** &ndash; esse é um `int` valor é usado para identificar um trabalho para o `JobScheduler`. Reutilizar esse valor atualizará todos os trabalhos existentes. O valor deve ser exclusivo para o aplicativo. 
* **JobService** &ndash; esse parâmetro é um `ComponentName` que identifica explicitamente o tipo que o `JobScheduler` deve usar para executar um trabalho. 
  

Esse método de extensão demonstra como criar uma `JobInfo.Builder` com um Android `Context`, como uma atividade:

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

Um recurso poderoso do Agendador de trabalho Android é a capacidade de controlar quando um trabalho é executado ou em quais condições um trabalho pode ser executado. A tabela a seguir descreve alguns dos métodos em `JobInfo.Builder` que permitem que um aplicativo influenciar quando um trabalho pode ser executado:  


|  Método | Descrição   |
|---|---|
| `SetMinimumLatency`  | Especifica que um atraso (em milissegundos) que deve ser observado antes de um trabalho é executado. |
| `SetOverridingDeadline`  | Declara o que o trabalho deve executar antes de decorrido esse tempo (em milissegundos). |
| `SetRequiredNetworkType`  | Especifica os requisitos de rede para um trabalho. |
| `SetRequiresBatteryNotLow` | O trabalho só pode ser executado quando o dispositivo não está exibindo um aviso de "bateria fraca" para o usuário. |
| `SetRequiresCharging` | O trabalho só pode ser executado quando a bateria está carregando. |
| `SetDeviceIdle` | O trabalho será executado quando o dispositivo está ocupado. |
| `SetPeriodic` | Especifica que o trabalho deve ser executado regularmente. |
| `SetPersisted` | O trabalho deve ser perisist entre as reinicializações do dispositivo. | 


O `SetBackoffCriteria` fornece algumas diretrizes sobre como do `JobScheduler` deve aguardar antes de tentar executar novamente um trabalho. Há duas partes para os critérios de retirada: um atraso em milissegundos (valor padrão de 30 segundos) e o tipo de retirada que deve ser usado (também conhecido como o _política de retirada_ ou o _política de repetição_) . As duas políticas são encapsuladas no `Android.App.Job.BackoffPolicy` enum:

* `BackoffPolicy.Exponential` &ndash; Uma política de retirada exponencial aumentará o valor inicial de retirada exponencialmente após cada falha. Na primeira vez que um trabalho falhar, a biblioteca aguardará o intervalo inicial que é especificado antes de reagendamento de trabalho – exemplo de 30 segundos. Na segunda vez que o trabalho falhar, a biblioteca aguardará pelo menos 60 segundos antes de tentar executar o trabalho. Depois que a terceira tentativa falha, a biblioteca de 120 segundos de espera e assim por diante. Este é o valor padrão.
* `BackoffPolicy.Linear` &ndash; Essa estratégia é uma retirada linear que o trabalho deve ser reagendado para execução em intervalos definidos (até obter êxito). Retirada linear é mais adequada para o trabalho que deve ser concluído assim que possível, ou para os problemas rapidamente resolver sozinho. 

Para obter mais detalhes sobre, crie uma `JobInfo` objeto, leia [documentação do Google para o `JobInfo.Builder` classe](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passando parâmetros para um trabalho por meio de JobInfo

Parâmetros são passados para um trabalho com a criação de um `PersistableBundle` que é passado junto com o `Job.Builder.SetExtras` método:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

O `PersistableBundle` é acessado a partir de `Android.App.Job.JobParameters.Extras` propriedade na `OnStartJob` método de um `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Agendar um trabalho

Para agendar um trabalho, um aplicativo xamarin. Android será obter uma referência para o `JobScheduler` serviço do sistema e a chamada a `JobScheduler.Schedule` método com o `JobInfo` objeto que foi criado na etapa anterior. `JobScheduler.Schedule` retornará imediatamente com um dos dois valores inteiros:

* **JobScheduler.ResultSuccess** &ndash; o trabalho foi agendado com êxito. 
* **JobScheduler.ResultFailure** &ndash; o trabalho não pôde ser agendado. Isso geralmente é causado por conflitos `JobInfo` parâmetros.

Esse código é um exemplo de um trabalho de agendamento e notificar o usuário dos resultados da tentativa de agendamento:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>Cancelar um trabalho

É possível cancelar todos os trabalhos que foram agendados, ou apenas um único trabalho usando o `JobsScheduler.CancelAll()` método ou o `JobScheduler.Cancel(jobId)` método:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Resumo

Este guia abordou como usar o Agendador de trabalho Android para executar o trabalho de forma inteligente em segundo plano. Ele discutiu como encapsular o trabalho a ser executado como um `JobService` e como usar o `JobScheduler` agendar esse trabalho, especificando os critérios com um `JobTrigger` e como as falhas devem ser tratadas com uma `RetryStrategy`.

## <a name="related-links"></a>Links relacionados

- [Agendamento de trabalho inteligente](https://developer.android.com/topic/performance/scheduling.html)
- [Referência da API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Agendamento de trabalhos como um profissional com JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android bateria e otimizações de memória – 2016 de e/s do Google (vídeo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
