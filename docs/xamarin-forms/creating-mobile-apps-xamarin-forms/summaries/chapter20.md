---
title: "Resumo do capítulo 20. E/s assíncrona e arquivo"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0ac316bc2cef04a80958c047427845dbdcc4137f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumo do capítulo 20. E/s assíncrona e arquivo

 Uma interface gráfica do usuário deve responder a eventos de entrada do usuário em sequência. Isso significa que todo o processamento de eventos de entrada do usuário deve ocorrer em um único thread, geralmente chamado de *thread principal* ou *thread de interface do usuário*.

Os usuários esperam interfaces gráficas do usuário de resposta. Isso significa que um programa deve processar os eventos de entrada do usuário rapidamente. Se não for possível, então o processamento deve ser relegado a secundárias threads de execução.

Vários programas de exemplo deste livro tem usado o [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe. Essa classe de [ `BeginGetReponse` ](https://developer.xamarin.com/api/member/System.Net.WebRequest.BeginGetResponse/p/System.AsyncCallback/System.Object/) método inicia um thread de trabalho, que chama uma função de retorno de chamada quando ela for concluída. No entanto, essa função de retorno de chamada é executado no thread de trabalho, o programa deve chamar [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método para acessar a interface do usuário.

Uma abordagem mais moderna para o processamento assíncrono está disponível no .NET e c#. Isso envolve a [ `Task` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task/) e [ `Task<TResult>` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task%3CTResult%3E/) classes e outros tipos de [ `System.Threading` ](https://developer.xamarin.com/api/namespace/System.Threading/) e [ `System.Threading.Tasks` ](https://developer.xamarin.com/api/namespace/System.Threading.Tasks/) namespaces, bem como o c# 5.0 `async` e `await` palavras-chave. É o que este capítulo enfoca.

## <a name="from-callbacks-to-await"></a>De retornos de chamada para aguardar

O `Page` própria classe contém três métodos assíncronos para exibir caixas de alerta:

- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) Retorna um `Task` objeto
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) Retorna um `Task<bool>` objeto
- [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) Retorna um `Task<string>` objeto

O `Task` objetos indicam que esses métodos implementam o padrão de assíncrono baseado em tarefa, conhecido como toque. Essas `Task` objetos são retornados rapidamente do método. O `Task<T>` retornar valores constituem uma "promessa" que um valor do tipo `TResult` estará disponível quando a tarefa é concluída. O `Task` valor indica uma ação assíncrona que será retornada mas concluída sem nenhum valor de retorno.

Nesses casos, o `Task` é concluída quando o usuário fecha a caixa de alerta.  

### <a name="an-alert-with-callbacks"></a>Um alerta com retornos de chamada

O [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) exemplo demonstra como tratar `Task<bool>` retornar objetos e `Device.BeginInvokeOnMainThread` chamadas usando métodos de retorno de chamada.

### <a name="an-alert-with-lambdas"></a>Um alerta com lambdas

O [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) demonstra como usar funções de lambda anônimo para manipulação `Task` e `Device.BeginInvokeOnMainThread` chamadas.  

### <a name="an-alert-with-await"></a>Um alerta com await

Uma abordagem mais simples envolve o `async` e `await` palavras-chave introduzidas em c# 5. O [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) demonstra seu uso.

### <a name="an-alert-with-nothing"></a>Um alerta com nada

Se o método assíncrono retorna `Task` em vez de `Task<TResult>`, em seguida, o programa não precisa usar qualquer uma dessas técnicas se ele não precisa saber quando a tarefa assíncrona é concluída. O [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) exemplo demonstra isso.

### <a name="saving-program-settings-asynchronously"></a>Salvando configurações do programa de forma assíncrona

