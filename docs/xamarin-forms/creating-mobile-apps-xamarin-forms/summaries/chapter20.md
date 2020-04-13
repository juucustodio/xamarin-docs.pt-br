---
title: Resumo do Capítulo 20. E/S assíncrona e de arquivo
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 20. E/S assíncrona e de arquivo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771046"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumo do Capítulo 20. E/S assíncrona e de arquivo

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

 Uma interface gráfica do usuário deve responder a eventos de entrada de usuário sequencialmente. Isso implica que todo o processamento de eventos de entrada de usuário deve ocorrer em um único segmento, muitas vezes chamado de *segmento principal* ou *de ui*.

Os usuários esperam que as interfaces gráficas do usuário sejam responsivas. Isso significa que um programa deve processar rapidamente os eventos de entrada de usuário. Se isso não for possível, então o processamento deve ser relegado a segmentos secundários de execução.

Vários programas de amostragem [`WebRequest`](xref:System.Net.WebRequest) neste livro têm usado a classe. Nesta classe, [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) o método inicia um segmento de trabalhador, que chama uma função de retorno de chamada quando está concluída. No entanto, essa função de retorno de chamada [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) é executada no segmento trabalhador, então o programa deve chamar método para acessar a interface do usuário.

> [!NOTE]
> Os programas Xamarin.Forms devem ser usados [`HttpClient`](xref:System.Net.Http.HttpClient) em vez de [`WebRequest`](xref:System.Net.WebRequest) acessar arquivos pela internet. `HttpClient`suporta operações assíncronas.

Uma abordagem mais moderna para o processamento assíncrono está disponível em .NET e C#. Isso envolve [`Task`](xref:System.Threading.Tasks.Task) [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) as classes e outros [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) tipos nos espaços de nome e `async` nomes, bem como o C # 5.0 e `await` palavras-chave. É nisso que este capítulo se concentra.

## <a name="from-callbacks-to-await"></a>De retornos para esperar

A `Page` classe em si contém três métodos assíncronos para exibir caixas de alerta:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))retorna `Task` um objeto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))retorna `Task<bool>` um objeto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))retorna `Task<string>` um objeto

Os `Task` objetos indicam que esses métodos implementam o Padrão Assíncrono baseado em tarefas, conhecido como TAP. Esses `Task` objetos são devolvidos rapidamente a partir do método. Os `Task<T>` valores de devolução constituem uma `TResult` "promessa" de que um valor do tipo estará disponível quando a tarefa for concluída. O `Task` valor de retorno indica uma ação assíncrona que será concluída, mas sem valor devolvido.

Em todos esses `Task` casos, o é completo quando o usuário descarta a caixa de alerta.  

### <a name="an-alert-with-callbacks"></a>Um alerta com retornos de chamada

A amostra [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) demonstra `Task<bool>` como `Device.BeginInvokeOnMainThread` lidar com objetos de retorno e chamadas usando métodos de retorno de chamada.

### <a name="an-alert-with-lambdas"></a>Um alerta com lambdas

A amostra [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) demonstra como usar funções `Task` de `Device.BeginInvokeOnMainThread` lambda anônimas para manuseio e chamadas.  

### <a name="an-alert-with-await"></a>Um alerta com espera

Uma abordagem mais `async` `await` simples envolve as palavras-chave introduzidas em C # 5. A amostra [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) demonstra seu uso.

### <a name="an-alert-with-nothing"></a>Um alerta sem nada

Se o método assíncrono retornar `Task` em vez de `Task<TResult>`, então o programa não precisa usar nenhuma dessas técnicas se ele não precisa saber quando a tarefa assíncrona é concluída. A amostra [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) demonstra isso.

### <a name="saving-program-settings-asynchronously"></a>Salvando as configurações do programa de forma assíncrona

A amostra [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) demonstra [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) o `Application` uso do método de salvar as `OnSleep` configurações do programa à medida que elas mudam sem sobrepor o método.

### <a name="a-platform-independent-timer"></a>Um temporizador independente de plataforma

É possível usar [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) para criar um temporizador independente da plataforma. A amostra [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) demonstra isso.

## <a name="file-inputoutput"></a>Entrada/saída de arquivos

