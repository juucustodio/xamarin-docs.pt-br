---
title: "Passo a passo - usando NSURLSession e o serviço de transferência em segundo plano"
description: "Neste passo a passo, usamos o serviço de transferência em segundo plano e a API NSURLSession para disparar baixar uma imagem grande que continua a fazer o download quando o aplicativo está em segundo plano."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d5a8baec164eb5c70f6dae5b2fa4fd5271afbd1c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="walkthrough---using-background-transfer-service-and-nsurlsession"></a>Passo a passo - usando NSURLSession e o serviço de transferência em segundo plano

_Neste passo a passo, usamos o serviço de transferência em segundo plano e a API NSURLSession para disparar baixar uma imagem grande que continua a fazer o download quando o aplicativo está em segundo plano._

Uma transferência em segundo plano é iniciada com a configuração de um plano de fundo `NSURLSession` e ao enfileirar carregar ou baixar tarefas. Se as tarefas forem concluídas enquanto o aplicativo é backgrounded, suspenso ou encerrado, o iOS notificará o aplicativo chamando o manipulador de conclusão do aplicativo *AppDelegate*. O diagrama a seguir demonstra isso em ação:

 [![](background-transfer-walkthrough-images/transfer.png "Uma transferência em segundo plano é iniciada com a configuração de um plano de fundo NSURLSession e ao enfileirar carregar ou baixar tarefas")](background-transfer-walkthrough-images/transfer.png#lightbox)

Vamos ver como isso aparece no código.

## <a name="configuring-a-background-session"></a>Configurar uma sessão do plano de fundo

Para criar uma sessão de plano de fundo, crie um novo `NSUrlSession` e configurá-lo usando um `NSUrlSessionConfiguration` objeto.

O objeto de configuração que determina o que pode fazer a sessão e os tipos de tarefas pode ser executada.
Sessões configuradas usando o `CreateBackgroundSessionConfiguration` método será executado em um processo separado e executar discricionárias transferências (WiFi) para preservar a vida útil da bateria e dados.
O exemplo de código a seguir demonstra a configuração correta de uma sessão de transferência em segundo plano usando o `CreateBackgroundSessionConfiguration` método e um identificador de cadeia de caracteres exclusiva:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate, new NSOperationQueue());

}
```

Além de um objeto de configuração, uma sessão também requer um delegado de sessão e uma fila.
A fila determina a ordem na qual as tarefas serão concluída. O representante de sessão chaperones o processo de transferência e gerencia a autenticação, armazenamento em cache e outros problemas relacionados à sessão.

## <a name="working-with-tasks-and-delegates"></a>Trabalhar com tarefas e delegados

Agora que configuramos uma sessão de plano de fundo, vamos iniciar tarefas para lidar com a transferência. Podemos pode manter controle dessas tarefas usando a um `NSUrlSessionDelegate` instância chamada um delegado de sessão. O representante de sessão é responsável por ativar um aplicativo encerrado ou suspenso em segundo plano para autenticação de identificador, erros ou conclusão de transferência.

Um `NSUrlSessionDelegate` fornece os métodos básicos a seguir para verificar o status da transferência:

-  *DidFinishEventsForBackgroundSession* -esse método é chamado quando terminar de todas as tarefas e a transferência for concluída.
-  *DidReceiveChallenge* - chamada a solicitação de credenciais quando a autorização é necessária.
-  *DidBecomeInvalidWithError* -chamado se o `NSURLSession` se torna invalidado.


Sessões de plano de fundo requerem mais especializados delegados dependendo dos tipos de tarefas que estão em execução. Sessões de plano de fundo são limitadas a dois tipos de tarefas:

-  *Carregar tarefas* -tarefas do tipo `NSUrlSessionUploadTask` usar o `NSUrlSessionTaskDelegate` , que herda de `NSUrlSessionDelegate` . Este delegado oferece métodos adicionais para controlar carregam progresso, redirecionamento de HTTP de identificador e muito mais.
-  *Baixar tarefas* -tarefas do tipo `NSUrlSessionDownloadTask` usar o `NSUrlSessionDownloadDelegate` , que herda de `NSUrlSessionTaskDelegate` . Este delegado fornece que todos os métodos para carregar tarefas, bem como métodos específicos de download para acompanhar o progresso do download e determinar quando uma tarefa de download foi retomado ou concluído.


O código a seguir define uma tarefa que pode ser usada para baixar uma imagem de uma URL. Começamos a tarefa chamando `CreateDownloadTask` em nossa sessão em segundo plano e passar a solicitação de URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Em seguida, vamos criar um delegado de download de nova sessão para controlar todas as tarefas de download nessa sessão:

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Se quisermos descobrir o progresso de uma tarefa de download, podemos substituir o `DidWriteData` método para controlar o andamento e até mesmo atualizar a interface do usuário. Atualizações de interface do usuário serão exibida imediatamente se o aplicativo estiver em primeiro plano ou ficará aguardando o usuário na próxima vez que abrirem o aplicativo.

A API de delegado de sessão fornece um kit de ferramentas abrangentes para interagir com as tarefas. Para obter uma lista completa de sessão delegar métodos, consulte o `NSUrlSessionDelegate` documentação da API.

> [!IMPORTANT]
> **Observação**: sessões de plano de fundo são iniciadas em um thread em segundo plano, portanto, todas as chamadas para atualizar a interface do usuário devem ser executadas explicitamente no thread da interface do usuário chamando `InvokeOnMainThread` para evitar encerrar o aplicativo do iOS. 


## <a name="handling-transfer-completion"></a>Tratamento de conclusão de transferência

A etapa final é permitir que o aplicativo saiba quando concluir todas as tarefas associadas com a sessão e tratar o conteúdo novo.

No *AppDelegate*, assinar o `HandleEventsForBackgroundUrl` evento. Quando o aplicativo entra no plano de fundo e uma sessão de transferência é executado, esse método é chamado e o sistema nos passa um manipulador de conclusão:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Usaremos o manipulador de conclusão para informar iOS quando é feito o nosso aplicativo de processamento.

Lembre-se de que uma sessão pode gerar várias tarefas para processar uma transferência. Quando a última tarefa é concluída, um aplicativo suspenso ou foi encerrado é iniciado novamente no plano de fundo. Em seguida, o aplicativo se conectará novamente para o `NSURLSession` usando o identificador de sessão exclusiva e chamadas `DidFinishEventsForBackgroundSession` sobre o delegado de sessão. Esse método é a oportunidade do aplicativo para lidar com novo conteúdo, incluindo a atualizar a interface do usuário para refletir os resultados da transferência:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Quando terminamos tratamento novo conteúdo, podemos chamar o manipulador de conclusão para informar o sistema é seguro fazer um instantâneo do aplicativo e voltar ao modo de suspensão:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
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

Neste passo a passo, abordamos as etapas básicas para implementar o serviço de transferência em segundo plano no iOS 7.



## <a name="related-links"></a>Links relacionados

- [Transferência em segundo plano simples (exemplo)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
