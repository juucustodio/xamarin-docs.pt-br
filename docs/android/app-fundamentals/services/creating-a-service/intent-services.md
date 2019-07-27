---
title: Serviços de intenção no Xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4c868623ae08ac1366c1c9ea55c8d635f0a6a061
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509132"
---
# <a name="intent-services-in-xamarinandroid"></a>Serviços de intenção no Xamarin. Android

## <a name="intent-services-overview"></a>Visão geral dos serviços de intenção

Os serviços iniciados e vinculados são executados no thread principal, o que significa que, para manter o desempenho suave, um serviço precisa executar o trabalho de forma assíncrona. Uma das maneiras mais simples de resolver essa preocupação é com um _padrão de processador de fila de trabalho_, em que o trabalho a ser feito é colocado em uma fila que é atendida por um único thread.

O [`IntentService`](xref:Android.App.IntentService) é uma subclasse `Service` da classe que fornece uma implementação específica do Android desse padrão. Ele gerenciará o trabalho de enfileiramento, inicializando um thread de trabalho para atender à fila e retirando as solicitações da fila a ser executada no thread de trabalho. Um `IntentService` se interromperá silenciosamente e removerá o thread de trabalho quando não houver mais trabalho na fila.

O trabalho é enviado para a fila criando um `Intent` e, em seguida `Intent` , passando `StartService` -o para o método.

Não é possível parar ou interromper o `OnHandleIntent` método `IntentService` enquanto ele está funcionando. Devido a esse design, um `IntentService` deve ser mantido sem &ndash; monitoração de estado e não deve confiar em uma conexão ativa ou comunicação do restante do aplicativo. Um `IntentService` é destinado a processar as solicitações de trabalho sem monitoração de estado.

Há dois requisitos para a subclasse `IntentService`:

1. O novo tipo (criado por subclasse `IntentService`) apenas substitui o `OnHandleIntent` método.
2. O construtor para o novo tipo requer uma cadeia de caracteres que é usada para nomear o thread de trabalho que tratará as solicitações. O nome desse thread de trabalho é usado principalmente ao depurar o aplicativo.

O código a seguir mostra `IntentService` uma implementação com o `OnHandleIntent` método substituído:

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

O trabalho é enviado para `IntentService` um instanciando um `Intent` e, em seguida [`StartService`](xref:Android.Content.Context.StartService*) , chamando o método com essa intenção como um parâmetro. A intenção será passada para o serviço como um parâmetro no `OnHandleIntent` método. Este trecho de código é um exemplo de envio de uma solicitação de trabalho para uma intenção: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

O `IntentService` pode extrair os valores da intenção, conforme demonstrado neste trecho de código:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>Links relacionados

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