Tradicionalmente, o [`System.IO`](xref:System.IO) namespace .NET tem sido a fonte de suporte de I/O do arquivo. Embora alguns métodos neste namespace suportem operações assíncronas, a maioria não. O namespace também suporta várias chamadas de método simples que executam funções sofisticadas de I/O de arquivo.

### <a name="good-news-and-bad-news"></a>Boas e más notícias

Todas as plataformas suportadas pelo Xamarin.Forms suportam &mdash; o armazenamento local do aplicativo que é privado para o aplicativo.

As bibliotecas Xamarin.iOS e Xamarin.Android incluem uma versão do .NET que a Xamarin expressamente adaptou para essas duas plataformas. Estes incluem `System.IO` classes a partir do que você pode usar para executar i/O de arquivo com armazenamento local de aplicativos nessas duas plataformas.

No entanto, se `System.IO` você procurar por essas classes em um Xamarin.Forms PCL, você não as encontrará. O problema é que a Microsoft renovou completamente a I/O do arquivo para a API do Windows Runtime. Programas direcionados ao Windows 8.1, Windows Phone 8.1 `System.IO` e à Universal Windows Platform não usam para I/O de arquivos.

Isso significa que você precisará [`DependencyService`](xref:Xamarin.Forms.DependencyService) usar o (discutido pela primeira vez no [**Capítulo 9. A API específica da plataforma chama**](chapter09.md) para implementar I/O de arquivo.

> [!NOTE]
> Os Libaries de classe portátil foram substituídos [`System.IO`](xref:System.IO) por bibliotecas .NET Standard 2.0 e o .NET Standard 2.0 suporta tipos para todas as plataformas Xamarin.Forms. Não é mais necessário `DependencyService` usar um para a maioria das tarefas de I/O de arquivo. Consulte [O tratamento de arquivos em Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) para obter uma abordagem mais moderna para arquivar I/O.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Um primeiro tiro em I/O de arquivo multiplataforma

A amostra [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) define uma interface para I/O de arquivo e implementações desta interface em todas as plataformas. No entanto, as implementações do Windows Runtime não funcionam com os métodos nesta interface porque os métodos de I/O do arquivo Windows Runtime são assíncronos.

### <a name="accommodating-windows-runtime-file-io"></a>Acomodando i/O de arquivo de tempo de execução do Windows

Os programas em execução sob [`Windows.Storage`](/uwp/api/Windows.Storage) o [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) Windows Runtime usam classes nos espaços de nome e espaços de nome para I/O de arquivo, incluindo o armazenamento local do aplicativo. Como a Microsoft determinou que qualquer operação que exija mais de 50 milissegundos deve ser assíncrona para evitar o bloqueio do segmento de IA, esses métodos de I/O de arquivo são em sua maioria assíncronos.

O código que demonstra essa nova abordagem estará em uma biblioteca para que possa ser usado por outros aplicativos.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas da plataforma

É vantajoso armazenar código reutilizável em bibliotecas. Isso é obviamente mais difícil quando diferentes peças do código reutilizável são para sistemas operacionais completamente diferentes.

A solução [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) demonstra uma abordagem. Esta solução contém sete projetos diferentes:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), um Xamarin.Forms PCL normal
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), uma biblioteca de classes para iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), uma biblioteca de classes Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), uma biblioteca de classes universal do Windows
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), um projeto compartilhado para código que é comum em todas as plataformas windows

Todos os projetos individuais da plataforma (com exceção de **Xamarin.FormsBook.Platform.WinRT**) têm referências ao **Xamarin.FormsBook.Platform**. Os três projetos do Windows têm uma referência a **Xamarin.FormsBook.Platform.WinRT**.

Todos os projetos `Toolkit.Init` contêm um método estático para garantir que a biblioteca seja carregada se não for diretamente referenciada por um projeto em uma solução de aplicativo Xamarin.Forms.

O projeto **Xamarin.FormsBook.Platform** [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) contém a nova interface. Todos os métodos agora `Async` têm nomes com `Task` sufixos e objetos de devolução.

O projeto **Xamarin.FormsBook.Platform.WinRT** contém a [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe para o Tempo de Execução do Windows.

O projeto **Xamarin.FormsBook.Platform.iOS** contém a [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe para iOS. Esses métodos devem agora ser assíncronos. Alguns dos métodos utilizam as versões assíncronas dos métodos definidos `StreamWriter` em e : `StreamReader` [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync). Outros convertem um `Task` resultado [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) em um objeto usando o método.

O projeto **Xamarin.FormsBook.Platform.Android** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) contém uma classe semelhante para Android.

O projeto **Xamarin.FormsBook.Platform** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) também contém uma classe `DependencyService` que facilita o uso do objeto.

