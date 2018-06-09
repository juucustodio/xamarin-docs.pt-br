---
title: Comunicação entre livremente acoplada componentes
description: 'Este capítulo explica como o aplicativo móvel eShopOnContainers implementa a publicação-assinatura padrão, permitindo que a comunicação baseada em mensagens entre componentes inconvenientes vincular por referências de objeto e do tipo '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 797032d17babe986de1357c6ac3291a4960d87ff
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245075"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicação entre livremente acoplada componentes

A publicação-assinatura padrão é um padrão de mensagens no qual Publicadores enviam mensagens sem ter conhecimento de qualquer destinatários, conhecido como assinantes. Da mesma forma, assinantes escutam mensagens específicas, sem ter conhecimento sobre quaisquer Publicadores.

Eventos no .NET implementam a publicação-assinatura padrão e são mais simple e direta abordagem para uma camada de comunicação entre os componentes se acoplamento não for necessário, como um controle e a página que o contém. No entanto, os tempos de vida do publicador e assinante estão ligados por referências de objeto para outro, e o tipo de assinante deve ter uma referência para o tipo do publicador. Isso pode criar problemas de gerenciamento de memória especialmente quando há objetos de vida curtos que assinam um evento de um objeto estático ou vida longa. Se o manipulador de eventos não é removido, o assinante será mantido ativo por referência a ele no publicador e isso impedir ou atrasar a coleta de lixo do assinante.

## <a name="introduction-to-messagingcenter"></a>Introdução ao MessagingCenter

O xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa a publicação-assinatura padrão, permitindo que a comunicação baseada em mensagens entre componentes inconvenientes vincular por referências de objeto e do tipo. Esse mecanismo permite Publicadores e assinantes para se comunicar sem ter uma referência uns aos outros, ajudando a reduzir as dependências entre componentes, permitindo também que os componentes ser desenvolvidos e testados independentemente.

