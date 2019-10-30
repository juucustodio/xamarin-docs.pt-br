---
title: Threading no Xamarin. iOS
description: Este documento descreve como usar as APIs System. Threading em um aplicativo Xamarin. iOS. Ele aborda a biblioteca paralela de tarefas, criando aplicativos responsivos e coleta de lixo.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 1c9282c790aa5436667b37e1861a96afffcaa668
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009446"
---
# <a name="threading-in-xamarinios"></a>Threading no Xamarin. iOS

O tempo de execução do Xamarin. iOS fornece aos desenvolvedores acesso às APIs de Threading do .NET, explicitamente ao usar threads (`System.Threading.Thread, System.Threading.ThreadPool`) e implicitamente ao usar os padrões de delegado assíncrono ou os métodos BeginXXX, bem como o intervalo completo de APIs que dão suporte à tarefa Biblioteca paralela.

O Xamarin recomenda enfaticamente que você use a TPL ( [biblioteca paralela de tarefas](https://msdn.microsoft.com/library/dd460717.aspx) ) para compilar aplicativos por alguns motivos:

- O Agendador TPL padrão delegará a execução da tarefa para o pool de threads, que, por sua vez, aumentará dinamicamente o número de threads necessários conforme o processo ocorre, evitando, ao mesmo tempo, um cenário em que muitos threads acabam concorrendo para a CPU. 
- É mais fácil pensar nas operações em termos de tarefas TPL. Você pode manipulá-los facilmente, agendá-los, serializar sua execução ou iniciar muitos em paralelo com um rico conjunto de APIs. 
- É a base para a programação com as novas C# extensões de linguagem assíncrona. 

O pool de threads aumentará lentamente o número de threads conforme necessário, com base no número de núcleos de CPU disponíveis no sistema, na carga do sistema e nas demandas do aplicativo. Você pode usar esse pool de threads invocando métodos no `System.Threading.ThreadPool` ou usando o `System.Threading.Tasks.TaskScheduler` padrão (parte das *estruturas paralelas*).

Normalmente, os desenvolvedores usam threads quando precisam criar aplicativos responsivos e não desejam bloquear o loop de execução da interface do usuário principal.

 <a name="Developing_Responsive_Applications" />

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

 <a name="Threading_and_Garbage_Collection" />

## <a name="threading-and-garbage-collection"></a>Threading e coleta de lixo

No decorrer da execução, o tempo de execução do Objective-C criará e liberará objetos. Se os objetos forem sinalizados para "liberação automática", o tempo de execução do Objective-C liberará esses objetos para o `NSAutoReleasePool`atual do thread. O Xamarin. iOS cria um pool de `NSAutoRelease` para cada thread do `System.Threading.ThreadPool` e para o thread principal. Essa extensão abrange todos os threads criados usando o TaskScheduler padrão em System. Threading. Tasks.

Se você criar seus próprios threads usando `System.Threading` você precisará fornecer seu próprio pool de `NSAutoRelease` para impedir que os dados sejam vazados. Para fazer isso, basta encapsular o thread na seguinte parte do código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Observação: como o Xamarin. iOS 5,2, você não precisa fornecer seu próprio `NSAutoReleasePool` mais uma vez que ele será fornecido automaticamente para você.

## <a name="related-links"></a>Links relacionados

- [Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)
