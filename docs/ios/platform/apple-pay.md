---
title: Apple Pay no Xamarin. iOS
description: Este guia explora a configuração do ambiente do Xamarin. iOS para uso com Apple Pay para pagar por bens físicos, como comida, entretenimento e associações por meio de seu aplicativo. Ele inclui informações sobre os identificadores, certificados e direitos necessários.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 538778d70e175afb8de0c61648f646c4a1669982
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655057"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay no Xamarin. iOS

_Este guia explora a configuração do ambiente do Xamarin. iOS para uso com Apple Pay para pagar por bens físicos, como comida, entretenimento e associações por meio de seu aplicativo. Ele inclui informações sobre os identificadores, certificados e direitos necessários._

Apple Pay foi introduzido junto com o iOS 8, permitindo que os usuários paguem por bens físicos, como comida, entretenimento e associações por meio de seus dispositivos iOS. Ele está disponível no iPhone 6 e no iPhone 6 Plus e também pode ser emparelhado com o Apple Watch para compras na loja. Quando usado em um iPhone, ele usa o Touch ID como uma maneira de confirmar e autorizar transações no cartão de crédito ou débito de um usuário.

## <a name="requirements"></a>Requisitos

Apple Pay só está disponível no iOS 8 e posterior e, portanto, requer no mínimo o Xcode 6.

Os itens a seguir também são necessários para integrar Apple Pay em seu aplicativo:

- Plataforma do processador de pagamento
- Identificador de comerciante
- Um certificado Apple Pay
- Apple Pay direito

Este documento examinará esses itens mais detalhadamente.

## <a name="differences-between-apple-pay-and-iap"></a>Diferenças entre Apple Pay e IAP da

A principal diferença entre Apple Pay e *a IAP da (compra no aplicativo* ) refere-se aos produtos que eles vendem. Bens *físicos* são vendidos por meio de Apple Pay; comida, acomodação e entretenimento físico (como cinema tickets) são exemplos disso. Por outro lado, o IAP da vende bens *virtuais* , como conteúdo premium ou extra, e assinaturas – imagine meses adicionais de um serviço de streaming ou vidas extras em um jogo.

