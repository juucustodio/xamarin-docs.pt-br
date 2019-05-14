---
title: Trabalhando com o Thread de interface do usuário no xamarin. IOS
description: Este documento descreve como trabalhar com o Thread de interface do usuário no xamarin. IOS. Ele aborda a execução do thread da interface do usuário, fornece um exemplo de thread em segundo plano e examina a async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 9bde539286b69569b4928bc28b20faecee0f5209
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557378"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Trabalhando com o Thread de interface do usuário no xamarin. IOS

Interfaces de usuário do aplicativo são sempre de thread único, mesmo em dispositivos com multithread – não há apenas uma representação da tela e as alterações para o que é exibido precisam ser coordenado por meio de um único 'ponto de acesso '. Isso impede que vários threads tentar atualizar o mesmo pixel ao mesmo tempo (por exemplo).

Seu código deve fazer apenas o thread de alterações em controles de interface na página principal do usuário (ou da interface do usuário). Todas as atualizações da interface do usuário que ocorrem em um thread diferente (por exemplo, um thread de retorno de chamada ou em segundo plano) não podem obter renderizadas na tela ou até mesmo poderia causar uma falha.

## <a name="ui-thread-execution"></a>Execução do Thread da interface do usuário

Quando você estiver criando controles em uma exibição ou manipular um evento iniciado pelo usuário, como um toque, o código já está em execução no contexto do thread da interface do usuário.

Se o código está em execução em um thread em segundo plano, em uma tarefa ou um retorno de chamada, em seguida, é provável que não está executando no thread da interface do usuário principal. Nesse caso, você deve encapsular o código em uma chamada para `InvokeOnMainThread` ou `BeginInvokeOnMainThread` semelhante a esta:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

O `InvokeOnMainThread` método é definido em `NSObject` para que ele pode ser chamado de dentro de métodos definidos em qualquer objeto de UIKit (por exemplo, uma exibição ou controlador de exibição).

Ao depurar aplicativos xamarin. IOS, um erro será gerado se o seu código tentar acessar um controle de interface do usuário do thread errado. Isso ajuda a rastrear e corrigir esses problemas com o método InvokeOnMainThread. Isso só ocorre durante a depuração e não gerará um erro em compilações de versão. A mensagem de erro será exibida como esta:

 ![](ui-thread-images/image10.png "Execução do Thread da interface do usuário")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Exemplo de Thread em segundo plano

Aqui está um exemplo que tenta acessar um controle de interface do usuário (um `UILabel`) de um thread em segundo plano usando um thread simple:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Que o código gerará o `UIKitThreadAccessException` durante a depuração. Para corrigir o problema (e certifique-se de que o controle de interface do usuário for acessado somente de thread da interface do usuário principal), encapsule os códigos que referenciam controles de interface do usuário dentro de um `InvokeOnMainThread` expressão como esta:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Você não precisará usar isso para o restante dos exemplos neste documento, mas é um importante conceito a lembrar quando seu aplicativo faz solicitações de rede usa o Centro de notificação ou outros métodos que exigem um manipulador de conclusão que será executado em outro thread.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Exemplo de Async/Await

Ao usar o C# 5 async/await palavras-chave `InvokeOnMainThread` não é necessário porque quando uma tarefa aguardada ser concluída o método continua no thread de chamada.

Este exemplo de código (que aguarda em uma chamada de método de atraso, meramente para fins de demonstração) mostra um método assíncrono que é chamado no thread da interface do usuário (é um manipulador de TouchUpInside). Porque o método que a contém é chamado no thread da interface do usuário, operações de interface do usuário, como definir o texto em uma `UILabel` ou mostrando um `UIAlertView` pode ser chamado com segurança depois de operações assíncronas sejam concluídas em threads em segundo plano.

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Se um método assíncrono é chamado de um thread em segundo plano (não o thread de interface do usuário principal), em seguida, `InvokeOnMainThread` ainda seria necessário.


## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/Controls/)
- [Threading](~/ios/app-fundamentals/threading.md)
