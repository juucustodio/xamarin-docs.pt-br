---
title: Threading
ms.topic: article
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 693ada611dc24d3bb22de7c51efe378939a732ad
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="threading"></a>Threading

O tempo de execução do xamarin oferece aos desenvolvedores acesso para o .NET threading de APIs, ambos explicitamente ao usar threads (`System.Threading.Thread, System.Threading.ThreadPool`) e implicitamente ao usar os padrões de delegado assíncrona ou os métodos BeginXXX, bem como o total a variedade de APIs que oferecem suporte a Biblioteca paralela de tarefas.



Xamarin recomenda que você use o [biblioteca paralela de tarefas](http://msdn.microsoft.com/en-us/library/dd460717.aspx) (TPL) para a criação de aplicativos por alguns motivos:
-  O Agendador TPL padrão delegará a execução da tarefa ao pool de threads, que por sua vez dinamicamente aumentar o número de threads necessário, pois o processo é executado, evitando um cenário onde muitos threads acabarem competindo por tempo de CPU. 
-  É mais fácil pensar sobre as operações em termos de TPL tarefas. Pode facilmente manipulá-los, agendá-los, serializar sua execução ou iniciar muitas em paralelo com um conjunto sofisticado de APIs. 
-  É a base para a programação com as novo c# async extensões de linguagem. 


O pool de threads lentamente aumentará o número de threads conforme necessário com base no número de núcleos de CPU disponíveis no sistema, a carga do sistema e suas demandas de aplicativo. Você pode usar este pool de threads ao chamar métodos em `System.Threading.ThreadPool` ou usando o padrão `System.Threading.Tasks.TaskScheduler` (parte do *estruturas paralelas*).

Normalmente os desenvolvedores usam threads quando precisa para criar aplicativos de capacidade de resposta e não desejar bloquear a interface do usuário principal executar loop.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Desenvolvendo aplicativos responsivos

Acesso aos elementos de interface do usuário deve ser limitado para o mesmo thread que está executando o loop principal para o seu aplicativo. Se você quiser fazer alterações na interface do usuário principal de um thread, você deve enfileirar o código usando [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/), assim:

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

Acima invoca o código dentro do delegado no contexto do thread principal, sem fazer com que as condições de corrida que podem travar o aplicativo.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Threading e coleta de lixo

No decorrer da execução, o tempo de execução Objective-C criará e objetos de versão. Se os objetos são sinalizados para "autoversão" o tempo de execução Objective-C liberará esses objetos para o thread do atual `NSAutoReleasePool`. Xamarin cria um `NSAutoRelease` pool para cada thread do `System.Threading.ThreadPool` e para o thread principal. Isso pela extensão cobre qualquer threads criados usando o padrão TaskScheduler em Tasks.

Se você criar seus próprios threads usando `System.Threading` você precisa fornecer a você possui `NSAutoRelease` pool para impedir que os dados contra vazamento. Para fazer isso, basta encapsule o thread no trecho de código a seguir:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Observação: Desde xamarin 5.2 você não precisa fornecer sua própria `NSAutoReleasePool` mais um será fornecido automaticamente para você.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)
