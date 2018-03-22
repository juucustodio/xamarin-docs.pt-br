---
title: "Noções básicas de compra no aplicativo e de configuração"
ms.topic: article
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b622da2d6d9777fc200a091ccf003369b1c2e8d1
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="in-app-purchase-basics-and-configuration"></a>Noções básicas de compra no aplicativo e de configuração

Implementação de compras no aplicativo requer que o aplicativo para utilizar a API StoreKit no dispositivo. StoreKit gerencia todas as comunicações com servidores do iTunes da Apple para obter informações sobre o produto e realizar transações. O perfil de provisionamento deve ser configurado para compras no aplicativo e informações de produto devem ser inseridas no iTunes conectar.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit gerencia todas as comunicações da Apple conforme mostrado neste gráfico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Usar o armazenamento de aplicativo para fornecer a compra no aplicativo requer a instalação e configuração a seguir:

-  **conectar-se de iTunes** – configurar os produtos para vender e configurar contas de usuário da área restrita para testar a compra. Você deve também forneceu as informações de serviços bancários e imposto para a Apple para que eles podem remeter fundos coletados em seu nome.
-   **Portal de provisionamento do iOS** – criar um identificador de pacote e habilitando o acesso de loja de aplicativos para seu aplicativo.
-  **Armazenar Kit** – adicionar código ao seu aplicativo para exibir produtos, compra de produtos e restauração de transações.
-  **Código personalizado** – para rastrear as compras feitas por clientes e fornecer os produtos ou serviços adquiridos. Você também pode precisa implementar um processo do servidor para validar recebimentos se os produtos consistem em conteúdo baixado de um servidor (como manuais e problemas de revista).


Há dois Store Kit "ambientes de servidor":

-  **Produção** – transações com dinheiro real. Acessível somente por meio de aplicativos que foram enviados e aprovados pela Apple. Produtos de compra no aplicativo também devem ser examinados e aprovados antes que eles estão disponíveis no ambiente de produção.
-  **Área restrita** – onde acontece o teste. Produtos estão disponíveis aqui imediatamente após a criação (o processo de aprovação se aplica somente ao ambiente de produção). As transações em área restrita exigem (IDs de Apple não real) de usuários de teste executar transações.

## <a name="in-app-purchase-rules"></a>Regras de compra no aplicativo

Você não pode aceitar outras formas de pagamento para produtos ou serviços digitais dentro de seu aplicativo, nem Mencione-los ou consulte os usuários de dentro de um aplicativo. Isso significa que você não pode aceitar cartões de crédito ou PayPal quando compra no aplicativo é o mecanismo de pagamento mais apropriado. Há um caso especial para comprar produtos digitais fora do aplicativo, mas para usar no aplicativo, como compra livros em um site que estão associados um determinado "login" e usando "login" no aplicativo permite ao usuário acesso os registros adquiridos.
Aplicativos que operam dessa maneira não são permitidos para mencionar ou vincular a recurso externo de compra – os desenvolvedores devem se comunicar esse recurso aos usuários de outras maneiras (talvez por meio de marketing de email ou algum outro canal direto).

No entanto, desde que você não pode usar compras no aplicativo de bens físicos, nesse caso, você pode usar um mecanismo alternativo de pagamento (por exemplo. cartão de crédito, PayPal) de dentro do aplicativo.

Apple deve aprovar todos os produtos antes de ele passa em vendas – o nome, descrição e uma captura de tela de 'produto' é necessária para revisão. Tempos de revisão do produto são as mesmas para revisões de aplicativos.

Você não pode escolher qualquer preço para o seu produto – você pode selecionar apenas uma 'camada de preço' que tem um valor específico em cada país/moeda dá suporte a Apple. Você não pode ter uma camada de preços diferentes em diferentes mercados.

## <a name="configuration"></a>Configuração

