---
title: Serviços de intenção no xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013104"
---
# <a name="intent-services-in-xamarinandroid"></a>Serviços de intenção no xamarin. Android

## <a name="intent-services-overview"></a>Visão geral de serviços de intenção

Ambos iniciado e associado serviços são executados no thread principal, o que significa que, para manter o desempenho suave, um serviço precisa executar o trabalho de forma assíncrona. Uma das maneiras mais simples de abordar essa preocupação é com um _padrão de processador da fila de trabalho_, em que o trabalho a ser feita é colocado em uma fila que é atendida por um único thread. 

O [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) é uma subclasse do `Service` classe que fornece uma implementação específica do Android desse padrão. Ele irá gerenciar o trabalho de enfileiramento, iniciar um thread de trabalho para a fila de serviço e solicitações de obtenção desativado a fila a ser executado no thread de trabalho. Um `IntentService` parará em si e remover o thread de trabalho quando não há nada mais trabalho na fila no modo silencioso.
 
Trabalho é enviado para a fila com a criação de um `Intent` e, em seguida, passando `Intent` para o `StartService` método.

Não é possível interromper ou o `OnHandleIntent` método `IntentService` enquanto ele está funcionando. Por causa desse design, uma `IntentService` deve ser mantida sem monitoração de estado &ndash; não deve depender uma conexão ativa ou a comunicação do restante do aplicativo. Um `IntentService` destina-se para statelessly processar as solicitações de trabalho.

Há dois requisitos para a criação de subclasses `IntentService`:

1. O novo tipo (criado pelo subclassificação `IntentService`) apenas substitui o `OnHandleIntent` método.
2. O construtor para o novo tipo requer uma cadeia de caracteres que é usada para nomear o thread de trabalho que manipulará as solicitações. O nome desse thread de trabalho é usado principalmente ao depurar o aplicativo.

O código a seguir mostra uma `IntentService` implementação com substituído `OnHandleIntent` método:

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

Trabalho é enviado para um `IntentService` instanciando um `Intent` e, em seguida, chamando o [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) método com essa intenção de como um parâmetro. A intenção será passada para o serviço como um parâmetro no `OnHandleIntent` método. Este trecho de código é um exemplo de envio de uma solicitação de trabalho para um propósito: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

O `IntentService` pode extrair os valores a intenção, conforme demonstrado neste trecho de código:  

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
