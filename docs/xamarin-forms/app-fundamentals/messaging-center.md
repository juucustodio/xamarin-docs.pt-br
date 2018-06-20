---
title: MessagingCenter xamarin. Forms
description: Este artigo explica como usar o MessagingCenter xamarin. Forms para enviar e receber mensagens, para reduzir o acoplamento entre classes como modelos de exibição.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 49a7ecdad53c7820594f7ebc047ae6fbc5a9bc56
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209408"
---
# <a name="xamarinforms-messagingcenter"></a>MessagingCenter xamarin. Forms

_Xamarin. Forms inclui um serviço simple de mensagens para enviar e receber mensagens._

<a name="Overview" />

## <a name="overview"></a>Visão geral

Xamarin. Forms `MessagingCenter` permite exibir modelos e outros componentes para se comunicar com sem precisar saber nada sobre uns aos outros, além de um contrato de mensagem simple.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Como funciona o MessagingCenter

Há duas partes para `MessagingCenter`:

-  **Assinar** - escutar mensagens com uma assinatura de determinados e executam alguma ação quando elas são recebidas. Vários assinantes podem estar escutando para a mesma mensagem.
-  **Enviar** -publicar uma mensagem para os ouvintes agir. Se nenhum ouvintes tem assinado a mensagem será ignorada.


O `MessagingService` é uma classe estática com `Subscribe` e `Send` métodos que são usados em toda a solução.

As mensagens têm uma cadeia de caracteres `message` parâmetro que é usado como forma de *endereço* mensagens. O `Subscribe` e `Send` métodos usam parâmetros genéricos para controlar como as mensagens são entregues - duas mensagens com o mesmo `message` texto mas argumentos de tipo genérico diferentes não serão entregues ao assinante mesmo.

A API para `MessagingCenter` é simples:

-  Assinar&lt;TSender > (assinante do objeto, a mensagem de cadeia de caracteres, ação&lt;TSender > retorno de chamada, uma fonte de TSender = null)
-  Assinar&lt;TSender, TArgs > (assinante do objeto, a mensagem de cadeia de caracteres, ação&lt;TSender, TArgs > retorno de chamada, uma fonte de TSender = null)
-  Enviar&lt;TSender > (TSender remetente, mensagem de cadeia de caracteres)
-  Enviar&lt;TSender, TArgs > (remetente de TSender, mensagem de cadeia de caracteres, TArgs args)
-  Cancelar a assinatura&lt;TSender, TArgs > (assinante do objeto, mensagem de cadeia de caracteres)
-  Cancelar a assinatura&lt;TSender > (assinante do objeto, mensagem de cadeia de caracteres)


Esses métodos são explicados abaixo.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Usando o MessagingCenter

As mensagens podem ser enviadas como resultado da interação do usuário (como um clique de botão), um evento do sistema (como alterar o estado de controles) ou alguns outros incidentes (como concluir um download assíncrono). Os assinantes podem estar escutando para alterar a aparência da interface do usuário, salvar os dados ou disparar alguma outra operação.

### <a name="simple-string-message"></a>Mensagem de cadeia de caracteres simples

A mensagem mais simples contém apenas uma cadeia de caracteres no `message` parâmetro. Um `Subscribe` método que *escuta* para uma mensagem de cadeia de caracteres simples é mostrada abaixo - Observe o tipo genérico, especificando o remetente deve ser do tipo `MainPage`. Todas as classes na solução podem se inscrever para a mensagem usando a seguinte sintaxe:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

No `MainPage` o código a seguir da classe *envia* a mensagem. O `this` parâmetro é uma instância de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

A cadeia de caracteres não altera - indica o *tipo de mensagem* e é usado para determinar quais assinantes para notificar. Esse tipo de mensagem é usado para indicar que um evento ocorreu, como "carregamento concluído", em que nenhuma informação adicional é necessária.

### <a name="passing-an-argument"></a>Passando um argumento

Para passar um argumento com a mensagem, especifique o tipo de argumento no `Subscribe` argumentos genéricos e na assinatura de ação.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Para enviar a mensagem com o argumento, inclua o parâmetro de tipo genérico e o valor do argumento no `Send` chamada de método.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Este exemplo simple usa um `string` argumento mas qualquer objeto c# pode ser passado.

### <a name="unsubscribe"></a>Cancelar a assinatura

Um objeto pode cancelar a assinatura de uma assinatura de mensagem para que nenhuma mensagem futuras é entregues. O `Unsubscribe` sintaxe de método deve refletir a assinatura da mensagem (portanto talvez seja necessário incluir o parâmetro de tipo genérico para o argumento de mensagem).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumo

O MessagingCenter é uma maneira simple para reduzir o acoplamento, especialmente entre modelos de exibição. Ele pode ser usado para enviar e receber mensagens simples ou passar um argumento entre classes. Classes devem cancelar a assinatura de mensagens que não desejam receber.


## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