As estruturas usadas também são uma diferença importante; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) é usado para Apple Pay, enquanto [STOREKIT](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornece a API de estrutura para IAP da.

Com o Apple Pay, a Apple [declara](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) que "[não] cobra os usuários, comerciantes ou desenvolvedores a usar Apple Pay para pagamentos". Em comparação, IAP da tem um encargo de 30% para cada transação. Além disso, com Apple Pay, a transação não passa pela Apple, em vez disso, ela passa por uma plataforma de pagamento.

## <a name="using-a-payment-processor-platform"></a>Usando uma plataforma de processador de pagamento

Uma das partes fundamentais do Apple Pay é o processamento de pagamentos. Embora seja possível fazer isso por conta própria, isso requer um conhecimento significativo da criptografia
- conforme detalhado no guia de [processamento de pagamento](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)da Apple.
As plataformas de processamento de pagamento, por outro lado, lidam com essas operações para você, permitindo que você se concentre na criação do seu aplicativo.

Duas opções incluem:

- **Distribuição** -Inscreva-se em [Stripe.com](https://stripe.com/) para acessar suas APIs.

- **JudoPay** -Confira o [código de exemplo do Xamarin no GitHub](https://github.com/Judopay/Xamarin-Sample-App)e registre-se em [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Provisionamento para Apple Pay

Configurar um aplicativo para usar Apple Pay requer a instalação no portal do desenvolvedor da Apple e no seu aplicativo. Há uma série de etapas que devem ser seguidas para provisionar com êxito seu aplicativo para o Apple Pay:

1. Criar uma ID de comerciante:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Crie uma ID de aplicativo com o recurso aplicar pagamento e adicione o comerciante a ela:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Gerar um certificado para a ID do comerciante:
    - Siga as etapas [aqui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Gerar um perfil de provisionamento com a ID do aplicativo recém-criada:
    - Siga as etapas [aqui](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Adicionar direitos de Apple Pay:
    - Selecione o direito de pagamento da Apple conforme detalhado [aqui](~/ios/deploy-test/provisioning/entitlements.md)ou adicione manualmente o par de chave/valor ao arquivo [aqui](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Trabalhando com Apple Pay

A Apple fez vários aprimoramentos para Apple Pay no iOS 10 que permitem ao usuário fazer pagamentos seguros de sites e por meio de interação com siri e mapas.

Com o iOS 10, foram adicionadas várias novas APIs que funcionam com o iOS e o watchOS para dar suporte a redes de pagamento dinâmico e um novo ambiente de teste da área restrita.

### <a name="apple-pay-website-integration"></a>Integração do site Apple Pay

Novo no iOS 10, o desenvolvedor pode incorporar Apple Pay diretamente em seus sites usando o **APPLEPAY js**. Os usuários que navegam no site com o Safari no iOS ou no macOS podem fazer pagamentos com Apple Pay validando a transação em seu iPhone ou Apple Watch. Para obter mais informações, consulte a [referência da estrutura APPLEPAY JP](https://developer.apple.com/reference/applepayjs)da Apple.

### <a name="passkit-framework-enhancements"></a>Aprimoramentos do PassKit Framework

No Ios 10, a estrutura PassKit foi expandida para dar suporte a Apple Pay `UIKit` fora do e para permitir que os emissores apresentem seus próprios cartões de dentro de seus aplicativos.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Dando suporte a Apple Pay fora do UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), um aplicativo pode dar suporte à mesma funcionalidade fornecida pelo [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sem usar UIKit. Embora essa nova API seja necessária para dar suporte a Apple Pay no Apple Watch (e em tentativas específicas também), ela é opcional em outras situações (como aplicativos existentes). No entanto, a Apple sugere a mudança para a nova API assim que possível para fornecer amplo suporte Apple Pay em todos os aplicativos do desenvolvedor com uma única base de código. Para obter mais informações sobre a integração de intenções e Siri, consulte nossa [introdução à](~/ios/platform/sirikit/index.md) documentação do SiriKit.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Apresentando os cartões do emissor de dentro dos aplicativos

Com o iOS 10, novos recursos foram adicionados à estrutura PassKit, que permitem que os emissores de cartões apresentem seus cartões de dentro de seus próprios aplicativos. O desenvolvedor pode adicionar um `PKPaymentButtonTypeInStore` UIButton à interface do usuário do aplicativo que exibirá um botão de Apple Pay para um cartão.

O `PresentPaymentPass` método da classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) também pode ser usado para exibir o cartão de forma programática.

### <a name="new-payment-network-support"></a>Novo suporte de rede de pagamento

Novo no iOS 10, um aplicativo pode dar suporte automaticamente a uma nova rede de pagamento quando ela se tornar disponível sem que o desenvolvedor precise modificar, recompilar o aplicativo e reenviá-lo para a loja de aplicativos.

O novo método [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) da `PKPaymentNetwork` classe permite que um aplicativo Descubra as redes disponíveis no dispositivo do usuário em tempo de execução. Além disso, a propriedade [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) foi expandida para pegar o nome do provedor de pagamentos como um argumento. Usando esses métodos, um aplicativo pode dar suporte automaticamente a qualquer rede à qual o provedor de pagamento dá suporte.

Para obter mais informações, consulte nossa [configuração de Apple Pay](~/ios/platform/apple-pay.md) e o [Guia de Apple Pay](https://developer.apple.com/apple-pay/)da Apple.

### <a name="new-testing-environment"></a>Novo ambiente de teste

Com o iOS 10, a Apple introduziu um novo ambiente de teste que permite ao desenvolvedor provisionar cartões de pagamento de teste diretamente em um dispositivo iOS. Esse novo ambiente de teste, em seguida, retorna dados de pagamento de teste criptografados para o aplicativo.

Para habilitar o novo ambiente de teste, faça o seguinte:

1. Crie uma nova conta do iCloud de teste no iTunes Connect.
2. Faça logon no dispositivo iOS com a nova conta de teste.
3. Defina a região desejada para testar o aplicativo.
4. Use um dos cartões de pagamento de teste do [Guia de Apple Pay](https://developer.apple.com/apple-pay/) para fazer pagamentos.

> [!IMPORTANT]
> Alternando as contas do iCloud, o dispositivo mudará automaticamente para o novo ambiente de teste. No entanto, a Apple ainda **requer** que o aplicativo seja testado com cartões reais em um ambiente de produção antes do envio para a iTunes App Store.

## <a name="summary"></a>Resumo

Neste artigo, exploramos os diferentes itens necessários para usar Apple Pay em seu aplicativo. Examinamos como criar uma ID de comerciante e como ela é usada dentro do **direitos. plist**, que precisa ser modificado manualmente.

## <a name="related-links"></a>Links relacionados

- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Introdução ao PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-emporium)
