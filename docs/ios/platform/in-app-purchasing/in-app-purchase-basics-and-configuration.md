---
title: Noções básicas de compra no aplicativo e a configuração no xamarin. IOS
description: Este documento descreve as compras no aplicativo no xamarin. IOS, discutindo as informações relevantes sobre as regras, a configuração e o iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 267dac5b6aec263f1d8b69d81f34f732118c1802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61406762"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Noções básicas de compra no aplicativo e a configuração no xamarin. IOS

Implementando a compras no aplicativo requer que o aplicativo para utilizar a API StoreKit no dispositivo. StoreKit gerencia toda a comunicação com servidores do iTunes da Apple para obter informações sobre o produto e realizar transações. O perfil de provisionamento deve ser configurado para compras no aplicativo e informações sobre o produto deve ser inserida no iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit gerencia toda a comunicação com da Apple, conforme mostrado neste gráfico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Usando o aplicativo Store para fornecer a compras no aplicativo requer a instalação e configuração a seguir:

-  **o iTunes Connect** – para vender os produtos e configuração de contas de usuário de área restrita para testar a compra. Você deve também forneceu suas informações de serviços bancários e imposto para a Apple para que eles podem remeter fundos coletados em seu nome.
-   **Portal de provisionamento iOS** – criando um identificador de pacote e habilitando o acesso da App Store para seu aplicativo.
-  **Store Kit** – adicionar código ao seu aplicativo para exibindo produtos, a compra de produtos e a restauração de transações.
-  **Código personalizado** : para acompanhar as compras feitas por clientes e fornecer produtos ou serviços adquiridos. Você também pode precisar implementar um processo de servidor para validar recebimentos se seus produtos consistem em conteúdo baixado de um servidor (por exemplo, livros e revistos problemas).


Há dois Store Kit "ambientes de servidor":

-  **Produção** – transações com dinheiro real. Acessível somente por meio de aplicativos que foram enviados e aprovados pela Apple. Produtos de compra no aplicativo também devem ser revisados e aprovados antes que eles estão disponíveis no ambiente de produção.
-  **Área restrita** – onde acontece o teste. Produtos estão disponíveis aqui imediatamente após a criação (o processo de aprovação se aplica somente ao ambiente de produção). Transações na área restrita exigem que usuários de teste (IDs de Apple não reais) executar transações.

## <a name="in-app-purchase-rules"></a>Regras de compra no aplicativo

Você não pode aceitar outras formas de pagamento para produtos ou serviços digitais dentro de seu aplicativo, nem mencioná-las ou encaminhe os seus usuários a eles de dentro de um aplicativo. Isso significa que você não pode aceitar cartões de crédito ou do PayPal, quando compras no aplicativo é o mecanismo mais apropriado de pagamento. Há um caso especial para a compra de produtos digitais fora do aplicativo, mas para usar no aplicativo, tais como compra livros em um site que estão associados um determinado "login" e usar o que "logon" no aplicativo permite o acesso do usuário os livros comprados.
Aplicativos que funcionam dessa forma não são permitidos para mencionar ou vincular a recurso de compra externo – os desenvolvedores devem se comunicar essa funcionalidade aos seus usuários de outras maneiras (talvez por meio de marketing de email ou algum outro canal direto).

No entanto, uma vez que você não pode usar compras no aplicativo produtos físicos, nesse caso, você pode usar um mecanismo alternativo de pagamento (por exemplo. cartão de crédito, PayPal) de dentro do aplicativo.

Apple deverá aprovar cada produto antes de ele ficar em vendas – o nome, descrição e uma captura de tela do produto é necessária para revisão. Tempos de revisão do produto são da mesma maneira que as revisões de aplicativo.

Você não pode escolher qualquer preço para o seu produto – você pode selecionar apenas uma 'camada de preço' que tem um valor específico em cada país/moeda que dá suporte a Apple. Você não pode ter uma camada de preço diferente em diferentes mercados.

## <a name="configuration"></a>Configuração

