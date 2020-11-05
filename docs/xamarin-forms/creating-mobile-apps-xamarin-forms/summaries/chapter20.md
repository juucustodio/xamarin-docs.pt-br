---
title: Resumo do capítulo 20. E/S assíncrona e de arquivo
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 20. E/S assíncrona e de arquivo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 75c79c7a5300cf5708bb46740bec11f84b59c786
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374011"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumo do capítulo 20. E/S assíncrona e de arquivo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Uma interface gráfica do usuário deve responder a eventos de entrada do usuário em sequência. Isso implica que todo o processamento de eventos de entrada do usuário deve ocorrer em um único thread, geralmente chamado de *thread principal* ou *thread de interface do* usuário.

Os usuários esperam que as interfaces gráficas do usuário sejam responsivas. Isso significa que um programa deve processar eventos de entrada do usuário rapidamente. Se isso não for possível, o processamento deverá ser relegado aos threads secundários de execução.

Vários programas de exemplo neste livro usaram a [`WebRequest`](xref:System.Net.WebRequest) classe. Nessa classe, o [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) método inicia um thread de trabalho, que chama uma função de retorno de chamada quando ele é concluído. No entanto, essa função de retorno de chamada é executada no thread de trabalho, de modo que o programa deve chamar [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) o método para acessar a interface do usuário.

> [!NOTE]
> Xamarin.Forms os programas devem usar [`HttpClient`](xref:System.Net.Http.HttpClient) o em vez de [`WebRequest`](xref:System.Net.WebRequest) para acessar arquivos pela Internet. `HttpClient` dá suporte a operações assíncronas.

Uma abordagem mais moderna para o processamento assíncrono está disponível em .NET e em C#. Isso envolve as [`Task`](xref:System.Threading.Tasks.Task) classes e e [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) outros tipos nos [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) namespaces e, bem como o C# 5,0 `async` e as `await` palavras-chave. É isso que se concentra neste capítulo.

## <a name="from-callbacks-to-await"></a>De retornos de chamada para Await

A `Page` própria classe contém três métodos assíncronos para exibir caixas de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Retorna um `Task` objeto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Retorna um `Task<bool>` objeto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Retorna um `Task<string>` objeto

Os `Task` objetos indicam que esses métodos implementam o padrão assíncrono baseado em tarefa, conhecido como Tap. Esses `Task` objetos são retornados rapidamente do método. Os `Task<T>` valores de retorno constituem uma "promessa" que um valor do tipo `TResult` estará disponível quando a tarefa for concluída. O `Task` valor de retorno indica uma ação assíncrona que será concluída, mas sem nenhum valor retornado.

Em todos esses casos, o `Task` é concluído quando o usuário ignora a caixa de alerta.  

### <a name="an-alert-with-callbacks"></a>Um alerta com retornos de chamada

O exemplo [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) demonstra como tratar `Task<bool>` objetos de retorno e `Device.BeginInvokeOnMainThread` chamadas usando métodos de retorno de chamada.

### <a name="an-alert-with-lambdas"></a>Um alerta com lambdas

O exemplo [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) demonstra como usar funções lambda anônimas para tratamento `Task` e `Device.BeginInvokeOnMainThread` chamadas.  

### <a name="an-alert-with-await"></a>Um alerta com Await

Uma abordagem mais direta envolve as `async` `await` palavras-chave e introduzidas no C# 5. O exemplo  [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) demonstra seu uso.

### <a name="an-alert-with-nothing"></a>Um alerta sem nada

Se o método assíncrono retornar `Task` em vez de `Task<TResult>` , o programa não precisará usar nenhuma dessas técnicas se não precisar saber quando a tarefa assíncrona é concluída. O exemplo [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) demonstra isso.

### <a name="saving-program-settings-asynchronously"></a>Salvando configurações do programa de forma assíncrona

O exemplo [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) demonstra o uso do [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) método de `Application` para salvar as configurações do programa à medida que elas são alteradas sem substituir o `OnSleep` método.

### <a name="a-platform-independent-timer"></a>Um temporizador independente de plataforma

É possível usar [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) o para criar um temporizador independente de plataforma. O exemplo [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) demonstra isso.

## <a name="file-inputoutput"></a>Entrada/saída de arquivo

Tradicionalmente, o [`System.IO`](xref:System.IO) namespace do .net tem sido a origem do suporte de e/s de arquivo. Embora alguns métodos neste namespace ofereçam suporte a operações assíncronas, a maioria não. O namespace também dá suporte a várias chamadas de método simples que executam funções sofisticadas de e/s de arquivo.

