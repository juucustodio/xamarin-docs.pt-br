---
title: Resumo do capítulo 20. E/s assíncrona e de arquivo
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 20. E/s assíncrona e de arquivo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 7d9630840983b36204214927136e0c9efe07d840
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058227"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumo do capítulo 20. E/s assíncrona e de arquivo

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

 Uma interface gráfica do usuário deve responder a eventos de entrada do usuário em sequência. Isso significa que todo o processamento de eventos de entrada do usuário deve ocorrer em um único thread, geralmente chamado de *thread principal* ou o *thread de interface do usuário*.

Os usuários esperam interfaces gráficas do usuário seja ágil na resposta. Isso significa que um programa deve processar os eventos de entrada do usuário rapidamente. Se isso não for possível, então o processamento deve estariam relegado às secundários threads de execução.

Vários programas de exemplo neste livro tem usado o [ `WebRequest` ](xref:System.Net.WebRequest) classe. Nessa classe de [ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) método inicia um thread de trabalho, que chama uma função de retorno de chamada quando ela for concluída. No entanto, essa função de retorno de chamada é executado no thread de trabalho, para que o programa deve chamar [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método para acessar a interface do usuário.

> [!NOTE]
> Programas do xamarin. Forms devem usar [ `HttpClient` ](xref:System.Net.Http.HttpClient) vez [ `WebRequest` ](xref:System.Net.WebRequest) para acessar arquivos pela internet. `HttpClient` dá suporte a operações assíncronas.

Uma abordagem mais moderna para processamento assíncrono está disponível no .NET e c#. Isso envolve a [ `Task` ](xref:System.Threading.Tasks.Task) e [ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1) classes e outros tipos no [ `System.Threading` ](xref:System.Threading) e [ `System.Threading.Tasks` ](xref:System.Threading.Tasks) namespaces, bem como o C# 5.0 `async` e `await` palavras-chave. Esse é o que este capítulo se concentra em.

## <a name="from-callbacks-to-await"></a>De retornos de chamada para aguardar

O `Page` própria classe contém três métodos assíncronos para exibir caixas de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Retorna um `Task` objeto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Retorna um `Task<bool>` objeto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Retorna um `Task<string>` objeto

O `Task` objetos indicam que esses métodos implementam o padrão de assíncrono baseado em tarefas, conhecido como toque. Eles `Task` objetos são retornados rapidamente a partir do método. O `Task<T>` retornar valores constituem uma "promessa" de um valor do tipo `TResult` estarão disponíveis quando a tarefa é concluída. O `Task` valor retornado indica uma ação assíncrona que será concluída mas sem nenhum valor retornada.

Nesses casos, o `Task` é concluída quando o usuário fechar a caixa de alerta.  

### <a name="an-alert-with-callbacks"></a>Um alerta com retornos de chamada

O [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) exemplo demonstra como manipular `Task<bool>` retornar objetos e `Device.BeginInvokeOnMainThread` chamadas usando os métodos de retorno de chamada.

### <a name="an-alert-with-lambdas"></a>Um alerta com lambdas

O [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) exemplo demonstra como usar as funções lambda anônima para manipulação `Task` e `Device.BeginInvokeOnMainThread` chamadas.  

### <a name="an-alert-with-await"></a>Um alerta com await

Uma abordagem mais simples envolve a `async` e `await` palavras-chave introduzidas no c# 5. O [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) demonstra seu uso.

### <a name="an-alert-with-nothing"></a>Um alerta com nada

Se o método assíncrono retorna `Task` em vez de `Task<TResult>`, em seguida, o programa não precisa usar qualquer uma dessas técnicas se ele não precise saber quando a tarefa assíncrona é concluída. O [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) demonstra isso.

### <a name="saving-program-settings-asynchronously"></a>Salvando as configurações do programa de forma assíncrona

O [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) exemplo demonstra o uso da [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método `Application` para salvar as configurações do programa conforme mudam sem substituindo o `OnSleep` método.

### <a name="a-platform-independent-timer"></a>Um temporizador independente de plataforma

É possível usar [ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32)) para criar um temporizador independente de plataforma. O [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) demonstra isso.

## <a name="file-inputoutput"></a>Arquivo de entrada/saída

Tradicionalmente, o .NET [ `System.IO` ](xref:System.IO) namespace tem sido a fonte de suporte de e/s de arquivo. Embora alguns métodos neste namespace dão suporte a operações assíncronas, a maioria não tem. O namespace também dá suporte a várias chamadas de método simples que executam funções de e/s de arquivo sofisticados.

### <a name="good-news-and-bad-news"></a>Boas e más notícias

Todas as plataformas com suporte do armazenamento local do aplicativo xamarin. Forms suporte &mdash; armazenamento privado para o aplicativo.

As bibliotecas do xamarin. IOS e xamarin. Android incluem uma versão do .NET que Xamarin tem expressamente personalizados para essas duas plataformas. Isso inclui classes de `System.IO` que você pode usar para executar e/s de arquivo com o armazenamento local do aplicativo nessas duas plataformas.

No entanto, se você pesquisar essas `System.IO` classes em uma PCL do xamarin. Forms, você não encontrá-los. O problema é o arquivo Microsoft totalmente renovada e/s para a API de tempo de execução do Windows. Programas direcionados para Windows 8.1, Windows Phone 8.1 e a plataforma Universal do Windows não usa `System.IO` para e/s de arquivo.

Isso significa que você precisará usar o [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (discutidos primeiro [ **capítulo 9. Chamadas à API específicas da plataforma** ](chapter09.md) para implementar a e/s de arquivo.

> [!NOTE]
> Bibliotecas de classe portáteis foram substituídas por bibliotecas .NET Standard 2.0 e dá suporte a .NET Standard 2.0 [ `System.IO` ](xref:System.IO) tipos para todas as plataformas do xamarin. Forms. Ele não é mais necessário usar um `DependencyService` para a maioria das tarefas de e/s de arquivo. Ver [tratamento de arquivos no xamarin. Forms](~/xamarin-forms/app-fundamentals/files.md) uma abordagem mais moderna para e/s de arquivo.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Uma primeira tentativa de e/s de arquivo de plataforma cruzada

O [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) exemplo define uma [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interface para e/s de arquivo e implementações dessa interface em todas as plataformas. No entanto, as implementações de tempo de execução do Windows não funcionam com os métodos nessa interface, pois os métodos de e/s de arquivo do Windows Runtime são assíncronos.

### <a name="accommodating-windows-runtime-file-io"></a>Acomodando a e/s de arquivo de tempo de execução do Windows

Programas em execução com o tempo de execução do Windows usam classes nos [ `Windows.Storage` ](/uwp/api/Windows.Storage) e [ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams) namespaces para o arquivo de e/s, incluindo o armazenamento local do aplicativo. Porque a Microsoft determinou que qualquer operação que exigem que mais de 50 milissegundos devem ser assíncronos para evitar o bloqueio do thread da interface do usuário, esses métodos de e/s de arquivo são basicamente assíncronos.

O código que demonstra essa nova abordagem será em uma biblioteca para que ele pode ser usado por outros aplicativos.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas da plataforma

É vantajoso para armazenar código reutilizável em bibliotecas. Isso é, obviamente, mais difícil quando diferentes partes do código reutilizável destinam-se totalmente diferentes sistemas operacionais.

O [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução demonstra uma abordagem. Essa solução contém sete diferentes projetos:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), uma PCL normal do xamarin. Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), uma biblioteca de classes do iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), uma biblioteca de classe do Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), uma biblioteca de classes do Windows Universal
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), um projeto compartilhado para o código que é comum a todas as plataformas do Windows

