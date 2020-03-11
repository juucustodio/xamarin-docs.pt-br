---
title: Resumo do capítulo 20. E/s assíncrona e de arquivo
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 20. E/s assíncrona e de arquivo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771046"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumo do capítulo 20. E/s assíncrona e de arquivo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

 Uma interface gráfica do usuário deve responder a eventos de entrada do usuário em sequência. Isso implica que todo o processamento de eventos de entrada do usuário deve ocorrer em um único thread, geralmente chamado de *thread principal* ou *thread de interface do*usuário.

Os usuários esperam interfaces gráficas do usuário seja ágil na resposta. Isso significa que um programa deve processar os eventos de entrada do usuário rapidamente. Se isso não for possível, então o processamento deve estariam relegado às secundários threads de execução.

Vários programas de exemplo neste livro usaram a classe [`WebRequest`](xref:System.Net.WebRequest) . Nessa classe, o método [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) inicia um thread de trabalho, que chama uma função de retorno de chamada quando ele é concluído. No entanto, essa função de retorno de chamada é executada no thread de trabalho, de modo que o programa deve chamar [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método para acessar a interface do usuário.

> [!NOTE]
> Os programas Xamarin. Forms devem usar [`HttpClient`](xref:System.Net.Http.HttpClient) em vez de [`WebRequest`](xref:System.Net.WebRequest) para acessar arquivos pela Internet. o `HttpClient` dá suporte a operações assíncronas.

Uma abordagem mais moderna para processamento assíncrono está disponível no .NET e c#. Isso envolve as classes [`Task`](xref:System.Threading.Tasks.Task) e [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) e outros tipos nos namespaces [`System.Threading`](xref:System.Threading) e [`System.Threading.Tasks`](xref:System.Threading.Tasks) , bem como as C# palavras-chave 5,0 `async` e `await`. Esse é o que este capítulo se concentra em.

## <a name="from-callbacks-to-await"></a>De retornos de chamada para aguardar

A própria classe `Page` contém três métodos assíncronos para exibir caixas de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) retorna um objeto `Task`
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) retorna um objeto `Task<bool>`
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) retorna um objeto `Task<string>`

Os objetos `Task` indicam que esses métodos implementam o padrão assíncrono baseado em tarefa, conhecido como TAP. Esses `Task` objetos são retornados rapidamente do método. Os valores de retorno `Task<T>` constituem uma "promessa" que um valor do tipo `TResult` estará disponível quando a tarefa for concluída. O valor de retorno de `Task` indica uma ação assíncrona que será concluída, mas sem nenhum valor retornado.

Em todos esses casos, o `Task` é concluído quando o usuário ignora a caixa de alerta.  

### <a name="an-alert-with-callbacks"></a>Um alerta com retornos de chamada

O exemplo [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) demonstra como tratar `Task<bool>` objetos de retorno e `Device.BeginInvokeOnMainThread` chamadas usando métodos de retorno de chamada.

### <a name="an-alert-with-lambdas"></a>Um alerta com lambdas

O exemplo [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) demonstra como usar funções lambda anônimas para lidar com chamadas `Task` e `Device.BeginInvokeOnMainThread`.  

### <a name="an-alert-with-await"></a>Um alerta com await

Uma abordagem mais direta envolve as palavras-chave `async` e `await` introduzidas C# em 5. O exemplo [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) demonstra seu uso.

### <a name="an-alert-with-nothing"></a>Um alerta com nada

Se o método assíncrono retornar `Task` em vez de `Task<TResult>`, o programa não precisará usar nenhuma dessas técnicas se não precisar saber quando a tarefa assíncrona é concluída. O exemplo [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) demonstra isso.

### <a name="saving-program-settings-asynchronously"></a>Salvando as configurações do programa de forma assíncrona

O exemplo [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) demonstra o uso do método [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) de `Application` para salvar as configurações do programa à medida que elas são alteradas sem substituir o método `OnSleep`.

### <a name="a-platform-independent-timer"></a>Um temporizador independente de plataforma

É possível usar [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) para criar um temporizador independente de plataforma. O exemplo [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) demonstra isso.

## <a name="file-inputoutput"></a>Arquivo de entrada/saída

Tradicionalmente, o namespace .NET [`System.IO`](xref:System.IO) foi a origem do suporte de e/s de arquivo. Embora alguns métodos neste namespace dão suporte a operações assíncronas, a maioria não tem. O namespace também dá suporte a várias chamadas de método simples que executam funções de e/s de arquivo sofisticados.

