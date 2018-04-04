---
title: Visão geral da assincronia
description: 'A versão mais recente da linguagem c# – versão 5 – introduzido duas novas palavras-chave para expressar operações assíncronas: async e await. Essas palavras-chave permitem que você escrever código simple que utiliza a biblioteca paralela de tarefas para executar operações de longa execução (como o acesso à rede) em outro thread e acessar facilmente os resultados após a conclusão. As versões mais recentes do xamarin e xamarin suportam async e await - este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin.'
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ecad6259cb0d472ac39afb0a6be980d4582812c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="async-support-overview"></a>Visão geral do suporte assíncrono

_A versão mais recente da linguagem c# – versão 5 – introduzido duas novas palavras-chave para expressar operações assíncronas: async e await. Essas palavras-chave permitem que você escrever código simple que utiliza a biblioteca paralela de tarefas para executar operações de longa execução (como o acesso à rede) em outro thread e acessar facilmente os resultados após a conclusão. As versões mais recentes do xamarin e xamarin suportam async e await - este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin._

O suporte assíncrono do Xamarin é criado na base Mono 3.0 e atualiza o perfil de API seja de uma versão de dispositivos móveis do Silverlight para ser uma versão de dispositivos móveis do .NET 4.5.

## <a name="overview"></a>Visão geral

Este documento apresenta a nova async e await palavras-chave, aborda alguns exemplos simples de implementar métodos assíncronos em xamarin e xamarin.

Para obter uma discussão mais completa dos novos recursos assíncronas de c# 5 (incluindo muitos exemplos e cenários de uso diferentes), consulte a documentação do MSDN [programação assíncrona com Async e Await](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx).