Todos os projetos de plataforma individual (com exceção de **Xamarin.FormsBook.Platform.WinRT**) têm referências a **Xamarin.FormsBook.Platform**. Os três projetos do Windows tem uma referência a **Xamarin.FormsBook.Platform.WinRT**.

Todos os projetos contêm um estático `Toolkit.Init` método para garantir que a biblioteca é carregada se não diretamente, ela é referenciada por um projeto em uma solução de aplicativo do xamarin. Forms.

O **Xamarin.FormsBook.Platform** projeto contém o novo [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interface. Todos os métodos têm agora nomes com `Async` sufixos e retorno `Task` objetos.

O **Xamarin.FormsBook.Platform.WinRT** projeto contém o [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe para o tempo de execução do Windows.

O **Xamarin.FormsBook.Platform.iOS** projeto contém o [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe para iOS. Esses métodos agora devem ser assíncronos. Alguns dos métodos usam as versões assíncronas dos métodos definidos no `StreamWriter` e `StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync). Outras convertem um resultado para um `Task` do objeto usando o [ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*) método.

O **Xamarin.FormsBook.Platform.Android** projeto contém um semelhante [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe para o Android.

O **Xamarin.FormsBook.Platform** projeto também contém uma [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) que facilita o uso da classe a `DependencyService` objeto.

Para usar essas bibliotecas, uma solução de aplicativo deve incluir todos os projetos de **Xamarin.FormsBook.Platform** solução e cada um dos projetos de aplicativo devem ter uma referência para a biblioteca correspondente em  **Xamarin.FormsBook.Platform**.

O [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) solução demonstra como usar o **Xamarin.FormsBook.Platform** bibliotecas. Cada um dos projetos tem uma chamada para `Toolkit.Init`. O aplicativo faz uso da e/s de arquivo assíncrona funções.

### <a name="keeping-it-in-the-background"></a>Mantê-lo em segundo plano

Os métodos nas bibliotecas que fazem chamadas para vários métodos assíncronos &mdash; como o `WriteFileAsync` e `ReadFileASync` métodos no tempo de execução do Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; pode ficar um pouco mais eficiente usando o [ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) método para evitar alternar de volta para o thread de interface do usuário.

### <a name="dont-block-the-ui-thread"></a>Não bloquear o thread de interface do usuário!

Às vezes, é tentador para evitar o uso de `ContinueWith` ou `await` usando o [ `Result` ](xref:System.Threading.Tasks.Task`1.Result) propriedade nos métodos. Isso deve ser evitado para que ele pode bloquear o thread de interface do usuário ou até mesmo travar o aplicativo.

## <a name="your-own-awaitable-methods"></a>Seus próprios métodos awaitable

Você pode executar algum código assincronamente passando-a para um dos [ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action)) métodos. Você pode chamar `Task.Run` dentro de um método assíncrono que lida com a parte da sobrecarga.

Os vários `Task.Run` padrões são discutidos abaixo.

### <a name="the-basic-mandelbrot-set"></a>O conjunto de Mandelbrot básico

Para desenhar o Mandelbrot definido em tempo real, o [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tem um [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) estrutura semelhante no `System.Numerics` namespace.

O [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) exemplo possui um `CalculateMandeblotAsync` método em seu arquivo code-behind que calcula o conjunto de Mandelbrot em preto e branco básico e usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)colocá-lo em um bitmap.

### <a name="marking-progress"></a>Marcação de progresso

Para relatar o progresso de um método assíncrono, você pode criar uma instância de um [ `Progress<T>` ](xref:System.Progress`1) classe e definir seu método assíncrono para ter um argumento do tipo [ `IProgress<T>` ](xref:System.IProgress`1). Isso é demonstrado na [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) exemplo.

### <a name="cancelling-the-job"></a>Cancelando o trabalho

Você também pode escrever um método assíncrono para ser cancelável. Começar com uma classe chamada [ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource). O [ `Token` ](xref:System.Threading.CancellationTokenSource.Token) propriedade é um valor do tipo [ `CancellationToken` ](xref:System.Threading.CancellationToken). Isso é passado para a função assíncrona. Um programa chama o [ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel) método `CancellationTokenSource` (geralmente em resposta a uma ação do usuário) para cancelar a função assíncrona.

O método assíncrono pode verificar periodicamente as [ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested) propriedade do `CancellationToken` e saia se a propriedade for `true`, ou simplesmente chamar o [ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) método, no Nesse caso, o método termina com um [ `OperationCancelledException` ](xref:System.OperationCanceledException).

O [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) exemplo demonstra o uso de uma função cancelável.

### <a name="an-mvvm-mandelbrot"></a>An MVVM Mandelbrot

O [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) exemplo tem uma interface de usuário mais ampla e baseia-se principalmente em um [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)classes:

[![Captura de tela de Mandelbrot X F tripla](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Volta para a web

O [ `WebRequest` ](xref:System.Net.WebRequest) classe usado em alguns exemplos de usa um protocolo assíncrono antigo, chamado de modelo de programação assíncrona ou APM. Você pode converter essa classe para o protocolo de toque modernos usando um dos `FromAsync` métodos em de [ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1) classe. O [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) demonstra isso.



## <a name="related-links"></a>Links relacionados

- [Capítulo 20 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capítulo 20 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabalhando com arquivos](~/xamarin-forms/app-fundamentals/files.md)
