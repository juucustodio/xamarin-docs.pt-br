---
title: Trabalhando com o thread de interface do usuário no Xamarin. iOS
description: Este documento descreve como trabalhar com o thread de interface do usuário no Xamarin. iOS. Ele aborda a execução de thread de interface do usuário, fornece um exemplo de thread em segundo plano e examina Async/Await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e91b7fdf99e8eb69cca240253f169ba16b0b11c4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432008"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Trabalhando com o thread de interface do usuário no Xamarin. iOS

As interfaces de usuário do aplicativo sempre são de thread único, mesmo em dispositivos multi-threaded – há apenas uma representação da tela, e qualquer alteração no que é exibida precisa ser coordenada por meio de um único ' ponto de acesso '. Isso impede que vários threads tentem atualizar o mesmo pixel ao mesmo tempo (por exemplo).

Seu código só deve fazer alterações nos controles da interface do usuário do thread principal (ou da interface do usuário). Todas as atualizações de interface do usuário que ocorrem em um thread diferente (como um thread de retorno de chamada ou em segundo plano) podem não ser renderizadas para a tela ou podem até mesmo causar uma falha.

## <a name="ui-thread-execution"></a>Execução de thread de interface do usuário

Quando você está criando controles em uma exibição ou manipulando um evento iniciado pelo usuário, como um toque, o código já está em execução no contexto do thread da interface do usuário.

Se o código estiver sendo executado em um thread em segundo plano, em uma tarefa ou em um retorno de chamada, provavelmente não será executado no thread da interface do usuário principal. Nesse caso, você deve encapsular o código em uma chamada para `InvokeOnMainThread` ou `BeginInvokeOnMainThread` assim:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

O `InvokeOnMainThread` método é definido em `NSObject` para que possa ser chamado de dentro de métodos definidos em qualquer objeto UIKit (como um modo de exibição ou controlador de exibição).

Durante a depuração de aplicativos Xamarin. iOS, um erro será gerado se o seu código tentar acessar um controle de interface do usuário a partir do thread errado. Isso ajuda a rastrear e corrigir esses problemas com o método InvokeOnMainThread. Isso ocorre apenas durante a depuração e não gera um erro nas compilações de versão. A mensagem de erro será exibida da seguinte maneira:

 ![Execução de thread de interface do usuário](ui-thread-images/image10.png)

 <a name="Background_Thread_Example"></a>

## <a name="background-thread-example"></a>Exemplo de thread em segundo plano

Aqui está um exemplo que tenta acessar um controle de interface do usuário (a `UILabel` ) de um thread em segundo plano usando um thread simples:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Esse código gerará o `UIKitThreadAccessException` durante a depuração. Para corrigir o problema (e garantir que o controle de interface do usuário seja acessado somente do thread principal da interface do usuário), empacote qualquer código que referencie controles da interface do usuário dentro de uma `InvokeOnMainThread` expressão como esta:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Você não precisará usá-lo para o restante dos exemplos neste documento, mas é um conceito importante a ser lembrado quando seu aplicativo faz solicitações de rede, usa o centro de notificação ou outros métodos que exigem um manipulador de conclusão que será executado em outro thread.

 <a name="Async_Await_Example"></a>

## <a name="asyncawait-example"></a>Exemplo de Async/Await

Ao usar as palavras-chave Async/Await do C# 5 `InvokeOnMainThread` não é necessário porque quando uma tarefa esperada é concluída, o método continua no thread de chamada.

Este código de exemplo (que aguarda em uma chamada de método de atraso, puramente para fins de demonstração) mostra um método assíncrono chamado no thread da interface do usuário (trata-se de um manipulador TouchUpInside). Como o método recipiente é chamado no thread da interface do usuário, as operações da interface do usuário, como definir o texto em um `UILabel` ou mostrar um, `UIAlertView` podem ser chamadas com segurança após a conclusão das operações assíncronas em threads em segundo plano.

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

Se um método assíncrono for chamado de um thread em segundo plano (não o thread da interface do usuário principal), `InvokeOnMainThread` ainda seria necessário.

## <a name="related-links"></a>Links Relacionados

- [Controles (exemplo)](/samples/xamarin/ios-samples/controls)
- [Threading](~/ios/app-fundamentals/threading.md)