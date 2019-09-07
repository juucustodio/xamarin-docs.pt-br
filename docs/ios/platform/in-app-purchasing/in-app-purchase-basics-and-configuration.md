---
title: Noções básicas de compra no aplicativo e configuração no Xamarin. iOS
description: Este documento descreve as compras no aplicativo no Xamarin. iOS, discutindo informações relevantes sobre regras, configuração e iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: fc76a642c27b7a2c7f3483911be596f8d6cdd1ce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752867"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Noções básicas de compra no aplicativo e configuração no Xamarin. iOS

A implementação de compras no aplicativo exige que o aplicativo utilize a API StoreKit no dispositivo. O StoreKit gerencia toda a comunicação com os servidores iTunes da Apple para obter informações do produto e executar transações. O perfil de provisionamento deve ser configurado para compra no aplicativo e as informações do produto devem ser inseridas no iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "O StoreKit gerencia toda a comunicação com a Apple, conforme mostrado neste gráfico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Usar a loja de aplicativos para fornecer compras no aplicativo requer a seguinte configuração e configuração:

- **iTunes Connect** – Configurando os produtos para vender e configurar contas de usuário de área restrita para testar a compra. Você também deve ter fornecido suas informações bancárias e fiscais para a Apple para que elas possam remeter os fundos coletados em seu nome.
- **portal de provisionamento do IOS** – criando um identificador de pacote e habilitando o acesso à App Store para seu aplicativo.
- **Kit de armazenamento** – adicionando código ao seu aplicativo para exibir produtos, comprar produtos e restaurar transações.
- **Código personalizado** – para controlar as compras feitas pelos clientes e fornecer os produtos ou serviços que eles compraram. Você também pode precisar implementar um processo do lado do servidor para validar os recibos se seus produtos consistem em conteúdo baixado de um servidor (como problemas de livros e da revista).

Há dois "ambientes de servidor" do kit de armazenamento:

- **Produção** – transações com dinheiro real. Acessível somente por meio de aplicativos que foram enviados e aprovados pela Apple. Os produtos de compra no aplicativo também devem ser examinados e aprovados antes de estarem disponíveis no ambiente de produção.
- **Área restrita** – onde o teste acontece. Os produtos estão disponíveis aqui imediatamente após a criação (o processo de aprovação só se aplica ao ambiente de produção). As transações na área restrita exigem que os usuários de teste (não IDs Apple reais) executem transações.

## <a name="in-app-purchase-rules"></a>Regras de compra no aplicativo

Você não pode aceitar outras formas de pagamento para produtos digitais ou serviços dentro de seu aplicativo, nem mencionar ou referir os usuários a eles de dentro de um aplicativo. Isso significa que você não pode aceitar cartões de crédito ou PayPal quando a compra no aplicativo é o mecanismo de pagamento mais apropriado. Há um caso especial para a compra de produtos digitais fora do aplicativo, mas para uso no aplicativo, como a compra de livros em um site que estão associados a um "logon" específico e o uso desse "logon" no aplicativo permite que o usuário acesse os livros adquiridos.
Os aplicativos que operam dessa maneira não têm permissão para mencionar ou vincular ao recurso de compra externa – os desenvolvedores devem comunicar esse recurso a seus usuários de outras maneiras (talvez por email de marketing ou algum outro canal direto).

No entanto, como você não pode usar compras no aplicativo para bens físicos, nesse caso, você tem permissão para usar um mecanismo de pagamento alternativo (por exemplo, cartão de crédito, PayPal) de dentro do aplicativo.

A Apple deve aprovar todos os produtos antes de entrar na venda – o nome, a descrição e uma captura de tela do ' produto ' são necessários para análise. Os tempos de revisão do produto são os mesmos para as revisões do aplicativo.

Você não pode escolher nenhum preço para seu produto – você pode selecionar apenas um ' tipo de preço ' que tenha um valor específico em cada país/moeda ao qual a Apple dá suporte. Você não pode ter um tipo de preço diferente em diferentes mercados.

