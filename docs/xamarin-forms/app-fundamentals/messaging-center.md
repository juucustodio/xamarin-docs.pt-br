---
title: Xamarin.Forms MessagingCenter
description: A Xamarin.Forms classe MessagingCenter implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagem entre componentes que são inconvenientes de vincular por referências de objeto e tipo.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d24ff6831bb8b52d840ff2db7c13c3eb674b7bd8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555782"
---
# <a name="no-locxamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

O padrão de publicação-assinatura é um padrão de troca de mensagens em que os publicadores enviam mensagens sem ter conhecimento de nenhum receptor, conhecido como assinante. Da mesma forma, os assinantes escutam mensagens específicas, sem ter conhecimento de nenhum publicador.

Os eventos no .NET implementam o padrão de publicação-assinatura e são a abordagem mais simples e direta para uma camada de comunicação entre componentes se não for necessário um acoplamento flexível, como um controle e a página que o contém. No entanto, os tempos de vida do Publicador e do Assinante são acoplados por referências de objeto entre si e o tipo de assinante deve ter uma referência ao tipo de Publicador. Isso pode criar problemas de gerenciamento de memória, especialmente quando há objetos de vida curta que se inscrevem em um evento de um objeto estático ou de vida longa. Se o manipulador de eventos não for removido, o Assinante será mantido ativo pela referência a ele no Publicador, e isso impedirá ou atrasará a coleta de lixo do Assinante.

A Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagem entre componentes que são inconvenientes de vincular por referências de objeto e tipo. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre eles.

A [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornece a funcionalidade de publicação/assinatura de multicast. Isso significa que pode haver vários publicadores que publicam uma única mensagem e pode haver vários assinantes ouvindo a mesma mensagem:

![Funcionalidade de publicação-assinatura multicast](messaging-center-images/messaging-center.png)

Os editores enviam mensagens usando o [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método, enquanto os assinantes ouvem mensagens usando o [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Além disso, os assinantes também podem cancelar a assinatura de assinaturas de mensagens, se necessário, com o [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método.

> [!IMPORTANT]
> Internamente, a [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe usa referências fracas. Isso significa que ela não manterá os objetos ativos e permitirá que eles sejam coletados como lixo. Portanto, só deverá ser necessário cancelar a assinatura de uma mensagem quando uma classe não desejar mais recebê-la.

## <a name="publish-a-message"></a>Publicar uma mensagem

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) as mensagens são cadeias de caracteres. Os editores notificam os assinantes de uma mensagem com uma das [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) sobrecargas. O exemplo de código a seguir publica uma mensagem `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

Neste exemplo, o [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica um argumento genérico que representa o remetente. Para receber a mensagem, um assinante também precisa especificar o mesmo argumento genérico, indicando que está escutando uma mensagem desse remetente. Além disso, este exemplo especifica dois argumentos de método:

- O primeiro argumento especifica a instância do remetente.
- O segundo argumento especifica a mensagem.

Dados de payload também podem ser enviados com uma mensagem:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

Neste exemplo, o [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica dois argumentos genéricos. O primeiro é o tipo que está enviando a mensagem e o segundo é o tipo dos dados de payload sendo enviados. Para receber a mensagem, um assinante também precisa especificar os mesmos argumentos genéricos. Isso permite que várias mensagens que compartilham uma identidade de mensagem, mas enviam tipos de dados de payload diferentes, sejam recebidas por diferentes assinantes. Além disso, este exemplo especifica um terceiro argumento de método que representa os dados de payload a serem enviados ao assinante. Nesse caso, os dados de payload são um `string`.

O [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método publicará a mensagem e quaisquer dados de carga, usando uma abordagem de incêndio e esqueci. Portanto, a mensagem é enviada mesmo quando não há assinantes registrados para recebê-la. Nessa situação, a mensagem enviada é ignorada.

## <a name="subscribe-to-a-message"></a>Assinar uma mensagem

Os assinantes podem se registrar para receber uma mensagem usando uma das [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sobrecargas. O código a seguir mostra um exemplo disso:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

Neste exemplo, o [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método assina o `this` objeto para `Hi` as mensagens enviadas pelo `MainPage` tipo e executa um delegado de retorno de chamada em resposta ao recebimento da mensagem. O delegado de retorno de chamada, especificado como uma expressão lambda, pode ser um código que atualiza a interface do usuário, salva alguns dados ou dispara alguma outra operação.

> [!NOTE]
> Um assinante pode não precisar manipular todas as instâncias de uma mensagem publicada, e isso pode ser controlado pelos argumentos de tipo genérico especificados no [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método.

O exemplo a seguir mostra como assinar uma mensagem que contém dados de payload:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

Neste exemplo, o [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método assina `Hi` as mensagens enviadas pelo `MainPage` tipo, cujos dados de carga são um `string` . Um delegado de retorno de chamada é executado em resposta ao recebimento desse tipo de mensagem, que exibe os dados de payload em um alerta.

> [!IMPORTANT]
> O delegado executado pelo `Subscribe` método será executado no mesmo thread que publica a mensagem usando o `Send` método.

## <a name="unsubscribe-from-a-message"></a>Cancelar a assinatura de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não desejam mais receber. Isso é obtido com uma das [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecargas:

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

Neste exemplo, o [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método cancela a assinatura do `this` objeto da `Hi` mensagem enviada pelo `MainPage` tipo.

As mensagens que contêm dados de carga devem ter a assinatura cancelada usando a [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecarga que especifica dois argumentos genéricos:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

Neste exemplo, o [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método cancela a assinatura do `this` objeto da `Hi` mensagem enviada pelo `MainPage` tipo, cujos dados de carga são um `string` .

## <a name="related-links"></a>Links relacionados

- [MessagingCenterSample](/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)