Antes de gravar qualquer código de compra no aplicativo, você deve realizar algum trabalho configuração iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) e o Portal de provisionamento do iOS ( [developer.apple.com/iOS](http://developer.apple.com/iOS)).

Essas três etapas devem ser concluídas antes de gravar qualquer código:

-  **Conta de desenvolvedor da Apple** – enviar as informações de serviços bancários e de tributação para a Apple.
-  **Portal de provisionamento do iOS** – Certifique-se de que seu aplicativo tem uma ID de aplicativo válida (não um caractere curinga com um asterisco * nele) e no aplicativo de compra habilitou.
-  **Conectar o gerenciamento de aplicativos do iTunes** – adicionar produtos ao seu aplicativo.


### <a name="apple-developer-account"></a>Conta de desenvolvedor da Apple

Criar e distribuir aplicativos gratuitos exigem muito pouca configuração no [iTunes conectar](https://itunesconnect.apple.com), no entanto, para vender paga aplicativos ou compras no aplicativo exige que você fornecer informações de serviços bancários e tributação Apple. Clique em **contratos, imposto e transações bancárias** no menu principal mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Clique em contratos, imposto e transações bancárias no menu principal")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Sua conta de desenvolvedor deve ter uma **aplicativos do iOS paga** em vigor, conforme mostrado nesta captura de tela do contrato:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "Sua conta de desenvolvedor deve ter um iOS aplicativos pagos contrato em vigor")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Você não poderá testar qualquer funcionalidade StoreKit até que você tenha um **aplicativos do iOS paga** contrato – chamadas de StoreKit em seu código falharão até que ocorra Apple seu **contratos, imposto e transações bancárias** informações.

### <a name="ios-provisioning-portal"></a>Portal de provisionamento do iOS

Novos aplicativos são configurados no **IDs de aplicativo** seção o **Portal de provisionamento do iOS**. Para criar uma nova ID de aplicativo, vá para o [Central de associados do Portal de provisionamento iOS](https://developer.apple.com/membercenter/index.action), navegue até **certificados, identificadores e perfis** seção do Portal e clique em **identificadores** em *aplicativos iOS*. Em seguida, clique no "+" no canto superior direito para gerar uma nova ID de aplicativo.


O formulário para criar um novo **IDs de aplicativo**

 esta aparência:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "O formulário para criar novas IDs de aplicativo")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Digitar algo apropriado para o *descrição*, portanto, você pode identificar facilmente essa ID de aplicativo em uma lista. Para o *prefixo de ID do aplicativo*, selecione a ID da equipe.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato de sufixo da ID do pacote identificador/aplicativo

Você pode usar qualquer cadeia de caracteres que você deseja para o seu **identificador de pacote** (desde que ele é exclusivo em sua conta), no entanto, Apple recomenda que você segue o formato DNS reversa em vez de usa qualquer cadeia de caracteres arbitrária. O aplicativo de exemplo que acompanha este artigo usa com.xamarin.storekit.testing para o identificador de pacote, no entanto, ela seria igualmente válida para usar um identificador como my_store_example (embora Apple não recomendável).

> [!IMPORTANT]
> Curinga asterisco a serem adicionadas ao final da Apple também permite que um **identificador de pacote** para que uma única ID de aplicativo pode ser usada para vários aplicativos, porém _IDs de aplicativos de curinga não pode ser usado paraAppPurchase_. Um identificador de pacote de curinga pode ser com.xamarin.* de exemplo

#### <a name="enabling-app-services"></a>Habilitar serviços de aplicativos

Observe que **compras no aplicativo** será automaticamente habilitada na lista de serviços:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "Compra no aplicativo será automaticamente habilitada na lista de serviços")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfis de Provisionamento

Crie perfis de provisionamento de produção e desenvolvimento, como você normalmente faria, selecionando a ID do aplicativo que você configurou para compras no aplicativo. Consulte o [iOS aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) e [de publicação para a loja de aplicativos](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) guias para obter mais informações.

## <a name="itunes-connect"></a>conectar-se de iTunes

Clique em **meus aplicativos** na iTunes conectar-se ao criar ou editar uma entrada de aplicativo do iOS. A página de visão geral do aplicativo é mostrada aqui:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "A página de visão geral do aplicativo")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Clique em **compras no aplicativo** para criar ou editar seus produtos de venda. Esta captura de tela mostra o aplicativo de exemplo com vários produtos já adicionados:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "O aplicativo de exemplo com vários produtos já adicionado")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