Para usar essas bibliotecas, uma solução de aplicativo deve incluir todos os projetos na solução **Xamarin.FormsBook.Platform,** e cada um dos projetos de aplicativo deve ter uma referência à biblioteca correspondente em **Xamarin.FormsBook.Platform**.

A solução [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) demonstra como usar as bibliotecas **Xamarin.FormsBook.Platform.** Cada um dos projetos `Toolkit.Init`tem uma chamada para . O aplicativo faz uso das funções assíncronas de I/O do arquivo.

### <a name="keeping-it-in-the-background"></a>Mantendo-o em segundo plano

Métodos em bibliotecas que fazem chamadas para &mdash; múltiplos `WriteFileAsync` métodos assíncronos, como os `ReadFileASync` métodos e métodos na classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; Windows Runtime, podem ser um pouco mais eficientes usando o [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) método para evitar mudar de volta para o segmento de interface do usuário.

### <a name="dont-block-the-ui-thread"></a>Não bloqueie o fio da ui!

Às vezes é tentador evitar `ContinueWith` o `await` uso [`Result`](xref:System.Threading.Tasks.Task`1.Result) ou usando a propriedade nos métodos. Isso deve ser evitado pois pode bloquear o segmento de interface do usuário ou até mesmo pendurar a aplicação.

## <a name="your-own-awaitable-methods"></a>Seus próprios métodos aguardados

Você pode executar algum código assíncronamente [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) passando-o para um dos métodos. Você pode `Task.Run` ligar dentro de um método de assincronia que lida com algumas das despesas gerais.

Os `Task.Run` vários padrões são discutidos abaixo.

### <a name="the-basic-mandelbrot-set"></a>O conjunto básico de Mandelbrot

Para desenhar o conjunto Mandelbrot em tempo real, a biblioteca [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) [**Xamarin.Forms.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tem uma estrutura semelhante à do `System.Numerics` namespace.

A amostra [**mandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) tem um `CalculateMandeblotAsync` método em seu arquivo de código atrás que calcula [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) o conjunto básico de Mandelbrot preto e branco e usa para colocá-lo em um bitmap.

### <a name="marking-progress"></a>Marcando o progresso

Para relatar o progresso de um método assíncrono, você pode instanciar [`Progress<T>`](xref:System.Progress`1) uma [`IProgress<T>`](xref:System.IProgress`1)classe e definir seu método assíncrono para ter um argumento de tipo . Isso é demonstrado na amostra [**mandelbrotProgress.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)

### <a name="cancelling-the-job"></a>Cancelando o trabalho

Você também pode escrever um método assíncrono para ser cancelável. Você começa com [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource)uma classe chamada . A [`Token`](xref:System.Threading.CancellationTokenSource.Token) propriedade é um [`CancellationToken`](xref:System.Threading.CancellationToken)valor de tipo. Isso é passado para a função assíncrona. Um programa [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) chama `CancellationTokenSource` o método de (geralmente em resposta a uma ação do usuário) para cancelar a função assíncrona.

O método assíncrono pode [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) verificar `CancellationToken` periodicamente a `true`propriedade e sair [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) se a propriedade é , [`OperationCancelledException`](xref:System.OperationCanceledException)ou simplesmente chamar o método, nesse caso o método termina com um .

A amostra [**MandelbrotCancelamento**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) demonstra o uso de uma função cancelável.

### <a name="an-mvvm-mandelbrot"></a>Um Mandelbrot MVVM

A amostra [**de MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) tem uma interface de usuário [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) mais [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) extensa, e é baseada principalmente em a e classes:

[![Captura de tela tripla de Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>De volta à web

A [`WebRequest`](xref:System.Net.WebRequest) classe usada em algumas amostras usa um protocolo assíncrono antiquado chamado Modelo de Programação Assíncrona ou APM. Você pode converter essa classe para o protocolo `FromAsync` TAP moderno [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) usando um dos métodos da classe. A amostra [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [Capítulo 20 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capítulo 20 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md)
