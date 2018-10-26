---
title: iOS Backgrounding com tarefas
description: Este documento descreve como usar tarefas em segundo plano para executar tarefas de longa execução depois que um aplicativo é colocado em segundo plano.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 48859afe2c988c1afe67d5c4350cef734f879fdf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120990"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding com tarefas

A maneira mais simples de executar backgrounding no iOS é dividir seus requisitos de backgrounding em tarefas e executar as tarefas em segundo plano. Tarefas estão em um limite estrito do tempo e obtém normalmente cerca de 600 segundos (10 minutos) de tempo de processamento depois que um aplicativo foi movida para o plano de fundo no iOS 6 e menos de 10 minutos no iOS 7 +.

Tarefas em segundo plano podem ser divididas em três categorias:

1.  **Tarefas em segundo plano Safe** - chamado em qualquer lugar no aplicativo em que você tem uma tarefa indesejadas interrompida deve o aplicativo inserir o plano de fundo.
1.  **Tarefas de DidEnterBackground** - chamado durante o `DidEnterBackground` método de ciclo de vida do aplicativo para ajudar a limpeza e o salvamento de estado.
1.  **Transferências (iOS 7 +) da tela de fundo** -um tipo especial de tarefa em segundo plano usado para executar transferências de rede no iOS 7. Ao contrário das tarefas regulares, transferências em segundo plano não tem um limite de tempo predeterminado.


Plano de fundo-safe e `DidEnterBackground` tarefas são seguras usar no iOS 6 e iOS 7, com algumas pequenas diferenças. Vamos investigar esses dois tipos de tarefas mais detalhadamente.

## <a name="creating-background-safe-tasks"></a>Criando tarefas de plano de fundo-Safe

Alguns aplicativos contêm as tarefas que não devem ser interrompidas por iOS deve o aplicativo mudar de estado. É uma maneira de proteger essas tarefas sejam interrompidos para registrá-los com o iOS como tarefas de longa execução. Você pode usar esse padrão em qualquer lugar em seu aplicativo onde você não deseja que uma tarefa que está sendo interrompida deve usuário colocar o aplicativo em segundo plano. Um ótimo candidato para esse padrão seria tarefas como enviar informações de registro de um novo usuário ao seu servidor ou verificando informações de logon.

O trecho de código a seguir demonstra como registrar uma tarefa para execução em segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

O processo de registro de uma tarefa com um identificador exclusivo, pares `taskID`e o encapsula em correspondência `BeginBackgroundTask` e `EndBackgroundTask` chamadas. Para gerar o identificador, podemos fazer uma chamada para o `BeginBackgroundTask` método no `UIApplication` de objeto e, em seguida, iniciar a tarefa de longa execução, geralmente em um novo thread. Quando a tarefa for concluída, podemos chamar `EndBackgroundTask` e passar o mesmo identificador. Isso é importante porque iOS para encerrar o aplicativo se um `BeginBackgroundTask` chamada não tem uma correspondência `EndBackgroundTask`.

> [!IMPORTANT]
> Tarefas em segundo plano safe podem ser executadas no thread principal ou em um thread em segundo plano, dependendo das necessidades do aplicativo.


## <a name="performing-tasks-during-didenterbackground"></a>Realizar tarefas durante DidEnterBackground

Além de tornar uma tarefa de longa execução em segundo plano-safe, o registro pode ser usado para disparar tarefas como um aplicativo está sendo colocado em segundo plano. iOS fornece um método de evento na *AppDelegate* classe chamada `DidEnterBackground` que pode ser usado para salvar o estado do aplicativo, salvar os dados de usuário e criptografar conteúdo confidencial antes que um aplicativo entra em segundo plano. Um aplicativo tem cerca de cinco segundos de retorno deste método ou ele será encerrado. Portanto, o que podem levar mais de cinco segundos para concluir as tarefas de limpeza podem ser chamadas de dentro de `DidEnterBackground` método. Essas tarefas devem ser invocadas em um thread separado.

O processo é quase idêntico de registrar uma tarefa de longa execução. O trecho de código a seguir ilustra isso em ação:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Começamos, substituindo o `DidEnterBackground` método na `AppDelegate`, em que registramos nossa tarefa por meio de `BeginBackgroundTask` como fizemos no exemplo anterior. Em seguida, podemos gerar um novo thread e executar a nossa tarefa de longa execução. Observe que o `EndBackgroundTask` é agora feita chamada de dentro da tarefa de execução longa, já que o `DidEnterBackground` método será já devolvidos.