O processo para adicionar novos produtos tem duas etapas:

1.   Escolha o tipo de produto: [ ![ ] (in-app-purchase-basics-and-configuration-images/image8.png "escolha o tipo de produto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Insira os atributos do produto, incluindo a identificação do produto, o tipo de preço e descrições localizadas: [ ![ ] (in-app-purchase-basics-and-configuration-images/image9.png "inserindo os atributos de produtos")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Os campos obrigatórios para cada produto de compra no aplicativo são descritos abaixo:


### <a name="reference-name"></a>Nome de Referência

O nome de referência não é exibido para os usuários; ele é para uso interno e aparece apenas na iTunes conectar.

### <a name="product-id-format"></a>Formato de ID do produto

Um identificador de produto só pode conter alfanuméricos (A-Z, a-z, 0-9), sublinhado (_) e caracteres de ponto (.). Embora você possa usar qualquer cadeia de caracteres para seus identificadores, Apple recomenda o formato inversa de DNS. Por exemplo, o aplicativo de exemplo usa esse identificador de pacote:

 `com.xamarin.storekit.testing`

Portanto, a convenção para identificar os produtos de compra no aplicativo deve ser o seguinte:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Essa convenção de nomenclatura não é imposta, simplesmente uma recomendação para ajudá-lo a gerenciar seus produtos. Além disso, apesar de seguir a mesma convenção de DNS reverso, os identificadores de produto são *não relacionadas* para o identificador de pacote e são não precisa para iniciar com a mesma cadeia de caracteres. Ele ainda será válido para usar identificadores como photo_product_greyscale (embora Apple não recomendável).

ID do produto não é exibida para os usuários, mas ele é usado para referenciar o produto no código do aplicativo.

### <a name="product-type"></a>Tipo de produto

Há cinco tipos de produto de compras no aplicativo, que você pode oferecer:

1.  **Consumo** – que é 'usado para cima', como moeda do jogo que o player pode gastar. Se o usuário não uma backup/restauração ou caso contrário, o dispositivo foi atualizado, uma transação pode ser consumida restaurada também (que efetivamente daria o player o mesmo benefício novamente). Código do aplicativo deve ser forneça 'item consumível' assim que a transação é concluída.
1.  **Não consumível** – produtos pertencentes ao usuário '' adquiridos de uma vez, como um problema de revista digital ou um nível de jogo.
1.  **Assinaturas automática renovável** – apenas como uma assinatura de revista do mundo real, no final do período de assinatura Apple automaticamente cobra o cliente novamente e estende o período de assinatura, para sempre ou até que o cliente explicitamente Cancela a ele. Esse é o método de pagamento preferencial para aplicativos de Newsstand (na verdade, aplicativos devem dar suporte a esse método de pagamento para ser aprovado para distribuição de Newsstand).
1.  **Liberar assinatura** – só podem ser oferecidos em aplicativos habilitados para Newsstand e permite que o cliente para acessar o conteúdo de assinatura em todos os seus dispositivos. Assinaturas gratuitas nunca expirarem.
1.  **Assinatura de renovação não** – deve ser usado para vender o limite de tempo de acesso ao conteúdo estático, como o acesso de um mês em um arquivo de fotos.


 *Este documento aborda atualmente apenas os primeiro tipos de produto de duas (utilizado e não consumível).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Camadas de preço

Loja de aplicativos não permitem que você escolha um preço arbitrário para seus produtos – Apple fornece níveis de preço fixo que você pode escolher. Os preços são fixas em cada moeda e Apple reserva o direito de ajustar os preços relativos (por exemplo, após uma alteração prolongada na taxa de câmbio relativo entre uma determinada moeda e o dólar americano).

Apple fornece uma matriz de preço para ajudar você a selecionar a camada correta para a moeda de preço que você deseja. Um trecho da matriz de preço (agosto de 2012) é mostrado aqui:

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Um trecho da matriz de preços de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

No momento da gravação (junho de 2013) existem 87 camadas de USD 0,99 para 999,99 USD. A matriz de preços mostra o preço que pagarão seus clientes e também o valor que você receberá da Apple – isso é menos seus custos de 30% e também quaisquer impostos locais são necessários para coletar (Observe que no exemplo que os vendedores nos EUA e Canadá recebem 70c para 99 p c roduto, enquanto os vendedores australiana recebem somente 63 c devido a ' mercadorias &amp; serviços imposto ' coletado convertido no preço de venda).

Preço do produto pode ser atualizada a qualquer momento, inclusive alterações de preço agendado que entram em vigor em uma data futura. Esta captura de tela mostra como uma alteração de preços datado de futuro é adicionada – o preço é sendo temporariamente alterado da camada 1 para camada 3 para o mês de setembro somente:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Uma alteração de preços datado de futuro em que o preço está sendo temporariamente alterado de nível 1 para camada 3 para o mês de setembro somente")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Produtos gratuitos não tem suportados

Embora Apple forneceu uma opção de assinatura gratuita especial para aplicativos de Newsstand, não é possível definir um preço zero (gratuito) para outros tipos de compras no aplicativo. Enquanto você pode editar (ie. inferior) preços para promoções de vendas, você não pode fazer compras no aplicativo 'gratuitas' por meio do Connect do iTunes.

### <a name="localization"></a>Localização

Conectar-se de iTunes, você pode inserir texto diferente do nome e uma descrição para qualquer número de idiomas com suporte. Cada idioma pode ser adicionado/editado por meio de um pop-up:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada idioma pode ser adicionado/editado por meio de um pop-up")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Quando você exibe informações sobre o produto em seu aplicativo, o texto localizado está disponível para você exibir via StoreKit. A exibição de moeda também deve ser localizada para mostrar o símbolo correto e a formatação decimal – essa formatação é abordada posteriormente neste documento.

### <a name="app-store-review"></a>Revisão da App Store

Mesmo que aplicativos – cada produto está revisado pela Apple antes de poder ir na venda. Produtos podem ser rejeitados por conteúdo inadequado no nome ou descrição ou Apple pode decidir que você selecionou o tipo de produto errada (por exemplo. Você já criou um livro ou problema revisto mas usado o tipo de produto consumível). Análises de produtos podem levar até uma revisão do aplicativo.

Na primeira vez que um aplicativo é enviado com habilitado (se é um novo aplicativo ou a funcionalidade foi adicionada a uma já existente) de compra no aplicativo você também deve escolher alguns produtos enviar com ele. O portal de conectar-se de iTunes perguntará se você deseja fazer isso, conforme mostrado nesta captura de tela:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "O portal de conectar-se de iTunes solicitará que você enviar também alguns produtos")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 O aplicativo e as compras no aplicativo serão examinadas juntos, para que todos eles tiverem aprovados ao mesmo tempo (de modo que o aplicativo não vai para o armazenamento sem qualquer produtos aprovados!).

Depois que a primeira versão com a funcionalidade de compra no aplicativo tiver sido aprovada, você pode adicionar mais produtos e enviá-los para examinar a qualquer momento. Você pode também optar por enviar uma nova versão junto com produtos de compra no aplicativo específico, usando o **detalhes de versão** página como sugere o prompt.

Consulte o [App Store Review Guidelines](https://developer.apple.com/appstore/guidelines.html) para obter mais informações.

 [Parte 2 - Visão geral do Kit de armazenamento e informações de produto ao recuperar](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