### <a name="good-news-and-bad-news"></a>Boas e más notícias

Todas as plataformas suportadas pelo Xamarin. Forms dão suporte ao armazenamento local do aplicativo &mdash; armazenamento que é privado para o aplicativo.

As bibliotecas do xamarin. IOS e xamarin. Android incluem uma versão do .NET que Xamarin tem expressamente personalizados para essas duas plataformas. Isso inclui classes de `System.IO` que você pode usar para executar a e/s de arquivo com O armazenamento local do aplicativo nessas duas plataformas.

No entanto, se você pesquisar essas `System.IO` classes em um PCL Xamarin. Forms, não as encontrará. O problema é o arquivo Microsoft totalmente renovada e/s para a API de tempo de execução do Windows. Os programas destinados Windows 8.1, Windows Phone 8,1 e os Plataforma Universal do Windows não usam `System.IO` para e/s de arquivo.

Isso significa que você precisará usar o [`DependencyService`](xref:Xamarin.Forms.DependencyService) (abordado primeiro no [**capítulo 9. Chamadas de API específicas da plataforma**](chapter09.md) para implementar e/s de arquivo.

> [!NOTE]
> A classe portátil Libaries foi substituída por bibliotecas .NET Standard 2,0 e .NET Standard 2,0 dá suporte a tipos de [`System.IO`](xref:System.IO) para todas as plataformas Xamarin. Forms. Não é mais necessário usar um `DependencyService` para a maioria das tarefas de e/s de arquivo. Consulte [manipulação de arquivos no Xamarin. Forms](~/xamarin-forms/data-cloud/data/files.md) para obter uma abordagem mais moderna para e/s de arquivo.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Uma primeira tentativa de e/s de arquivo de plataforma cruzada

O exemplo [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) define uma interface [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) para e/s de arquivo e implementações dessa interface em todas as plataformas. No entanto, as implementações de tempo de execução do Windows não funcionam com os métodos nessa interface, pois os métodos de e/s de arquivo do Windows Runtime são assíncronos.

### <a name="accommodating-windows-runtime-file-io"></a>Acomodando a e/s de arquivo de tempo de execução do Windows

Programas em execução no Windows Runtime usam classes nos namespaces de [`Windows.Storage`](/uwp/api/Windows.Storage) e [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) para e/s de arquivo, incluindo O armazenamento local do aplicativo. Porque a Microsoft determinou que qualquer operação que exigem que mais de 50 milissegundos devem ser assíncronos para evitar o bloqueio do thread da interface do usuário, esses métodos de e/s de arquivo são basicamente assíncronos.

O código que demonstra essa nova abordagem será em uma biblioteca para que ele pode ser usado por outros aplicativos.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas da plataforma

É vantajoso para armazenar código reutilizável em bibliotecas. Isso é, obviamente, mais difícil quando diferentes partes do código reutilizável destinam-se totalmente diferentes sistemas operacionais.

A solução [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) demonstra uma abordagem. Essa solução contém sete diferentes projetos:

- [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), uma PCL do Xamarin. Forms normal
- [**Xamarin. FormsBook. Platform. Ios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), uma biblioteca de classes do IOS
- [**Xamarin. FormsBook. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), uma biblioteca de classes do Android
- [**Xamarin. FormsBook. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), uma biblioteca de classes universal do Windows
- [**Xamarin. FormsBook. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), um projeto compartilhado para o código que é comum a todas as plataformas Windows

Todos os projetos da plataforma individual (com exceção do **xamarin. FormsBook. Platform. WinRT**) têm referências a **Xamarin. FormsBook. Platform**. Os três projetos do Windows têm uma referência a **Xamarin. FormsBook. Platform. WinRT**.

Todos os projetos contêm um método de `Toolkit.Init` estático para garantir que a biblioteca seja carregada se não for referenciada diretamente por um projeto em uma solução de aplicativo Xamarin. Forms.

O projeto **Xamarin. FormsBook. Platform** contém a nova interface [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) . Agora, todos os métodos têm nomes com `Async` sufixos e retornam `Task` objetos.

O projeto **Xamarin. FormsBook. Platform. WinRT** contém a classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) para o Windows Runtime.