### <a name="good-news-and-bad-news"></a>Boas notícias e notícias ruins

Todas as plataformas com suporte no Xamarin.Forms armazenamento local do aplicativo de suporte &mdash; são privadas para o aplicativo.

As bibliotecas Xamarin. iOS e Xamarin. Android incluem uma versão do .NET que o Xamarin foi expressamente adaptada para essas duas plataformas. Isso inclui classes do `System.IO` que você pode usar para executar a e/s de arquivo com o armazenamento local do aplicativo nessas duas plataformas.

No entanto, se você pesquisar essas `System.IO` classes em um Xamarin.Forms PCL, não as encontrará. O problema é que a Microsoft remodelava completamente a e/s de arquivos para a API Windows Runtime. Os programas destinados Windows 8.1, Windows Phone 8,1 e os Plataforma Universal do Windows não usam `System.IO` para e/s de arquivo.

Isso significa que você precisará usar o [`DependencyService`](xref:Xamarin.Forms.DependencyService) (primeiro discutido no [**capítulo 9. Chamadas de API específicas da plataforma**](chapter09.md) para implementar e/s de arquivo.

> [!NOTE]
> A classe portátil Libaries foi substituída por bibliotecas .NET Standard 2,0 e .NET Standard 2,0 dá suporte a [`System.IO`](xref:System.IO) tipos para todas as Xamarin.Forms plataformas. Não é mais necessário usar um `DependencyService` para a maioria das tarefas de e/s de arquivo. Consulte [manipulação de arquivos Xamarin.Forms em](~/xamarin-forms/data-cloud/data/files.md) para obter uma abordagem mais moderna para e/s de arquivo.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Uma primeira captura em e/s de arquivo de plataforma cruzada

O exemplo [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) define uma [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interface para e/s de arquivo e implementações dessa interface em todas as plataformas. No entanto, as implementações de Windows Runtime não funcionam com os métodos nesta interface porque os métodos de e/s de arquivo Windows Runtime são assíncronos.

### <a name="accommodating-windows-runtime-file-io"></a>Acomodando Windows Runtime e/s de arquivo

Programas em execução no Windows Runtime usam classes nos [`Windows.Storage`](/uwp/api/Windows.Storage) [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) namespaces e para e/s de arquivo, incluindo o armazenamento local do aplicativo. Como a Microsoft determinou que qualquer operação que exija mais de 50 milissegundos deve ser assíncrona para evitar o bloqueio do thread da interface do usuário, esses métodos de e/s de arquivo são principalmente assíncronos.

O código que demonstra essa nova abordagem estará em uma biblioteca para que possa ser usado por outros aplicativos.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas da plataforma

É vantajoso armazenar código reutilizável em bibliotecas. Isso é obviamente mais difícil quando partes diferentes do código reutilizável são para sistemas operacionais totalmente diferentes.

A solução [**Xamarin.Forms book. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) demonstra uma abordagem. Essa solução contém sete projetos diferentes:

- [**Xamarin.Forms Book. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), um normal Xamarin.Forms PCL
- [**Xamarin.Forms Book. Platform. Ios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), uma biblioteca de classes do IOS
- [**Xamarin.Forms Book. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), uma biblioteca de classes do Android
- [**Xamarin.Forms Book. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), uma biblioteca de classes universal do Windows
- [**Xamarin.Forms Book. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), um projeto compartilhado para o código que é comum a todas as plataformas Windows

Todos os projetos de plataforma individuais (com exceção de **Xamarin.Forms book. Platform. WinRT** ) têm referências a **Xamarin.Forms book. Platform**. Os três projetos do Windows têm uma referência a **Xamarin.Forms book. Platform. WinRT**.

Todos os projetos contêm um `Toolkit.Init` método estático para garantir que a biblioteca seja carregada se não for referenciada diretamente por um projeto em uma Xamarin.Forms solução de aplicativo.

O projeto **Xamarin.Forms book. Platform** contém a nova [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interface. Todos os métodos agora têm nomes com `Async` sufixos e `Task` objetos de retorno.

O projeto **Xamarin.Forms book. Platform. WinRT** contém a [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe para o Windows Runtime.

O projeto **Xamarin.Forms book. Platform. Ios** contém a [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe para Ios. Esses métodos agora devem ser assíncronos. Alguns dos métodos usam as versões assíncronas dos métodos definidos em `StreamWriter` e `StreamReader` : [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync) . Outras convertem um resultado em um `Task` objeto usando o [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) método.

O projeto **Xamarin.Forms book. Platform. Android** contém uma [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe semelhante para Android.

O projeto **Xamarin.Forms book. Platform** também contém uma [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe que facilita o uso do `DependencyService` objeto.

Para usar essas bibliotecas, uma solução de aplicativo deve incluir todos os projetos na solução **Xamarin.Forms book. Platform** e cada um dos projetos de aplicativo deve ter uma referência à biblioteca correspondente em **Xamarin.Forms book. Platform**.

A solução [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) demonstra como usar as bibliotecas **Xamarin.Forms book. Platform** . Cada um dos projetos tem uma chamada para `Toolkit.Init` . O aplicativo utiliza as funções de e/s de arquivo assíncrono.

### <a name="keeping-it-in-the-background"></a>Mantendo em segundo plano

Métodos em bibliotecas que fazem chamadas para vários métodos assíncronos &mdash; , como `WriteFileAsync` os `ReadFileASync` métodos e na classe Windows Runtime, [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; podem ser tornados um pouco mais eficientes usando o [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) método para evitar voltar para o thread da interface do usuário.

### <a name="dont-block-the-ui-thread"></a>Não bloqueie o thread da interface do usuário!

Às vezes, é tentador evitar o uso de `ContinueWith` ou `await` usando a [`Result`](xref:System.Threading.Tasks.Task`1.Result) Propriedade nos métodos. Isso deve ser evitado para que possa bloquear o thread da interface do usuário ou até mesmo interromper o aplicativo.

## <a name="your-own-awaitable-methods"></a>Seus próprios métodos awaitable

Você pode executar algum código de forma assíncrona passando-o para um dos [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) métodos. Você pode chamar `Task.Run` dentro de um método assíncrono que lida com parte da sobrecarga.

Os vários `Task.Run` padrões são discutidos abaixo.

### <a name="the-basic-mandelbrot-set"></a>O conjunto de Mandelbrot básico

Para desenhar o conjunto de Mandelbrot em tempo real, o [**Xamarin.Forms .**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)A biblioteca do kit de ferramentas tem uma [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) estrutura semelhante à do `System.Numerics` namespace.

O exemplo [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) tem um `CalculateMandeblotAsync` método em seu arquivo code-behind que calcula o conjunto de Mandelbrot preto e branco básico e usa [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) para colocá-lo em um bitmap.

### <a name="marking-progress"></a>Progresso da marcação

Para relatar o progresso de um método assíncrono, você pode criar uma instância de uma [`Progress<T>`](xref:System.Progress`1) classe e definir seu método assíncrono para ter um argumento do tipo [`IProgress<T>`](xref:System.IProgress`1) . Isso é demonstrado no exemplo de [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Cancelando o trabalho

Você também pode escrever um método assíncrono para ser cancelado. Você começa com uma classe chamada [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource) . A [`Token`](xref:System.Threading.CancellationTokenSource.Token) propriedade é um valor do tipo [`CancellationToken`](xref:System.Threading.CancellationToken) . Isso é passado para a função assíncrona. Um programa chama o [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) método de `CancellationTokenSource` (geralmente em resposta a uma ação pelo usuário) para cancelar a função assíncrona.

O método assíncrono pode verificar periodicamente a [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) propriedade de `CancellationToken` e sair se a propriedade for `true` , ou simplesmente chamar o [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) método; nesse caso, o método termina com um [`OperationCancelledException`](xref:System.OperationCanceledException) .

O exemplo [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) demonstra o uso de uma função cancelável.

### <a name="an-mvvm-mandelbrot"></a>Um MVVM Mandelbrot

O exemplo [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) tem uma interface de usuário mais abrangente e é basicamente baseado em [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) classes e:

[![Captura de tela tripla de Mandelbrot X F](images/ch20fg13-small.png "Mandelbrot MVVM")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Voltar para a Web

A [`WebRequest`](xref:System.Net.WebRequest) classe usada em alguns exemplos usa um protocolo assíncrono antigo chamado modelo de programação assíncrona ou APM. Você pode converter essa classe no protocolo TAP moderno usando um dos `FromAsync` métodos na [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) classe. O exemplo [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [Capítulo 20 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capítulo 20 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md)
