---
title: iOS Backgrounding com tarefas
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 559c8ccce0ff41dccce0fdc93d6c18a1fee3db7d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding com tarefas

A maneira mais simples de realizar backgrounding no iOS é dividir seus requisitos backgrounding em tarefas e executar as tarefas em segundo plano. Tarefas estão em um limite de tempo rigorosa e normalmente obtém cerca de 600 segundos (10 minutos) de tempo de processamento depois que um aplicativo foi movida para o plano de fundo no iOS 6 e menos de 10 minutos no iOS 7 +.

Tarefas em segundo plano podem ser divididas em três categorias:

1.  **Tarefas do plano de fundo-Safe** - chamado em qualquer lugar o aplicativo onde você tem uma tarefa você não deseja interrompida, o aplicativo insira o plano de fundo.
1.  **Tarefas de DidEnterBackground** - chamado durante o `DidEnterBackground` método de ciclo de vida do aplicativo para ajudar a limpeza e o salvamento de estado.
1.  **Transferências (iOS 7 +) de plano de fundo** -um tipo especial de tarefa em segundo plano usado para executar transferências de rede no iOS 7. Ao contrário das tarefas regulares, transferências em segundo plano não tem um limite de tempo predeterminado.


Segurança de plano de fundo e `DidEnterBackground` tarefas são seguras para usar no iOS 6 e iOS 7, com algumas pequenas diferenças. Vamos investigar esses dois tipos de tarefas mais detalhadamente.

## <a name="creating-background-safe-tasks"></a>Criando tarefas de plano de fundo-Safe

Alguns aplicativos contêm tarefas que não devem ser interrompidas pelo iOS deve o aplicativo mudar de estado. É uma maneira de proteger essas tarefas sejam interrompidos para registrá-los com iOS como tarefas de longa execução. Você pode usar este padrão em qualquer lugar no seu aplicativo onde você não deseja que uma tarefa que está sendo interrompida deve colocar o usuário do aplicativo em segundo plano. Um ótimo candidato para esse padrão seria tarefas como enviar informações de registro de um novo usuário ao seu servidor ou verificando informações de logon.

O trecho de código a seguir demonstra como registrar uma tarefa para execução em segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

O processo de registro pares uma tarefa com um identificador exclusivo, `taskID`e, em seguida, organize-os na correspondência de `BeginBackgroundTask` e `EndBackgroundTask` chamadas. Para gerar o identificador, podemos fazer uma chamada para o `BeginBackgroundTask` método sobre o `UIApplication` de objeto e, em seguida, iniciar a tarefa de execução longa, geralmente em um novo thread. Quando a tarefa for concluída, podemos chamar `EndBackgroundTask` e passar o mesmo identificador. Isso é importante porque iOS para encerrar o aplicativo se um `BeginBackgroundTask` chamada não tem uma correspondência `EndBackgroundTask`.

> [!IMPORTANT]
> Tarefas do plano de fundo-safe podem executar no thread principal ou em um thread em segundo plano, dependendo das necessidades do aplicativo.


## <a name="performing-tasks-during-didenterbackground"></a>Realizar tarefas durante DidEnterBackground

Além de fazer uma tarefa de longa execução segura em segundo plano, o registro pode ser usado para iniciar tarefas como um aplicativo está sendo colocado em segundo plano. iOS fornece um método de evento no *AppDelegate* classe chamada `DidEnterBackground` que pode ser usado para salvar o estado do aplicativo, salve os dados de usuário e criptografar conteúdo confidencial antes que um aplicativo entre o plano de fundo. Um aplicativo tem cerca de cinco segundos para retornar desse método ou ele será encerrado. Portanto, o que podem levar mais de cinco segundos para concluir as tarefas de limpeza podem ser chamadas de dentro de `DidEnterBackground` método. Essas tarefas devem ser invocadas em um thread separado.

O processo é quase idêntico de registrar uma tarefa demorada. O trecho de código a seguir ilustra isso em ação:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Começamos, substituindo o `DidEnterBackground` método no `AppDelegate`, onde é registrar nossa tarefa via `BeginBackgroundTask` como fizemos no exemplo anterior. Em seguida, podemos gerar um novo thread e executar a nossa tarefa de longa duração. Observe que o `EndBackgroundTask` é agora feita chamada de dentro da tarefa de execução longa, desde o `DidEnterBackground` método será já devolvidos.

