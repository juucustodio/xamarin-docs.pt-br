---
title: Assinaturas e relatórios no xamarin. IOS
description: Este documento descreve as assinaturas não renovar, assinaturas gratuitas, assinaturas automática renovável e usando o iTunes Connect para esses itens de relatório.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 44dd2b2eea31f47ed53d8ef25ff7b0667286179d
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674554"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Assinaturas e relatórios no xamarin. IOS

## <a name="about-non-renewing-subscriptions"></a>Sobre como não renovar assinaturas

Assinaturas de renovação de não são destinadas a produtos que representam a venda de um serviço com uma restrição de tempo, como (acesso a uma semana para um aplicativo de navegação) ou acesso de tempo limitado a um arquivo de dados.   
   
Principais diferenças entre assinaturas não-renovar e outros tipos de produto:

-  A definição de produto no iTunes Connect não inclui o termo. O código do aplicativo deve ser capaz de inferir o período de validade da identificação do produto. 
-  Eles podem ser comprados várias vezes (como um produto de consumo). Aplicativos são necessários para gerenciar o termo/expiração de assinatura e a renovação e impedir que o usuário comprando assinaturas de sobreposição. 
-  Não há suporte para as compras pela função StoreKit restaurar. Se a assinatura deve estar disponível em todos os dispositivos do usuário, o aplicativo terá que projetar e implementar esse recurso em conjunto com um servidor remoto. Aplicativos também são responsáveis por fazer o backup o status da assinatura para casos quando um dispositivo é armazenado em backup, em seguida, restaurados-do-backup. 
-  Visão geral da implementação
-  Assinaturas não-renovando normalmente devem ser implementadas usando o fluxo de trabalho entregue ao servidor e gerenciados como produtos de consumo. 


## <a name="about-free-subscriptions"></a>Sobre assinaturas gratuitas

Assinaturas gratuitas permitem aos desenvolvedores de colocar conteúdo gratuito em aplicativos de Newsstand (eles não podem ser usados em aplicativos não-Newsstand). Depois que uma assinatura gratuita é iniciada, ele estará disponível em todos os dispositivos do usuário. Assinaturas gratuitas nunca expirarem; eles só terminam quando o aplicativo é desinstalado.

### <a name="implementation-overview"></a>Visão geral da implementação

Assinaturas gratuitas têm um comportamento semelhante pode ser renovado automaticamente assinaturas. O aplicativo deve ter um produto de assinatura gratuita disponível para 'compra' no iTunes Connect. Quando adquirido pelo usuário, a compra de assinatura gratuita deve ser validada como um produto de assinatura pode ser renovado automaticamente. Transações de assinatura gratuita podem ser restauradas.


## <a name="about-auto-renewable-subscriptions"></a>Sobre assinaturas pode ser renovado automaticamente

Pode ser renovado automaticamente assinaturas são usadas principalmente em aplicativos de Newsstand. Eles representam um produto que concede ao usuário acesso a conteúdo dinâmico para um determinado período de tempo, que é configurado no iTunes Connect (definir os períodos que variam de 7 dias para 1 ano). As assinaturas são renovadas automaticamente, cobrar os usuários a ID da Apple no final de cada período de assinatura, a menos que o usuário aceita-out. Esse tipo de produto funciona bem para assinaturas de revista ou notícias, em que o usuário obtém acesso a cada problema publicado enquanto sua assinatura é válida.

### <a name="implementation-overview"></a>Visão geral da implementação

Assinaturas automática renovável deve ser implementadas usando o fluxo de trabalho Server-Delivered produtos (consulte a *verificação de recebimento e produtos Server-Delivered* seção).

#### <a name="shared-secret"></a>Segredo compartilhado

O segredo de compartilhado de compra no aplicativo deve ser usado na solicitação JSON durante a verificação de assinaturas pode ser renovado automaticamente no seu servidor. O segredo compartilhado é criado/acessados por meio do iTunes Connect.

Da página de home para conectar-se selecionar iTunes **meus aplicativos**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Selecionar Meus aplicativos")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Selecione um aplicativo e clique no **compras no aplicativo** guia:

[![](subscriptions-and-reporting-images/image6.png "Clique na guia compras no aplicativo")](subscriptions-and-reporting-images/image6.png#lightbox)

Na parte inferior da página, selecione **exibir ou gerar um segredo compartilhado**:
   
 [![](subscriptions-and-reporting-images/image40.png "Selecione o modo de exibição ou gerar um segredo compartilhado")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Gerar um segredo compartilhado")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Para usar o segredo compartilhado, incluí-lo na carga JSON que é enviada aos servidores da Apple ao validar um recebimento de compra no aplicativo para uma assinatura pode ser renovado automaticamente, como este:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Campo de status da resposta será zero se a compra for válida, como com outros tipos de produto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Baixando itens após o prazo de assinatura inicial

Como parte do fornecimento de produtos por assinatura, o código com frequência deve verificar o recebimento mais recente conhecido em servidores da Apple. Se uma assinatura foi renovado automaticamente desde a última verificação, a resposta JSON conterá os campos adicionais que notificar o aplicativo da transação que ocorreu (que deve estender a validade de assinaturas). A resposta JSON conterá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se o status for zero, em seguida, a assinatura ainda é válida e os outros campos contêm dados válidos. Se o status for 21006 a assinatura expirou. Consulte a [Verificando um recibo de assinatura automática renovável](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentação para outros códigos de erro.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restauração automática renovável assinaturas

Você obterá novamente várias transações – a transação de compra original além de uma transação separada para cada período de tempo que a assinatura foi renovada. Você precisa controlar as datas de início e os termos para entender o que é o período de validade.   
   
   
   
 O objeto SKPaymentTransaction não inclui o termo de assinatura – você deve usar uma ID de produto diferente para cada termo e escrever um código que pode extrapolar o período de assinatura a partir da data de compra da transação.

#### <a name="testing-auto-renewal"></a>Testar a renovação automática

Para tornar mais fácil de testar as assinaturas, suas durações são compactadas durante o teste na área restrita. 1 semana assinaturas sejam renovadas a cada 3 minutos, 1 ano assinaturas sejam renovadas a cada hora. As assinaturas serão renovadas automaticamente um máximo de 6 vezes durante o teste na área restrita.

## <a name="reporting"></a>Relatórios

o iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) fornece:   
   
 **Vendas e tendências** – exibe os detalhes do aplicativo de downloads, atualizações e compras no aplicativo.   
   
 **Pagamentos e relatórios financeiros** – fornece detalhes sobre a receita de seus aplicativos, bem como os pagamentos de listagem que foram feitos para você e o quanto você está relacionada.

Um relatório de vendas e tendências de exemplo é mostrado abaixo:   

 [![](subscriptions-and-reporting-images/image42.png "Um exemplo de relatório de vendas e tendências")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 Também há um [ **ITC conectar Mobile**aplicativo do iOS (link iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
capturas de tela de iPhone para algumas das estatísticas disponíveis são mostradas aqui:   
   
 [![](subscriptions-and-reporting-images/image43.png "capturas de tela de iPhone para algumas das estatísticas disponíveis")](subscriptions-and-reporting-images/image43.png#lightbox)
