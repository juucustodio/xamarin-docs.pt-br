---
title: Transferência em segundo plano e NSURLSession no Xamarin. iOS
description: Este documento fornece instruções que demonstram como usar a transferência em segundo plano e NSUrlSession para iniciar o download de uma imagem grande e continuar o download quando o aplicativo for colocado em segundo plano.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/02/2020
ms.openlocfilehash: b95b78ed36fcec122006cab7ce9663c43f9b3096
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436657"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Transferência em segundo plano e NSURLSession no Xamarin. iOS

Uma transferência em segundo plano é iniciada Configurando uma `NSURLSession` tarefa de carregar e enfileirar tarefas de carregamento ou download. Se as tarefas forem concluídas enquanto o aplicativo estiver em segundo plano, suspenso ou encerrado, o iOS notificará o aplicativo chamando o manipulador de conclusão no *AppDelegate*do aplicativo. O diagrama a seguir demonstra isso em ação:

 [![Uma transferência em segundo plano é iniciada Configurando uma NSURLSession em segundo plano e enfileirando tarefas de carregamento ou download](background-transfer-walkthrough-images/transfer.png)](background-transfer-walkthrough-images/transfer.png#lightbox)

## <a name="configuring-a-background-session"></a>Configurando uma sessão de plano de fundo

Para fazer uma sessão de plano de fundo, crie um novo `NSUrlSession` e configure-o usando um `NSUrlSessionConfiguration` objeto.

O objeto de configuração determina o que a sessão pode fazer e os tipos de tarefas que ele pode executar.
As sessões configuradas usando o `CreateBackgroundSessionConfiguration` método serão executadas em um processo separado e executarão transferências (WiFi) condicionais para preservar os dados e a vida útil da bateria.
O exemplo de código a seguir demonstra a configuração correta de uma sessão de transferência em segundo plano usando o `CreateBackgroundSessionConfiguration` método e um identificador de cadeia de caracteres exclusivo:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, new MySessionDelegate(), new NSOperationQueue());

}
```

Além de um objeto de configuração, uma sessão também requer um delegado de sessão e uma fila.
A fila determina a ordem na qual as tarefas serão concluídas. O delegado de sessão chaperones o processo de transferência e manipula a autenticação, o cache e outros problemas relacionados à sessão.

## <a name="working-with-tasks-and-delegates"></a>Trabalhando com tarefas e delegados

Agora que configuramos uma sessão de plano de fundo, vamos disparar tarefas para lidar com a transferência. Podemos manter o controle dessas tarefas usando a instância do `NSUrlSessionDelegate` chamada um delegado de sessão. O delegado de sessão é responsável por ativar um aplicativo finalizado ou suspenso em segundo plano para lidar com a autenticação, os erros ou a conclusão da transferência.

Um `NSUrlSessionDelegate` fornece os seguintes métodos básicos para verificar o status da transferência:

- *DidFinishEventsForBackgroundSession* -esse método é chamado quando todas as tarefas são concluídas e a transferência é concluída.
- *DidReceiveChallenge* -chamado para solicitar credenciais quando a autorização é necessária.
- *DidBecomeInvalidWithError* – chamado se o  `NSURLSession` se tornar invalidado.

As sessões em segundo plano exigem delegados mais especializados, dependendo dos tipos de tarefas em execução. As sessões em segundo plano são limitadas a dois tipos de tarefas:

- *Tarefas de carregamento* – tarefas do tipo  `NSUrlSessionUploadTask` usam a `INSUrlSessionTaskDelegate` interface, que implementa `INSUrlSessionDelegate` . Isso fornece métodos adicionais para acompanhar o progresso do upload, manipular o redirecionamento de HTTP e muito mais.
- *Tarefas de download* -tarefas do tipo  `NSUrlSessionDownloadTask` use a `INSUrlSessionDownloadDelegate` interface, que implementa `INSUrlSessionDelegate` e `INSUrlSessionTaskDelegate` . Isso fornece todos os métodos para carregar tarefas, bem como métodos específicos de download para acompanhar o progresso do download e determinar quando uma tarefa de download foi retomada ou concluída.

O código a seguir define uma tarefa que pode ser usada para baixar uma imagem de uma URL. A tarefa é iniciada chamando `CreateDownloadTask` na sessão de segundo plano e passando a solicitação de URL:

```csharp
const string DownloadURLString = "http://xamarin.com/images/xamarin.png"; // or other hosted file
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Em seguida, crie um novo delegado de download de sessão para manter o controle de todas as tarefas de download nesta sessão. A classe delegate deve herdar de `NSObject` e implementar a interface necessária:

```csharp
public class MySessionDelegate : NSObject, INSUrlSessionDownloadDelegate
{
  public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Para descobrir o progresso de uma tarefa de download, substitua o `DidWriteData` método para acompanhar o progresso e até mesmo atualize a interface do usuário. As atualizações da interface do usuário aparecerão imediatamente se o aplicativo estiver em primeiro plano ou estará aguardando o usuário na próxima vez que abrir o aplicativo.

A API de delegação de sessão fornece um amplo kit de ferramentas para interagir com tarefas. Para obter uma lista completa de métodos de delegação de sessão, consulte a `NSUrlSessionDelegate` documentação da API.

> [!IMPORTANT]
> As sessões em segundo plano são iniciadas em um thread em segundo plano, portanto, qualquer chamada para atualizar a interface do usuário deve ser executada explicitamente no thread da interface do usuário chamando `InvokeOnMainThread` para evitar o encerramento do aplicativo. 

## <a name="handling-transfer-completion"></a>Lidando com a conclusão da transferência

A etapa final é permitir que o aplicativo saiba quando todas as tarefas associadas à sessão foram concluídas e manipular o novo conteúdo.

No `AppDelegate` , assine o `HandleEventsForBackgroundUrl` evento. Quando o aplicativo entra no plano de fundo e uma sessão de transferência está em execução, esse método é chamado e o sistema nos passa um manipulador de conclusão:

```csharp
public System.Action backgroundSessionCompletionHandler;

public void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Use o manipulador de conclusão para permitir que o iOS saiba quando o processamento de nosso aplicativo é concluído.

Lembre-se de que uma sessão pode gerar várias tarefas para processar uma transferência. Quando a última tarefa for concluída, um aplicativo suspenso ou encerrado será iniciado novamente em segundo plano. Em seguida, o aplicativo se conecta novamente ao `NSURLSession` usando o identificador de sessão exclusivo e chama `DidFinishEventsForBackgroundSession` o delegado da sessão. Esse método é a oportunidade do aplicativo para lidar com o novo conteúdo, incluindo a atualização da interface do usuário para refletir os resultados da transferência:

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Depois de concluir o tratamento de novo conteúdo, chame o manipulador de conclusão para permitir que o sistema saiba que é seguro tirar um instantâneo do aplicativo e voltar para o modo de suspensão:

```csharp
public void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

Este tutorial abordou as etapas básicas para implementar o serviço de transferência em segundo plano no iOS 7 e mais recente.

## <a name="related-links"></a>Links relacionados

- [Transferência simples em segundo plano (exemplo)](/samples/xamarin/ios-samples/simplebackgroundtransfer)