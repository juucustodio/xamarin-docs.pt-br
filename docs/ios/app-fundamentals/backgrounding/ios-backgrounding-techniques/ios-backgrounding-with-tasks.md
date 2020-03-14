---
title: Operação em segundo plano no iOS com tarefas
description: Este documento descreve como usar tarefas em segundo plano para executar tarefas de longa execução depois que um aplicativo é colocado em segundo plano.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f39ab83e00db1abd6508d26a9280fb708e681445
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304250"
---
# <a name="ios-backgrounding-with-tasks"></a>Operação em segundo plano no iOS com tarefas

A maneira mais simples de executar o plano de fundo no iOS é quebrar os requisitos de plano de fundo em tarefas e executar as tarefas em segundo plano. As tarefas estão abaixo de um limite de tempo estrito e geralmente têm cerca de 600 segundos (10 minutos) de tempo de processamento depois que um aplicativo é movido para o plano de fundo no iOS 6 e menos de 10 minutos no iOS 7 +.

As tarefas em segundo plano podem ser divididas em três categorias:

1. **Tarefas seguras em segundo plano** – chamadas em qualquer lugar no aplicativo em que você tem uma tarefa que você não deseja interromper se o aplicativo entrar no plano de fundo.
1. **Tarefas DidEnterBackground** -chamadas durante o `DidEnterBackground` método de ciclo de vida do aplicativo para auxiliar na limpeza e no salvamento de estado.
1. **Transferências em segundo plano (Ios 7 +)** – um tipo especial de tarefa em segundo plano usada para executar transferências de rede no Ios 7. Diferentemente das tarefas regulares, as transferências em segundo plano não têm um limite de tempo predeterminado.

As tarefas de plano de fundo e `DidEnterBackground` são seguras para uso no iOS 6 e no iOS 7, com algumas pequenas diferenças. Vamos investigar esses dois tipos de tarefas com mais detalhes.

## <a name="creating-background-safe-tasks"></a>Criando tarefas em segundo plano-seguras

Alguns aplicativos contêm tarefas que não devem ser interrompidas pelo iOS caso o aplicativo mude de estado. Uma maneira de proteger essas tarefas de ser interrompida é registrá-las com o iOS como tarefas de execução longa. Você pode usar esse padrão em qualquer lugar no aplicativo em que você não deseja que uma tarefa seja interrompida, caso o usuário Coloque o aplicativo em segundo plano. Um excelente candidato para esse padrão seria tarefas como enviar as informações de registro de um novo usuário para o servidor ou verificar as informações de logon.

O trecho de código a seguir demonstra como registrar uma tarefa para ser executado em segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

O processo de registro emparelha uma tarefa com um identificador exclusivo, `taskID`e, em seguida, a encapsula em chamadas de `BeginBackgroundTask` e `EndBackgroundTask` correspondentes. Para gerar o identificador, fazemos uma chamada para o método `BeginBackgroundTask` no objeto `UIApplication` e, em seguida, iniciamos a tarefa de execução longa, geralmente em um novo thread. Quando a tarefa for concluída, chamamos `EndBackgroundTask` e passamos o mesmo identificador. Isso é importante porque o iOS encerrará o aplicativo se uma chamada de `BeginBackgroundTask` não tiver um `EndBackgroundTask`correspondente.

> [!IMPORTANT]
> As tarefas seguras em segundo plano podem ser executadas no thread principal ou em um thread em segundo plano, dependendo das necessidades do aplicativo.

## <a name="performing-tasks-during-didenterbackground"></a>Executando tarefas durante DidEnterBackground

Além de tornar uma tarefa de execução longa em segundo plano, o registro pode ser usado para disparar tarefas à medida que um aplicativo está sendo colocado em segundo plano. o iOS fornece um método de evento na classe *AppDelegate* chamado `DidEnterBackground` que pode ser usado para salvar o estado do aplicativo, salvar dados do usuário e criptografar conteúdo confidencial antes de um aplicativo entrar no plano de fundo. Um aplicativo tem aproximadamente cinco segundos para retornar desse método ou ele será encerrado. Portanto, as tarefas de limpeza que podem levar mais de cinco segundos para serem concluídas podem ser chamadas de dentro do método de `DidEnterBackground`. Essas tarefas devem ser invocadas em um thread separado.