O aplicativo de exemplo faz uma solicitação web assíncrona simples (sem bloquear o thread principal) e atualiza a interface do usuário com o html baixado e a contagem de caracteres.

 [![](async-images/AsyncAwait_427x368.png "O aplicativo de exemplo faz uma solicitação web assíncrona simples sem bloquear o thread principal, em seguida, atualiza a interface do usuário com o html baixado e a contagem de caracteres")](async-images/AsyncAwait.png#lightbox)

O suporte assíncrono do Xamarin é criado na base Mono 3.0 e atualiza o perfil de API seja de uma versão de dispositivos móveis do Silverlight para ser uma versão de dispositivos móveis do .NET 4.5.

## <a name="requirements"></a>Requisitos

Recursos do c# 5 exigem Mono 3.0 que está incluído no 6.4 xamarin e xamarin 4.8. Você precisará atualizar seu Mono, xamarin, xamarin e Xamarin.Mac para tirar proveito dele.

## <a name="using-async-amp-await"></a>Usando o async &amp; await

 `async` e `await` c# idioma recursos novos que trabalham em conjunto com a biblioteca paralela de tarefas para tornar mais fácil escrever código para executar tarefas de longa execução sem bloquear o thread principal de seu aplicativo.

## <a name="async"></a>async

### <a name="declaration"></a>Declaração

O `async` palavra-chave é colocado em uma declaração de método (ou em um lambda ou um método anônimo) para indicar que ele contém código que pode ser executados de forma assíncrona, ie. não bloquear o thread do chamador.

Um método marcado com `async` deve conter pelo menos uma expressão ou instrução de espera. Se nenhum `await`s estão presentes no método, em seguida, ele será executado de forma síncrona (o mesmo como se houvesse nenhum `async` modificador). Isso também resulta em um aviso do compilador (mas não um erro).

### <a name="return-types"></a>Tipos de Retorno

Um método assíncrono deve retornar um `Task`, `Task<TResult>` ou `void`.

Especifique o `Task` tipo de retorno, se o método não retornar qualquer outro valor.

Especifique `Task<TResult>` se o método deve retornar um valor, onde `TResult` é o tipo que está sendo retornado (como um `int`, por exemplo).

O `void` retornar o tipo é usado principalmente para manipuladores de eventos que precisam disso. O código que chama os métodos assíncronos que retornam void não é possível `await` no resultado.

### <a name="parameters"></a>Parâmetros

Métodos assíncronos não podem declarar `ref` ou `out` parâmetros.

## <a name="await"></a>await

O operador await pode ser aplicado a uma tarefa dentro de um método marcado como assíncronas. Faz com que o método para interromper a execução nesse ponto e aguarde a conclusão da tarefa.

Usando await não bloqueia o thread do chamador – em vez disso, o controle é retornado ao chamador. Isso significa que o thread de chamada não está bloqueado, portanto, por exemplo, o usuário thread de interface não é bloqueada quando estão aguardando uma tarefa.

Quando a tarefa é concluída, o método continua em execução no mesmo ponto no código. Isso inclui retornando para o escopo de bloco try de um bloco try-catch-finally (se houver). await não pode ser usado em um catch ou finally bloquear.

Leia mais sobre [await no MSDN](http://msdn.microsoft.com/en-us/library/vstudio/hh156528.aspx).

## <a name="exception-handling"></a>Tratamento de Exceção

Exceções que ocorrem dentro de um método assíncrono são armazenadas na tarefa e geradas quando a tarefa é `await`ed. Essas exceções podem ser capturadas e tratadas dentro de um bloco try-catch.

## <a name="cancellation"></a>Cancelamento

Métodos assíncronos que levar muito tempo para concluir devem oferecer suporte ao cancelamento. Normalmente, o cancelamento é chamado da seguinte maneira:

- Um `CancellationTokenSource` objeto é criado.
- O `CancellationTokenSource.Token` instância é passada para um método assíncrono pode ser cancelado.
- Cancelamento é solicitado ao chamar o `CancellationTokenSource.Cancel` método.

A tarefa, em seguida, cancela a mesmo e confirma o cancelamento.

Para obter mais informações sobre cancelamento, consulte [como cancelar uma tarefa assíncrona](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx) no MSDN.

## <a name="example"></a>Exemplo

Baixe o [exemplo solução Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (para iOS e Android) para ver um exemplo de `async` e `await` nos aplicativos móveis. O exemplo de código é abordado em mais detalhes nesta seção.

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

Observe que esses pontos:

-  A declaração de método inclui o `async` palavra-chave.
-  O tipo de retorno é `Task<int>` para chamar código possa acessar o `int` valor calculado nesse método.
-  A instrução de retorno é `return exampleInt;` que é um objeto inteiro – o fato de que o método retorna `Task<int>` faz parte dos aprimoramentos de linguagem.


### <a name="calling-an-async-method-1"></a>Chamando um método assíncrono 1

Este evento de clique manipulador pode ser encontrado no aplicativo de exemplo do Android para chamar o método discutido acima:

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

-  O delegado anônimo tem o prefixo de palavra-chave async.
-  O método assíncrono DownloadHomepage retorna uma tarefa <int> que é armazenado na variável sizeTask.
-  Aguarda o código na variável sizeTask.  *Isso* é o local em que o método está suspenso e o controle é retornado para o código de chamada até que a tarefa assíncrona seja concluída em seu próprio thread.
-  Execução *não* pausar quando a tarefa é criada na primeira linha do método, apesar da tarefa que está sendo criado. A palavra-chave await significa o local em que a execução está em pausa.
-  Quando a tarefa assíncrona é concluída, intResult é definida e a execução continua no thread original, na linha de espera.


### <a name="calling-an-async-method-2"></a>Chamando um método assíncrono 2

O aplicativo de exemplo do iOS o exemplo é gravado ligeiramente diferente para demonstrar uma abordagem alternativa. Em vez de usar um delegado anônimo Este exemplo declara um `async` manipulador de eventos que é atribuído como um manipulador de eventos regulares:

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

-  O método está marcado como `async` , mas retorna `void` . Isso geralmente é feito apenas para manipuladores de eventos (caso contrário, você poderia retornar um `Task` ou `Task<TResult>` ).
-  O código `await` s no `DownloadHomepage` método diretamente em uma atribuição em uma variável ( `intResult` ) ao contrário do exemplo anterior, onde usamos um intermediário `Task<int>` variável para fazer referência a tarefa.  *Isso* é o local onde controle é retornado ao chamador até que o método assíncrono foi concluída em outro thread.
-  Quando o método assíncrono é concluída e retorna, a execução reinicia no `await` que significa que o resultado de inteiro é retornado e, em seguida, renderizado em um widget de interface do usuário.


## <a name="summary"></a>Resumo

Usando async e await simplifica o código necessário para gerar operações de longa duração em threads em segundo plano sem bloquear o thread principal. Eles também tornam mais fácil acessar os resultados quando a tarefa foi concluída.

Este documento foi atribuído a uma visão geral de novas palavras-chave de idioma e exemplos de xamarin e xamarin.



## <a name="related-links"></a>Links relacionados

- [AsyncAwait (exemplo)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [Retornos de chamada como nosso gerações Ir para a instrução](http://tirania.org/blog/archive/2013/Aug-15.html)
- [Dados (iOS) (amostra)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (sample)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (amostra)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Webinar: C# assíncrono no iOS e Android (vídeo)](http://xamarin.wistia.com/medias/k27mc627xz)
- [Programação assíncrona com Async e Await (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx)
- [Ajustando seu aplicativo Async (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx)
- [Await e a interface do usuário e deadlocks! AH meu! (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [Processar tarefas conforme são concluídas (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [TAP (Padrão Assíncrono Baseado em Tarefa)](http://msdn.microsoft.com/en-us/library/hh873175.aspx)
- [Assincronia no c# 5 (blog de Eric Lippert) – sobre a introdução de palavras-chave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