Antes de escrever qualquer código de compra no aplicativo, você deve fazer algum trabalho de configuração no iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) e o Portal de provisionamento do iOS ( [developer.apple.com/iOS](https://developer.apple.com/iOS)).

Essas três etapas devem ser concluídas antes de escrever qualquer código:

-  **Conta de desenvolvedor da Apple** – enviar suas informações de serviços bancários e tributação para a Apple.
-  **Portal de provisionamento iOS** – Verifique se seu aplicativo tem uma ID de aplicativo válida (não um caractere curinga com um asterisco * nele) e no aplicativo de compra habilitou.
-  **o iTunes Connect gerenciamento de aplicativos** – adicionar produtos ao seu aplicativo.


### <a name="apple-developer-account"></a>Conta de desenvolvedor da Apple

Criar e distribuir aplicativos gratuitos requer muito pouca configuração no [iTunes Connect](https://itunesconnect.apple.com), no entanto, para vender paga compras no aplicativo ou aplicativos exige que você fornecer Apple informações bancárias e tributação. Clique em **contratos, impostos e transações bancárias** no menu principal, mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Clique em contratos, impostos e bancárias no menu principal")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Sua conta de desenvolvedor deve ter uma **aplicativos do iOS pago** contrato em vigor, conforme mostrado nesta captura de tela:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "Sua conta de desenvolvedor deve ter um contrato em vigor de aplicativos pagos do iOS")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Não será capaz de testar qualquer funcionalidade StoreKit até que você tenha um **aplicativos do iOS pago** contrato – chamadas de StoreKit no seu código falharão até que ocorra Apple seu **contratos, impostos e transações bancárias** informações.

### <a name="ios-provisioning-portal"></a>Portal de provisionamento do iOS

Novos aplicativos são definidos na **IDs do aplicativo** seção o **Portal de provisionamento iOS**. Para criar uma nova ID do aplicativo, vá para o [Central de associados do Portal de provisionamento do iOS](https://developer.apple.com/membercenter/index.action), navegue até **certificados, identificadores e perfis** seção do Portal e clique em **identificadores** sob *aplicativos do iOS*. Em seguida, clique em "+" no canto superior direito para gerar uma nova ID do aplicativo.


O formulário para criação de novos **IDs do aplicativo**

 esta aparência:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "O formulário para criar novas IDs de aplicativo")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Insira algo apropriado para o *descrição*, portanto, você pode identificar facilmente essa ID do aplicativo em uma lista. Para o *prefixo de ID do aplicativo*, selecione a ID de equipe.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato de sufixo da ID do pacote identificador/aplicativo

Você pode usar qualquer cadeia de caracteres que você deseja para o seu **identificador de pacote** (desde que ele é exclusivo em sua conta), no entanto, Apple recomenda que você segue o formato de DNS reverso em vez de usa qualquer cadeia de caracteres arbitrária. O aplicativo de exemplo que acompanha este artigo usa com.xamarin.storekit.testing para o identificador de pacote, no entanto, seria igualmente válida para usar um identificador como my_store_example (embora Apple não seja recomendável).

> [!IMPORTANT]
> Asterisco curinga a ser adicionado ao final da Apple também permite que um **identificador de pacote** para que uma única ID de aplicativo pode ser usada para vários aplicativos, porém _IDs do aplicativo curinga não pode ser usadas paraAppPurchase_. Um exemplo de identificador do pacote curinga pode ser com.xamarin.*

#### <a name="enabling-app-services"></a>Habilitando serviços de aplicativos

Observe que **compra no aplicativo** será habilitado automaticamente na lista de serviços:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "Compra no aplicativo será habilitada automaticamente na lista de serviços")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfis de Provisionamento

Crie perfis de provisionamento de produção e de desenvolvimento como normalmente faria, selecionando a ID do aplicativo que você configurou para compras no aplicativo. Consulte a [provisionamento de dispositivos do iOS](~/ios/get-started/installation/device-provisioning/index.md) e [publicando o aplicativo Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) guias para obter mais informações.

## <a name="itunes-connect"></a>iTunes Connect

Clique em **meus aplicativos** no iTunes Connect para criar ou editar uma entrada de aplicativo do iOS. A página de visão geral do aplicativo é mostrada aqui:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "A página de visão geral do aplicativo")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Clique em **compras no aplicativo** para criar ou editar seus produtos para venda. Esta captura de tela mostra o aplicativo de exemplo com vários produtos já adicionados:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "O aplicativo de exemplo com vários produtos já adicionado")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

