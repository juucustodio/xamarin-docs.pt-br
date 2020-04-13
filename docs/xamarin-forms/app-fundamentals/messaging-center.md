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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489868"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

O padrão de publicação-assinatura é um padrão de troca de mensagens em que os publicadores enviam mensagens sem ter conhecimento de nenhum receptor, conhecido como assinante. Da mesma forma, os assinantes escutam mensagens específicas, sem ter conhecimento de nenhum publicador.

Eventos no .NET implementam o padrão de assinatura de publicação e são a abordagem mais simples e simples para uma camada de comunicação entre componentes se o acoplamento solto não for necessário, como um controle e a página que o contém. No entanto, as vidas do editor e do assinante são acopladas por referências de objetos entre si, e o tipo de assinante deve ter uma referência ao tipo de editor. Isso pode criar problemas de gerenciamento de memória, especialmente quando há objetos de curta duração que assinam um evento de um objeto estático ou de longa duração. Se o manipulador de eventos não for removido, o assinante será mantido vivo pela referência a ele na editora, e isso impedirá ou atrasará a coleta de lixo do assinante.

A classe Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) implementa o padrão de publicação-subscrição, permitindo a comunicação baseada em mensagens entre componentes inconvenientes para vincular por objeto e tipo de referências. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre eles.

A [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornece funcionalidade de assinatura de publicação multicast. Isso significa que pode haver vários publicadores que publicam uma única mensagem e pode haver vários assinantes ouvindo a mesma mensagem:

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

Os editores enviam [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) mensagens usando o método, enquanto os assinantes ouvem mensagens usando o [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Além disso, os assinantes também podem cancelar a assinatura [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) de mensagens, se necessário, com o método.

> [!IMPORTANT]
> Internamente, [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) a classe usa referências fracas. Isso significa que ela não manterá os objetos ativos e permitirá que eles sejam coletados como lixo. Portanto, só deverá ser necessário cancelar a assinatura de uma mensagem quando uma classe não desejar mais recebê-la.

## <a name="publish-a-message"></a>Publicar uma mensagem

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)mensagens são strings. Os editores notificam os assinantes [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) de uma mensagem com uma das sobrecargas. O exemplo de código a seguir publica uma mensagem `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

Neste exemplo, [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) o método especifica um argumento genérico que representa o remetente. Para receber a mensagem, um assinante também precisa especificar o mesmo argumento genérico, indicando que está escutando uma mensagem desse remetente. Além disso, este exemplo especifica dois argumentos de método:

- O primeiro argumento especifica a instância do remetente.
- O segundo argumento especifica a mensagem.

Dados de payload também podem ser enviados com uma mensagem:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

Neste exemplo, [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) o método especifica dois argumentos genéricos. O primeiro é o tipo que está enviando a mensagem e o segundo é o tipo dos dados de payload sendo enviados. Para receber a mensagem, um assinante também precisa especificar os mesmos argumentos genéricos. Isso permite que várias mensagens que compartilham uma identidade de mensagem, mas enviam tipos de dados de payload diferentes, sejam recebidas por diferentes assinantes. Além disso, este exemplo especifica um terceiro argumento de método que representa os dados de payload a serem enviados ao assinante. Nesse caso, os dados de payload são um `string`.

O [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método publicará a mensagem e quaisquer dados de carga, usando uma abordagem de fogo e esquecimento. Portanto, a mensagem é enviada mesmo quando não há assinantes registrados para recebê-la. Nessa situação, a mensagem enviada é ignorada.

## <a name="subscribe-to-a-message"></a>Assinar uma mensagem

Os assinantes podem se cadastrar para [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) receber uma mensagem usando uma das sobrecargas. O código a seguir mostra um exemplo disso:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

Neste exemplo, [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) o método `this` subscreve o objeto `Hi` `MainPage` às mensagens enviadas pelo tipo e executa um delegado de retorno de chamada em resposta ao recebimento da mensagem. O delegado de retorno de chamada, especificado como uma expressão lambda, pode ser um código que atualiza a interface do usuário, salva alguns dados ou dispara alguma outra operação.

> [!NOTE]
> Um assinante pode não precisar lidar com todas as instâncias de uma mensagem publicada, [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e isso pode ser controlado pelos argumentos de tipo genéricos especificados no método.

O exemplo a seguir mostra como assinar uma mensagem que contém dados de payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

Neste exemplo, [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) o método `Hi` subscreve mensagens `MainPage` enviadas pelo tipo, `string`cujos dados de carga são um . Um delegado de retorno de chamada é executado em resposta ao recebimento desse tipo de mensagem, que exibe os dados de payload em um alerta.

> [!IMPORTANT]
> O delegado executado pelo `Subscribe` método será executado no mesmo segmento que publica `Send` a mensagem usando o método.

## <a name="unsubscribe-from-a-message"></a>Cancelar a assinatura de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não desejam mais receber. Isso é conseguido com [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) uma das sobrecargas:

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

Neste exemplo, [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) o método desinscreve `this` `Hi` o objeto `MainPage` da mensagem enviada pelo tipo.

As mensagens que contêm dados de carga [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) devem ser desassinadas usando a sobrecarga que especifica dois argumentos genéricos:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

Neste exemplo, [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) o método `this` desinscreve `Hi` o objeto `MainPage` da mensagem enviada pelo `string`tipo, cujos dados de carga são um .

## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
