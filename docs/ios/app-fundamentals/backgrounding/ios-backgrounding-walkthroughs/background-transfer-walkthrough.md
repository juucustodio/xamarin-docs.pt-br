---
title: Transferência em segundo plano e NSURLSession no xamarin. IOS
description: Este documento fornece um passo a passo que demonstra como usar a transferência em segundo plano e NSUrlSession para iniciar o download de uma imagem grande e continuar esse download, quando o aplicativo é colocado em segundo plano.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e525388290d92901e68e61f1ffa81866f5aac4d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392201"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Transferência em segundo plano e NSURLSession no xamarin. IOS

Uma transferência em segundo plano é iniciada por meio da configuração de um plano de fundo `NSURLSession` e enfileirando carregar ou baixar tarefas. Se as tarefas forem concluídas enquanto o aplicativo é colocado em segundo plano, suspenso ou encerrado, o iOS notificará o aplicativo chamando o manipulador de conclusão na caixa de diálogo *AppDelegate*. O diagrama a seguir demonstra isso em ação:

 [![](background-transfer-walkthrough-images/transfer.png "Uma transferência em segundo plano é iniciada por meio da configuração de um plano de fundo NSURLSession e enfileirando carregar ou baixar tarefas")](background-transfer-walkthrough-images/transfer.png#lightbox)

Vamos ver como isso aparece no código.

## <a name="configuring-a-background-session"></a>Configurar uma sessão do plano de fundo

Para criar uma sessão de plano de fundo, crie um novo `NSUrlSession` e configurá-lo usando um `NSUrlSessionConfiguration` objeto.

O objeto de configuração determina o que pode fazer a sessão e os tipos de tarefas pode ser executada.
As sessões configuradas usando o `CreateBackgroundSessionConfiguration` método será executado em um processo separado e executar discricionárias transferências (WiFi) para preservar a vida útil da bateria e dados.
O exemplo de código a seguir demonstra a configuração apropriada de uma sessão de transferência em segundo plano usando o `CreateBackgroundSessionConfiguration` método e um identificador de cadeia de caracteres exclusiva:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

Além de um objeto de configuração, uma sessão também requer um delegado de sessão e uma fila.
A fila determina a ordem na qual as tarefas serão concluída. O delegado de sessão chaperones o processo de transferência e lida com a autenticação, armazenamento em cache e outros problemas relacionados à sessão.

## <a name="working-with-tasks-and-delegates"></a>Trabalhando com tarefas e delegados

Agora que configuramos uma sessão de plano de fundo, vamos disparar tarefas para lidar com a transferência. Podemos pode manter controle dessas tarefas usando o um `NSUrlSessionDelegate` instância chamada um delegado de sessão. O representante de sessão é responsável pela ativação de um aplicativo encerrado ou suspenso em segundo plano para autenticação de identificador, erros ou conclusão da transferência.

Um `NSUrlSessionDelegate` fornece os seguintes métodos básicos para verificar o status de transferência:

-  *DidFinishEventsForBackgroundSession* -esse método é chamado quando terminar todas as tarefas e a transferência for concluída.
-  *DidReceiveChallenge* - chamado a solicitação de credenciais quando a autorização é necessária.
-  *DidBecomeInvalidWithError* -chamado se o `NSURLSession` fica invalidada.


Sessões de plano de fundo exigem mais especializados delegados, dependendo dos tipos de tarefas que estão em execução. Sessões de plano de fundo são limitadas a dois tipos de tarefas:

-  *Carregar tarefas* -tarefas do tipo `NSUrlSessionUploadTask` usar o `NSUrlSessionTaskDelegate` , que herda de `NSUrlSessionDelegate` . Esse delegado fornece métodos adicionais para rastrear carregam progresso, redirecionamento de HTTP do identificador e muito mais.
-  *Tarefas de download* -tarefas do tipo `NSUrlSessionDownloadTask` usar o `NSUrlSessionDownloadDelegate` , que herda de `NSUrlSessionTaskDelegate` . Este delegado oferece que todos os métodos para carregar as tarefas, bem como métodos específicos do download para acompanhar o andamento do download e determinar quando uma tarefa de download foi retomada ou concluído.


O código a seguir define uma tarefa que pode ser usada para baixar uma imagem de uma URL. Começamos a tarefa chamando `CreateDownloadTask` em nossa sessão em segundo plano e passar a solicitação de URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Em seguida, vamos criar um delegado de download de nova sessão para controlar todas as tarefas de download nesta sessão:

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

Se quisermos descobrir o progresso de uma tarefa de download, podemos substituir o `DidWriteData` método para acompanhar o progresso e até mesmo atualizar a interface do usuário. Atualizações da interface do usuário serão exibida imediatamente se o aplicativo estiver em primeiro plano ou ficará aguardando o usuário na próxima vez que abrirem o aplicativo.

A API de delegado de sessão fornece um kit de ferramentas abrangente para interagir com as tarefas. Para obter uma lista completa de sessão delegar métodos, consulte o `NSUrlSessionDelegate` documentação da API.

> [!IMPORTANT]
> Sessões de plano de fundo são iniciadas em um thread em segundo plano, portanto, todas as chamadas para atualizar a interface do usuário devem ser explicitamente executadas no thread da interface do usuário chamando `InvokeOnMainThread` para evitar encerrar o aplicativo do iOS. 


## <a name="handling-transfer-completion"></a>Tratamento de conclusão da transferência

A etapa final é permitir que o aplicativo sabe quando todas as tarefas associadas com a sessão foram concluídos e lidar com o novo conteúdo.

No *AppDelegate*, assine o `HandleEventsForBackgroundUrl` eventos. Quando o aplicativo entra em segundo plano e uma sessão de transferência está em execução, esse método é chamado e o sistema passa-em um manipulador de conclusão:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Usaremos o manipulador de conclusão para informar iOS quando terminar de nosso aplicativo de processamento.

Lembre-se de que uma sessão pode gerar várias tarefas para processar uma transferência. Quando a última tarefa é concluída, um aplicativo suspenso ou foi encerrado é iniciado novamente na tela de fundo. Em seguida, o aplicativo se conectará novamente para o `NSURLSession` usando o identificador de sessão exclusivo e chama `DidFinishEventsForBackgroundSession` sobre o delegado de sessão. Esse método é a oportunidade do aplicativo para lidar com novo conteúdo, incluindo atualização de interface do usuário para refletir os resultados da transferência:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Depois que o tratamento de novos conteúdos estiverem concluídas, chamamos o manipulador de conclusão para informar ao sistema é seguro tirar um instantâneo do aplicativo e voltar ao modo de suspensão:

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

- [Transferência simples em segundo plano (amostra)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
