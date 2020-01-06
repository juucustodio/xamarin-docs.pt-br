---
title: Xamarin.Forms MessagingCenter
description: A classe MessagingCenter do Xamarin.Forms implementa o padrão de publicação-assinatura, permitindo uma comunicação baseada em mensagens entre componentes que são inconvenientes de vincular por referências de objeto e de tipo.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 0e5fd88678becd7becfcb1c43e14b1e33aad72de
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489868"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

O padrão de publicação-assinatura é um padrão de troca de mensagens em que os publicadores enviam mensagens sem ter conhecimento de nenhum receptor, conhecido como assinante. Da mesma forma, os assinantes escutam mensagens específicas, sem ter conhecimento de nenhum publicador.

Os eventos no .NET implementam o padrão de publicação-assinatura e são a abordagem mais simples e direta para uma camada de comunicação entre componentes se não for necessário um acoplamento flexível, como um controle e a página que o contém. No entanto, os tempos de vida do Publicador e do Assinante são acoplados por referências de objeto entre si e o tipo de assinante deve ter uma referência ao tipo de Publicador. Isso pode criar problemas de gerenciamento de memória, especialmente quando há objetos de vida curta que se inscrevem em um evento de um objeto estático ou de vida longa. Se o manipulador de eventos não for removido, o Assinante será mantido ativo pela referência a ele no Publicador, e isso impedirá ou atrasará a coleta de lixo do Assinante.

A classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) do Xamarin.Forms implementa o padrão de publicação-assinatura, permitindo uma comunicação baseada em mensagens entre componentes que são inconvenientes de vincular por referências de objeto e de tipo. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre eles.

A classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fornece a funcionalidade de publicação-assinatura multicast. Isso significa que pode haver vários publicadores que publicam uma única mensagem e pode haver vários assinantes ouvindo a mesma mensagem:

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

Os publicadores enviam mensagens usando o método [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), enquanto os assinantes ouvem mensagens usando o método [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). Além disso, os assinantes também podem cancelar a assinatura de mensagens, se necessário, usando o método [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

> [!IMPORTANT]
> Internamente, a classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) usa referências fracas. Isso significa que ela não manterá os objetos ativos e permitirá que eles sejam coletados como lixo. Portanto, só deverá ser necessário cancelar a assinatura de uma mensagem quando uma classe não desejar mais recebê-la.

## <a name="publish-a-message"></a>Publicar uma mensagem

As mensagens [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) são cadeias de caracteres. Os publicadores notificam os assinantes de uma mensagem com uma das sobrecargas [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). O exemplo de código a seguir publica uma mensagem `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

Neste exemplo, o método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) especifica um argumento genérico que representa o remetente. Para receber a mensagem, um assinante também precisa especificar o mesmo argumento genérico, indicando que está escutando uma mensagem desse remetente. Além disso, este exemplo especifica dois argumentos de método:

- O primeiro argumento especifica a instância do remetente.
- O segundo argumento especifica a mensagem.

Dados de payload também podem ser enviados com uma mensagem:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

Neste exemplo, o método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) especifica dois argumentos genéricos. O primeiro é o tipo que está enviando a mensagem e o segundo é o tipo dos dados de payload sendo enviados. Para receber a mensagem, um assinante também precisa especificar os mesmos argumentos genéricos. Isso permite que várias mensagens que compartilham uma identidade de mensagem, mas enviam tipos de dados de payload diferentes, sejam recebidas por diferentes assinantes. Além disso, este exemplo especifica um terceiro argumento de método que representa os dados de payload a serem enviados ao assinante. Nesse caso, os dados de payload são um `string`.

O método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) publicará a mensagem, bem como os dados de payload, usando uma abordagem do tipo “disparar e esquecer”. Portanto, a mensagem é enviada mesmo quando não há assinantes registrados para recebê-la. Nessa situação, a mensagem enviada é ignorada.

## <a name="subscribe-to-a-message"></a>Assinar uma mensagem

Os assinantes podem se registrar para receber uma mensagem usando uma das sobrecargas [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). O código a seguir mostra um exemplo disso:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

Neste exemplo, o método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) assina o objeto `this` para mensagens `Hi` enviadas pelo tipo `MainPage` e executa um delegado de retorno de chamada em resposta ao recebimento da mensagem. O delegado de retorno de chamada, especificado como uma expressão lambda, pode ser um código que atualiza a interface do usuário, salva alguns dados ou dispara alguma outra operação.

> [!NOTE]
> Um assinante pode não precisar manipular todas as instâncias de uma mensagem publicada, e isso pode ser controlado pelos argumentos de tipo genérico especificados no método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*).

O exemplo a seguir mostra como assinar uma mensagem que contém dados de payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

Neste exemplo, o método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) assina mensagens `Hi` enviadas pelo tipo `MainPage`, cujos dados de payload são um `string`. Um delegado de retorno de chamada é executado em resposta ao recebimento desse tipo de mensagem, que exibe os dados de payload em um alerta.

> [!IMPORTANT]
> O delegado executado pelo método `Subscribe` será executado no mesmo thread que publica a mensagem usando o método `Send`.

## <a name="unsubscribe-from-a-message"></a>Cancelar a assinatura de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não desejam mais receber. Isso é feito com uma das sobrecargas [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*):

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

Neste exemplo, o método [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) cancela a assinatura do objeto `this` da mensagem `Hi` enviada pelo tipo `MainPage`.

Mensagens que contêm dados de payload devem ter a assinatura cancelada usando a sobrecarga [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) que especifica dois argumentos genéricos:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

Neste exemplo, o método [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) cancela a assinatura do objeto `this` da mensagem `Hi` enviada pelo tipo `MainPage`, cujos dados de payload são um `string`.

## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
