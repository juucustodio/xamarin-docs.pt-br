---
title: Xamarin. Forms MessagingCenter
description: Este artigo explica como usar o xamarin. Forms MessagingCenter para enviar e receber mensagens, a reduzir o acoplamento entre classes, como modelos de exibição.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675166"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin. Forms MessagingCenter

_Xamarin. Forms inclui um serviço de mensagens simple para enviar e receber mensagens._

<a name="Overview" />

## <a name="overview"></a>Visão geral

Xamarin. Forms `MessagingCenter` permite exibir modelos e outros componentes para se comunicar com sem precisar saber nada sobre uns aos outros, além de um contrato de mensagem simple.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Como funciona o MessagingCenter

Há duas partes para `MessagingCenter`:

-  **Inscrever-se** - escutar mensagens com uma determinada assinatura e executar alguma ação quando elas são recebidas. Vários assinantes podem estar escutando para a mesma mensagem.
-  **Enviar** -publicar uma mensagem para os ouvintes agir. Se nenhum ouvinte assinou a mensagem será ignorada.


O `MessagingService` é uma classe estática com `Subscribe` e `Send` métodos que são usados em toda a solução.

Mensagens têm uma cadeia de caracteres `message` parâmetro é usado como maneira *endereço* mensagens. O `Subscribe` e `Send` métodos usam parâmetros genéricos para controlar como as mensagens são entregues – duas mensagens com a mesma `message` texto, mas os argumentos de tipo genérico diferentes não serão entregues ao assinante mesmo.

A API para `MessagingCenter` é simples:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Esses métodos são explicados abaixo.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Usando o MessagingCenter

As mensagens podem ser enviadas como resultado da interação do usuário (como um clique de botão), um evento do sistema (como a alteração do estado de controles) ou alguns outros incidentes (como um download assíncrono concluir). Os assinantes podem estar escutando para alterar a aparência da interface do usuário, salvar os dados ou disparar alguma outra operação.

### <a name="simple-string-message"></a>Mensagem de cadeia de caracteres simples

A mensagem mais simples contém apenas uma cadeia de caracteres no `message` parâmetro. Um `Subscribe` método que *escuta* para uma mensagem de cadeia de caracteres simples é mostrada abaixo - Observe o tipo genérico, especificando o remetente deve ser do tipo `MainPage`. As classes na solução podem assinar a mensagem usando esta sintaxe:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

No `MainPage` classe o código a seguir *envia* a mensagem. O `this` parâmetro é uma instância de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

A cadeia de caracteres não for alterada, ele indica o *tipo de mensagem* e é usado para determinar quais assinantes para notificar. Esse tipo de mensagem é usado para indicar que algum evento ocorrido, como "carregamento concluído", em que nenhuma informação adicional é necessária.

### <a name="passing-an-argument"></a>Passar um argumento

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

Este exemplo simple usa uma `string` argumento, mas qualquer C# objeto pode ser passado.

### <a name="unsubscribe"></a>Cancelar a assinatura

Um objeto pode cancelar uma assinatura de mensagem para que nenhuma mensagem futuras sejam entregues. O `Unsubscribe` sintaxe de método deve refletir a assinatura da mensagem (portanto, talvez seja necessário incluir o parâmetro de tipo genérico para o argumento de mensagem).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumo

O MessagingCenter é uma maneira simples de reduzir o acoplamento, especialmente entre modelos de exibição. Ele pode ser usado para enviar e receber mensagens simples ou passar um argumento entre classes. Classes devem cancelar a assinatura de mensagens que não desejam receber.


## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
