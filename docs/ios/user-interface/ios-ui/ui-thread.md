---
title: Trabalhando com o Thread de interface do usuário no xamarin
description: Este documento descreve como trabalhar com o Thread de interface do usuário em xamarin. Ele aborda a execução do thread da interface do usuário, fornece um exemplo de thread em segundo plano e examina async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 4328b84625aff4c92d6e97029ced7dde747d4fc4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790403"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Trabalhando com o Thread de interface do usuário no xamarin

Interfaces de usuário do aplicativo são sempre thread único, mesmo em dispositivos multithread – há apenas uma representação da tela e as alterações para o que é exibido precisam ser coordenadas por meio de um único 'ponto de acesso '. Isso impede que vários threads tentar atualizar o mesmo pixel ao mesmo tempo (por exemplo).

Seu código deve fazer apenas o thread de alterações para controles de interface na página principal do usuário (ou da interface do usuário). Todas as atualizações da interface do usuário que ocorrem em um thread diferente (por exemplo, um thread de retorno de chamada ou plano de fundo) não podem obter renderizadas na tela ou mesmo podem causar uma falha.

## <a name="ui-thread-execution"></a>Execução do Thread da interface do usuário

Quando você estiver criando controles em uma exibição ou manipular um evento iniciado pelo usuário, como um toque, o código já está em execução no contexto do thread da interface do usuário.

Se o código está em execução em um thread em segundo plano, em uma tarefa ou um retorno de chamada é provavelmente não em execução no thread da interface do usuário principal. Nesse caso, você deve encapsular o código em uma chamada para `InvokeOnMainThread` ou `BeginInvokeOnMainThread` esta aparência:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

O `InvokeOnMainThread` método é definido em `NSObject` para que ele pode ser chamado de dentro de métodos definidos em qualquer objeto UIKit (como uma exibição ou View Controller).

Durante a depuração de aplicativos xamarin, um erro será gerado se o seu código tentar acessar um controle de interface do usuário do thread errado. Isso ajuda a rastrear e corrigir esses problemas com o método InvokeOnMainThread. Isso só ocorre durante a depuração e não gerará um erro nas compilações de lançamento. A mensagem de erro será exibido como este:

 ![](ui-thread-images/image10.png "Execução do Thread da interface do usuário")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Exemplo de Thread em segundo plano

Aqui está um exemplo que tenta acessar um controle de interface do usuário (um `UILabel`) de um thread em segundo plano usando um thread simple:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Se o código gerará o `UIKitThreadAccessException` durante a depuração. Para corrigir o problema (e certifique-se de que o controle de interface do usuário é acessado somente do thread da interface do usuário principal), incluir qualquer código que faz referência a controles de interface do usuário dentro de um `InvokeOnMainThread` expressão como esta:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Você não precisa fazer use isso para o restante dos exemplos neste documento, mas ele é um conceito importante para lembrar ao seu aplicativo faz solicitações de rede usa o Centro de notificação ou outros métodos que exigem um processador de conclusão que será executado em outro thread.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Exemplo de Async/Await

Ao usar as palavras-chave async/await de c# 5 `InvokeOnMainThread` não é necessário porque ao concluir uma tarefa em espera o método continua no thread de chamada.

Este exemplo de código (que aguarda em uma chamada de método de atraso, meramente para fins de demonstração) mostra um método assíncrono que é chamado no thread da interface do usuário (é um manipulador de TouchUpInside). Porque o método contém é chamado no thread da interface do usuário, operações de interface do usuário, como definir o texto em uma `UILabel` ou mostrando um `UIAlertView` pode ser chamado com segurança depois de operações assíncronas sejam concluídas em threads em segundo plano.

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

Se um método assíncrono é chamado de um thread em segundo plano (não o thread de interface do usuário principal), em seguida, `InvokeOnMainThread` ainda será necessário.


## <a name="related-links"></a>Links relacionados

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
- [Threading](~/ios/app-fundamentals/threading.md)
