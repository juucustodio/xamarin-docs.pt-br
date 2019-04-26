---
title: Apple Pay no xamarin. IOS
description: Este guia explora como configurar o ambiente do xamarin. IOS para uso com o Apple Pay paguem produtos físicos, como comida, entretenimento e as associações por meio de seu aplicativo. Ele inclui informações sobre os identificadores necessários, certificados e direitos.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: b971029ff3b2b1e8f5e63233d1d754c44b0e3309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346955"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay no xamarin. IOS

_Este guia explora como configurar o ambiente do xamarin. IOS para uso com o Apple Pay paguem produtos físicos, como comida, entretenimento e as associações por meio de seu aplicativo. Ele inclui informações sobre os identificadores necessários, certificados e direitos._

Apple Pay foi introduzido junto com o iOS 8, permitindo que os usuários paguem produtos físicos, como a comida, entretenimento e as associações por meio de seus dispositivos iOS. Ele está disponível no iPhone 6 e iPhone 6 Plus e também pode ser emparelhado com o Apple Watch para todas as compras na store. Quando usado em um iPhone, ele usa a ID de toque como uma maneira de confirmar e autorizar transações de cartão de débito ou de crédito de um usuário.

## <a name="requirements"></a>Requisitos

Apple Pay só está disponível no iOS 8 e superior e, portanto, requer um mínimo de Xcode 6.

Os itens a seguir também são necessários para integrar o Apple Pay em seu aplicativo:

 - Plataforma do processador de pagamento
 - Identificador do comerciante
 - Um certificado do Apple Pay
 - Qualificação de Apple Pay

Este documento abordará esses itens mais detalhadamente.

## <a name="differences-between-apple-pay-and-iap"></a>Diferenças entre o Apple Pay e IAP

A principal diferença entre o Apple Pay e *compras no aplicativo* (IAP) se refere aos produtos que eles vendem. *Físico* bens são vendidos por meio do Apple Pay; comida, a acomodação e entretenimento físico (por exemplo, tíquetes de cinema) são exemplos disso. Em contraste, IAP vende *virtual* mercadorias, como meses adicionais de assinaturas – processamento de um serviço de streaming ou vidas extras em um jogo ou conteúdo extra e premium.

