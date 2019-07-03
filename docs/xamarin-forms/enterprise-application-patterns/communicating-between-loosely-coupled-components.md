---
title: Comunicação entre componentes flexíveis
description: 'Este capítulo explica como o aplicativo móvel do eShopOnContainers implementa a publicar-padrão, permitindo a comunicação baseada em mensagens entre os componentes são inconvenientes para vincular as referências de objeto e o tipo de assinatura '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9848d2b832990032bc7eb7f2e3a93c896457134c
ms.sourcegitcommit: e95296f9e516975f5f32d822c323a71fd84007b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538690"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicação entre componentes flexíveis

Publicar-assinar padrão é um padrão de mensagens na qual os editores enviam mensagens sem ter conhecimento de quaisquer destinatários, conhecido como assinantes. Da mesma forma, assinantes escutam mensagens específicas, sem a necessidade de Conhecimento de quaisquer Publicadores.

Eventos no .NET implementam a publicar-assinar padrão e são mais simple e direta abordagem para uma camada de comunicação entre os componentes se um acoplamento flexível não for necessário, como um controle e a página que o contém. No entanto, os tempos de vida do publicador e assinante estejam ligados por referências de objeto entre si e o tipo de assinante deve ter uma referência para o tipo do publicador. Isso pode criar problemas de gerenciamento de memória especialmente quando há objetos de vida útil curtos que assinem um evento de um objeto estático ou de longa duração. Se o manipulador de eventos não é removido, o assinante será mantido ativo, a referência a ele no publicador, e isso impedirá ou atrasar a coleta de lixo do assinante.

## <a name="introduction-to-messagingcenter"></a>Introdução ao MessagingCenter

O xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa a publicar-padrão, permitindo a comunicação baseada em mensagens entre os componentes são inconvenientes para vincular as referências de objeto e o tipo de assinatura. Esse mecanismo permite que os publicadores e assinantes para se comunicar sem a necessidade de uma referência entre si, ajudando a reduzir as dependências entre componentes, enquanto também permite que componentes de forma independente seja desenvolvido e testado.

