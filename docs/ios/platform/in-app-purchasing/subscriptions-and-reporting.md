---
title: Assinaturas e relatórios no Xamarin. iOS
description: Este documento descreve assinaturas não renovadas, assinaturas gratuitas, assinaturas renováveis automaticamente e uso do iTunes Connect para relatar esses itens.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 7f455d2164573d68db0a9c764f2b2cef5cc6d739
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284033"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Assinaturas e relatórios no Xamarin. iOS

## <a name="about-non-renewing-subscriptions"></a>Sobre assinaturas que não são renovadas

Assinaturas não renovadas destinam-se a produtos que representam a venda de um serviço com uma restrição de tempo (como o acesso de uma semana a um aplicativo de navegação ou acesso limitado a tempo a um arquivo de dados).   
   
Principais diferenças entre assinaturas não renovadas e outros tipos de produtos:

- A definição do produto no iTunes Connect não inclui o termo. O código do aplicativo deve ser capaz de inferir o período de validade da ID do produto. 
- Eles podem ser comprados várias vezes (como um produto consumível). Os aplicativos são necessários para gerenciar o período de assinatura/expiração e renovação e impedir que o usuário compre assinaturas sobrepostas. 
- As compras não são suportadas pela função de restauração StoreKit. Se a assinatura estiver disponível em todos os dispositivos de um usuário, o aplicativo terá que projetar e implementar esse recurso em conjunto com um servidor remoto. Os aplicativos também são responsáveis por fazer backup do status da assinatura para casos em que é feito backup de um dispositivo e restaurado do backup. 
- Visão geral da implementação
- Assinaturas não renovadas normalmente devem ser implementadas usando o fluxo de trabalho fornecido pelo servidor e gerenciadas como produtos consumíveis. 


## <a name="about-free-subscriptions"></a>Sobre assinaturas gratuitas

As assinaturas gratuitas permitem que os desenvolvedores coloquem conteúdo gratuito em aplicativos Newsstand (não podem ser usados em aplicativos não Newsstand). Depois que uma assinatura gratuita for iniciada, ela estará disponível em todos os dispositivos do usuário. As assinaturas gratuitas nunca expiram; Eles somente terminam quando o aplicativo é desinstalado.

### <a name="implementation-overview"></a>Visão geral da implementação

As assinaturas gratuitas se comportam de forma muito semelhante às assinaturas renováveis automaticamente. O aplicativo deve ter um produto de assinatura gratuito disponível para ' compra ' no iTunes Connect. Quando adquirido pelo usuário, a compra de assinatura gratuita deve ser validada como um produto de assinatura autorenovável. As transações de assinatura gratuitas podem ser restauradas.


## <a name="about-auto-renewable-subscriptions"></a>Sobre assinaturas renováveis automaticamente

As assinaturas renováveis automaticamente são usadas principalmente em aplicativos Newsstand. Eles representam um produto que concede ao usuário acesso ao conteúdo dinâmico por um determinado período de tempo, que é configurado no iTunes Connect (definir períodos variando de 7 dias a 1 ano). As assinaturas são renovadas automaticamente, carregando a ID da Apple dos usuários no final de cada período de assinatura, a menos que o usuário as tenha. Esse tipo de produto funciona bem para assinaturas de revistas ou notícias, em que o usuário obtém acesso a cada problema publicado enquanto sua assinatura é válida.

### <a name="implementation-overview"></a>Visão geral da implementação

As assinaturas renováveis automaticamente devem ser implementadas usando o fluxo de trabalho produtos entregues pelo servidor (consulte a seção *verificação de recebimento e produtos entregues pelo servidor* ).

#### <a name="shared-secret"></a>Segredo compartilhado

O segredo compartilhado de compra no aplicativo deve ser usado na solicitação JSON ao verificar assinaturas renováveis automaticamente no servidor. O segredo compartilhado é criado/acessado por meio do iTunes Connect.

No iTunes Connect home page selecione **meus aplicativos**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Selecionar Meus aplicativos")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Selecione um aplicativo e clique na guia **compras no aplicativo** :

[![](subscriptions-and-reporting-images/image6.png "Clique na guia compras no aplicativo")](subscriptions-and-reporting-images/image6.png#lightbox)

Na parte inferior da página, selecione **Exibir ou gerar um segredo compartilhado**:
   
 [![](subscriptions-and-reporting-images/image40.png "Selecione Exibir ou gerar um segredo compartilhado")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Gerar um segredo compartilhado")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Para usar o segredo compartilhado, inclua-o no conteúdo JSON que é enviado aos servidores da Apple ao validar uma confirmação de compra no aplicativo para uma assinatura renovável automaticamente, desta forma:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

O campo status da resposta será zero se a compra for válida, como com outros tipos de produto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Baixando itens após o prazo de assinatura inicial

Como parte do fornecimento de produtos de assinatura, o código deve verificar frequentemente o recebimento conhecido mais recente nos servidores da Apple. Se uma assinatura tiver renovado automaticamente desde a última verificação, a resposta JSON conterá campos adicionais que notificam o aplicativo da transação que ocorreu (o que deve estender a validade das assinaturas). A resposta JSON conterá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se o status for zero, a assinatura ainda será válida e os outros campos armazenarão dados válidos. Se o status for 21006, a assinatura expirou. Consulte a documentação [verificando uma assinatura de recebimento de assinaturas renovável automaticamente](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) para outros códigos de erro.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restaurando assinaturas renováveis automaticamente

Você receberá várias transações – a transação de compra original mais uma transação separada para cada período de tempo em que a assinatura foi renovada. Você precisa acompanhar as datas de início e os termos para entender qual é o período de validade.   
   
   
   
 O objeto SKPaymentTransaction não inclui o termo de assinatura – você deve usar uma ID de produto diferente para cada termo e escrever código que possa extrapolar o período de assinatura da data de compra da transação.

#### <a name="testing-auto-renewal"></a>Testando renovação automática

Para facilitar o teste de assinaturas, suas durações são compactadas durante o teste na área restrita. uma renovação de assinaturas de 1 semana a cada 3 minutos, as assinaturas de 1 ano são renovadas a cada hora. As assinaturas serão renovadas automaticamente no máximo seis vezes durante o teste na área restrita.

## <a name="reporting"></a>Relatório

o iTunes Connect ( [iTunesConnect.Apple.com](http://itunesconnect.apple.com)) fornece:   
   
 **Vendas e tendências** – exibe detalhes de downloads de aplicativos, atualizações e compras no aplicativo.   
   
 **Pagamentos e relatórios financeiros** – detalha a renda obtida por seus aplicativos, bem como a listagem de pagamentos que foram feitos a você e o quanto você está devido.

Um relatório de vendas e tendências de exemplo é mostrado abaixo:   

 [![](subscriptions-and-reporting-images/image42.png "Um relatório de vendas e tendências de exemplo")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 Há também um [aplicativo IOS **Mobile do ITC Connect**(iTunes link)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
as capturas de tela do iPhone para algumas das estatísticas disponíveis são mostradas aqui:   
   
 [![](subscriptions-and-reporting-images/image43.png "capturas de tela do iPhone para algumas das estatísticas disponíveis")](subscriptions-and-reporting-images/image43.png#lightbox)
