---
title: Comunicação entre componentes flexíveis
description: 'Este capítulo explica como o aplicativo móvel eShopOnContainers implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagens entre componentes que são inconvenientes de vincular por referências de objeto e tipo '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c35cd6e30e7843cda0431581025aa7440a21cc29
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140043"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicação entre componentes flexíveis

O padrão de publicação-assinatura é um padrão de troca de mensagens em que os publicadores enviam mensagens sem ter conhecimento de nenhum receptor, conhecido como assinante. Da mesma forma, os assinantes escutam mensagens específicas, sem ter conhecimento de nenhum publicador.

Os eventos no .NET implementam o padrão de publicação-assinatura e são a abordagem mais simples e direta para uma camada de comunicação entre componentes se não for necessário um acoplamento flexível, como um controle e a página que o contém. No entanto, os tempos de vida do Publicador e do Assinante são acoplados por referências de objeto entre si e o tipo de assinante deve ter uma referência ao tipo de Publicador. Isso pode criar problemas de gerenciamento de memória, especialmente quando há objetos de vida curta que se inscrevem em um evento de um objeto estático ou de vida longa. Se o manipulador de eventos não for removido, o Assinante será mantido ativo pela referência a ele no Publicador, e isso impedirá ou atrasará a coleta de lixo do Assinante.

## <a name="introduction-to-messagingcenter"></a>Introdução ao MessagingCenter

A Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagem entre componentes que são inconvenientes de vincular por referências de objeto e tipo. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre os componentes, permitindo também que os componentes sejam desenvolvidos e testados de forma independente.