O [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe fornece a publicação / assinatura funcionalidade de multicast. Isso significa que pode haver vários Publicadores que publicam uma única mensagem e pode haver vários assinantes escutando a mesma mensagem. Figura 4-1 ilustra essa relação:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Publicar-assinar funcionalidade de multicast")

**Figura 4-1:** Publicar-assinar funcionalidade de multicast

Editores enviam mensagens usando o [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método, enquanto os assinantes escutam mensagens usando o [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Além disso, os assinantes podem também cancelar a assinatura de assinaturas de mensagem, se necessário, com o [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método.

Internamente, o [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe usa referências fracas. Isso significa que ele será não manter objetos ativos e permitirá que eles sejam coletados como lixo. Portanto, só deve ser necessária para cancelar a assinatura de uma mensagem quando não deseja que uma classe receber a mensagem.

O aplicativo móvel de eShopOnContainers usa a [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe para se comunicar entre fracamente acoplada componentes. O aplicativo define três mensagens:

-   O `AddProduct` mensagem é publicada pelo `CatalogViewModel` classe quando um item é adicionado ao carrinho de compras. Em troca, o `BasketViewModel` classe assina a mensagem e incrementa o número de itens no carrinho de compras em resposta. Além disso, o `BasketViewModel` classe também cancela a assinatura desta mensagem.
-   O `Filter` mensagem é publicada pelo `CatalogViewModel` classe quando o usuário aplica um filtro de marca e o tipo para os itens exibidos a partir do catálogo. Em troca, o `CatalogView` classe assina a mensagem e atualiza a interface do usuário para que somente os itens que correspondem aos critérios de filtro são exibidos.
-   O `ChangeTab` mensagem é publicada pela `MainViewModel` quando o `CheckoutViewModel` navega para o `MainViewModel` após a criação bem-sucedida e o envio de uma nova ordem. Em troca, o `MainView` classe assina a mensagem e as atualizações da interface do usuário até que o **meu perfil** guia estiver ativa, para mostrar os pedidos do usuário.

> [!NOTE]
> Enquanto o [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe permite a comunicação entre classes acoplados de forma flexível, ele não oferece a solução de arquitetura somente para esse problema. Por exemplo, a comunicação entre um modelo de exibição e um modo de exibição também pode ser obtida pelo mecanismo de associação e por meio de notificações de alteração de propriedade. Além disso, a comunicação entre dois modelos de exibição também pode ser obtida pela passagem de dados durante a navegação.

No aplicativo móvel do eShopOnContainers [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) é usado para atualizar na interface do usuário em resposta a uma ação que ocorrem em outra classe. Portanto, as mensagens são publicadas no thread da interface do usuário, com assinantes para receber a mensagem no mesmo thread.

> [!TIP]
> Marshaling para o thread de interface do usuário quando realizar a interface do usuário atualiza. Se uma mensagem que é enviada de um thread em segundo plano é necessária para atualizar a interface do usuário, processar a mensagem no thread da interface do usuário no assinante, invocando o [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

Para obter mais informações sobre [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter), consulte [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definindo uma mensagem

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) as mensagens são cadeias de caracteres que são usadas para identificar mensagens. O exemplo de código a seguir mostra as mensagens definidas dentro do aplicativo móvel do eShopOnContainers:

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

Neste exemplo, as mensagens são definidas usando constantes. A vantagem dessa abordagem é que ele fornece segurança de tipo de tempo de compilação e o suporte de refatoração.

## <a name="publishing-a-message"></a>Publicar uma mensagem

Os editores notificam assinantes de uma mensagem com um dos [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) sobrecargas. O exemplo de código a seguir demonstra a publicação de `AddProduct` mensagem:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

Neste exemplo, o [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica três argumentos:

-   O primeiro argumento especifica a classe do remetente. A classe do remetente deve ser especificada por qualquer assinante que desejam receber a mensagem.
-   O segundo argumento especifica a mensagem.
-   O terceiro argumento especifica os dados de carga a ser enviada ao assinante. Nesse caso, os dados de carga são um `CatalogItem` instância.

O [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método publicará a mensagem e seus dados de carga, usando uma abordagem de disparar e esquecer. Portanto, a mensagem é enviada, mesmo se não houver nenhum assinante registrado para receber a mensagem. Nessa situação, a mensagem enviada é ignorada.

> [!NOTE]
> O [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método pode usar parâmetros genéricos para controlar como as mensagens são entregues. Portanto, várias mensagens que compartilham uma identidade de mensagem, mas enviar carga diferentes tipos de dados podem ser recebidas pelos assinantes diferentes.

## <a name="subscribing-to-a-message"></a>Inscrever-se em uma mensagem

Os assinantes podem se registrar para receber uma mensagem usando um dos [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sobrecargas. O exemplo de código a seguir demonstra como o aplicativo móvel do eShopOnContainers assina e processa, o `AddProduct` mensagem:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

Neste exemplo, o [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método assina o `AddProduct` da mensagem e executa um delegado de retorno de chamada em resposta ao recebimento da mensagem. Esse delegado de retorno de chamada, especificado como uma expressão lambda, executa o código que atualiza a interface do usuário.

> [!TIP]
> Considere o uso de dados de conteúdo imutáveis. Não tente modificar os dados de carga de dentro de um delegado de retorno de chamada porque vários threads podem estar acessando os dados recebidos simultaneamente. Nesse cenário, os dados de carga devem ser imutáveis para evitar erros de simultaneidade.

Um assinante talvez não precise lidar com todas as instâncias de uma mensagem publicada, e isso pode ser controlado pelos argumentos de tipo genérico que são especificados na [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Neste exemplo, o assinante receberá apenas `AddProduct` mensagens que são enviadas do `CatalogViewModel` classe, cujos dados de carga são um `CatalogItem` instância.

## <a name="unsubscribing-from-a-message"></a>Cancelando a assinatura de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não deseja receber. Isso é feito com um dos [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecargas, como demonstrado no exemplo de código a seguir:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

Neste exemplo, o [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sintaxe de método reflete os argumentos de tipo especificados ao inscrever-se para receber o `AddProduct` mensagem.

## <a name="summary"></a>Resumo

O xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa a publicar-padrão, permitindo a comunicação baseada em mensagens entre os componentes são inconvenientes para vincular as referências de objeto e o tipo de assinatura. Esse mecanismo permite que os publicadores e assinantes para se comunicar sem a necessidade de uma referência entre si, ajudando a reduzir as dependências entre componentes, enquanto também permite que componentes de forma independente seja desenvolvido e testado.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