> [!IMPORTANT]
> o iOS usa uma [mecanismo de watchdog](http://developer.apple.com/library/ios/qa/qa1693/_index.html) para garantir que a interface de usuário do aplicativo permaneça responsivo. Um aplicativo que gasta muito tempo na `DidEnterBackground` deixará de responder na interface do usuário. Iniciar as tarefas sejam executadas em segundo plano permite que `DidEnterBackground` retornar de maneira oportuna, manter a interface do usuário responsiva e impedindo que o watchdog encerrar o aplicativo.


## <a name="handling-background-task-time-limits"></a>Tratamento de limites de tempo de tarefa de segundo plano

iOS limita strict como longa uma tarefa em segundo plano pode ser executados e se o `EndBackgroundTask` chamada não é feita no tempo alocado, o aplicativo será encerrado. Manter o controle da backgrounding tempo restante e usando manipuladores de expiração quando necessário, podemos evitar encerrar o aplicativo do iOS.

### <a name="accessing-background-time-remaining"></a>Acessando o plano de fundo tempo restante

Se um aplicativo com tarefas registradas foi movido para o plano de fundo, as tarefas registradas receberá cerca de 600 segundos ser executado. Podemos verificar quanto tempo a tarefa tem para ser concluída usando estático `BackgroundTimeRemaining` propriedade do `UIApplication` classe. O código a seguir fornecerá o tempo, em segundos, o que saiu nossa tarefa em segundo plano:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitando o encerramento do aplicativo com manipuladores de expiração

Além de conceder acesso para o `BackgroundTimeRemaining` propriedade, o iOS fornece uma forma amigável para lidar com o tempo de expiração em segundo plano por meio de um **expiração manipulador**. Este é um bloco opcional de código que será executado quando o tempo alocado para uma tarefa está prestes a expirar. Chamadas de código no manipulador de expiração `EndBackgroundTask` e passa a ID da tarefa, que indica que o aplicativo está se comportando bem e impede que o iOS encerrando o aplicativo, mesmo se a tarefa é executada fora do tempo. `EndBackgroundTask` deve ser chamado dentro do manipulador de expiração, bem como no curso normal de execução. 

O manipulador de expiração é expressa como uma função anônima usando uma expressão lambda, conforme ilustrado abaixo:

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

Enquanto os manipuladores de expiração não são necessários para o código seja executado, você sempre deve usar um manipulador de expiração com uma tarefa em segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tarefas em segundo plano no iOS 7 +

A maior alteração no iOS 7 em relação a tarefas em segundo plano não é como as tarefas são implementadas, mas quando executados.

Lembre-se de que anterior ao iOS 7, uma tarefa em execução em segundo plano tinha 600 segundos para ser concluída. Um motivo para esse limite é que uma tarefa em execução em segundo plano seria manter o dispositivo ativo para a duração da tarefa:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Gráfico da tarefa mantendo o aplicativo pré-iOS ativos 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

processamento em segundo plano de iOS 7 é otimizado para a vida útil mais longa da bateria. No iOS 7, backgrounding se torna oportunista: em vez de manter o dispositivo ativo, tarefas respeitarem quando o dispositivo vai para o modo de suspensão e, em vez disso, faça o processamento em partes quando o dispositivo é ativado para lidar com chamadas telefônicas, notificações, emails de entrada e muito outros interrupções comuns. O diagrama a seguir fornece informações sobre como uma tarefa pode estar quebrada backup:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Gráfico da tarefa que está sendo dividido em partes pós-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Como a tempo de execução de tarefa não é mais contínua, tarefas que executam transferências de rede devem ser tratadas de forma diferente no iOS 7. Os desenvolvedores são incentivados a usar o `NSURlSession` API para manipular transferências de rede. A próxima seção é uma visão geral das transferências em segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferências em segundo plano

O backbone de transferências em segundo plano no iOS 7 é a nova `NSURLSession` API. `NSURLSession` nos permite criar tarefas para:

1.  Transferência de conteúdo por meio de interrupções de rede e dispositivo.
1.  Carregar e baixar arquivos grandes ( *o serviço de transferência em segundo plano* ).


Vamos examinar mais de perto como isso funciona.

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` é uma API eficiente para transferir conteúdo pela rede. Ele fornece um conjunto de ferramentas para lidar com a transferência de dados por meio de interrupções de rede e as alterações nos Estados de aplicativo.

O `NSURLSession` API cria uma ou várias sessões, que por sua vez, gerar tarefas para o painel blocos de dados relacionados em toda a rede. Tarefas executadas de forma assíncrona para transferir dados de forma rápida e confiável. Porque `NSURLSession` é assíncrona, cada sessão requer um bloco do manipulador de conclusão para permitir que o sistema e o aplicativo sabe quando a transferência é concluída.

Para executar uma transferência de rede que é válida em pré-iOS 7 e posterior ao iOS 7, verifique se um `NSURLSession` está disponível para transferências de enfileirar e usar uma tarefa em segundo plano regular para executar a transferência se não for:

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
> Evite fazer chamadas para atualizar a interface do usuário do plano de fundo no código do iOS em conformidade 6, iOS 6 não oferece suporte a atualizações de interface do usuário do plano de fundo e encerrar o aplicativo.


O `NSURLSession` API inclui um conjunto avançado de recursos para lidar com a autenticação, gerenciar transferências com falha e relatar erros do lado do cliente - mas não lado do servidor -. Ele ajuda a ponte que as interrupções na tarefa executar hora introduzida no iOS 7 e também fornece suporte para transferir arquivos grandes de forma rápida e confiável. A próxima seção explora Este segundo recurso.

### <a name="background-transfer-service"></a>Serviço de transferência em segundo plano

Antes do iOS 7, carregar ou baixar arquivos em segundo plano era confiável. Tarefas em segundo plano obtém um tempo limitado para ser executado, mas o tempo necessário para transferir um arquivo varia de acordo com a rede e o tamanho do arquivo. No iOS 7, podemos usar um `NSURLSession` para carregar e baixar arquivos grandes com êxito. Determinada `NSURLSession` tipo de sessão que manipula as transferências de rede de arquivos grandes em segundo plano é conhecido como o *o serviço de transferência em segundo plano*.

Transferências iniciadas usando o serviço de transferência em segundo plano são gerenciadas pelo sistema operacional e fornecem APIs para lidar com erros de autenticação e. Porque transferências não são associadas por um tempo limite arbitrário, eles podem ser usados para carregar ou baixar arquivos grandes, atualização automática de conteúdo na tela de fundo e muito mais. Consulte a [passo a passo em segundo plano transferir](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obter detalhes sobre como implementar o serviço.

O serviço de transferência em segundo plano geralmente é emparelhado com busca em segundo plano ou notificações remotas para ajudar aplicativos atualizar conteúdo em segundo plano. Nas próximas duas seções, apresentamos o conceito de registro de todos aplicativos sejam executados em segundo plano no iOS 6 e iOS 7.

