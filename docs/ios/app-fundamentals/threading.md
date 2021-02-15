---
title: Threading no Xamarin. iOS
description: Este documento descreve como usar as APIs System. Threading em um aplicativo Xamarin. iOS. Ele aborda a biblioteca paralela de tarefas, criando aplicativos responsivos e coleta de lixo.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 30709b9b75c18f954135e950b95094f9ee2d71ac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435269"
---
# <a name="threading-in-xamarinios"></a>Threading no Xamarin. iOS

O tempo de execução do Xamarin. iOS fornece aos desenvolvedores acesso às APIs de Threading do .NET, explicitamente ao usar threads ( `System.Threading.Thread, System.Threading.ThreadPool` ) e implicitamente ao usar os padrões de delegado assíncrono ou os métodos BeginXxx, bem como o intervalo completo de APIs que dão suporte à biblioteca paralela de tarefas.

O Xamarin recomenda enfaticamente que você use a TPL ( [biblioteca paralela de tarefas](/dotnet/standard/parallel-programming/task-parallel-library-tpl) ) para compilar aplicativos por alguns motivos:

- O Agendador TPL padrão delegará a execução da tarefa para o pool de threads, que, por sua vez, aumentará dinamicamente o número de threads necessários conforme o processo ocorre, evitando, ao mesmo tempo, um cenário em que muitos threads acabam concorrendo para a CPU. 
- É mais fácil pensar nas operações em termos de tarefas TPL. Você pode manipulá-los facilmente, agendá-los, serializar sua execução ou iniciar muitos em paralelo com um rico conjunto de APIs. 
- É a base para a programação com as novas extensões de linguagem Async do C#. 

O pool de threads aumentará lentamente o número de threads conforme necessário, com base no número de núcleos de CPU disponíveis no sistema, na carga do sistema e nas demandas do aplicativo. Você pode usar esse pool de threads invocando métodos no `System.Threading.ThreadPool` ou usando o padrão `System.Threading.Tasks.TaskScheduler` (parte das *estruturas paralelas*).

Normalmente, os desenvolvedores usam threads quando precisam criar aplicativos responsivos e não desejam bloquear o loop de execução da interface do usuário principal.

 <a name="Developing_Responsive_Applications"></a>

## <a name="developing-responsive-applications"></a>Desenvolvendo aplicativos responsivos

O acesso aos elementos da interface do usuário deve ser limitado ao mesmo thread que está executando o loop principal para seu aplicativo. Se você quiser fazer alterações na interface do usuário principal de um thread, deverá enfileirar o código usando [NSObject. InvokeOnMainThread](xref:Foundation.NSObject), da seguinte maneira:

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

O acima invoca o código dentro do delegado no contexto do thread principal, sem causar quaisquer condições de corrida que possam falhar o aplicativo.

 <a name="Threading_and_Garbage_Collection"></a>

## <a name="threading-and-garbage-collection"></a>Threading e coleta de lixo

No decorrer da execução, o tempo de execução do Objective-C criará e liberará objetos. Se os objetos forem sinalizados para "liberação automática", o tempo de execução do Objective-C liberará esses objetos para o atual do thread `NSAutoReleasePool` . O Xamarin. iOS cria um `NSAutoRelease` pool para cada thread do `System.Threading.ThreadPool` e para o thread principal. Essa extensão abrange todos os threads criados usando o TaskScheduler padrão em System. Threading. Tasks.

Se você criar seus próprios threads usando o, precisará `System.Threading` fornecer seu próprio `NSAutoRelease` pool para impedir que os dados sejam vazados. Para fazer isso, basta encapsular o thread na seguinte parte do código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Observação: como o Xamarin. iOS 5,2, você não precisa mais fornecer o seu próprio `NSAutoReleasePool` como um será fornecido automaticamente para você.

## <a name="related-links"></a>Links Relacionados

- [Como trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)