> [!IMPORTANT]
> iOS usa um [watchdog mecanismo](http://developer.apple.com/library/ios/qa/qa1693/_index.html) para garantir que a interface de usuário do aplicativo permanece responsivo. Um aplicativo que leva muito tempo em `DidEnterBackground` responderá na interface de usuário. Iniciar tarefas sejam executadas em segundo plano permite `DidEnterBackground` para retornar de maneira oportuna, mantendo a interface do usuário responsivo e impedindo que o watchdog eliminando o aplicativo.


## <a name="handling-background-task-time-limits"></a>Tratamento de limites de tempo de tarefa de plano de fundo

iOS limita estrito como longa uma tarefa em segundo plano pode ser executado e se o `EndBackgroundTask` chamada não é feita no tempo alocado, o aplicativo será encerrado. Controlando o backgrounding tempo restante e uso de manipuladores de expiração, quando necessário, podemos pode evitar encerrar o aplicativo do iOS.

### <a name="accessing-background-time-remaining"></a>Acessando o plano de fundo tempo restante

Se um aplicativo com tarefas registradas foi movido para o plano de fundo, as tarefas registradas obterá cerca de 600 segundos ser executado. Podemos verificar quanto tempo a tarefa tem para ser concluída com estático `BackgroundTimeRemaining` propriedade o `UIApplication` classe. O código a seguir fornece o tempo, em segundos, que deixou nossa tarefa em segundo plano:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitando o encerramento do aplicativo com os manipuladores de expiração

Além de fornecer acesso para o `BackgroundTimeRemaining` propriedade iOS fornece uma forma amigável para lidar com tempo de expiração em segundo plano por meio de um **expiração manipulador**. Esse é um bloco opcional de código que será executado quando o tempo alocado para uma tarefa está prestes a expirar. Chamadas de código no manipulador de expiração `EndBackgroundTask` e passa na ID de tarefa, que indica que o aplicativo está funcionando bem e impede que o iOS encerrando o aplicativo, mesmo se a tarefa é executada fora do tempo. `EndBackgroundTask` deve ser chamado dentro do manipulador de expiração, bem como no curso normal de execução. 

O manipulador de expiração é expresso como uma função anônima usando uma expressão lambda, conforme ilustrado abaixo:

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Enquanto os manipuladores de expiração não são necessários para executar o código, você sempre deve usar um manipulador de validade com uma tarefa em segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tarefas em segundo plano no iOS 7 +

A maior alteração no iOS 7 em relação a tarefas em segundo plano não é como as tarefas são implementadas, mas quando executadas.

Lembre-se de que pré-iOS 7, uma tarefa em execução em segundo plano tinha 600 segundos para concluir. Um motivo para esse limite é que uma tarefa em execução em segundo plano deve manter o dispositivo ativo para a duração da tarefa:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Gráfico da tarefa mantendo o aplicativo pré-iOS ativos 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

processamento em segundo plano de iOS 7 é otimizado para mais longa da bateria. No iOS 7, backgrounding se torna oportunista: em vez de manter o dispositivo ativo, tarefas respeitam quando o dispositivo passar a suspensão e, em vez disso, faça seu processamento em partes quando o dispositivo for ativado para lidar com chamadas telefônicas, notificações, entrada emails e outros interrupções comuns. O diagrama a seguir fornece informações sobre como uma tarefa pode ser interrompida se:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Gráfico da tarefa que está sendo dividido em blocos pós-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Porque a tempo de execução da tarefa não é mais contínua, tarefas que executam transferências de rede devem ser tratadas de forma diferente no iOS 7. Os desenvolvedores são incentivados a usar o `NSURlSession` API para controlar transferências de rede. A seção a seguir é uma visão geral das transferências em segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferências em segundo plano

O backbone de transferências em segundo plano no iOS 7 é o novo `NSURLSession` API. `NSURLSession` permite a criação de tarefas:

1.  Transferência de conteúdo por meio de interrupções de rede e dispositivo.
1.  Carregar e baixar arquivos grandes ( *o serviço de transferência em segundo plano* ).


Vamos examinar mais detalhadamente como isso funciona.

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` é uma API eficiente para transferir o conteúdo pela rede. Ele fornece um conjunto de ferramentas para lidar com a transferência de dados através de interrupções de rede e as alterações nos Estados de aplicativo.

O `NSURLSession` API cria uma ou várias sessões, que por sua vez geram tarefas para o painel blocos de dados relacionados em toda a rede. Tarefas executados de forma assíncrona para transferir dados de forma rápida e confiável. Porque `NSURLSession` é assíncrona, cada sessão requer um bloco do manipulador de conclusão para permitir que o sistema e o aplicativo saiba quando a transferência é concluída.

Para executar uma transferência de rede que seja válida em pré-lançamento iOS 7 e pós-iOS 7, verifique se um `NSURLSession` está disponível para transferências de enfileiramento e usar uma tarefa em segundo plano regular para executar a transferência se não for:

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> Evite fazer chamadas para atualizar a interface do usuário do plano de fundo no código do iOS 6 compatíveis, como iOS 6 não oferece suporte a atualizações de interface do usuário do plano de fundo e encerrar o aplicativo.


O `NSURLSession` API inclui um conjunto avançado de recursos para lidar com a autenticação, gerenciar transferências com falha e relatar erros de cliente - mas não no lado do servidor -. Ele ajuda a ponte que interrupções na tarefa executar hora introduzida no iOS 7 e também fornece suporte para transferir arquivos grandes de maneira rápida e confiável. A próxima seção explora Este segundo recurso.

### <a name="background-transfer-service"></a>Serviço de transferência em segundo plano

Antes do iOS 7, carregar ou baixar os arquivos em segundo plano era confiável. Tarefas em segundo plano tem um tempo limitado para ser executado, mas varia de acordo com o tempo necessário para transferir um arquivo com a rede e o tamanho do arquivo. No iOS 7, podemos usar um `NSURLSession` para carregar e baixar arquivos grandes com êxito. Particular `NSURLSession` tipo de sessão que manipula as transferências de rede de arquivos grandes em segundo plano é conhecido como o *o serviço de transferência em segundo plano*.

Transferências iniciadas usando o serviço de transferência em segundo plano são gerenciadas pelo sistema operacional e fornecem APIs para manipular erros e autenticação. Porque transferências não são associadas por um limite de tempo arbitrário, eles podem ser usados para carregar ou baixar arquivos grandes, o plano de fundo e muito mais conteúdo de atualização automática. Consulte o [passo a passo em segundo plano transferir](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obter detalhes sobre como implementar o serviço.

O serviço de transferência em segundo plano geralmente é emparelhado com busca em segundo plano ou notificações remoto para ajudar a atualizar o conteúdo no plano de fundo de aplicativos. Nas próximas duas seções, apresentamos o conceito de registro de inteira seja executado em segundo plano em iOS 6 e iOS 7.

