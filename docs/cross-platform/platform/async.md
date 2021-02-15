---
title: Visão geral do suporte assíncrono
description: Este documento descreve a programação com o Async e o Await, conceitos introduzidos no C# 5 para facilitar a gravação de código assíncrono.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 9821519c680de61767792c8122938bf826e457f5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453240"
---
# <a name="async-support-overview"></a>Visão geral do suporte assíncrono

_O C# 5 introduziu duas palavras-chave para simplificar o programa assíncrono: Async e Await. Essas palavras-chave permitem escrever código simples que utiliza a biblioteca de tarefas paralelas para executar operações de longa execução (como acesso à rede) em outro thread e acessar facilmente os resultados na conclusão. As versões mais recentes do Xamarin. iOS e Xamarin. Android dão suporte a Async e Await-este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin._

O suporte assíncrono do Xamarin é criado com base no mono 3,0 Foundation e atualiza o perfil de API a partir de ser uma versão para dispositivos móveis do Silverlight para ser uma versão amigável para o .NET 4,5.

## <a name="overview"></a>Visão geral

Este documento apresenta as novas palavras-chave Async e Await, em seguida, percorre alguns exemplos simples que implementam métodos assíncronos no Xamarin. iOS e Xamarin. Android.

Para obter uma discussão mais completa sobre os novos recursos assíncronos do C# 5 (incluindo vários exemplos e diferentes cenários de uso), consulte o artigo [programação assíncrona](/dotnet/csharp/async).

