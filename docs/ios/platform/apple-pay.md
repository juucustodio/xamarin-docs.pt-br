---
title: Apple Pay
description: Este guia explora a configuração do ambiente de xamarin para uso com o pagamento da Apple para pagar por bens físicos, como alimentos, entretenimento e membros por meio do seu aplicativo. Ele inclui informações sobre os identificadores necessários, certificados e direitos.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: fc7c247e5edcdc25d53c34c922801a5497b8c367
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="apple-pay"></a>Apple Pay

_Este guia explora a configuração do ambiente de xamarin para uso com o pagamento da Apple para pagar por bens físicos, como alimentos, entretenimento e membros por meio do seu aplicativo. Ele inclui informações sobre os identificadores necessários, certificados e direitos._


Pagamento da Apple foi introduzido junto com iOS 8, permitindo que os usuários pagar por bens físicos como alimentos, entretenimento e membros por meio de seus dispositivos iOS. Ele está disponível no iPhone 6 e iPhone 6 Plus e também pode ser emparelhado com o Apple Watch para compras na loja. Quando usado em um iPhone, ele usa o ID de toque como uma maneira de confirmar e autorizar transações de débito ou de crédito do usuário.


## <a name="requirements"></a>Requisitos

Pagamento da Apple só está disponível no iOS 8 e superior e, portanto, requer um mínimo de 6 Xcode.

Os itens a seguir também são necessários para integrar o pagamento da Apple em seu aplicativo:

 - Plataforma de processador de pagamento
 - Identificador comercial
 - Um certificado de pagamento da Apple
 - Qualificação de pagamento da Apple

Este documento examinar esses itens mais detalhadamente.

## <a name="differences-between-apple-pay-and-iap"></a>Diferenças entre o pagamento da Apple e IAP

A principal diferença entre o pagamento da Apple e *compras no aplicativo* (IAP) se refere aos produtos vendidos. *Físico* mercadorias são vendidas por meio de pagamento da Apple; alimentos, acomodação e entretenimento físico (como tíquetes de cinema) são exemplos disso. Por outro lado, IAP vende *virtual* mercadorias, como assinaturas – pense mais meses de um serviço de streaming ou extra reside em um jogo ou conteúdo adicional e premium.