O processo para adicionar novos produtos tem duas etapas:

1.   Escolha o tipo de produto: [![](in-app-purchase-basics-and-configuration-images/image8.png "Escolha o tipo de produto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Insira os atributos do produto, incluindo a Id do produto, tipo de preço e descrições localizadas: [![](in-app-purchase-basics-and-configuration-images/image9.png "Inserir os atributos de produtos")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Os campos obrigatórios para cada produto de compra no aplicativo são descritos abaixo:


### <a name="reference-name"></a>Nome de Referência

O nome de referência não é exibido aos usuários; ele é para uso interno e só é exibido no iTunes Connect.

### <a name="product-id-format"></a>Formato de ID do produto

Um identificador de produto pode conter somente alfanuméricos (A-Z, a-z, 0-9), sublinhado (_) e caracteres de ponto (.). Embora você possa usar qualquer cadeia de caracteres para seus identificadores, a Apple recomenda o formato de DNS reverso. Por exemplo, o aplicativo de exemplo usa esse identificador de pacote:

 `com.xamarin.storekit.testing`

Portanto, a convenção para identificar os produtos de compra no aplicativo seria da seguinte maneira:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Esta convenção de nomenclatura não é imposta, simplesmente uma recomendação para ajudá-lo a gerenciar seus produtos. Além disso, apesar de seguir a mesma convenção de DNS reverso, os identificadores de produto estão *não relacionadas* para o identificador de pacote e são não necessários para iniciar com a mesma cadeia de caracteres. Ela ainda seria válida para usar identificadores como photo_product_greyscale (embora Apple não seja recomendável).

ID do produto não é exibido aos usuários, mas ele é usado para referenciar o produto no código do aplicativo.

### <a name="product-type"></a>Tipo de produto

Há cinco tipos de produto de compra no aplicativo, que você pode oferecer:

1.  **Consumível** – coisas que estão 'utilizado backup', como moeda do jogo que o player pode gastar. Se o usuário faz uma backup/restauração ou caso contrário, o dispositivo foi atualizado, uma transação consumível restaurada também (que efetivamente daria o player o mesmo benefício novamente). O código do aplicativo deve ser-se de fornecer 'item consumível' assim que a transação seja concluída.
1.  **Não consumível** – produtos que o usuário 'possui' uma vez adquiridos, como um problema de revista digital ou um nível de jogo.
1.  **Pode ser renovado automaticamente assinaturas** – apenas como uma assinatura de revista do mundo real, no final do período da assinatura Apple automaticamente cobra o cliente novamente e estende o período de assinatura, indefinidamente ou até que o cliente explicitamente Cancela a ele. Esse é o método de pagamento preferencial para aplicativos de Newsstand (na verdade, aplicativos devem dar suporte a esse método de pagamento para ser aprovado para distribuição de Newsstand).
1.  **Assinatura gratuita** – só podem ser oferecidos em aplicativos habilitados para Newsstand e permite que o cliente para acessar o conteúdo de assinatura em todos os seus dispositivos. Assinaturas gratuitas nunca expirarem.
1.  **Assinatura de renovação de não** – deve ser usado para vender o acesso de tempo limitado ao conteúdo estático, como o acesso de um mês para um arquivo de fotos.


 *Atualmente, este documento aborda apenas os primeiros tipos de produto de dois (utilizado e não consumível).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Camadas de preço

O aplicativo Store não permitem que você escolha um preço arbitrário para seus produtos – a Apple fornece camadas de preço fixo que você pode escolher. Os preços são fixos em cada moeda e Apple reserva o direito de ajustar os preços relativos (por exemplo, após uma alteração prolongada na taxa de câmbio relativo entre uma determinada moeda e o dólar americano).

A Apple fornece uma matriz de preço para ajudar você a selecionar a camada correta para a moeda/preço que você deseja. Um trecho da matriz de preço (agosto de 2012) é mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Um trecho da matriz de preço de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

No momento da gravação (junho de 2013) há 87 camadas de USD 0,99 para US $ 999,99. A matriz de preços mostra o preço que seus clientes pagarão e também a quantidade que você receberá da Apple – isso é menos seus custos de 30% e também quaisquer impostos locais são necessários para coletar (Observe que no exemplo que vendedores dos EUA e Canadá recebam 70c para 99 p c roduto, enquanto os vendedores australianos recebem somente 63 c devido a ' mercadorias &amp; imposto de serviços ' coletado convertido no preço de venda).

Preços de seu produto podem ser atualizado a qualquer momento, incluindo alterações de preço agendado que entram em vigor em uma data futura. Esta captura de tela mostra como uma alteração de preço datado de futuro é adicionada – o preço está sendo temporariamente alterado da camada 1 para a camada 3 para o mês de setembro só:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Uma alteração de preço datado de futuro em que o preço está sendo temporariamente alterado da camada 1 para a camada 3 para o mês de setembro somente")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Não tem suportados de produtos gratuitos

Embora Apple forneceu uma opção especial de assinatura gratuita para aplicativos de Newsstand, não é possível definir um preço zero (gratuito) para outros tipos de compra no aplicativo. Embora você possa editar (ie. inferior) os preços para promoções de vendas, você não pode fazer compras no aplicativo gratuitas por meio do iTunes Connect.

### <a name="localization"></a>Localização

No iTunes Connect, você pode inserir texto diferente do nome e uma descrição para qualquer número de idiomas com suporte. Cada linguagem pode ser adicionado/editados por meio de um pop-up:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada linguagem pode ser adicionado/editados por meio de um pop-up")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Quando você exibe informações sobre o produto em seu aplicativo, o texto localizado está disponível para a exibição por meio de StoreKit. A exibição de moeda também deve ser localizada para mostrar o símbolo correto e a formatação de decimais – essa formatação é abordada posteriormente neste documento.

### <a name="app-store-review"></a>Revisão da App Store

Mesmo que os aplicativos – cada produto é examinado pela Apple antes de terem permissão para ir na venda. Produtos podem ser rejeitados por conteúdo inadequado no nome ou descrição ou Apple pode decidir que escolheu (por exemplo, o tipo de produto errada Você já criou um livro ou um problema de revista, mas usado o tipo de produto de consumo). Análises de produtos podem levar até uma revisão de aplicativo.

Na primeira vez que um aplicativo é enviado com compras no aplicativo habilitado (se ele é um novo aplicativo ou a funcionalidade foi adicionada a um existente) você também deve escolher alguns produtos para enviar com ele. O portal do iTunes Connect solicitará que você fizer isso, conforme mostrado nesta captura de tela:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "O portal do iTunes Connect solicitará que você enviar também alguns produtos")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 O aplicativo e as compras no aplicativo serão analisadas juntos, para que todos eles tiverem aprovados ao mesmo tempo (de modo que o aplicativo não vá para o repositório sem quaisquer produtos aprovados!).

Depois que a primeira versão com o recurso de compra no aplicativo tiver sido aprovada, você pode adicionar mais produtos e enviá-los para revisão em qualquer momento. Você pode também optar por enviar uma nova versão juntamente com produtos as compras no aplicativo específico, usando o **detalhes da versão** página como sugere o prompt.

Consulte a [diretrizes de revisão da App Store](https://developer.apple.com/appstore/guidelines.html) para obter mais informações.

 [Parte 2 – Visão geral do Store Kit e informações sobre o produto de recuperar as configurações](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