O aplicativo de exemplo faz uma simples solicitação da Web assíncrona (sem bloquear o thread principal) e, em seguida, atualiza a interface do usuário com o HTML e a contagem de caracteres baixados.

 [![O aplicativo de exemplo faz uma simples solicitação da Web assíncrona sem bloquear o thread principal e, em seguida, atualiza a interface do usuário com o HTML e a contagem de caracteres baixados](async-images/AsyncAwait_427x368.png)](async-images/AsyncAwait.png#lightbox)

O suporte assíncrono do Xamarin é criado com base no mono 3,0 Foundation e atualiza o perfil de API a partir de ser uma versão para dispositivos móveis do Silverlight para ser uma versão amigável para o .NET 4,5.

## <a name="requirements"></a>Requisitos

Os recursos do C# 5 exigem o mono 3,0, que está incluído no Xamarin. iOS 6,4 e no Xamarin. Android 4,8. Você será solicitado a atualizar seu mono, Xamarin. iOS, Xamarin. Android e Xamarin. Mac para tirar proveito dele.

## <a name="using-async-amp-await"></a>Usando Async &amp; Await

 `async` e `await` são novos recursos da linguagem C# que trabalham em conjunto com a biblioteca de tarefas paralelas para facilitar a gravação de código threaded para executar tarefas de longa execução sem bloquear o thread principal do seu aplicativo.

## <a name="async"></a>async

### <a name="declaration"></a>Declaração

A `async` palavra-chave é colocada em uma declaração de método (ou em um método lambda ou anônimo) para indicar que ela contém um código que pode ser executado de forma assíncrona, ou seja, não bloquear o thread do chamador.

Um método marcado com `async` deve conter pelo menos uma expressão ou instrução Await. Se nenhuma `await` instrução estiver presente no método, ela será executada de forma síncrona (o mesmo que se não houvesse nenhum `async` Modificador). Isso também resultará em um aviso do compilador (mas não um erro).

### <a name="return-types"></a>Tipos de retorno

Um método assíncrono deve retornar um `Task` , `Task<TResult>` ou `void` .

Especifique o `Task` tipo de retorno se o método não retornar nenhum outro valor.

Especifique `Task<TResult>` se o método precisa retornar um valor, em que `TResult` é o tipo que está sendo retornado (como um `int` , por exemplo).

O `void` tipo de retorno é usado principalmente para manipuladores de eventos que exigem isso. O código que chama os métodos assíncronos de retorno nulo não pode `await` fazer isso.

### <a name="parameters"></a>Parâmetros

Os métodos assíncronos não podem declarar `ref` ou `out` parâmetros.

## <a name="await"></a>await

O operador Await pode ser aplicado a uma tarefa dentro de um método marcado como Async. Isso faz com que o método interrompa a execução nesse ponto e aguarde até que a tarefa seja concluída.

O uso de Await não bloqueia o thread do chamador – em vez disso, o controle é retornado para o chamador. Isso significa que o thread de chamada não é bloqueado, por exemplo, o thread da interface do usuário não seria bloqueado ao aguardar uma tarefa.

Quando a tarefa é concluída, o método retoma a execução no mesmo ponto no código. Isso inclui retornar ao escopo try de um bloco try-catch-finally (se houver um). Await não pode ser usado em um bloco catch ou finally.

Leia mais sobre [Await](/dotnet/csharp/language-reference/keywords/await) em Microsoft docs.

## <a name="exception-handling"></a>Tratamento de Exceção

As exceções que ocorrem dentro de um método assíncrono são armazenadas na tarefa e geradas quando a tarefa é `await` Ed. Essas exceções podem ser capturadas e manipuladas dentro de um bloco try-catch.

## <a name="cancellation"></a>Cancelamento

Os métodos assíncronos que levam muito tempo para serem concluídos devem dar suporte ao cancelamento. Normalmente, o cancelamento é invocado da seguinte maneira:

- Um `CancellationTokenSource` objeto é criado.
- A `CancellationTokenSource.Token` instância é passada para um método assíncrono cancelável.
- O cancelamento é solicitado chamando o `CancellationTokenSource.Cancel` método.

A tarefa é cancelada e confirma o cancelamento.

Para obter mais informações sobre o cancelamento, consulte [Ajuste fino de seu aplicativo assíncrono (C#)](/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application).

## <a name="example"></a>Exemplo

Baixe a [solução Xamarin de exemplo](/samples/xamarin/mobile-samples/asyncawait/) (para IOS e Android) para ver um exemplo de trabalho de `async` e `await` em aplicativos móveis. O código de exemplo é discutido mais detalhadamente nesta seção.

### <a name="writing-an-async-method"></a>Escrevendo um método assíncrono

O método a seguir demonstra como codificar um `async` método com uma `await` tarefa Ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("https://visualstudio.microsoft.com/xamarin"); // async method!

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

Observe estes pontos:

- A declaração do método inclui a  `async` palavra-chave.
- O tipo de retorno é  `Task<int>` , portanto, o código de chamada pode acessar o  `int` valor calculado nesse método.
- A instrução Return é  `return exampleInt;` um objeto inteiro – o fato de que o método retorna faz  `Task<int>` parte das melhorias de idioma.

### <a name="calling-an-async-method-1"></a>Chamando um método assíncrono 1

Esse manipulador de eventos de clique no botão pode ser encontrado no aplicativo de exemplo do Android para chamar o método discutido acima:

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

Observações:

- O delegado anônimo tem o prefixo de palavra-chave Async.
- O método assíncrono DownloadHomepage retorna uma tarefa \<int> que é armazenada na variável sizeTask.
- O código aguarda na variável sizeTask.  *Esse* é o local em que o método é suspenso e o controle é retornado para o código de chamada até que a tarefa assíncrona seja concluída em seu próprio thread.
- A execução  *não pausa quando* a tarefa é criada na primeira linha do método, apesar da tarefa que está sendo criada lá. A palavra-chave Await significa o local em que a execução está em pausa.
- Quando a tarefa assíncrona é concluída, a intresult é definida e a execução continua no thread original, da linha Await.

### <a name="calling-an-async-method-2"></a>Chamando um método assíncrono 2

No aplicativo de exemplo do iOS, o exemplo é escrito ligeiramente diferente para demonstrar uma abordagem alternativa. Em vez de usar um delegado anônimo, este exemplo declara um `async` manipulador de eventos que é atribuído como um manipulador de eventos regular:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

O método do manipulador de eventos é definido como mostrado aqui:

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

- O método é marcado como  `async` , mas retorna  `void` . Isso geralmente é feito apenas para manipuladores de eventos (caso contrário, você retorna um  `Task` ou  `Task<TResult>` ).
- A `await` palavra-chave no  `DownloadHomepage` método atribui diretamente a uma variável ( `intResult` ), ao contrário do exemplo anterior, em que usamos uma variável intermediária  `Task<int>` para fazer referência à tarefa.  *Esse* é o local onde o controle é retornado para o chamador até que o método assíncrono seja concluído em outro thread.
- Quando o método assíncrono é concluído e retorna, a execução é retomada no  `await` que significa que o resultado inteiro é retornado e, em seguida, renderizado em um widget de interface do usuário.

## <a name="summary"></a>Resumo

Usar Async e Await simplifica bastante o código necessário para gerar operações de longa execução em threads em segundo plano sem bloquear o thread principal. Eles também facilitam o acesso aos resultados quando a tarefa é concluída.

Este documento forneceu uma visão geral das novas palavras-chave de linguagem e exemplos para Xamarin. iOS e Xamarin. Android.

## <a name="related-links"></a>Links Relacionados

- [AsyncAwait (exemplo)](/samples/xamarin/mobile-samples/asyncawait/)
- [Retornos de chamada como a instrução Go de nossas gerações](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Dados (iOS) (exemplo)](/samples/xamarin/ios-samples/data/)
- [HttpClient (iOS) (exemplo)](/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch (iOS) (exemplo)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programação assíncrona](/dotnet/csharp/async)
- [Ajuste fino de seu aplicativo assíncrono (C#)](/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await, interface do usuário e deadlocks! Meu Oh!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Processando tarefas conforme elas são concluídas)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [Padrão assíncrono baseado em tarefa (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Assincronia em C# 5 (blog de Eric Lippert) – sobre a introdução das palavras-chave](/archive/blogs/ericlippert/asynchrony-in-c-5-part-six-whither-async)