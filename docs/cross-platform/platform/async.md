---
title: Visão geral do suporte assíncrono
description: Este documento descreve a programação com async e await, conceitos introduzidos no C# 5 para torná-lo mais fácil de escrever código assíncrono.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: cca147f0c5dd1a217f464ffbed2a1ad2618c9b80
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830189"
---
# <a name="async-support-overview"></a>Visão geral do suporte assíncrono

_C#5 introduziu duas palavras-chave para simplificar a programação assíncrona: async e await. Essas palavras-chave permitem que você escreva código simple que utiliza a biblioteca paralela de tarefas para executar operações de longa execução (como o acesso à rede) em outro thread e acessar facilmente os resultados após a conclusão. As versões mais recentes do xamarin. IOS e xamarin. Android dão suporte a async e await – este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin._

Suporte de assíncrono do Xamarin é criado sobre a base do Mono 3.0 e atualiza o perfil de API a seja uma versão de dispositivos móveis do Silverlight para ser uma versão de dispositivos móveis do .NET 4.5.

## <a name="overview"></a>Visão geral

Este documento apresenta o novo async e await palavras-chave, o orienta por meio de alguns exemplos simples de implementar métodos assíncronos em xamarin. IOS e xamarin. Android.

Para uma discussão mais completa sobre os novos recursos assíncronos de C# 5 (incluindo muitos exemplos e cenários de uso diferentes), consulte o artigo [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async).