A [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornece a funcionalidade de publicação/assinatura de multicast. Isso significa que pode haver vários Publicadores que publicam uma única mensagem, e pode haver vários assinantes ouvindo a mesma mensagem. A Figura 4-1 ilustra essa relação:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multicast publish-subscribe functionality")

**Figura 4-1:** Funcionalidade de publicação-assinatura de multicast

Os editores enviam mensagens usando o [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método, enquanto os assinantes ouvem mensagens usando o [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Além disso, os assinantes também podem cancelar a assinatura de assinaturas de mensagens, se necessário, com o [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método.

Internamente, a [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe usa referências fracas. Isso significa que ela não manterá os objetos ativos e permitirá que eles sejam coletados como lixo. Portanto, só deverá ser necessário cancelar a assinatura de uma mensagem quando uma classe não desejar mais recebê-la.

O aplicativo móvel eShopOnContainers usa a [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe para se comunicar entre componentes livremente acoplados. O aplicativo define três mensagens:

- A `AddProduct` mensagem é publicada pela `CatalogViewModel` classe quando um item é adicionado à cesta de compras. Em retorno, a `BasketViewModel` classe assina a mensagem e incrementa o número de itens na cesta de compras em resposta. Além disso, a `BasketViewModel` classe também cancela a assinatura dessa mensagem.
- A `Filter` mensagem é publicada pela `CatalogViewModel` classe quando o usuário aplica uma marca ou um filtro de tipo aos itens exibidos no catálogo. Em retorno, a `CatalogView` classe assina a mensagem e atualiza a interface do usuário para que apenas os itens que correspondem aos critérios de filtro sejam exibidos.
- A `ChangeTab` mensagem é publicada pela `MainViewModel` classe quando o `CheckoutViewModel` navega para o `MainViewModel` seguinte a criação e o envio com êxito de um novo pedido. Em retorno, a `MainView` classe assina a mensagem e atualiza a interface do usuário para que a guia **meu perfil** esteja ativa, para mostrar os pedidos do usuário.

> [!NOTE]
> Embora a [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe permita a comunicação entre classes livremente acopladas, ela não oferece a única solução de arquitetura para esse problema. Por exemplo, a comunicação entre um modelo de exibição e uma exibição também pode ser obtida pelo mecanismo de associação e por meio de notificações de alteração de propriedade. Além disso, a comunicação entre dois modelos de exibição também pode ser obtida passando dados durante a navegação.

No aplicativo móvel eShopOnContainers, [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) é usado para atualizar na interface do usuário em resposta a uma ação que ocorre em outra classe. Portanto, as mensagens são publicadas no thread da interface do usuário, com assinantes recebendo a mensagem no mesmo thread.

> [!TIP]
> Realize marshaling no thread da interface do usuário ao executar atualizações da interface do usuário. Se uma mensagem enviada de um thread em segundo plano for necessária para atualizar a interface do usuário, processe a mensagem no thread da interface do usuário no Assinante invocando o [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

Para obter mais informações sobre o [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) , consulte [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definindo uma mensagem

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)as mensagens são cadeias de caracteres usadas para identificar mensagens. O exemplo de código a seguir mostra as mensagens definidas no aplicativo móvel eShopOnContainers:

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

Neste exemplo, as mensagens são definidas usando constantes. A vantagem dessa abordagem é que ela fornece segurança de tipo em tempo de compilação e suporte de refatoração.

## <a name="publishing-a-message"></a>Publicando uma mensagem

Os editores notificam os assinantes de uma mensagem com uma das [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) sobrecargas. O exemplo de código a seguir demonstra a publicação da `AddProduct` mensagem:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

Neste exemplo, o [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica três argumentos:

- O primeiro argumento especifica a classe do remetente. A classe de remetente deve ser especificada por todos os assinantes que desejam receber a mensagem.
- O segundo argumento especifica a mensagem.
- O terceiro argumento especifica os dados de carga a serem enviados ao Assinante. Nesse caso, os dados de carga são uma `CatalogItem` instância.

O [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método publicará a mensagem e seus dados de carga, usando uma abordagem de incêndio e esqueci. Portanto, a mensagem é enviada mesmo quando não há assinantes registrados para recebê-la. Nessa situação, a mensagem enviada é ignorada.

> [!NOTE]
> O [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método pode usar parâmetros genéricos para controlar como as mensagens são entregues. Portanto, várias mensagens que compartilham uma identidade de mensagem, mas enviam tipos de dados de carga diferentes podem ser recebidas por diferentes assinantes.

## <a name="subscribing-to-a-message"></a>Inscrevendo-se em uma mensagem

Os assinantes podem se registrar para receber uma mensagem usando uma das [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sobrecargas. O exemplo de código a seguir demonstra como o aplicativo móvel eShopOnContainers assina e processa a `AddProduct` mensagem:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

Neste exemplo, o [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método assina a `AddProduct` mensagem e executa um delegado de retorno de chamada em resposta ao recebimento da mensagem. Esse delegado de retorno de chamada, especificado como uma expressão lambda, executa o código que atualiza a interface do usuário.

> [!TIP]
> Considere o uso de dados de carga imutáveis. Não tente modificar os dados de carga de dentro de um delegado de retorno de chamada porque vários threads podiam acessar os dados recebidos simultaneamente. Nesse cenário, os dados de carga devem ser imutáveis para evitar erros de simultaneidade.

Um assinante pode não precisar manipular todas as instâncias de uma mensagem publicada, e isso pode ser controlado pelos argumentos de tipo genérico especificados no [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Neste exemplo, o assinante receberá apenas `AddProduct` mensagens enviadas da `CatalogViewModel` classe, cujos dados de carga são uma `CatalogItem` instância.

## <a name="unsubscribing-from-a-message"></a>Cancelando a de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não desejam mais receber. Isso é obtido com uma das [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecargas, conforme demonstrado no exemplo de código a seguir:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

Neste exemplo, a [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sintaxe do método reflete os argumentos de tipo especificados durante a assinatura para receber a `AddProduct` mensagem.

## <a name="summary"></a>Resumo

A Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagem entre componentes que são inconvenientes de vincular por referências de objeto e tipo. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre os componentes, permitindo também que os componentes sejam desenvolvidos e testados de forma independente.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
