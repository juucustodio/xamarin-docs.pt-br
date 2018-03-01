---
title: "Serviços iniciados com xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b6c0e67e3411aa5b7846a1b7bc0de2473a3546fd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="started-services-with-xamarinandroid"></a>Serviços iniciados com xamarin

## <a name="started-services-overview"></a>Visão geral de serviços iniciados

Serviços iniciados normalmente executam uma unidade de trabalho sem fornecer nenhum feedback direto ou resultados ao cliente. Um exemplo de uma unidade de trabalho é um serviço que carrega um arquivo em um servidor. O cliente irá fazer uma solicitação a um serviço para carregar um arquivo do dispositivo para um site. O serviço silenciosamente carregar o arquivo (mesmo se o aplicativo não tem atividades em primeiro plano) e quando o carregamento for concluído. É importante observar que um serviço iniciado será executado no thread da interface do usuário de um aplicativo. Isso significa que, se um serviço executar o trabalho que bloqueia o thread de interface do usuário, ele deve criar e descartar threads conforme necessário.

Ao contrário de um serviço vinculado, não há nenhum canal de comunicação entre um serviço iniciado "puro" e seus clientes. Isso significa que um serviço iniciado implementar alguns métodos de ciclo de vida diferente de um serviço vinculado. A lista a seguir destaca os métodos de ciclo de vida comuns em um serviço iniciado:

* `OnCreate` &ndash; Chamado uma vez quando o serviço é iniciado pela primeira vez. Isso é onde o código de inicialização deve ser implementado.
* `OnBind` &ndash; Esse método deve ser implementado por todas as classes de serviço, no entanto, um serviço iniciado normalmente não tem um cliente associado a ele. Por isso, um serviço iniciado apenas retorna `null`. Por outro lado, um serviço híbrido (que é a combinação de um serviço vinculado e um serviço iniciado) tem que implementar e retornar um `Binder` para o cliente.
* `OnStartCommand` &ndash; Chamado para cada solicitação para iniciar o serviço, em resposta a uma chamada para `StartService` ou uma reinicialização do sistema. Isso é onde o serviço pode começar a todas as tarefas de longa execução. O método retorna um `StartCommandResult` valor que indica como ou se o sistema deve tratar de reiniciar o serviço após um desligamento devido à memória insuficiente. Essa chamada ocorre no thread principal. Esse método é descrito em mais detalhes abaixo.
* `OnDestroy` &ndash; Esse método é chamado quando o serviço está sendo destruído. Ele é usado para executar qualquer final limpeza necessária.

O método importante para um serviço iniciado é o `OnStartCommand` método. Ele será invocado sempre que o serviço recebe uma solicitação para fazer algum trabalho. O trecho de código a seguir é um exemplo de `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

O primeiro parâmetro é um `Intent` objeto que contém os metadados sobre o trabalho para executar. O segundo parâmetro contém um `StartCommandFlags` valor que fornece algumas informações sobre a chamada do método. Esse parâmetro tem um dos dois valores possíveis:

* `StartCommandFlag.Redelivery` &ndash; Isso significa que o `Intent` é uma re-entrega de anterior `Intent`. Esse valor é fornecido quando o serviço retornou `StartCommandResult.RedeliverIntent` , mas foi interrompido antes que ele pode ser desligado corretamente.
* `StartCommandFlag.Retry` &dash; Esse valor é recebido quando anterior `OnStartCommand` Falha na chamada e Android está tentando iniciar o serviço novamente com a mesma intenção como a tentativa anterior com falha.
 
Por fim, o terceiro parâmetro é um valor inteiro que é exclusivo para o aplicativo que identifica a solicitação. É possível que várias chamadores podem chamar o mesmo objeto de serviço. Esse valor é usado para associar uma solicitação para parar um serviço com uma determinada solicitação para iniciar um serviço. Ele será abordado mais detalhadamente na seção [parar o serviço](#Stopping_the_Service). 

O valor `StartCommandResult` é retornado pelo serviço como uma sugestão para Android sobre o que fazer se o serviço for interrompido devido a restrições de recursos. Há três valores possíveis para `StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash; esse valor informa Android que não é necessário reiniciar o serviço que foi eliminado. Como um exemplo disso, considere um serviço que gera miniaturas para uma galeria de um aplicativo. Se o serviço for interrompido, não é crucial para recriar a miniatura imediatamente &ndash; a miniatura pode ser recriada na próxima vez que o aplicativo é executado.
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash; isso diz ao Android para reiniciar o serviço, mas não para entregar a última tentativa de iniciou o serviço. Se não houver nenhum tentativas pendentes para lidar com, em seguida, um `null` será fornecido para o parâmetro de método. Um exemplo disso pode ser um aplicativo de player de música; o serviço reiniciará pronto para reproduzir música, mas ele será executado a última música. 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash; esse valor será informar o Android para reiniciar o serviço e entregar novamente a última `Intent`. Um exemplo disso é um serviço que baixa um arquivo de dados para um aplicativo. Se o serviço for interrompido, o arquivo de dados ainda precisa ser baixados. Retornando `StartCommandResult.RedeliverIntent`, quando Android reinicia o serviço também fornecerá a intenção (que contém a URL do arquivo a ser baixado) para o serviço. Isso permitirá que o download reiniciar ou retomar (dependendo da implementação exata do código).

Há um quarto valor para `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Esse valor é retornado por `OnStartCommand` e descreve como Android continuará o serviço foi interrompido. Normalmente, esse valor não é usado para iniciar um serviço.

Os eventos de ciclo de vida de um serviço iniciado são mostrados neste diagrama: 

![Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados](started-services-images/started-service-01.png "um diagrama que mostra a ordem em que são chamados os métodos de ciclo de vida.")


## <a name="stopping-the-service"></a>Parar o serviço

Um serviço iniciado continuarão sendo executadas indefinidamente; Android manterá o serviço em execução desde que haja recursos suficientes do sistema. O cliente deve parar o serviço ou o serviço pode parar quando tiver terminado seu trabalho. Há duas maneiras para interromper um serviço: 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; um cliente (como uma atividade) pode solicitar um serviço interrompido chamar o `StopService` método: 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash; um serviço pode encerrá-lo ao chamar o `StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>Usando startId para parar um serviço

Vários chamadores podem solicitar que um serviço ser iniciado. Se houver uma solicitação pendente de início, o serviço pode usar o `startId` que é passado para `OnStartCommand` para impedir que o serviço está sendo interrompido prematuramente. O `startId` corresponderá à última chamada para `StartService`e será incrementado toda vez que ele é chamado. Portanto, se uma solicitação subsequente para `StartService` ainda não resultou em uma chamada para `OnStartCommand`, o serviço pode chamar `StopSelfResult`, passando o valor mais recente do `startId` recebeu (em vez de simplesmente chamar `StopSelf`). Se uma chamada para `StartService` ainda não resultou em uma chamada correspondente para `OnStartCommand`, o sistema não irá parar o serviço, porque o `startId` usado no `StopSelf` não corresponderão chamada para a versão mais recente `StartService` chamar.


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
