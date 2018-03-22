---
title: Pagamento da Apple em watchOS
description: "Este artigo aborda os aprimoramentos a Apple tem feitas ao pagamento da Apple watchOS 3 e como implementá-los no xamarin para Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 591f6f53c9e787ee9499b2a1a3cc812f7e72749a
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="apple-pay-on-watchos"></a>Pagamento da Apple em watchOS

Apple fez várias melhorias para pagamento da Apple watchOS 3 que adiciona suporte para pagamentos no aplicativo. Isso permite que o usuário forneça pagamento com segurança e informações para pagar pelos serviços e produtos físicos diretamente a partir do Apple Watch de contato.


## <a name="about-apple-pay-enhancements"></a>Sobre os aprimoramentos de pagamento da Apple

Como Stated acima, Apple fez várias melhorias para pagamento da Apple watchOS 3 que permitem pagamento seguro e entre em contato com informações para pagar pelos serviços e produtos físicos diretamente a partir do Apple Watch. Essas melhorias são fornecidas por modificações para a estrutura PassKit.

Com iOS 10 e watchOS 3, várias novas APIs foram adicionadas que funcionam com iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

## <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No iOS 10, a estrutura de PassKit foi expandida para dar suporte a pagamento da Apple fora do `UIKit` e permitir que os emissores de cartão apresentar seus cartões de dentro de seus aplicativos. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Suporte pago Apple fora UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte a mesma funcionalidade fornecida pelo [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem usar UIKit. Embora essa nova API é necessária para suportar o pagamento da Apple a Apple Watch (e em bem propósitos específicos), é opcional em outras situações (como aplicativos existentes). No entanto, a Apple sugere mover para a nova API assim que possível para fornecer amplo suporte de pagamento da Apple ao longo de todos os aplicativos do desenvolvedor com uma base de código único. Para obter mais informações sobre tentativas e Siri integração, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) documentação.

### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentando os cartões de emissor de dentro de aplicativos

Com iOS 10 e watchOS 3, novos recursos foram adicionados para a estrutura PassKit que permitem que os emissores de cartão apresentar seus cartões de pagamento de dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que será exibido um botão de pagamento da Apple para um cartão.

O `PresentPaymentPass` método o [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe também pode ser usada para exibir programaticamente o cartão.

## <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo para iOS 10 e watchOS 3, um aplicativo pode automaticamente dar suporte a uma nova rede de pagamento quando eles ficarem disponíveis sem o necessidade de modificar, recompile o aplicativo e reenviá-lo à loja de aplicativos de desenvolvedor.

O novo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método o `PKPaymentNetwork` classe permite que um aplicativo descobrir as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, o [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propriedade foi expandida para levar o nome do provedor de pagamentos como um argumento. Usando esses métodos, um aplicativo pode suportar automaticamente qualquer rede que dá suporte ao provedor de pagamentos.

Para obter mais informações, consulte nosso [Apple pagar configuração](~/ios/platform/apple-pay.md) da Apple [guia pagamento da Apple](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Novo ambiente de teste

Com iOS 10 e watchOS 3, a Apple introduziu um novo ambiente de teste que permite que o desenvolvedor provisionar cartões de pagamento de teste diretamente em um dispositivo iOS. Esse novo ambiente de teste, em seguida, retorna dados de pagamento de teste criptografado para o aplicativo.

Para ativar o novo ambiente de teste, faça o seguinte:

1. Crie um nova conta de iCloud teste no iTunes conectar.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo no.
4. Use uma das placas de pagamento de teste do [guia pagamento da Apple](https://developer.apple.com/apple-pay/) para fazer pagamentos.

> [!NOTE]
> Alternando iCloud contas, o dispositivo alternará automaticamente para o novo ambiente de teste. No entanto, ainda Apple **requer** o aplicativo a ser testado com real cartões em um ambiente de produção antes do envio à iTunes App Store.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos a Apple tem feitas ao pagamento da Apple watchOS 3 e como implementá-los no xamarin.
