---
title: Apple Pay no watchOS no Xamarin
description: Este artigo aborda as melhorias feitas até o Apple Pay Apple no watchOS 3 e como implementá-los no xamarin. IOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364096"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay no watchOS no Xamarin

Apple fez várias melhorias para Apple Pay no watchOS 3 que adiciona suporte para pagamentos no aplicativo. Isso permite que o usuário fornecer pagamento com segurança e informações para pagar por serviços e produtos físicos diretamente a partir do Apple Watch de contato.


## <a name="about-apple-pay-enhancements"></a>Sobre os aprimoramentos do Apple Pay

Como Stated acima, Apple fez várias melhorias para Apple Pay no watchOS 3 que permitem pagamento seguro e entre em contato com informações de pagamento de serviços e produtos físicos diretamente a partir do Apple Watch. Esses aprimoramentos são fornecidos pelas modificações ao PassKit framework.

Com o iOS 10 e watchOS 3, diversas APIs novas foram adicionadas que funcionam com o iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

## <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No iOS 10, PassKit framework foi expandido para dar suporte a Apple Pay fora do `UIKit` e permitir que os emissores de cartão apresentar seus cartões de dentro de seus aplicativos. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Suporte a Apple Pay fora UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte a mesma funcionalidade fornecida pelo [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem o uso de UIKit. Embora essa nova API é necessária para dar suporte a Apple Pay sobre o Apple Watch (e intenções específicas também), é opcional em outras situações (por exemplo, os aplicativos existentes). No entanto, a Apple sugere mudar para a nova API assim que possível para fornecer amplo suporte a Apple Pay ao longo de todos os aplicativos do desenvolvedor com uma única base de código. Para obter mais informações sobre as intenções e Siri integração, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) documentação.

### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentação de cartões de emissor de dentro de aplicativos

Com o iOS 10 e watchOS 3, novos recursos foram adicionados ao PassKit framework que permitem que os emissores de cartão apresentar seus cartões de pagamento de dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que será exibido um botão Apple Pay para um cartão.

O `PresentPaymentPass` método da [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe também pode ser usada para exibir o cartão de forma programática.

## <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo para iOS 10 e watchOS 3, um aplicativo pode automaticamente dar suporte a uma nova rede de pagamento quando ele estiver disponível, sem que o desenvolvedor ter que modificar, recompilar o aplicativo e reenviá-la para a App Store.

O novo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método o `PKPaymentNetwork` classe permite que um aplicativo descobrir as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, o [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propriedade foi expandida para levar o nome do provedor de pagamento como um argumento. Usando esses métodos, um aplicativo pode automaticamente suporte a nenhuma rede com suporte para o provedor de pagamento.

Para obter mais informações, consulte nosso [configuração de pagamento de Apple](~/ios/platform/apple-pay.md) da Apple [guia de pagamento de Apple](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Novo ambiente de teste

Com o iOS 10 e watchOS 3, a Apple introduziu um novo ambiente de teste que permite ao desenvolvedor provisionar os cartões de pagamento de teste diretamente em um dispositivo iOS. Neste novo ambiente de teste, em seguida, retorna os dados de pagamento de teste criptografada para o aplicativo.

Para habilitar o novo ambiente de teste, faça o seguinte:

1. Crie um nova conta do iCloud teste no iTunes Connect.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo no.
4. Use um dos cartões de pagamento de teste do [guia de pagamento de Apple](https://developer.apple.com/apple-pay/) fazer pagamentos.

> [!NOTE]
> Ao alternar as contas do iCloud, o dispositivo alternará automaticamente para o novo ambiente de teste. No entanto, a Apple ainda **requer** o aplicativo a ser testado com real cartões em um ambiente de produção antes do envio à iTunes App Store.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos de Apple fez para o Apple Pay no watchOS 3 e como implementá-los no xamarin. IOS.
