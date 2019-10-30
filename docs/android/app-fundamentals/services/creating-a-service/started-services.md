---
title: Serviços iniciados com Xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: f5b5f8cf224c18852a0a0e7e4f591b49905ba026
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024769"
---
# <a name="started-services-with-xamarinandroid"></a>Serviços iniciados com Xamarin. Android

## <a name="started-services-overview"></a>Visão geral dos serviços iniciados

Os serviços iniciados normalmente executam uma unidade de trabalho sem fornecer nenhum comentário ou resultado direto ao cliente. Um exemplo de uma unidade de trabalho é um serviço que carrega um arquivo em um servidor. O cliente fará uma solicitação a um serviço para carregar um arquivo do dispositivo para um site. O serviço carregará silenciosamente o arquivo (mesmo se o aplicativo não tiver atividades em primeiro plano) e terminará quando o carregamento for concluído. É importante perceber que um serviço iniciado será executado no thread da interface do usuário de um aplicativo. Isso significa que, se um serviço for executar o trabalho que bloqueará o thread da interface do usuário, ele deverá criar e descartar os threads conforme necessário.

Ao contrário de um serviço associado, não há nenhum canal de comunicação entre um serviço "puro" iniciado e seus clientes. Isso significa que um serviço iniciado implementará alguns métodos de ciclo de vida diferentes do que um serviço associado. A lista a seguir destaca os métodos comuns de ciclo de vida em um serviço iniciado:

- `OnCreate` &ndash; chamado uma vez quando o serviço é iniciado pela primeira vez. É aqui que o código de inicialização deve ser implementado.
- `OnBind` &ndash; esse método deve ser implementado por todas as classes de serviço, no entanto, um serviço iniciado normalmente não tem um cliente associado a ele. Por isso, um serviço iniciado apenas retorna `null`. Por outro lado, um serviço híbrido (que é a combinação de um serviço vinculado e um serviço iniciado) precisa implementar e retornar um `Binder` para o cliente.
- `OnStartCommand` &ndash; chamado para cada solicitação para iniciar o serviço, seja em resposta a uma chamada para `StartService` ou reinicialização pelo sistema. É aí que o serviço pode iniciar qualquer tarefa de execução longa. O método retorna um valor `StartCommandResult` que indica como ou se o sistema deve lidar com a reinicialização do serviço após um desligamento devido à memória insuficiente. Essa chamada ocorre no thread principal. Esse método é descrito mais detalhadamente abaixo.
- `OnDestroy` &ndash; esse método é chamado quando o serviço está sendo destruído. Ele é usado para executar qualquer limpeza final necessária.