## <a name="configuration"></a>Configuração

Antes de escrever qualquer código de compra no aplicativo, você deve fazer algum trabalho de configuração no iTunes Connect ( [iTunesConnect.Apple.com](http://itunesconnect.apple.com)) e no portal de provisionamento do iOS ( [Developer.Apple.com/Ios](https://developer.apple.com/iOS)).

Essas três etapas devem ser concluídas antes de escrever qualquer código:

- **Conta de desenvolvedor da Apple** – Envie suas informações bancárias e de tributação para a Apple.
- **portal de provisionamento do IOS** – Verifique se seu aplicativo tem uma ID de aplicativo válida (não um curinga com um asterisco *) e se a compra do aplicativo está habilitada.
- **Gerenciamento de aplicativos do iTunes Connect** – adicione produtos ao seu aplicativo.

### <a name="apple-developer-account"></a>Conta de desenvolvedor da Apple

A criação e a distribuição de aplicativos gratuitos exigem muito pouca configuração no [iTunes Connect](https://itunesconnect.apple.com), no entanto, para vender aplicativos pagos ou compras no aplicativo exige que você forneça a Apple com informações bancárias e de tributação. Clique em **contratos, imposto e bancário** no menu principal mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Clique em contratos, imposto e bancário no menu principal")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Sua conta de desenvolvedor deve ter um contrato de **aplicativos pagos do IOS** em vigor, conforme mostrado nesta captura de tela:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "Sua conta de desenvolvedor deve ter um contrato de aplicativos pagos do iOS em vigor")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Você não poderá testar nenhuma funcionalidade StoreKit até ter um contrato de **aplicativos pagos do IOS** – as chamadas do StoreKit em seu código falharão até que a Apple tenha processado seus **contratos, impostos e informações bancárias** .

### <a name="ios-provisioning-portal"></a>Portal de provisionamento do iOS

Novos aplicativos são configurados na seção **IDs de aplicativo** do **portal de provisionamento do IOS**. Para criar uma nova ID de aplicativo, vá para o [centro de membros do portal de provisionamento do IOS](https://developer.apple.com/membercenter/index.action), navegue até a seção **certificados, identificadores e perfis** do portal e clique em **identificadores** em *aplicativos Ios*. Em seguida, clique no sinal "+" no canto superior direito para gerar uma nova ID de aplicativo.

O formulário para criar novas **IDs de aplicativo**

 tem a seguinte aparência:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "O formulário para criar novas IDs de aplicativo")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Insira algo apropriado para a *Descrição*, para que você possa identificar facilmente essa ID de aplicativo em uma lista. Para o *prefixo da ID do aplicativo*, selecione a ID da equipe.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Identificador de pacote/formato de sufixo de ID do aplicativo

Você pode usar qualquer cadeia de caracteres que desejar para o **identificador do pacote** (desde que seja exclusivo em sua conta), no entanto, a Apple recomenda que você siga o formato DNS reverso em vez de usar qualquer cadeia de caracteres arbitrária. O aplicativo de exemplo que acompanha este artigo usa com. xamarin. storekit. Testing para o identificador de pacote, no entanto, seria igualmente válido usar um identificador como my_store_example (mesmo que a Apple não recomende isso).

> [!IMPORTANT]
> A Apple também permite que o asterisco curinga seja adicionado ao final de um **identificador de pacote** para que uma única ID de aplicativo possa ser usada para vários aplicativos; no entanto, as _IDs de aplicativo curinga não podem ser usadas para AppPurchase_. Um exemplo de identificador de pacote curinga pode ser com. xamarin. *

#### <a name="enabling-app-services"></a>Habilitando serviços de aplicativos

Observe que a **compra no aplicativo** será habilitada automaticamente na lista de serviços:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "A compra no aplicativo será habilitada automaticamente na lista de serviços")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfis de Provisionamento

Crie perfis de provisionamento de desenvolvimento e produção como faria normalmente, selecionando a ID do aplicativo que você configurou para a compra no aplicativo. Consulte o [provisionamento do dispositivo IOS](~/ios/get-started/installation/device-provisioning/index.md) e a [publicação nos guias da loja de aplicativos](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obter mais informações.

## <a name="itunes-connect"></a>Conexão do iTunes

Clique em **meus aplicativos** no iTunes Connect para criar ou editar uma entrada de aplicativo Ios. A página Visão geral do aplicativo é mostrada aqui:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "A página Visão geral do aplicativo")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Clique em **compras no aplicativo** para criar ou editar seus produtos para venda. Esta captura de tela mostra o aplicativo de exemplo com vários produtos já adicionados:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "O aplicativo de exemplo com vários produtos já adicionados")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

O processo para adicionar novos produtos tem duas etapas:

1. Escolha o tipo de produto:  [![](in-app-purchase-basics-and-configuration-images/image8.png "Escolha o tipo de produto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. Insira os atributos do produto, incluindo a ID do produto, o tipo de preço e as descrições localizadas:  [![](in-app-purchase-basics-and-configuration-images/image9.png "Inserindo os atributos Products")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Os campos necessários para cada produto de compra no aplicativo são descritos abaixo:

### <a name="reference-name"></a>Nome de Referência

O nome de referência não é exibido para seus usuários; é para uso interno e aparece apenas no iTunes Connect.

### <a name="product-id-format"></a>Formato da ID do produto

Um identificador de produto só pode conter caracteres alfanuméricos (A-Z, a-z, 0-9), sublinhado (_) e ponto (.). Embora você possa usar qualquer cadeia de caracteres para seus identificadores, a Apple recomenda o formato DNS reverso. Por exemplo, o aplicativo de exemplo usa esse identificador de pacote:

 `com.xamarin.storekit.testing`

Portanto, a Convenção para identificar produtos de compra no aplicativo seria a seguinte:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Essa convenção de nomenclatura não é imposta, simplesmente uma recomendação para ajudá-lo a gerenciar seus produtos. Além disso, apesar de seguir a mesma convenção de DNS reverso, os identificadores de produto *não estão relacionados* ao identificador de pacote e não precisam começar com a mesma cadeia de caracteres. Ainda seria válido usar identificadores como photo_product_greyscale (embora a Apple não recomende isso).

A ID do produto não é exibida para os usuários, mas é usada para fazer referência ao produto no código do aplicativo.

### <a name="product-type"></a>Tipo de produto

Há cinco tipos de produtos de compra no aplicativo que você pode oferecer:

1. **Consumo** – coisas que são "usadas", como a moeda no jogo que o jogador pode gastar. Se o usuário faz um backup/restauração ou, caso contrário, tem seu dispositivo atualizado, uma transação consumível também não é restaurada (o que efetivamente daria ao jogador o mesmo benefício novamente). O código do aplicativo deve ter certeza de fornecer o ' item de consumo ' assim que a transação for concluída.
1. **Não consumível** – produtos que o usuário ' possui ' depois de adquirido, como um problema de revista digital ou um nível de jogo.
1. **Assinaturas renováveis automaticamente** – assim como uma assinatura da revista real, no final do período da assinatura, a Apple cobra automaticamente o cliente de novo e estende o termo da assinatura, para sempre ou até que o cliente o cancele explicitamente. Esse é o método de pagamento preferencial para aplicativos Newsstand (na verdade, os aplicativos devem dar suporte a esse método de pagamento para ser aprovado para distribuição Newsstand).
1. **Assinatura gratuita** – só pode ser oferecida em aplicativos habilitados para Newsstand e permite que o cliente acesse o conteúdo da assinatura em todos os seus dispositivos. As assinaturas gratuitas nunca expiram.
1. **Assinatura de não renovação** – deve ser usada para vender o acesso limitado ao tempo ao conteúdo estático, como o acesso de um mês a um arquivo de foto.

 *Este documento atualmente aborda apenas os dois primeiros tipos de produtos (consumíveis e não consumíveis).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Tipos de preço

A loja de aplicativos não permite que você escolha um preço arbitrário para seus produtos – a Apple fornece tipos de preço fixos que podem ser escolhidos. Os preços são fixos em cada moeda e a Apple reserva-se o direito de ajustar os preços relativos (por exemplo, após uma alteração sustentada na taxa de câmbio internacional relativa entre uma moeda específica e o dólar dos EUA).

A Apple fornece uma matriz de preços para ajudá-lo a selecionar a camada correta para a moeda/o preço desejado. Um trecho da matriz de preços (agosto de 2012) é mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Um trecho da matriz de preços de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

No momento da gravação (junho de 2013), há 87 camadas de USD 0,99 a USD 999,99. A matriz de preços mostra o preço que seus clientes pagarão e também o valor que você receberá da Apple – isso é menos de 30% de cobrança e também quaisquer impostos locais que precisam ser coletados (Observe no exemplo que os vendedores dos EUA e Canadá recebem 70C para um 99C p roduto, enquanto os vendedores australianos recebem apenas 63c devido a &amp; ' imposto sobre serviços de mercadorias ' arrecadado no preço de venda).

O preço do produto pode ser atualizado a qualquer momento, incluindo alterações de preço agendadas que entram em vigor em uma data futura. Esta captura de tela mostra como uma alteração de preço no futuro é adicionada – o preço está sendo alterado temporariamente da camada 1 para a camada 3 apenas para o mês de setembro:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Uma alteração de preço no futuro em que o preço está sendo temporariamente alterado da camada 1 para a camada 3 apenas para o mês de setembro")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Produtos gratuitos sem suporte

Embora a Apple tenha fornecido uma opção de assinatura gratuita especial para aplicativos Newsstand, não é possível definir um preço zero (gratuito) para quaisquer outros tipos de compra no aplicativo. Embora você possa editar os preços (IE. Low) para as promoções de vendas, não é possível fazer compras no aplicativo "gratuito" por meio do iTunes Connect.

### <a name="localization"></a>Localização

No iTunes Connect, você pode inserir um nome e texto de descrição diferentes para qualquer número de idiomas com suporte. Cada idioma pode ser adicionado/editado em por meio de um pop-up:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada idioma pode ser adicionado/editado por meio de um pop-up")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   

Quando você exibe informações do produto em seu aplicativo, o texto localizado está disponível para exibição por meio de StoreKit. A exibição de moeda também deve ser localizada para mostrar o símbolo correto e a formatação decimal – essa formatação será abordada posteriormente no documento.

### <a name="app-store-review"></a>Revisão da loja de aplicativos

O mesmo que aplicativos – cada produto é revisado pela Apple antes de ter permissão para entrar na venda. Os produtos podem ser rejeitados por conteúdo inadequado no nome ou na descrição ou a Apple pode decidir que você escolheu o tipo de produto errado (por exemplo, Você criou um problema de livro ou revista, mas usou o tipo de produto consumível). As revisões de produtos podem levar desde a revisão de um aplicativo.

Na primeira vez que um aplicativo é enviado com a compra no aplicativo habilitada (seja um novo aplicativo ou a funcionalidade foi adicionada a um existente), você também deve escolher alguns produtos para enviar com ele. O portal do iTunes Connect solicitará que você faça isso, conforme mostrado nesta captura de tela:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "O portal do iTunes Connect também solicitará que você envie alguns produtos")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   

O aplicativo e as compras no aplicativo serão analisados juntos, para que todos sejam aprovados de uma vez (para que o aplicativo não vá para a loja sem nenhum produto aprovado!).

Depois que sua primeira versão com a funcionalidade de compra no aplicativo tiver sido aprovada, você poderá adicionar outros produtos e enviá-los para revisão a qualquer momento. Você também pode optar por enviar uma nova versão junto com produtos de compra no aplicativo específicos, usando a página **detalhes da versão** , como sugere o prompt.

Consulte as [diretrizes de revisão da App Store](https://developer.apple.com/appstore/guidelines.html) para obter mais informações.

 [Parte 2-Visão geral do kit de loja e recuperando informações do produto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