O projeto **Xamarin. FormsBook. Platform. Ios** contém a classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) para Ios. Esses métodos agora devem ser assíncronos. Alguns dos métodos usam as versões assíncronas dos métodos definidos em `StreamWriter` e `StreamReader`: [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync). Outros convertem um resultado em um objeto `Task` usando o método [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) .

O projeto **Xamarin. FormsBook. Platform. Android** contém uma classe de [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) semelhante para Android.

O projeto **Xamarin. FormsBook. Platform** também contém uma classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) que facilita o uso do objeto `DependencyService`.

Para usar essas bibliotecas, uma solução de aplicativo deve incluir todos os projetos na solução **xamarin. FormsBook. Platform** , e cada um dos projetos de aplicativo deve ter uma referência à biblioteca correspondente no **Xamarin. FormsBook. Platform**.

A solução [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) demonstra como usar as bibliotecas **Xamarin. FormsBook. Platform** . Cada um dos projetos tem uma chamada para `Toolkit.Init`. O aplicativo faz uso da e/s de arquivo assíncrona funções.

### <a name="keeping-it-in-the-background"></a>Mantê-lo em segundo plano

Os métodos em bibliotecas que fazem chamadas para vários métodos assíncronos &mdash; como os métodos `WriteFileAsync` e `ReadFileASync` na classe Windows Runtime [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; podem ser tornados um pouco mais eficientes usando o método [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) para evitar voltar para o thread da interface do usuário.

### <a name="dont-block-the-ui-thread"></a>Não bloquear o thread de interface do usuário!

Às vezes, é tentador evitar o uso de `ContinueWith` ou `await` usando a propriedade [`Result`](xref:System.Threading.Tasks.Task`1.Result) nos métodos. Isso deve ser evitado para que ele pode bloquear o thread de interface do usuário ou até mesmo travar o aplicativo.

## <a name="your-own-awaitable-methods"></a>Seus próprios métodos awaitable

Você pode executar algum código de forma assíncrona passando-o para um dos métodos [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) . Você pode chamar `Task.Run` em um método assíncrono que lida com parte da sobrecarga.

Os vários padrões de `Task.Run` são discutidos abaixo.

### <a name="the-basic-mandelbrot-set"></a>O conjunto de Mandelbrot básico

Para desenhar o conjunto de Mandelbrot em tempo real, a biblioteca [**Xamarin. Forms. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tem uma estrutura [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) semelhante à do namespace `System.Numerics`.

O exemplo [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) tem um método `CalculateMandeblotAsync` em seu arquivo code-behind que calcula o conjunto de Mandelbrot básico preto e branco e usa [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) para colocá-lo em um bitmap.

### <a name="marking-progress"></a>Marcação de progresso

Para relatar o progresso de um método assíncrono, você pode criar uma instância de uma [`Progress<T>`](xref:System.Progress`1) classe e definir seu método assíncrono para ter um argumento do tipo [`IProgress<T>`](xref:System.IProgress`1). Isso é demonstrado no exemplo de [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Cancelando o trabalho

Você também pode escrever um método assíncrono para ser cancelável. Você começa com uma classe chamada [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource). A propriedade [`Token`](xref:System.Threading.CancellationTokenSource.Token) é um valor do tipo [`CancellationToken`](xref:System.Threading.CancellationToken). Isso é passado para a função assíncrona. Um programa chama o método [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) de `CancellationTokenSource` (geralmente em resposta a uma ação pelo usuário) para cancelar a função assíncrona.

O método assíncrono pode verificar periodicamente a propriedade [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) de `CancellationToken` e sair se a propriedade for `true`ou simplesmente chamar o método [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) ; nesse caso, o método termina com um [`OperationCancelledException`](xref:System.OperationCanceledException).

O exemplo [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) demonstra o uso de uma função cancelável.

### <a name="an-mvvm-mandelbrot"></a>An MVVM Mandelbrot

O exemplo [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) tem uma interface de usuário mais abrangente e é basicamente baseado em uma [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) classes:

[![Captura de tela tripla de Mandelbrot X F](images/ch20fg13-small.png "Mandelbrot MVVM")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Volta para a web

A classe [`WebRequest`](xref:System.Net.WebRequest) usada em alguns exemplos usa um protocolo assíncrono antigo chamado modelo de programação assíncrona ou APM. Você pode converter essa classe no protocolo TAP moderno usando um dos métodos `FromAsync` na classe [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) . O exemplo [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [Capítulo 20 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capítulo 20 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md)