O [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe fornece a publicação / assinatura funcionalidade de multicast. Isso significa que pode haver vários Publicadores que publicam uma única mensagem e pode haver vários assinantes escutando a mesma mensagem. Figura 4-1 ilustra a relação:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Publicação / assinatura funcionalidade de multicast")

**Figura 4-1:** publicação / assinatura funcionalidade de Multicast

Editores de enviam mensagens usando o [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender%7D/p/TSender/System.String/) método, enquanto os assinantes escutam mensagens usando o [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método. Além disso, os assinantes podem também cancelar a assinatura de assinaturas de mensagem, se necessário, com o [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender%7D/p/System.Object/System.String/) método.

Internamente, o [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe usa referências fracas. Isso significa que ele não irá manter objetos ativo e permitirá que eles sejam coletados como lixo. Portanto, só deverá ser necessária cancelar a assinatura de uma mensagem quando uma classe não deseja receber a mensagem.

Os usos de aplicativo móvel eShopOnContainers o [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe para se comunicar entre livremente acoplada componentes. O aplicativo define três mensagens:

-   O `AddProduct` mensagem é publicada pelo `CatalogViewModel` classe quando um item é adicionado ao carrinho de compras. No retorno, a `BasketViewModel` classe assina a mensagem e incrementa o número de itens no carrinho de compras em resposta. Além disso, a `BasketViewModel` classe também cancela a inscrição dessa mensagem.
-   O `Filter` mensagem é publicada pelo `CatalogViewModel` classe quando o usuário aplica um filtro de marca e o tipo para os itens exibidos a partir do catálogo. No retorno, a `CatalogView` classe assina a mensagem e atualiza a interface do usuário para que somente os itens que correspondem aos critérios de filtro são exibidos.
-   O `ChangeTab` mensagem é publicada pelo `MainViewModel` quando o `CheckoutViewModel` navega para o `MainViewModel` após a criação bem-sucedida e o envio de uma nova ordem. Em troca, o `MainView` classe assina as atualizações da interface do usuário e uma mensagem assim que o **meu perfil** guia está ativa, para mostrar os pedidos do usuário.

> [!NOTE]
> Enquanto o [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe permite a comunicação entre classes flexível, ela não oferece a solução arquitetura apenas a esse problema. Por exemplo, a comunicação entre um modelo de exibição e um modo de exibição pode ser feita pelo mecanismo de associação e por meio de notificações de alteração de propriedade. Além disso, a comunicação entre dois modelos de exibição pode ser feita passando dados durante a navegação.

No aplicativo móvel do eShopOnContainers[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) é usado para atualizar a interface do usuário em resposta a uma ação que ocorrem em outra classe. Portanto, as mensagens são publicadas no thread da interface do usuário, com assinantes para receber a mensagem no mesmo thread.

> [!TIP]
> Marshaling para o thread de interface do usuário quando atualizações de executar a interface do usuário. Se uma mensagem que é enviada de um thread em segundo plano é necessária para atualizar a interface do usuário, processar a mensagem no thread da interface do usuário no assinante, chamando o [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método.

Para obter mais informações sobre [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/), consulte [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definindo uma mensagem

[`MessagingCenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) as mensagens são cadeias de caracteres que são usadas para identificar mensagens. O exemplo de código a seguir mostra as mensagens de definido dentro do aplicativo móvel eShopOnContainers:

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

Neste exemplo, as mensagens são definidas usando constantes. A vantagem dessa abordagem é que ele fornece suporte à refatoração e segurança de tipo de tempo de compilação.

## <a name="publishing-a-message"></a>Publicar uma mensagem

Editores de notificam os assinantes de uma mensagem com uma da [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) sobrecargas. O exemplo de código a seguir demonstra a publicação de `AddProduct` mensagem:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

Neste exemplo, o [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método especifica três argumentos:

-   O primeiro argumento especifica a classe do remetente. A classe do remetente deve ser especificada por qualquer assinante que desejam receber a mensagem.
-   O segundo argumento especifica a mensagem.
-   O terceiro argumento especifica os dados de carga a ser enviada ao assinante. Nesse caso, os dados de carga são um `CatalogItem` instância.

O [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método publicará a mensagem e seus dados de carga, usando uma abordagem disparar e esquecer. Portanto, a mensagem é enviada, mesmo se não houver nenhum assinantes registrados para receber a mensagem. Nessa situação, a mensagem enviada será ignorada.

> [!NOTE]
> O [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método pode usar parâmetros genéricos para controlar como as mensagens são entregues. Portanto, várias mensagens que compartilham uma identidade de mensagem, mas enviar carga diferentes tipos de dados podem ser recebidas por assinantes diferentes.

## <a name="subscribing-to-a-message"></a>Inscrever-se em uma mensagem

Os assinantes podem se registrar para receber uma mensagem usando um do [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) sobrecargas. O exemplo de código a seguir demonstra como o aplicativo móvel eShopOnContainers assina e processa, o `AddProduct` mensagem:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

Neste exemplo, o [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método assina o `AddProduct` da mensagem e um representante de retorno de chamada é executado em resposta ao recebimento da mensagem. Este representante de retorno de chamada, especificado como uma expressão lambda, executa o código que atualiza a interface do usuário.

> [!TIP]
> Considere o uso de dados de carga imutável. Não tente modificar os dados de carga de dentro de um representante de retorno de chamada porque vários threads podem acessar os dados recebidos simultaneamente. Nesse cenário, os dados de carga devem ser imutáveis para evitar erros de simultaneidade.

Um assinante talvez não seja necessário lidar com todas as instâncias de uma mensagem publicada, e isso pode ser controlado por argumentos de tipo genérico que são especificados no [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método. Neste exemplo, o assinante receberá apenas `AddProduct` mensagens que são enviadas do `CatalogViewModel` classe, cujos dados de carga são um `CatalogItem` instância.

## <a name="unsubscribing-from-a-message"></a>Cancelamento de inscrição de uma mensagem

Os assinantes podem cancelar a assinatura de mensagens que não deseja receber. Isso é feito com uma da [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) sobrecargas, conforme demonstrado no exemplo de código a seguir:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

Neste exemplo, o [ `Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) sintaxe de método reflete os argumentos de tipo especificados ao inscrever-se para receber a `AddProduct` mensagem.

## <a name="summary"></a>Resumo

O xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa a publicação-assinatura padrão, permitindo que a comunicação baseada em mensagens entre componentes inconvenientes vincular por referências de objeto e do tipo. Esse mecanismo permite Publicadores e assinantes para se comunicar sem ter uma referência uns aos outros, ajudando a reduzir as dependências entre componentes, permitindo também que os componentes ser desenvolvidos e testados independentemente.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
