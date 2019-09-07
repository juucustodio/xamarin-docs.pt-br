---
title: Apple Pay em watchOS no Xamarin
description: Este artigo aborda os aprimoramentos que a Apple fez para Apple Pay no watchOS 3 e como implementá-los no Xamarin. iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 579f2afd8e52251973900f35ef91ac086adf7603
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768644"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay em watchOS no Xamarin

A Apple fez vários aprimoramentos para Apple Pay no watchOS 3, que adiciona suporte para pagamentos no aplicativo. Isso permite que o usuário forneça com segurança informações de pagamento e de contato para pagar bens e serviços físicos diretamente da Apple Watch.

## <a name="about-apple-pay-enhancements"></a>Sobre os aprimoramentos de Apple Pay

Conforme mencionado acima, a Apple fez vários aprimoramentos para Apple Pay no watchOS 3, que permitem o pagamento seguro e as informações de contato para pagar bens e serviços físicos diretamente da Apple Watch. Esses aprimoramentos são fornecidos por modificações na estrutura PassKit.

Com o iOS 10 e o watchOS 3, foram adicionadas várias novas APIs que funcionam com o iOS e o watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste da área restrita.

## <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No Ios 10, a estrutura PassKit foi expandida para dar suporte a Apple Pay `UIKit` fora do e para permitir que os emissores apresentem seus cartões de dentro de seus aplicativos. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Dando suporte a Apple Pay fora do UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte à mesma funcionalidade fornecida pelo [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem usar UIKit. Embora essa nova API seja necessária para dar suporte a Apple Pay no Apple Watch (e em tentativas específicas também), ela é opcional em outras situações (como aplicativos existentes). No entanto, a Apple sugere a mudança para a nova API assim que possível para fornecer amplo suporte Apple Pay em todos os aplicativos do desenvolvedor com uma única base de código. Para obter mais informações sobre a integração de intenções e Siri, consulte nossa [introdução à](~/ios/platform/sirikit/index.md) documentação do SiriKit.

### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentando os cartões do emissor de dentro dos aplicativos

Com o iOS 10 e o watchOS 3, novos recursos foram adicionados à estrutura PassKit, que permitem que os emissores de cartões apresentem seus cartões de pagamento dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que exibirá um botão de Apple Pay para um cartão.

O `PresentPaymentPass` método da classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) também pode ser usado para exibir o cartão de forma programática.

## <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo no iOS 10 e no watchOS 3, um aplicativo pode dar suporte automaticamente a uma nova rede de pagamento quando ela se tornar disponível sem que o desenvolvedor precise modificar, recompilar o aplicativo e reenviá-lo para a loja de aplicativos.

O novo método [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) da `PKPaymentNetwork` classe permite que um aplicativo Descubra as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, a propriedade [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) foi expandida para pegar o nome do provedor de pagamentos como um argumento. Usando esses métodos, um aplicativo pode dar suporte automaticamente a qualquer rede à qual o provedor de pagamento dá suporte.

Para obter mais informações, consulte nossa [configuração de Apple Pay](~/ios/platform/apple-pay.md) e o [Guia de Apple Pay](https://developer.apple.com/apple-pay/)da Apple.

## <a name="new-testing-environment"></a>Novo ambiente de teste

Com o iOS 10 e o watchOS 3, a Apple introduziu um novo ambiente de teste que permite ao desenvolvedor provisionar cartões de pagamento de teste diretamente em um dispositivo iOS. Esse novo ambiente de teste, em seguida, retorna dados de pagamento de teste criptografados para o aplicativo.

Para habilitar o novo ambiente de teste, faça o seguinte:

1. Crie uma nova conta do iCloud de teste no iTunes Connect.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo.
4. Use um dos cartões de pagamento de teste do [Guia de Apple Pay](https://developer.apple.com/apple-pay/) para fazer pagamentos.

> [!NOTE]
> Alternando as contas do iCloud, o dispositivo mudará automaticamente para o novo ambiente de teste. No entanto, a Apple ainda **requer** que o aplicativo seja testado com cartões reais em um ambiente de produção antes do envio para a iTunes App Store.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos que a Apple fez para Apple Pay no watchOS 3 e como implementá-los no Xamarin. iOS.
