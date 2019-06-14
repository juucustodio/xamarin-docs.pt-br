---
title: Xamarin.Forms MessagingCenter
description: Este artigo explica como usar o Xamarin.Forms MessagingCenter para enviar e receber mensagens de modo a reduzir o acoplamento entre classes, como modelos de exibição.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b6e68f6b99803edd8d50a172d598a3410aa1bbf4
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740758"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UsingMessagingCenter)

_O Xamarin.Forms inclui um serviço de mensagens simples para enviar e receber mensagens._

<a name="Overview" />

## <a name="overview"></a>Visão geral

O `MessagingCenter` do Xamarin.Forms habilita a exibição de modelos e outros componentes para se comunicar sem que eles precisem saber nada uns sobre os outros, além de um contrato de mensagem simples.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Como funciona o MessagingCenter

Há duas partes para `MessagingCenter`:

-  **Assinar** – escute mensagens com uma determinada assinatura e execute alguma ação quando elas forem recebidas. Vários assinantes podem escutar a mesma mensagem.
-  **Enviar** – publique uma mensagem na qual os ouvintes devem agir. Se nenhum ouvinte tiver assinado, a mensagem será ignorada.

O `MessagingCenter` é uma classe estática com os métodos `Subscribe` e `Send` que são usados em toda a solução.

As mensagens têm um parâmetro `message` de cadeia de caracteres que é usado como uma maneira de *endereçar* mensagens. Os métodos `Subscribe` e `Send` usam parâmetros genéricos para controlar como as mensagens são entregues – duas mensagens com o mesmo texto `message`, mas os argumentos de tipo genérico diferentes não serão entregues ao mesmo assinante.

A API para `MessagingCenter` é simples:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Esses métodos são explicados abaixo.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Usar o MessagingCenter

As mensagens podem ser enviadas como resultado da interação do usuário (como um clique de botão), um evento do sistema (como controles alterando um estado) ou alguns outros incidentes (como um download assíncrono sendo concluído). Os assinantes podem estar escutando para alterar a aparência da interface do usuário, salvar os dados ou disparar alguma outra operação.

Para obter mais informações sobre como usar a classe `MessagingCenter`, confira [Comunicação entre componentes fracamente acoplados](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).

### <a name="simple-string-message"></a>Mensagem de cadeia de caracteres simples

A mensagem mais simples contém apenas uma cadeia de caracteres no parâmetro `message`. Um método `Subscribe` que *escuta* uma mensagem de cadeia de caracteres simples é mostrado abaixo. Observe o tipo genérico especificando que o remetente deve ser do tipo `MainPage`. As classes na solução podem assinar a mensagem usando esta sintaxe:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

Na classe `MainPage`, o código a seguir *envia* a mensagem. O parâmetro `this` é uma instância de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

A cadeia de caracteres não é alterada. Ela indica o *tipo de mensagem* e é usada para determinar quais assinantes notificar. Esse tipo de mensagem é usado para indicar que algum evento ocorreu, como "upload concluído", em que nenhuma informação adicional é necessária.

### <a name="passing-an-argument"></a>Passar um argumento

Para passar um argumento com a mensagem, especifique o tipo de argumento nos argumentos genéricos `Subscribe` e na assinatura Ação.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Para enviar a mensagem com o argumento, inclua o parâmetro Tipo genérico e o valor do argumento na chamada de método `Send`.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Este exemplo simples usa um argumento `string`, mas qualquer objeto C# pode ser passado.

### <a name="unsubscribe"></a>Cancelar assinatura

Um objeto pode cancelar uma assinatura de uma mensagem para que nenhuma mensagem futura seja entregue. A sintaxe do método `Unsubscribe` deve refletir a assinatura da mensagem (portanto, talvez seja necessário incluir o parâmetro Tipo genérico para o argumento de mensagem).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumo

O MessagingCenter é uma maneira simples de reduzir o acoplamento, especialmente entre modelos de exibição. Ele pode ser usado para enviar e receber mensagens simples ou passar um argumento entre classes. As classes devem cancelar a assinatura de mensagens que não desejam receber.


## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](https://developer.xamarin.com/samples/xamarin-forms/UsingMessagingCenter)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Comunicação entre componentes flexíveis](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)
