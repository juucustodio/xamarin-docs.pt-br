---
title: Serviços iniciados com o xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9e7dabf87314d87ab5ab335c220c0e292e56073b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110878"
---
# <a name="started-services-with-xamarinandroid"></a>Serviços iniciados com o xamarin. Android

## <a name="started-services-overview"></a>Visão geral de serviços iniciados

Serviços iniciados normalmente executam uma unidade de trabalho sem fornecer comentários diretos ou resultados ao cliente. Um exemplo de uma unidade de trabalho é um serviço que carrega um arquivo em um servidor. O cliente fará uma solicitação a um serviço para carregar um arquivo do dispositivo para um site. O serviço carregará o arquivo no modo silencioso (mesmo se o aplicativo não tem atividades em primeiro plano) e encerrado quando o upload for concluído. É importante perceber que um serviço iniciado será executado no thread da interface do usuário de um aplicativo. Isso significa que, se um serviço é executar o trabalho bloqueará o thread de interface do usuário, ele deve criar e descartar threads conforme necessário.

Ao contrário de um serviço vinculado, não há nenhum canal de comunicação entre um serviço iniciado "puro" e seus clientes. Isso significa que um serviço iniciado implementará alguns métodos de ciclo de vida diferente que um serviço vinculado. A lista a seguir destaca os métodos de ciclo de vida comum em um serviço iniciado:

* `OnCreate` &ndash; Chamado uma vez quando o serviço é iniciado pela primeira vez. Isso é onde o código de inicialização deve ser implementado.
* `OnBind` &ndash; Esse método deve ser implementado por todas as classes de serviço, no entanto, um serviço iniciado normalmente têm um cliente associado a ele. Por isso, um serviço iniciado apenas retorna `null`. Por outro lado, um serviço híbrido (que é a combinação de um serviço vinculado e um serviço iniciado) tem que implementar e retornar um `Binder` para o cliente.
* `OnStartCommand` &ndash; Chamado para cada solicitação para iniciar o serviço, em resposta a uma chamada para `StartService` ou uma reinicialização do sistema. Isso é onde o serviço pode começar a todas as tarefas de longa execução. O método retorna um `StartCommandResult` valor que indica como ou se o sistema deve tratar a reinicialização do serviço após um desligamento devido à memória insuficiente. Essa chamada ocorre no thread principal. Esse método é descrito mais detalhadamente abaixo.
* `OnDestroy` &ndash; Esse método é chamado quando o serviço está sendo destruído. Ele é usado para executar qualquer final limpeza necessária.

O método importante para um serviço iniciado é o `OnStartCommand` método. Ele será invocado sempre que o serviço recebe uma solicitação para fazer algum trabalho. O trecho de código a seguir está um exemplo de `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

O primeiro parâmetro é um `Intent` objeto que contém os metadados sobre o trabalho a ser executado. O segundo parâmetro contém um `StartCommandFlags` valor que fornece algumas informações sobre a chamada de método. Esse parâmetro tem um dos dois valores possíveis:

* `StartCommandFlag.Redelivery` &ndash; Isso significa que o `Intent` é uma re-entrega de um anterior `Intent`. Esse valor é fornecido quando o serviço retornou `StartCommandResult.RedeliverIntent` , mas foi interrompido antes que ele poderia ser corretamente desligado.
* `StartCommandFlag.Retry` &dash; Esse valor é recebido quando um anterior `OnStartCommand` chamada falhou e Android está tentando iniciar o serviço novamente com a mesma intenção como a falha na tentativa anterior.
 
Por fim, o terceiro parâmetro é um valor inteiro que é exclusivo para o aplicativo que identifica a solicitação. É possível que os chamadores vários podem invocar o mesmo objeto de serviço. Esse valor é usado para associar a uma solicitação para parar um serviço com uma determinada solicitação para iniciar um serviço. Ele será discutido mais detalhadamente na seção [parando o serviço](#Stopping_the_Service). 

O valor `StartCommandResult` é retornado pelo serviço como uma sugestão para Android sobre o que fazer se o serviço é interrompido devido a restrições de recursos. Há três valores possíveis para `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; esse valor informa Android que não é necessário reiniciar o serviço que ele foi interrompido. Como um exemplo disso, considere um serviço que gera miniaturas para uma galeria em um aplicativo. Se o serviço for interrompido, ele não é crucial para recriar a miniatura imediatamente &ndash; a miniatura pode ser recriada na próxima vez que o aplicativo é executado.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; isso informa ao Android para reiniciar o serviço, mas não para entregar a última tentativa de iniciou o serviço. Se não houver nenhum intenções pendentes para lidar com, em seguida, um `null` será fornecido para o parâmetro intencional. Um exemplo disso pode ser um aplicativo de player de música; o serviço reiniciará pronto para reproduzir música, mas ele será reproduzido a última música. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; esse valor será informe o Android para reiniciar o serviço e entregar novamente o último `Intent`. Um exemplo disso é um serviço que baixa um arquivo de dados para um aplicativo. Se o serviço for interrompido, o arquivo de dados ainda precisa ser baixado. Retornando `StartCommandResult.RedeliverIntent`, quando o Android reinicia o serviço também fornecerá a intenção (que contém a URL do arquivo para download) para o serviço. Isso permitirá que o download reiniciar ou retomar (dependendo da implementação exata do código).

Não há um quarto valor para `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Esse valor é retornado por `OnStartCommand` e descreve como Android continuará o serviço ele foi interrompido. Esse valor normalmente não é usado para iniciar um serviço.

Os eventos de ciclo de vida de um serviço iniciado são mostrados neste diagrama: 

![Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados](started-services-images/started-service-01.png "um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados.")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Parando o serviço

Um serviço iniciado continuará em execução indefinidamente; Android manterá o serviço em execução desde que haja recursos suficientes do sistema. O cliente deve interromper o serviço ou o serviço pode parar em si quando terminar seu trabalho. Há duas maneiras para interromper um serviço: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; cliente (por exemplo, uma atividade) pode solicitar um serviço ser interrompido chamar o `StopService` método: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; um serviço pode encerrá-lo, invocando o `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Usando startId para interromper um serviço

Os chamadores vários podem solicitar que um serviço ser iniciado. Se houver uma solicitação pendente de início, o serviço pode usar o `startId` que é passado para `OnStartCommand` para impedir que o serviço que está sendo interrompido prematuramente. O `startId` corresponderá à chamada mais recente para `StartService`e será incrementado toda vez que é chamado. Portanto, se uma solicitação subsequente para `StartService` ainda não resultaram em uma chamada para `OnStartCommand`, o serviço pode chamar `StopSelfResult`, passando o valor mais recente do `startId` recebeu (em vez de simplesmente chamar `StopSelf`). Se uma chamada para `StartService` ainda não resultaram em uma chamada correspondente para `OnStartCommand`, o sistema não irá parar o serviço, porque o `startId` usado no `StopSelf` chamada não corresponderão para o versão mais recente `StartService` chamar.


## <a name="related-links"></a>Links relacionados

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [Ícones da barra de status](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
