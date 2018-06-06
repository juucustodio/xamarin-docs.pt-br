---
title: Assinaturas e relatórios no xamarin
description: Este documento descreve não renovar assinaturas, assinaturas gratuitas, assinaturas automática renovável e usando iTunes Connect para relatar esses itens.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7e0873107a60b48e5ebfd8e159f3bf3b85d02867
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787023"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Assinaturas e relatórios no xamarin

## <a name="about-non-renewing-subscriptions"></a>Sobre assinaturas não renovar

Assinaturas renovando não se destinam a produtos que representam a venda de um serviço com uma restrição de tempo, como (acesso de uma semana para um aplicativo de navegação) ou acesso de tempo limitado a um arquivo de dados.   
   
Principais diferenças entre as assinaturas não renovar e outros tipos de produto:

-  A definição de produto no iTunes conectar não inclui o termo. O código do aplicativo deve ser capaz de inferir o período de validade da identificação do produto. 
-  Eles podem ser adquiridos várias vezes (como um produto consumível). Aplicativos são necessárias para gerenciar o termo/expiração de assinatura e a renovação e impedir que o usuário comprar assinaturas sobrepostas. 
-  Não há suporte para as compras pela função StoreKit restaurar. Se a assinatura deve estar disponível em todos os dispositivos do usuário, o aplicativo terá que projetar e implementar esse recurso em conjunto com um servidor remoto. Aplicativos também são responsáveis por fazer o backup do status da assinatura para casos quando o backup de um dispositivo é então restaurada-do-backup. 
-  Visão geral da implementação
-  Renovando não assinaturas normalmente deve ser implementadas usando o fluxo de trabalho fornecida pelo servidor e gerenciados como produtos de consumo. 


## <a name="about-free-subscriptions"></a>Sobre assinaturas gratuitas

Assinaturas gratuitas permitem aos desenvolvedores de colocar o conteúdo livre em aplicativos de Newsstand (eles não podem ser usados em aplicativos de Newsstand não). Depois que uma assinatura gratuita é iniciada ele estará disponível em todos os dispositivos do usuário. Assinaturas gratuitas nunca expirarem; eles apenas fim quando o aplicativo é desinstalado.

### <a name="implementation-overview"></a>Visão geral da implementação

Assinaturas gratuitas se comportam muito como Auto renovável assinaturas. O aplicativo deve ter um produto de assinatura gratuita disponível para 'compra' na iTunes conectar. Quando adquirido pelo usuário, a compra de assinatura deve ser validada como um produto de inscrição automática renováveis. Transações de assinatura podem ser restauradas.


## <a name="about-auto-renewable-subscriptions"></a>Sobre assinaturas automática renovável

Auto renovável assinaturas são usadas principalmente em aplicativos de Newsstand. Eles representam um produto que concede ao usuário acesso ao conteúdo dinâmico para um determinado período de tempo, que é configurado no iTunes Connect (definir os períodos que variam de 7 dias para 1 ano). As assinaturas são renovadas automaticamente, carregando os usuários ID da Apple no final de cada período de assinatura, a menos que o usuário aceita-out. Esse tipo de produto funciona bem para assinaturas de revista ou notícias, onde o usuário obtém acesso a cada problema publicado enquanto sua assinatura é válida.

### <a name="implementation-overview"></a>Visão geral da implementação

Assinaturas automática renovável deve ser implementadas usando o fluxo de trabalho de produtos Server-Delivered (consulte a *verificação de recebimento e produtos Server-Delivered* seção).

#### <a name="shared-secret"></a>Segredo compartilhado

O segredo do aplicativo na compra compartilhado deve ser usado na solicitação JSON ao verificar assinaturas renovável automática no servidor. O segredo compartilhado é criado/acessados por meio do Connect do iTunes.

Da página de home para conectar-se selecionar iTunes **meus aplicativos**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Selecionar Meus aplicativos")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Selecione um aplicativo e clique no **compras no aplicativo** guia:

[![](subscriptions-and-reporting-images/image6.png "Clique na guia de compras em aplicativo")](subscriptions-and-reporting-images/image6.png#lightbox)

Na parte inferior da página, selecione **exibição ou gerar um segredo compartilhado**:
   
 [![](subscriptions-and-reporting-images/image40.png "Selecione o modo de exibição ou gerar um segredo compartilhado")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Gerar um segredo compartilhado")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Para usar o segredo compartilhado, incluí-lo na carga JSON que é enviada para servidores da Apple ao validar uma confirmação de compra no aplicativo para uma assinatura automática renovável, como este:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Campo de status da resposta será zero se a compra for válida, como com outros tipos de produto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Download de itens após o prazo da inscrição inicial

Como parte do fornecimento de produtos de assinatura, o código deve verificar com frequência o recebimento mais recente conhecido em servidores da Apple. Se uma assinatura foi renovada automaticamente desde a última verificação, a resposta JSON contém campos adicionais para notificar o aplicativo da transação que ocorreu (que deve estender a validade de assinaturas). A resposta JSON conterá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se o status for zero, em seguida, a assinatura ainda é válida e os outros campos mantêm dados válidos. Se o status for 21006 a assinatura expirou. Consulte o [verificando uma confirmação de inscrição automática renovável](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentação para outros códigos de erro.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restauração automática renovável assinaturas

Você terá várias transações – a transação de compra original além de uma transação separada para cada período de tempo que a assinatura foi renovada. Você precisa controlar as datas de início e os termos para entender o que é o período de validade.   
   
   
   
 O objeto SKPaymentTransaction não inclui o termo de assinatura – você deve usar uma ID de produto diferente para cada termo e escrever código que pode extrapolar o período de assinatura a partir da data de compra da transação.

#### <a name="testing-auto-renewal"></a>Testar a renovação automática

Para facilitar o teste de assinaturas, suas durações são compactadas quando testar em área restrita. 1 semana as assinaturas são renovadas a cada 3 minutos, 1 ano, as assinaturas são renovadas a cada hora. Assinaturas renovará automaticamente um máximo de 6 horas durante o teste na área restrita.

## <a name="reporting"></a>Relatórios

conectar-se de iTunes ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) fornece:   
   
 **Vendas e tendências** – exibe os detalhes do aplicativo downloads, atualizações e compras no aplicativo.   
   
 **Pagamentos e relatórios financeiros** – fornece detalhes sobre a receita de seus aplicativos, bem como pagamentos de listagem que foram feitos para você e o quanto você está relacionada.

Um relatório de vendas e as tendências de exemplo é mostrado abaixo:   

 [![](subscriptions-and-reporting-images/image42.png "Um exemplo de relatório de vendas e tendências")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 Também há um [ **ITC conectar Mobile**aplicativo iOS (link iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
capturas de tela de iPhone para algumas das estatísticas disponíveis são mostradas aqui:   
   
 [![](subscriptions-and-reporting-images/image43.png "capturas de tela de iPhone para algumas das estatísticas disponíveis")](subscriptions-and-reporting-images/image43.png#lightbox)