As estruturas usadas também são uma diferença importante; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) é usada para pagamento da Apple, enquanto [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornece a API da estrutura IAP.

Com o pagamento da Apple, Apple [estados](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) [que ele "não] cobra usuários, desenvolvedores ou comerciantes usar pagamento da Apple para pagamentos". Em comparação, IAP tem uma taxa de 30% para cada transação. Além disso, com o pagamento da Apple, a transação não passa pelo Apple Afinal, em vez disso, ele passa por meio de uma plataforma de pagamento.


## <a name="using-a-payment-processor-platform"></a>Usando uma plataforma de processador de pagamento

Uma das partes fundamentais do pagamento da Apple é o processamento de pagamentos. Embora seja possível fazer isso sozinho, requer conhecimento significativo de criptografia
- conforme detalhado da Apple [guia de processamento do pagamento](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Plataformas de processamento de pagamento, por outro lado, tratar essas operações para você, permitindo que você se concentre em criar seu aplicativo.

Duas opções incluem:

- **Distribuição** -Inscreva-se em [Stripe.com](https://stripe.com/) para acessar suas APIs.

- **JudoPay** -check-out de seus [Xamarin código de exemplo no github](https://github.com/Judopay/Xamarin-Sample-App)e registre-se em [JudoPay.com](https://www.judopay.com/).


## <a name="provisioning-for-apple-pay"></a>O provisionamento de pagamento da Apple

Configurar um aplicativo para usar o pagamento da Apple requer a instalação no Portal do desenvolvedor Apple e dentro de seu aplicativo. Há uma série de etapas que devem ser seguidas para provisionar com êxito seu aplicativo para pagamento da Apple:

1. Crie uma ID comercial:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Crie uma ID de aplicativo com a capacidade de aplicar pagar e adicione o fornecedor a ele:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Gere um certificado para a ID da loja:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Gere um perfil de provisionamento com a ID de aplicativo criado recentemente:
    - Siga as etapas [aqui](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Adicione direitos de pagamento da Apple:
    - Selecione o direito de pagamento da Apple, conforme detalhado [aqui](~/ios/deploy-test/provisioning/entitlements.md), ou adicione manualmente o par chave/valor no arquivo de [aqui](~/ios/deploy-test/provisioning/entitlements.md)


## <a name="working-with-apple-pay"></a>Trabalhando com pagamento da Apple

Apple fez várias melhorias para pagamento da Apple iOS 10 que permitem que o usuário faça pagamentos seguros de sites e por meio da interação com Siri e mapas.

Com o iOS 10, várias novas APIs foram adicionadas que funcionam com iOS e watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste de área restrita.


### <a name="apple-pay-website-integration"></a>Integração do site de pagamento da Apple

Novo para iOS 10, o desenvolvedor pode incorporar pagamento da Apple diretamente em seus sites usando **ApplePay JS**. Os usuários que navegam o site com o Safari no iOS ou macOS podem fazer pagamentos com pagamento da Apple, validando a transação em seu iPhone ou Apple Watch. Para obter mais informações, consulte da Apple [ApplePay JP Framework referência](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No iOS 10, a estrutura de PassKit foi expandida para dar suporte a pagamento da Apple fora do `UIKit` e permitir que os emissores de cartão apresentar seus próprios cartões de dentro de seus aplicativos.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Suporte pago Apple fora UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte a mesma funcionalidade fornecida pelo [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem usar UIKit. Embora essa nova API é necessária para suportar o pagamento da Apple a Apple Watch (e em bem propósitos específicos), é opcional em outras situações (como aplicativos existentes). No entanto, a Apple sugere mover para a nova API assim que possível para fornecer amplo suporte de pagamento da Apple ao longo de todos os aplicativos do desenvolvedor com uma base de código único. Para obter mais informações sobre tentativas e Siri integração, consulte nosso [Introdução ao SiriKit](~/ios/platform/sirikit/index.md) documentação.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentando os cartões de emissor de dentro de aplicativos

Com o iOS 10, novos recursos foram adicionados para a estrutura PassKit que permitem que os emissores de cartão apresentar seus cartões de dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que será exibido um botão de pagamento da Apple para um cartão.

O `PresentPaymentPass` método o [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe também pode ser usada para exibir programaticamente o cartão.

### <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo para iOS 10, um aplicativo pode automaticamente dar suporte a uma nova rede de pagamento quando eles ficarem disponíveis sem o necessidade de modificar, recompile o aplicativo e reenviá-lo à loja de aplicativos de desenvolvedor.

O novo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método o `PKPaymentNetwork` classe permite que um aplicativo descobrir as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, o [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propriedade foi expandida para levar o nome do provedor de pagamentos como um argumento. Usando esses métodos, um aplicativo pode suportar automaticamente qualquer rede que dá suporte ao provedor de pagamentos.

Para obter mais informações, consulte nosso [Apple pagar configuração](~/ios/platform/apple-pay.md) da Apple [guia pagamento da Apple](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Novo ambiente de teste

Com o iOS 10, a Apple introduziu um novo ambiente de teste que permite que o desenvolvedor provisionar cartões de pagamento de teste diretamente em um dispositivo iOS. Esse novo ambiente de teste, em seguida, retorna dados de pagamento de teste criptografado para o aplicativo.

Para ativar o novo ambiente de teste, faça o seguinte:

1. Crie um nova conta de iCloud teste no iTunes conectar.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo no.
4. Use uma das placas de pagamento de teste do [guia pagamento da Apple](https://developer.apple.com/apple-pay/) para fazer pagamentos.

> [!IMPORTANT]
> Alternando iCloud contas, o dispositivo alternará automaticamente para o novo ambiente de teste. No entanto, ainda Apple **requer** o aplicativo a ser testado com real cartões em um ambiente de produção antes do envio à iTunes App Store.

## <a name="summary"></a>Resumo

Neste artigo, vamos explorou os itens diferentes que precisam usar o pagamento da Apple dentro de seu aplicativo. Vimos como criar uma ID de loja e como ele é usado dentro de **Entitlements.plist**, que deve ser modificado manualmente.


## <a name="related-links"></a>Links relacionados

- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Introdução ao PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (exemplo)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
