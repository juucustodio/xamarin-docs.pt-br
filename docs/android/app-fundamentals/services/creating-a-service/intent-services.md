---
title: Serviços de intenção no Xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 1fa1e5f06ebc847775a11c92ccdfc1055bee196a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070335"
---
# <a name="intent-services-in-xamarinandroid"></a>Serviços de intenção no Xamarin. Android

Os serviços iniciados e vinculados são executados no thread principal, o que significa que, para manter o desempenho suave, um serviço precisa executar o trabalho de forma assíncrona. Uma das maneiras mais simples de resolver essa preocupação é com um _padrão de processador de fila de trabalho_, em que o trabalho a ser feito é colocado em uma fila que é atendida por um único thread.

O [`IntentService`](xref:Android.App.IntentService) é uma subclasse da classe `Service` que fornece uma implementação específica do Android desse padrão. Ele gerenciará o trabalho de enfileiramento, inicializando um thread de trabalho para atender à fila e retirando as solicitações da fila a ser executada no thread de trabalho. Um `IntentService` se interromperá silenciosamente e removerá o thread de trabalho quando não houver mais trabalho na fila.

O trabalho é enviado para a fila criando uma `Intent` e, em seguida, passando essa `Intent` para o método `StartService`.

Não é possível parar ou interromper o método `OnHandleIntent` `IntentService` enquanto ele está funcionando. Devido a esse design, um `IntentService` deve ser mantido sem monitoração de estado &ndash; não deve depender de uma conexão ativa ou comunicação do resto do aplicativo. Um `IntentService` é destinado a processar as solicitações de trabalho sem monitoração de estado.

Há dois requisitos para `IntentService`de subclasse:

1. O novo tipo (criado por `IntentService`de subclasse) substitui apenas o método `OnHandleIntent`.
2. O construtor para o novo tipo requer uma cadeia de caracteres que é usada para nomear o thread de trabalho que tratará as solicitações. O nome desse thread de trabalho é usado principalmente ao depurar o aplicativo.

O código a seguir mostra uma implementação de `IntentService` com o método de `OnHandleIntent` substituído:

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

O trabalho é enviado para um `IntentService` instanciando um `Intent` e, em seguida, chamando o método [`StartService`](xref:Android.Content.Context.StartService*) com essa intenção como um parâmetro. A intenção será passada para o serviço como um parâmetro no método `OnHandleIntent`. Este trecho de código é um exemplo de envio de uma solicitação de trabalho para uma intenção: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.PutExtra("file_to_download", "http://www.somewhere.com/file/to/download.zip");

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