O método importante para um serviço iniciado é o método `OnStartCommand`. Ele será invocado toda vez que o serviço receber uma solicitação para realizar algum trabalho. O trecho de código a seguir é um exemplo de `OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

O primeiro parâmetro é um objeto `Intent` que contém os metadados sobre o trabalho a ser executado. O segundo parâmetro contém um valor `StartCommandFlags` que fornece algumas informações sobre a chamada do método. Esse parâmetro tem um dos dois valores possíveis:

- `StartCommandFlag.Redelivery` &ndash; isso significa que o `Intent` é uma nova entrega de uma `Intent` anterior. Esse valor é fornecido quando o serviço retornou `StartCommandResult.RedeliverIntent`, mas foi interrompido antes de ser desligado corretamente.
- `StartCommandFlag.Retry` &dash; esse valor é recebido quando uma chamada de `OnStartCommand` anterior falhou e o Android está tentando iniciar o serviço novamente com a mesma intenção que a tentativa anterior com falha.

Por fim, o terceiro parâmetro é um valor inteiro que é exclusivo para o aplicativo que identifica a solicitação. É possível que vários chamadores invoquem o mesmo objeto de serviço. Esse valor é usado para associar uma solicitação para interromper um serviço com uma determinada solicitação para iniciar um serviço. Ele será discutido mais detalhadamente na seção [parando o serviço](#Stopping_the_Service). 

O valor `StartCommandResult` é retornado pelo serviço como uma sugestão ao Android sobre o que fazer se o serviço for eliminado devido a restrições de recursos. Há três valores possíveis para `StartCommandResult`:

- **[StartCommandResult. não adesivo](xref:Android.App.StartCommandResult.NotSticky)** &ndash; esse valor informa ao Android que não é necessário reiniciar o serviço que foi eliminado. Como exemplo disso, considere um serviço que gera miniaturas para uma galeria em um aplicativo. Se o serviço for eliminado, não é crucial recriar a miniatura imediatamente &ndash; a miniatura pode ser recriada na próxima vez em que o aplicativo for executado.
- **[StartCommandResult. adesivo](xref:Android.App.StartCommandResult.Sticky)** &ndash; isso informa ao Android para reiniciar o serviço, mas não para entregar a última intenção que iniciou o serviço. Se não houver nenhuma intenção pendente para manipular, um `null` será fornecido para o parâmetro de intenção. Um exemplo disso pode ser um aplicativo de player de música; o serviço será reiniciado pronto para reproduzir música, mas tocará a última música.
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; esse valor será instruir o Android a reiniciar o serviço e entregar novamente a última `Intent`. Um exemplo disso é um serviço que baixa um arquivo de dados para um aplicativo. Se o serviço for eliminado, o arquivo de dados ainda precisará ser baixado. Ao retornar `StartCommandResult.RedeliverIntent`, quando o Android reinicia o serviço, ele também fornecerá a intenção (que mantém a URL do arquivo a ser baixado) para o serviço. Isso permitirá que o download seja reiniciado ou retomado (dependendo da implementação exata do código).

Há um quarto valor para `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`. Esse valor é retornado por `OnStartCommand` e descreve como o Android continuará o serviço que foi eliminado. Esse valor normalmente não é usado para iniciar um serviço.

Os eventos de ciclo de vida da chave de um serviço iniciado são mostrados neste diagrama: 

![Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados](started-services-images/started-service-01.png "Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados.")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Parando o serviço

Um serviço iniciado continuará em execução indefinidamente; O Android manterá o serviço em execução contanto que haja recursos suficientes do sistema. O cliente deve parar o serviço ou o serviço pode parar a si mesmo quando terminar seu trabalho. Há duas maneiras de interromper um serviço: 

- **[Android. Content. Context. StopService ()](xref:Android.Content.Context.StopService*)** &ndash; um cliente (como uma atividade) pode solicitar uma parada de serviço chamando o método `StopService`:

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. app. Service. StopSelf ()](xref:Android.App.Service.StopSelf*)** &ndash; um serviço pode ser desligado invocando o `StopSelf`:

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>Usando startId para interromper um serviço

Vários chamadores podem solicitar que um serviço seja iniciado. Se houver uma solicitação de início pendente, o serviço poderá usar o `startId` passado para `OnStartCommand` para impedir que o serviço seja interrompido prematuramente. O `startId` corresponderá à chamada mais recente para `StartService` e será incrementado a cada vez que for chamado. Portanto, se uma solicitação subsequente para `StartService` ainda não tiver resultado em uma chamada para `OnStartCommand`, o serviço poderá chamar `StopSelfResult`, passando o valor mais recente de `startId` recebido (em vez de simplesmente chamar `StopSelf`). Se uma chamada para `StartService` ainda não tiver resultado em uma chamada correspondente para `OnStartCommand`, o sistema não irá parar o serviço, pois o `startId` usado na chamada `StopSelf` não corresponderá à chamada de `StartService` mais recente.

## <a name="related-links"></a>Links relacionados

- [StartedServicesDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android. app. Service](xref:Android.App.Service)
- [Android. app. StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android. app. StartCommandResult](xref:Android.App.StartCommandResult)
- [Android. Content. BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android. Content. intuito](xref:Android.Content.Intent)
- [Android. OS. Handler](xref:Android.OS.Handler)
- [Android. widget. notificação](xref:Android.Widget.Toast)
- [Ícones da barra de status](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