O [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) exemplo demonstra o uso do [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método `Application` para salvar configurações do programa, como eles mudam sem substituindo o `OnSleep` método.

### <a name="a-platform-independent-timer"></a>Um temporizador independente de plataforma

É possível usar [ `Task.Delay` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Delay/p/System.Int32/) para criar um timer independente de plataforma. O [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) exemplo demonstra isso.

## <a name="file-inputoutput"></a>Arquivo de entrada/saída

Tradicionalmente, o .NET [ `System.IO` ](https://developer.xamarin.com/api/namespace/System.IO/) namespace foi a fonte de suporte de e/s de arquivo. Embora alguns métodos no namespace de dar suporte a operações assíncronas, a maioria não tem. O namespace também oferece suporte a várias chamadas de método simples que executam funções de e/s de arquivo sofisticados.

### <a name="good-news-and-bad-news"></a>Boas e más notícias

Todas as plataformas com suporte no armazenamento local de aplicativo de suporte de xamarin. Forms &mdash; armazenamento privado para o aplicativo.

As bibliotecas de xamarin e xamarin incluem uma versão do .NET Xamarin expressamente foi desenvolvido para essas duas plataformas. Isso inclui classes de `System.IO` que você pode usar para executar e/s de arquivo com armazenamento local do aplicativo nessas duas plataformas.

No entanto, se você pesquisar essas `System.IO` classes um PCL xamarin. Forms, você não encontrá-los. O problema é que e/s do arquivo Microsoft completamente reformulada para a API de tempo de execução do Windows. Programas de direcionamento do Windows 8.1, Windows Phone 8.1 e a plataforma Universal do Windows não usa `System.IO` para e/s de arquivo.

Isso significa que você precisará usar o [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) (abordados primeiro [ **capítulo 9. Chamadas à API específica de plataforma** ](chapter09.md) para implementar a e/s de arquivo.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Uma primeira captura e/s de arquivo de plataforma cruzada

O [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) exemplo define uma [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interface para e/s de arquivo e implementações dessa interface em todas as plataformas. No entanto, as implementações de tempo de execução do Windows não funcionam com os métodos nessa interface porque os métodos de e/s de arquivo do Windows Runtime são assíncronos.

### <a name="accommodating-windows-runtime-file-io"></a>Acomodar e/s de arquivo de tempo de execução do Windows

Programas em execução com o tempo de execução do Windows usam classes de [ `Windows.Storage` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.aspx) e [ `Windows.Storage.Streams` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.aspx) namespaces para o arquivo de e/s, incluindo o armazenamento local do aplicativo. Como a Microsoft determinou que qualquer operação que exigem que mais de 50 milissegundos devem ser assíncronos para evitar o bloqueio de thread de interface do usuário, esses métodos de e/s de arquivo geralmente são assíncronos.

O código que demonstra essa nova abordagem será em uma biblioteca para que ele pode ser usado por outros aplicativos.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas da plataforma

É vantajoso para armazenar o código reutilizável em bibliotecas. Isso é obviamente mais difícil quando diferentes partes do código reutilizável para completamente diferentes sistemas operacionais.

O [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução demonstra uma abordagem. Esta solução contém sete diferentes projetos:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), um PCL xamarin. Forms normal
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), uma biblioteca de classes do iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), uma biblioteca de classe do Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), uma biblioteca de classes do Universal do Windows
- [**Xamarin.FormsBook.Platform.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Windows), um PCL para Windows 8.1.
- [**Xamarin.FormsBook.Platform.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinPhone), um PCL para Windows Phone 8.1
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), um projeto compartilhado para o código que é comuns a todas as plataformas do Windows

Todos os projetos individuais de plataforma (com exceção de **Xamarin.FormsBook.Platform.WinRT**) têm referências a **Xamarin.FormsBook.Platform**. Os três projetos do Windows têm uma referência a **Xamarin.FormsBook.Platform.WinRT**.

Todos os projetos contêm estático `Toolkit.Init` método para garantir que a biblioteca é carregada se não diretamente, ela é referenciada por um projeto em uma solução de aplicativo xamarin. Forms.

