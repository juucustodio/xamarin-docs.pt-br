---
title: Threading no xamarin. IOS
description: Este documento descreve como usar as APIs de System.Threading em um aplicativo xamarin. IOS. Ele aborda a biblioteca paralela de tarefas, criação de aplicativos responsivos e coleta de lixo.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 7dbb0044f09d5bc00f2393eb647efba05a061c3f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399811"
---
# <a name="threading-in-xamarinios"></a>Threading no xamarin. IOS

O tempo de execução do xamarin. IOS fornece aos desenvolvedores acesso para o .NET threading de APIs, ambos explicitamente ao usar threads (`System.Threading.Thread, System.Threading.ThreadPool`) e implicitamente ao usar os padrões de delegado assíncrono ou os métodos BeginXXX, bem como o completo variedade de APIs que oferecem suporte a Biblioteca paralela de tarefas.



Xamarin recomenda que você use o [Task Parallel Library](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) para a criação de aplicativos por alguns motivos:
-  O Agendador TPL padrão delegará a execução da tarefa para o pool de thread, que por sua vez crescerá dinamicamente o número de threads necessária, pois o processo é executado, evitando um cenário em que muitos threads acabarem competindo por tempo da CPU. 
-  É mais fácil pensar sobre operações em termos de tarefas TPL. Pode facilmente manipulá-los, agendá-los, serializar sua execução ou iniciar muitas em paralelo com um conjunto avançado de APIs. 
-  É a base para a programação com as novo c# async extensões de linguagem. 


O pool de threads lentamente aumentará o número de threads conforme necessário com base no número de núcleos de CPU disponíveis no sistema, a carga do sistema e suas demandas de aplicativo. Você pode usar esse pool de threads chamando métodos na `System.Threading.ThreadPool` ou usando o padrão `System.Threading.Tasks.TaskScheduler` (parte do *estruturas paralelas*).

Normalmente os desenvolvedores usam threads quando eles precisam para criar aplicativos responsivos e não desejam bloquear a interface do usuário principal loop de execução.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Desenvolvendo aplicativos responsivos

Acesso aos elementos de interface do usuário deve ser limitado para o mesmo thread que está executando o loop principal para o seu aplicativo. Se você quiser fazer alterações na interface do usuário principal de um thread, você deve enfileirar o código por meio [NSObject.InvokeOnMainThread](xref:Foundation.NSObject), semelhante a esta:

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

As opções acima invoca o código dentro do delegado no contexto do thread principal, sem fazer com que quaisquer condições de corrida que poderiam potencialmente causar falha no seu aplicativo.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Threading e coleta de lixo

No decorrer da execução, o tempo de execução do Objective-C criará e liberar objetos. Se objetos são sinalizados para "liberação automática" tempo de execução do Objective-C irá liberar esses objetos para o thread do atual `NSAutoReleasePool`. Xamarin. IOS cria uma `NSAutoRelease` pool para cada thread do `System.Threading.ThreadPool` e para o thread principal. Isso pela extensão cobre os threads criados com o TaskScheduler padrão em Tasks.

Se você criar seus próprios threads usando `System.Threading` você precisa fornecer a você possui `NSAutoRelease` pool para impedir que os dados contra vazamento. Para fazer isso, basta encapsule seu thread no seguinte trecho de código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Observação: Desde o xamarin. IOS 5.2 você não precisa fornecer seu próprio `NSAutoReleasePool` mais um será fornecido automaticamente para você.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)