O processo é quase idêntico ao do registro de uma tarefa de execução longa. O trecho de código a seguir ilustra isso em ação:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Começamos substituindo o método `DidEnterBackground` na `AppDelegate`, onde registramos nossa tarefa por meio de `BeginBackgroundTask` como fizemos no exemplo anterior. Em seguida, geramos um novo thread e executamos nossa tarefa de execução longa. Observe que a chamada de `EndBackgroundTask` agora é feita de dentro da tarefa de execução longa, já que o método `DidEnterBackground` já terá retornado.

> [!IMPORTANT]
> o iOS usa um [mecanismo de Watchdog](https://developer.apple.com/library/ios/qa/qa1693/_index.html) para garantir que a interface do usuário de um aplicativo permaneça responsiva. Um aplicativo que passa muito tempo em `DidEnterBackground` deixará de responder na interface do usuário. Iniciar as tarefas para serem executadas em segundo plano permitem que `DidEnterBackground` retornem em tempo hábil, mantendo a interface do usuário responsiva e impedindo o Watchdog de interromper o aplicativo.

## <a name="handling-background-task-time-limits"></a>Manipulando limites de tempo de tarefa em segundo plano

o iOS coloca limites estritos de quanto tempo uma tarefa em segundo plano pode ser executada e, se a chamada de `EndBackgroundTask` não for feita dentro do tempo alocado, o aplicativo será encerrado. Acompanhando o tempo de fundo restante e usando os manipuladores de expiração quando necessário, podemos evitar que o iOS encerre o aplicativo.

### <a name="accessing-background-time-remaining"></a>Tempo de fundo restante do acesso

Se um aplicativo com tarefas registradas for movido para o plano de fundo, as tarefas registradas chegarão cerca de 600 segundos para serem executadas. Podemos verificar quanto tempo a tarefa tem para ser concluída usando a propriedade estática `BackgroundTimeRemaining` da classe `UIApplication`. O código a seguir nos fornecerá o tempo, em segundos, que a nossa tarefa em segundo plano saiu:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitando encerramento de aplicativo com manipuladores de expiração

Além de conceder acesso à propriedade `BackgroundTimeRemaining`, o iOS fornece uma maneira normal de lidar com a expiração do tempo de fundo por meio de um **manipulador de expiração**. Esse é um bloco de código opcional que será executado quando o tempo alocado para uma tarefa estiver prestes a expirar. O código no manipulador de expiração chama `EndBackgroundTask` e passa a ID da tarefa, que indica que o aplicativo está se comportando bem e impede que o iOS encerre o aplicativo, mesmo que a tarefa fique sem tempo. `EndBackgroundTask` deve ser chamado dentro do manipulador de expiração, bem como no curso normal de execução. 

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
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Embora os manipuladores de expiração não sejam necessários para que o código seja executado, você sempre deve usar um manipulador de expiração com uma tarefa em segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tarefas em segundo plano no iOS 7 +

A maior alteração no iOS 7 em relação às tarefas em segundo plano não é como as tarefas são implementadas, mas quando elas são executadas.

Lembre-se de que, antes do iOS 7, uma tarefa em execução em segundo plano tinha 600 segundos para ser concluída. Um motivo para esse limite é que uma tarefa em execução em segundo plano manteria o dispositivo ativo durante a tarefa:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Graph of the task keeping the app awake pre-iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

o processamento em segundo plano do iOS 7 é otimizado para duração mais longa da bateria. No iOS 7, o plano de fundo se torna oportunista: em vez de manter o dispositivo ativo, as tarefas respeitam quando o dispositivo entra em suspensão e, em vez disso, faz seu processamento em partes quando o dispositivo é ativado para lidar com chamadas telefônicas, notificações, emails de entrada e outros interrupções comuns. O diagrama a seguir fornece informações sobre como uma tarefa pode ser dividida:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Graph of the task being broken into chunks post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Como o tempo de execução da tarefa não é mais contínuo, as tarefas que executam transferências de rede devem ser tratadas de forma diferente no iOS 7. Os desenvolvedores são incentivados a usar a API de `NSURlSession` para lidar com transferências de rede. A próxima seção é uma visão geral das transferências em segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferências em segundo plano

O backbone de transferências em segundo plano no iOS 7 é a nova API `NSURLSession`. `NSURLSession` nos permite criar tarefas para:

1. Transfira conteúdo por meio de interrupções de rede e dispositivo.
1. Carregar e baixar arquivos grandes ( *serviço de transferência em segundo plano* ).

Vamos examinar mais de perto como isso funciona.

### <a name="nsurlsession-api"></a>API NSURLSession

 `NSURLSession` é uma API poderosa para transferir conteúdo pela rede. Ele fornece um conjunto de ferramentas para lidar com a transferência de dados por meio de interrupções de rede e alterações nos Estados do aplicativo.

A API de `NSURLSession` cria uma ou várias sessões, que, por sua vez, geram tarefas para bloquear blocos de dados relacionados na rede. As tarefas são executadas de forma assíncrona para transferir dados de forma rápida e confiável. Como `NSURLSession` é assíncrona, cada sessão requer um bloco de manipulador de conclusão para permitir que o sistema e o aplicativo saibam quando uma transferência for concluída.

Para executar uma transferência de rede válida no pre-iOS 7 e no pós-iOS 7, verifique se um `NSURLSession` está disponível para as transferências de enfileiramento e use uma tarefa de plano de fundo regular para executar a transferência se ela não for:

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
> Evite fazer chamadas para atualizar a interface do usuário de segundo plano no código em conformidade com o iOS 6, pois o iOS 6 não oferece suporte a atualizações de interface do usuário em segundo plano e encerrará o aplicativo.

A API do `NSURLSession` inclui um conjunto avançado de recursos para lidar com a autenticação, gerenciar transferências com falha e relatar erros do lado do cliente, mas não do lado do servidor. Ele ajuda a ligar as interrupções no tempo de execução da tarefa introduzido no iOS 7 e também fornece suporte para transferir arquivos grandes de forma rápida e confiável. A próxima seção explora esse segundo recurso.

### <a name="background-transfer-service"></a>Serviço de transferência em segundo plano

Antes do iOS 7, carregar ou baixar arquivos em segundo plano não era confiável. As tarefas em segundo plano têm um tempo limitado para serem executadas, mas o tempo necessário para transferir um arquivo varia de acordo com a rede e o tamanho do arquivo. No iOS 7, podemos usar um `NSURLSession` para carregar e baixar com êxito arquivos grandes. O tipo de sessão `NSURLSession` específico que lida com transferências de rede de arquivos grandes em segundo plano é conhecido como *serviço de transferência em segundo plano*.

As transferências iniciadas usando o serviço de transferência em segundo plano são gerenciadas pelo sistema operacional e fornecem APIs para lidar com a autenticação e os erros. Como as transferências não são associadas por um limite de tempo arbitrário, elas podem ser usadas para carregar ou baixar arquivos grandes, atualizar automaticamente o conteúdo em segundo plano e muito mais. Consulte a [orientação de transferência em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obter detalhes sobre como implementar o serviço.

O serviço de transferência em segundo plano geralmente é emparelhado com a busca em segundo plano ou notificações remotas para ajudar os aplicativos a atualizar o conteúdo em segundo plano. Nas próximas duas seções, apresentamos o conceito de registro de aplicativos inteiros para serem executados em segundo plano no iOS 6 e no iOS 7.