As estruturas usadas também são uma diferença-chave; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) é usada para o Apple Pay, enquanto [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornece a estrutura API para IAP.

Com o Apple Pay, Apple [estados](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) que ele "[] não cobra pelo usuários, os comerciantes ou os desenvolvedores a usar o Apple Pay para pagamentos". Em comparação, IAP tem uma cobrança de 30% para cada transação. Além disso, com o Apple Pay, a transação não passa pela Apple em todos os, em vez disso, ele passa por uma plataforma de pagamento.

## <a name="using-a-payment-processor-platform"></a>Usando uma plataforma de processador de pagamento

Uma das partes fundamentais do Apple Pay é o processamento de pagamentos. Embora seja possível fazer isso sozinho, ele requer conhecimento significativo da criptografia
- como detalhado da Apple [guia de processamento de pagamento](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Plataformas de processamento de pagamento, por outro lado, manipular essas operações para você, permitindo que você se concentre na criação de seu aplicativo.

Duas opções incluem:

- **Stripe** -Inscreva-se em [Stripe.com](https://stripe.com/) para acessar suas APIs.

- **JudoPay** -Confira suas [código de exemplo do Xamarin no github](https://github.com/Judopay/Xamarin-Sample-App)e registre-se na [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Provisionamento do Apple Pay

Configurando um aplicativo para usar o Apple Pay requer uma configuração no Portal do desenvolvedor da Apple e dentro de seu aplicativo. Há uma série de etapas que devem ser seguidas para provisionar com êxito seu aplicativo para o Apple pay:

1. Crie uma ID do comerciante:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Criar uma ID do aplicativo com a capacidade de aplicar a pagar e adicionar o comerciante a ele:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Gere um certificado para a ID do comerciante:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Gere um perfil de provisionamento com a ID do aplicativo recém-criado:
    - Siga as etapas [aqui](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Adicione direitos Apple Pay:
    - Selecione os direitos de pagamento de Apple, conforme detalhado [aqui](~/ios/deploy-test/provisioning/entitlements.md), ou adicione manualmente o par chave/valor para o arquivo de [aqui](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Trabalhando com o Apple Pay

Apple fez várias melhorias para Apple Pay no iOS 10 que permitem ao usuário fazer pagamentos seguros de sites e por meio de interação com Siri e mapas.

Com o iOS 10, diversas APIs novas foram adicionadas que funcionam com o iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.

### <a name="apple-pay-website-integration"></a>Integração do site do Apple Pay

Novo para o iOS 10, o desenvolvedor pode incorporar Apple Pay diretamente em seus sites usando **ApplePay JS**. Usuários que navegam o site com o Safari no iOS ou macOS podem fazer pagamentos com o Apple Pay, validando a transação em seu iPhone ou Apple Watch. Para obter mais informações, consulte da Apple [referência de estrutura ApplePay JP](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No iOS 10, PassKit framework foi expandido para dar suporte a Apple Pay fora do `UIKit` e permitir que os emissores de cartão apresentar suas próprias placas de dentro de seus aplicativos.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Suporte a Apple Pay fora UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte a mesma funcionalidade fornecida pelo [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem o uso de UIKit. Embora essa nova API é necessária para dar suporte a Apple Pay sobre o Apple Watch (e intenções específicas também), é opcional em outras situações (por exemplo, os aplicativos existentes). No entanto, a Apple sugere mudar para a nova API assim que possível para fornecer amplo suporte a Apple Pay ao longo de todos os aplicativos do desenvolvedor com uma única base de código. Para obter mais informações sobre as intenções e Siri integração, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) documentação.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentação de cartões de emissor de dentro de aplicativos

Com o iOS 10, novos recursos foram adicionados ao PassKit framework que permitem que os emissores de cartão apresentar seus cartões de dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que será exibido um botão Apple Pay para um cartão.

O `PresentPaymentPass` método da [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe também pode ser usada para exibir o cartão de forma programática.

### <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo para o iOS 10, um aplicativo pode automaticamente dar suporte a uma nova rede de pagamento quando ele estiver disponível, sem que o desenvolvedor ter que modificar, recompilar o aplicativo e reenviá-la para a App Store.

O novo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método o `PKPaymentNetwork` classe permite que um aplicativo descobrir as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, o [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propriedade foi expandida para levar o nome do provedor de pagamento como um argumento. Usando esses métodos, um aplicativo pode automaticamente suporte a nenhuma rede com suporte para o provedor de pagamento.

Para obter mais informações, consulte nosso [configuração de pagamento de Apple](~/ios/platform/apple-pay.md) da Apple [guia de pagamento de Apple](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Novo ambiente de teste

Com o iOS 10, a Apple introduziu um novo ambiente de teste que permite ao desenvolvedor provisionar os cartões de pagamento de teste diretamente em um dispositivo iOS. Neste novo ambiente de teste, em seguida, retorna os dados de pagamento de teste criptografada para o aplicativo.

Para habilitar o novo ambiente de teste, faça o seguinte:

1. Crie um nova conta do iCloud teste no iTunes Connect.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo no.
4. Use um dos cartões de pagamento de teste do [guia de pagamento de Apple](https://developer.apple.com/apple-pay/) fazer pagamentos.

> [!IMPORTANT]
> Ao alternar as contas do iCloud, o dispositivo alternará automaticamente para o novo ambiente de teste. No entanto, a Apple ainda **requer** o aplicativo a ser testado com real cartões em um ambiente de produção antes do envio à iTunes App Store.

## <a name="summary"></a>Resumo

Neste artigo, exploramos diferentes itens necessários para usar o Apple Pay dentro de seu aplicativo. Vimos como criar uma ID do comerciante e como ele é usado dentro de **Entitlements. plist**, que precisa ser modificado manualmente.

## <a name="related-links"></a>Links relacionados

- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Introdução ao PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (amostra)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
