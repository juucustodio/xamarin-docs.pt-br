---
title: Tentativa de serviços no xamarin
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 80849213649707615f8bd8e941e1a51c6b54e76e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="intent-services-in-xamarinandroid"></a>Tentativa de serviços no xamarin

## <a name="intent-services-overview"></a>Visão geral dos serviços de tentativa

Ambos iniciado e associado serviços são executados no thread principal, o que significa que, para manter o desempenho suave, um serviço precisa executar o trabalho de forma assíncrona. Uma das maneiras mais simples de abordar essa preocupação é com um _padrão de processador de fila de trabalho_, onde o trabalho a ser feito é colocado em uma fila que é atendida por um único thread. 

O [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) é uma subclasse do `Service` classe que fornece uma implementação específica Android desse padrão. Ele irá gerenciar o trabalho de fila, iniciar um thread de trabalho para a fila de serviço e solicitações de reunião desativada, a fila a ser executada no thread de trabalho. Um `IntentService` silenciosamente parar e remover o thread de trabalho quando não há mais trabalho na fila.
 
Trabalho é enviado para a fila, criando um `Intent` e, em seguida, passando `Intent` para o `StartService` método.

Não é possível interromper ou o `OnHandleIntent` método `IntentService` enquanto ele está funcionando. Por causa desse design, uma `IntentService` devem ser mantidos sem monitoração de estado &ndash; ele não deve confiar em uma conexão ativa ou a comunicação do restante do aplicativo. Um `IntentService` deve statelessly processar as solicitações de trabalho.

Há dois requisitos para subclassificação `IntentService`:

1. O novo tipo (criado pelo subclassificação `IntentService`) apenas substitui o `OnHandleIntent` método.
2. O construtor para o novo tipo requer uma cadeia de caracteres que é usada para nomear o thread de trabalho que tratará as solicitações. O nome deste thread de trabalho é usado principalmente ao depurar o aplicativo.

O código a seguir mostra um `IntentService` implementação com substituído `OnHandleIntent` método:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }
    
    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

Trabalho é enviado para um `IntentService` instanciando um `Intent` e, em seguida, chamar o [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) método com essa intenção como um parâmetro. A intenção será passada para o serviço como um parâmetro no `OnHandleIntent` método. Este trecho de código é um exemplo de como enviar uma solicitação de trabalho para um propósito: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

O `IntentService` pode extrair os valores de intenção, conforme demonstrado neste trecho de código:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Links relacionados

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