O aplicativo de exemplo faz uma solicitação web assíncrona simples (sem bloquear o thread principal) e atualiza a interface do usuário com o html baixado e a contagem de caracteres.

 [![](async-images/AsyncAwait_427x368.png "O aplicativo de exemplo faz uma solicitação web assíncrona simples sem bloquear o thread principal, em seguida, atualiza a interface do usuário com o html baixado e a contagem de caracteres")](async-images/AsyncAwait.png#lightbox)

Suporte de assíncrono do Xamarin é criado sobre a base do Mono 3.0 e atualiza o perfil de API a seja uma versão de dispositivos móveis do Silverlight para ser uma versão de dispositivos móveis do .NET 4.5.

## <a name="requirements"></a>Requisitos

C#5 recursos exigem 3.0 Mono que está incluído no xamarin. IOS 6.4 e 4.8 do xamarin. Android. Você será solicitado a atualizar o Mono, xamarin. IOS, xamarin. Android e xamarin. Mac para tirar proveito dele.

## <a name="using-async-amp-await"></a>Usando o async &amp; await

 `async` e `await` são um novo C# recursos de linguagem que trabalham em conjunto com a biblioteca paralela de tarefas para torná-lo mais fácil de escrever código multithread para executar tarefas de longa execução sem bloquear o thread principal do seu aplicativo.

## <a name="async"></a>async

### <a name="declaration"></a>Declaração

O `async` palavra-chave é colocado em uma declaração de método (ou em um lambda ou um método anônimo) para indicar que ele contém código que pode ser executados de forma assíncrona, ie. não bloquear o thread do chamador.

Um método marcado com `async` deve conter pelo menos uma expressão await, ou instrução. Se nenhum `await`s estão presentes no método e em seguida, ele será executado de forma síncrona (o mesmo como se houvesse nenhum `async` modificador). Isso também resultará em um aviso do compilador (mas não um erro).

### <a name="return-types"></a>Tipos de Retorno

Um método assíncrono deve retornar um `Task`, `Task<TResult>` ou `void`.

Especifique o `Task` tipo de retorno se o método não retornar qualquer outro valor.

Especificar `Task<TResult>` se o método deve retornar um valor, onde `TResult` é o tipo que está sendo retornado (como um `int`, por exemplo).

O `void` retornar o tipo é usado principalmente para manipuladores de eventos que precisam dele. Não é possível de código que chama os métodos assíncronos que retornam void `await` no resultado.

### <a name="parameters"></a>Parâmetros

Métodos assíncronos não podem declarar `ref` ou `out` parâmetros.

## <a name="await"></a>await

O operador de espera pode ser aplicado a uma tarefa dentro de um método marcado como async. Isso faz com que o método para parar a execução nesse ponto e aguarde até que a tarefa é concluída.

Usando await não bloqueia o thread do chamador – em vez disso, o controle é retornado ao chamador. Isso significa que o thread de chamada não está bloqueado, portanto, por exemplo, o usuário o thread de interface não fosse bloqueado ao aguardar uma tarefa.

Quando a tarefa for concluída, o método retoma a execução no mesmo ponto no código. Isso inclui a retornar ao escopo try de um bloco try-catch-finally (caso haja algum). await não pode ser usado em um catch ou o bloco finally.

Leia mais sobre [await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) no Microsoft Docs.

## <a name="exception-handling"></a>Tratamento de Exceção

As exceções que ocorrem dentro de um método assíncrono são armazenadas na tarefa e geradas quando a tarefa é `await`ed. Essas exceções podem ser capturadas e manipuladas em um bloco try-catch.

## <a name="cancellation"></a>Cancelamento

Métodos assíncronos que levar muito tempo para ser concluído devem dar suporte a cancelamento. Normalmente, o cancelamento é invocado da seguinte maneira:

- Um `CancellationTokenSource` objeto é criado.
- O `CancellationTokenSource.Token` instância é passada para um método assíncrono cancelável.
- O cancelamento é solicitado ao chamar o `CancellationTokenSource.Cancel` método.

A tarefa, em seguida, cancela a mesmo e confirma o cancelamento.

Para obter mais informações sobre o cancelamento, consulte [Ajuste fino de seu aplicativo assíncrono (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application).

## <a name="example"></a>Exemplo

Baixe o [exemplo de solução do Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (para iOS e Android) para ver um exemplo prático de `async` e `await` nos aplicativos móveis. O exemplo de código é abordado em mais detalhes nesta seção.

### <a name="writing-an-async-method"></a>Escrever um método assíncrono

O método a seguir demonstra como codificar um `async` método com um `await`tarefa ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Observe esses pontos:

-  A declaração do método inclui o `async` palavra-chave.
-  É o tipo de retorno `Task<int>` para que o código de chamada pode acessar o `int` valor calculado nesse método.
-  A instrução return estiver `return exampleInt;` que é um objeto inteiro – o fato de que o método retorna `Task<int>` faz parte das melhorias de linguagem.


### <a name="calling-an-async-method-1"></a>Chamar um método assíncrono 1

Evento de clique neste botão manipulador pode ser encontrado no aplicativo de exemplo do Android para chamar o método discutido acima:

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Notas:

-  O representante anônimo tem o prefixo de palavra-chave async.
-  O método assíncrono DownloadHomepage retorna uma tarefa <int> que é armazenado na variável sizeTask.
-  O código aguarda na variável sizeTask.  *Isso* é o local em que o método é suspenso e o controle é retornado para o código de chamada até que a tarefa assíncrona seja concluída em seu próprio thread.
-  Execução faz *não* pausar quando a tarefa é criada na primeira linha do método, apesar da tarefa que está sendo criada ali. A palavra-chave await significa o local em que a execução está em pausa.
-  Quando a tarefa assíncrona for concluída, intResult é definido e a execução continua no thread original da linha de espera.


### <a name="calling-an-async-method-2"></a>Chamar um método assíncrono 2

No aplicativo de exemplo de iOS o exemplo é escrito ligeiramente diferente para demonstrar uma abordagem alternativa. Em vez de usar um delegado anônimo Este exemplo declara um `async` manipulador de eventos que é atribuído como um manipulador de eventos regulares:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

O método de manipulador de eventos, em seguida, é definido como mostrado aqui:

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Alguns pontos importantes:

-  O método é marcado como `async` , mas retorna `void` . Isso normalmente é feito apenas para manipuladores de eventos (caso contrário, você poderia retornar um `Task` ou `Task<TResult>` ).
-  O código `await` s na `DownloadHomepage` método diretamente em uma atribuição a uma variável ( `intResult` ) ao contrário do exemplo anterior em que usamos intermediário `Task<int>` variável para fazer referência a tarefa.  *Isso* é o local onde controle é retornado ao chamador até que o método assíncrono seja concluído em outro thread.
-  Quando o método assíncrono é concluída e retorna, a execução reinicia no `await` que significa que o resultado de inteiro é retornado e, em seguida, renderizado em um widget de interface do usuário.


## <a name="summary"></a>Resumo

Usando o async e await simplifica bastante o código necessário para gerar operações de longa execução em threads em segundo plano sem bloquear o thread principal. Eles também tornam fácil acessar os resultados quando a tarefa foi concluída.

Este documento forneceu uma visão geral de como as novas palavras-chave e os exemplos de xamarin. IOS e xamarin. Android.



## <a name="related-links"></a>Links relacionados

- [AsyncAwait (amostra)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [Retornos de chamada das nossas gerações ir para declaração](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Dados (iOS) (amostra)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (sample)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (amostra)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async)
- [Ajuste fino de seu aplicativo assíncrono (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await e a interface do usuário e deadlocks! Ah, meu!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Processamento de tarefas conforme são concluídas)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [TAP (Padrão Assíncrono Baseado em Tarefa)](https://msdn.microsoft.com/library/hh873175.aspx)
- [A assincronia em C# 5 (blog de Eric Lippert –) sobre a introdução de palavras-chave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