O **Xamarin.FormsBook.Platform** projeto contém o novo [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interface. Todos os métodos têm nomes com `Async` sufixos e retornar `Task` objetos.

O **Xamarin.FormsBook.Platform.WinRT** projeto contém o [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe para o tempo de execução do Windows.

O **Xamarin.FormsBook.Platform.iOS** projeto contém o [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe para iOS. Esses métodos agora devem ser assíncronos. Alguns dos métodos usam as versões assíncronas dos métodos definidos no `StreamWriter` e `StreamReader`: [ `WriteAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamWriter.WriteAsync/p/System.String/) e [ `ReadToEndAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamReader.ReadToEndAsync()/). Outras convertem um resultado para um `Task` objeto usando o [ `FromResult` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.FromResult%7BTResult%7D/p/TResult/) método.

O **Xamarin.FormsBook.Platform.Android** projeto contém semelhantes [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe para Android.

O **Xamarin.FormsBook.Platform** projeto também contém um [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) que facilita o uso da classe a `DependencyService` objeto.

Para usar essas bibliotecas, uma solução de aplicativo deve incluir todos os projetos no **Xamarin.FormsBook.Platform** solução e todos os projetos de aplicativo devem ter uma referência para a biblioteca correspondente em  **Xamarin.FormsBook.Platform**.

O [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) solução demonstra como usar o **Xamarin.FormsBook.Platform** bibliotecas. Cada um dos projetos tem uma chamada para `Toolkit.Init`. O aplicativo faz uso de e/s de arquivo assíncrono funções.

### <a name="keeping-it-in-the-background"></a>Mantê-lo em segundo plano

Métodos nas bibliotecas que fazer chamadas para métodos assíncronos vários &mdash; , como o `WriteFileAsync` e `ReadFileASync` métodos no Windows Runtime [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; pode ficar um pouco mais eficiente usando o [ `ConfigureAwait` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task%3CTResult%3E.ConfigureAwait/p/System.Boolean/) método para evitar alternar de volta para o thread de interface do usuário.

### <a name="dont-block-the-ui-thread"></a>Não bloqueiam o thread de interface do usuário!

Às vezes é tentador para evitar o uso de `ContinueWith` ou `await` usando o [ `Result` ](https://developer.xamarin.com/api/property/System.Threading.Tasks.Task%3CTResult%3E.Result/) propriedade sobre os métodos. Isso deve ser evitado para que ele pode bloquear o thread de interface do usuário ou até mesmo de suspensão do aplicativo.

## <a name="your-own-awaitable-methods"></a>Seus próprios métodos awaitable

Você pode executar algum código assincronamente passando-a uma da [ `Task.Run` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Run/p/System.Action/) métodos. Você pode chamar `Task.Run` dentro de um método assíncrono que lida com algumas a sobrecarga.

Vários `Task.Run` padrões são discutidos abaixo.

### <a name="the-basic-mandelbrot-set"></a>O conjunto de Mandelbrot básico

Para desenhar o Mandelbrot definido em tempo real, o [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tem um [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) estrutura semelhante ao `System.Numerics` namespace.

O [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) exemplo tem uma `CalculateMandeblotAsync` método no seu arquivo de code-behind que calcula o conjunto básico de Mandelbrot preto e branco e usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)colocá-lo em um bitmap.

### <a name="marking-progress"></a>Marcação de andamento

Para relatar o progresso de um método assíncrono, você pode instanciar uma [ `Progress<T>` ](https://developer.xamarin.com/api/type/System.Progress%3CT%3E/) classe e definir o método assíncrono para ter um argumento de tipo [ `IProgress<T>` ](https://developer.xamarin.com/api/type/System.IProgress%3CT%3E/). Isso é demonstrado no [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) exemplo.

### <a name="cancelling-the-job"></a>Cancelando o trabalho

Você também pode escrever um método assíncrono para ser cancelável. Começar com uma classe chamada [ `CancellationTokenSource` ](https://developer.xamarin.com/api/type/System.Threading.CancellationTokenSource/). O [ `Token` ](https://developer.xamarin.com/api/property/System.Threading.CancellationTokenSource.Token/) propriedade é um valor do tipo [ `CancellationToken` ](https://developer.xamarin.com/api/type/System.Threading.CancellationToken/). Isso é passado para a função assíncrona. Um programa chama o [ `Cancel` ](https://developer.xamarin.com/api/member/System.Threading.CancellationTokenSource.Cancel()/) método `CancellationTokenSource` (geralmente em resposta a uma ação do usuário) para cancelar a função assíncrona.

O método assíncrono pode verificar periodicamente o [ `IsCancellationRequested` ](https://developer.xamarin.com/api/property/System.Threading.CancellationToken.IsCancellationRequested/) propriedade `CancellationToken` e sair se a propriedade é `true`, ou simplesmente chamar o [ `ThrowIfCancellationRequested` ](https://developer.xamarin.com/api/member/System.Threading.CancellationToken.ThrowIfCancellationRequested()/) método, no Nesse caso, o método termina com um [ `OperationCancelledException` ](https://developer.xamarin.com/api/type/System.OperationCanceledException/).

O [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) exemplo demonstra o uso de uma função pode ser cancelado.

### <a name="an-mvvm-mandelbrot"></a>An MVVM Mandelbrot

O [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) exemplo tem uma interface de usuário mais ampla e baseia-se principalmente em um [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)classes:

[![Captura de tela de Mandelbrot X F tripla](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Voltar para a web

O [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe usada em alguns exemplos usa um protocolo de assíncrono antigo chamado de modelo de programação assíncrona ou APM. Você pode converter essa classe para o protocolo de toque moderno usando um do `FromAsync` métodos de [ `TaskFactory` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskFactory%3CTResult%3E/) classe. O [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) exemplo demonstra isso.



## <a name="related-links"></a>Links relacionados

- [20 de capítulo de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Exemplos de capítulo 20](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabalhando com arquivos](~/xamarin-forms/app-fundamentals/files.md)
