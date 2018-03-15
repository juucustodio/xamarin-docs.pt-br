---
title: Configurando um aplicativo no iTunes Connect
description: "Este artigo aborda as etapas necessárias para configurar e manter um aplicativo Xamarin.iOS no iTunes Connect para que ele possa ser liberado para distribuição na App Store."
ms.topic: article
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 2552538c4d586e9471c42aaf7d696955a0908d1b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="configuring-an-app-in-itunes-connect"></a>Configurando um aplicativo no iTunes Connect

_Este artigo aborda as etapas necessárias para configurar e manter um aplicativo Xamarin.iOS no iTunes Connect para que ele possa ser liberado para distribuição na App Store._

O iTunes Connect é um pacote de ferramentas baseadas na Web para gerenciamento dos aplicativos iOS na App Store, entre outras coisas. Um aplicativo Xamarin.iOS precisará ser corretamente definido e configurado no iTunes Connect antes de ser enviado à Apple para análise e, por fim, ser lançado para venda ou lançado como um aplicativo gratuito na App Store.

O iTunes Connect pode ser usado para o seguinte:

- Definir o nome do aplicativo (como exibido na App Store).
- Fornecer capturas de tela ou vídeo do aplicativo em ação nos dispositivos iOS aos quais ele dá suporte.
- Fornecer uma descrição clara e sucinta do aplicativo, incluindo suas funcionalidades e benefícios para o usuário final.
- Fornecer categorias e subcategorias para ajudar o usuário a localizar o aplicativo na App Store.
- Fornecer qualquer palavra-chave que possa ajudar ainda mais um usuário a encontrar o aplicativo.
- Fornecer URLs de contato e suporte para o seu site (exigido pela Apple).
- Definir a Classificação do aplicativo, que é usada para informar os controles dos pais na App Store.
- Selecionar um preço de venda ou especificar que o aplicativo será liberado gratuitamente.
- Configurar tecnologias opcionais da App Store como Game Center e Compra no Aplicativo.

Além disso, o aplicativo também deve ter arte atraente e em alta resolução disponível caso a Apple decida disponibilizá-lo na App Store. Para obter mais informações, consulte o [Guia do desenvolvedor do iTunes Connect](https://developer.apple.com/support/itunes-connect/) da Apple.

## <a name="managing-agreements-tax-and-banking"></a>Gerenciamento de contratos, impostos e transações bancárias

A seção **Agreements, Tax, and Banking (Contratos, imposto e transações bancárias)** do iTunes Connect é usada para fornecer informações financeiras necessárias relacionadas a pagamentos de desenvolvedor e retenções de imposto do iTunes, além de acompanhar o status de contratos em vigor com a Apple. Antes que você possa liberar um aplicativo iOS na App Store (gratuitamente ou para venda), você precisará ter os contratos adequados em vigor e ter concordado com eventuais modificações nos contratos existentes.

[![](itunesconnect-images/agreement01.png "Gerenciando contratos, tributos e transações bancárias")](itunesconnect-images/agreement01.png#lightbox)

Deste ponto em diante, você pode:

- Fornecer um **Team Agent (Agente de Equipe)** e definir outras funções de usuário para sua conta do iTunes Connect, por exemplo, **Admin (Administrador)** ou **Finance (Finanças)**.
- Registrar-se em **Contracts (Contratos)** que permitam que uma organização distribua aplicativos na App Store e manter esses contratos.
- Especificar o nome da **Legal Entity (Pessoa Jurídica)** (nome do vendedor na App Store) usado para fazer a correspondência de informações tributárias, contratos e informações fiscais associadas à sua organização.
- Fornecer informações **Banking (Tributárias)** e **Tax (Fiscais)**, caso você pretenda vender aplicativos por meio da App Store.

Novamente, essas informações _devem_ estar configuradas corretamente, in-loco e atualizadas antes que um aplicativo iOS possa ser enviado ao iTunes Connect para análise e liberação. Para obter mais informações, consulte a documentação de [Gerenciamento de contratos, impostos e transações bancárias](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1) da Apple.

<a name="creating" />

## <a name="creating-an-itunes-connect-record"></a>Criando um registro do iTunes Connect

Antes que um aplicativo Xamarin.iOS possa ser carregado no iTunes Connect para distribuição por meio da App Store, você precisará criar um registro para o aplicativo no iTunes Connect. Esse registro inclui todas as informações sobre o aplicativo do modo como ele aparecerá na App Store (em tantos idiomas quantos forem necessários) e todas as informações necessárias para gerenciar o aplicativo por meio do processo de distribuição. Além disso, ele será usado para configurar tecnologias de App Store como Game Center ou iAd App Network.

Para adicionar um aplicativo iOS ao iTunes Connect, é necessário ser o **Team Agent (Agente de Equipe)** ou um usuário com uma função de **Admin (Administrador)** ou **Technical (Técnico)**.

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **Meus Aplicativos**:

    [![](itunesconnect-images/add01.png "Clicar em Meus Aplicativos")](itunesconnect-images/add01.png#lightbox)
2. Clique em **+** no canto superior esquerdo e selecione **Novo Aplicativo iOS**:

    [![](itunesconnect-images/add02.png "Adicionando um Novo aplicativo iOS")](itunesconnect-images/add02.png#lightbox)
3. O iTunes Connect exibirá a caixa de diálogo **Novo Aplicativo iOS**:

    [![](itunesconnect-images/add03.png "A caixa de diálogo Novo Aplicativo iOS")](itunesconnect-images/add03.png#lightbox)
4. Insira um **Name (Nome)** e o **Version Number (Número de Versão)** para o aplicativo como eles devem ser exibidos na App Store.
5. Selecione o **Primary Language (Idioma primário)**.
6. Insira um número de **SKU**, que é um identificador constante e exclusivo que será usado para rastrear o aplicativo. Ele não será exibido para o usuário final e _não poderá_ ser alterado depois que o aplicativo for criado.
7. Selecione a **Bundle ID (ID do pacote)** para o aplicativo que você criou no centro de desenvolvedores ao provisionar o aplicativo. Será necessário usar essa mesma ID do pacote ao assinar o pacote de distribuição no Visual Studio para Mac ou Visual Studio. Para obter mais informações, veja nossa documentação sobre [Criação de um perfil de distribuição](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) e [Seleção de um perfil de distribuição em um projeto Xamarin.iOS](~/ios/get-started/installation/device-provisioning/index.md).
8. Clique no botão **Create (Criar)** para compilar o novo Registro do iTunes Connect para o aplicativo.

O novo aplicativo será criado no iTunes Connect e estará pronto para que você o preencha com as informações necessárias como a Descrição, Preço, Categorias, Classificações, etc.:

[![](itunesconnect-images/add04.png "O novo aplicativo será criado no iTunes Connect")](itunesconnect-images/add04.png#lightbox)

<a name="managing" />

## <a name="managing-app-videos-and-screenshots"></a>Gerenciamento de vídeos e capturas de tela de aplicativo

Um dos elementos mais importantes para uma colocação no mercado bem-sucedida de seu aplicativo iOS na App Store é um ótimo conjunto de capturas de tela e, opcionalmente, visualizações de vídeo. Use as exibições reais do seu aplicativo em execução que realcem a interação do usuário e demonstrem suas funcionalidades exclusivas. Use vídeos de visualização do aplicativo para dar aos usuários uma ideia de como é utilizá-lo.

A Apple sugere o seguinte ao fazer capturas de tela:

- Otimize a captura de tela para melhor apresentação nos dispositivos iOS com suporte pelo seu aplicativo e verifique se o conteúdo está legível.
- Não enquadre a captura de tela em uma imagem de dispositivo iOS.
- Mostre a exibição real do seu aplicativo, usando a tela inteira, sem elementos gráficos ou bordas em torno de captura de tela.
- Sempre remova a barra de status de capturas de tela, o iTunes Connect espera capturas de tela com dimensões que excluem nessa área.
- Quando possível, faça capturas de tela em hardware de iOS Retina real e de alta resolução (e não no Simulador iOS).
- A primeira captura de tela aparecerá como um resultado da pesquisa na App Store no iPhone e iPad se nenhum vídeo de visualização do aplicativo estiver disponível, então coloque a melhor captura de tela primeiro.
- Use todas as cinco capturas de tela para contar a história do aplicativo, realçando momentos que o tornam atraente.

A Apple requer capturas de tela e vídeos fornecidos em cada tamanho de tela e resolução para os quais seu aplicativo dá suporte. Além disso, as versões de retrato e paisagem devem ser fornecidas com base nas orientações para as quais há suporte.

Os seguintes tamanhos de tela e resoluções são necessários no momento:

[!include[](~/ios/includes/table-itunesconnect.html)]

### <a name="editing-screenshots-in-itunes-connect"></a>Edição de capturas de tela no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Versions (Versões)**.
4. Role até a seção **Screenshots (Capturas de tela)**.
5. Selecione o **Tamanho da imagem** e arraste as imagens necessárias (até 5 por tamanho da tela):

    [![](itunesconnect-images/screenshot01.png "Selecionar o Tamanho da Imagem e arrastar as imagens necessárias")](itunesconnect-images/screenshot01.png#lightbox)
6. Repita para todos os tamanhos de tela necessários.
7. Clique no botão **Save (Salvar)** na parte superior da tela para salvar suas alterações.

> [!NOTE]
> Observação: a Apple rejeitará o envio se as capturas de tela ou o vídeo de visualização do aplicativo não corresponderem à funcionalidade atual em seu aplicativo.

<a name="metadata" />

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>Gerenciamento de nome, descrição, novidades, URLs e palavras-chave

Esta seção do Registro de Aplicativo do iTunes Connect fornece informações localizadas sobre o aplicativo, o que ele faz, as modificações a novas versões, palavras-chave usadas na pesquisa e em suporte iAd e as URLs de suporte.

### <a name="app-name"></a>Nome do Aplicativo

Escolha um nome do aplicativo descritivo, que reflita o que seu aplicativo faz. Tente mantê-lo tão breve e conciso quanto possível. O nome do seu aplicativo desempenha um papel fundamental em como os usuários pesquisam por ele e o descobrem, então mantenha o nome simples e fácil de lembrar. Preste atenção especial a como o nome aparece quando exibido em um dispositivo iOS (iPad, iPhone e iPod touch).

A Apple sugere as diretrizes a seguir ao escolher o nome de um aplicativo:

- Mantenha-o curto, simples e fácil de lembrar.
- Verifique se ele não viola os direitos autorais ou marcas comerciais de terceiros.
- Faça-o corresponder à funcionalidade do aplicativo.
- Forneça nomes localizados para mercados estrangeiros quando apropriado.

### <a name="description"></a>Descrição

Escreva uma descrição clara, concisa e informativa do aplicativo e seus recursos. As primeiras linhas são os mais importantes e lhe dão a oportunidade de fazer uma primeira impressão excelente e atrair o usuário. Descreva o que torna o seu aplicativo especial e o distingue de outros aplicativos semelhantes.

Apple sugere o seguinte para escrever a descrição de um aplicativo:

- Inclua um ou dois breves parágrafos de abertura e uma lista com marcadores, curta, dos principais recursos.
- Forneça descrições localizadas para mercados estrangeiros quando apropriado.
- Se houver análises de usuários, premiações ou depoimentos de clientes, inclua-os apenas no final.
- Use quebras de linha e marcadores para melhorar a legibilidade.
- Esteja ciente de como a descrição do aplicativo é exibida na loja de aplicativos em cada tipo de dispositivo, para certificar-se de que as frases mais importantes em sua descrição estão prontamente visíveis.

### <a name="whats-new"></a>O Que Há de Novo

Ao carregar uma nova versão do seu aplicativo, ficará disponível um campo **Novidades nesta versão** que deve ser preenchido por completo e cuidadosamente.

A Apple sugere as seguintes diretrizes ao preencher as informações em Novidades:

- Adicione um sistema de mensagens para encorajar os usuários a atualizar.
- Liste itens em ordem de importância e identifique alterações e correções de bug.
- Apresente as alterações em linguagem simples e autêntica em vez de jargões técnicos.

### <a name="keywords"></a>Palavras-chave

Palavras-chave estratégicas e ponderadas referentes à funcionalidade do seu aplicativo ajudam os usuários a localizar seu aplicativo facilmente ao pesquisar na App Store. Além disso, se seu aplicativo serve anúncios do iAd, o aplicativo de rede do iAd usa as palavras-chave ao escolher os anúncios a usar como destino em seu aplicativo.

A Apple sugere o seguinte ao escolher palavras-chave:

- Não use nomes de aplicativos, nomes de empresa nem produtos concorrentes, tampouco nomes de marca registrada.
- Não use palavras ou termos genéricos.
- Evite termos indesejáveis ou inadequados ou palavras irrelevantes como nomes de celebridades.
- Localize palavras-chave para mercados estrangeiros quando apropriado.

### <a name="urls"></a>URLs

A Apple requer que o desenvolvedor forneça um link para seu site para dar suporte a qualquer problema ou perguntas que um usuário possa ter sobre o aplicativo. Ela também exige um link para a política de privacidade do aplicativo (que, novamente, deve estar hospedado no seu site).

Opcionalmente, você pode fornecer um link para informações de marketing hospedadas no seu site que podem ser usadas para prover mais informações sobre o aplicativo que é fornecido na App Store.

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>Editando Nome, Descrição, Novidades, Palavras-Chave e URLs no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Versions (Versões)**.
4. Role até a seção **Name (Nome)**.
5. Preencha todas as informações necessárias:

    [![](itunesconnect-images/name01.png "Editando Nome, Descrição, Novidades, Palavras-Chave e URLs no iTunes Connect")](itunesconnect-images/name01.png#lightbox)
6. Clique no botão **Save (Salvar)** na parte superior da tela para salvar suas alterações.

> [!IMPORTANT]
> Observação: a Apple rejeitará o envio se o Nome, Descrição, Novidades, URLs e Palavras-chave não corresponderem à funcionalidade atual em seu aplicativo.

<a name="general" />

## <a name="maintaining-general-app-information"></a>Manutenção de informações gerais do aplicativo

Esta seção do registro de aplicativo do iTunes Connect fornece a ID exclusiva do aplicativo (como atribuído pelo Apple), as categorias às quais o aplicativo pertence, classificação, direitos autorais e informações sobre a empresa lançando o aplicativo.

### <a name="app-icon"></a>Ícone do aplicativo

> [!IMPORTANT]
>  **Observação**: os Ícones de Aplicativos não são mais enviados pelo iTunes Connect. Eles devem ser enviados pela imagem **AppIcon** definida no arquivo **Assets.xcassets** do seu projeto. Para obter mais informações, confira o guia [Ícone da App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md).

O ícone do aplicativo é a aparência do seu aplicativo para os usuários, portanto, ele deve ser fácil de lembrar e ter boa aparência em um tamanho pequeno. Ícones memoráveis são limpos, simples e imediatamente reconhecíveis.

A Apple sugere as seguintes diretrizes ao criar o ícone do seu aplicativo:

- Torne o ícone apropriado para seu aplicativo.
- Crie um ícone simples, consistente com o design do seu aplicativo.
- Evite usar palavras em seu ícone.
- Pense globalmente: um único ícone do aplicativo é usado em todos os territórios de repositório.

Uma imagem de 1.024 x 1.024 pixels é necessária para o ícone do aplicativo que será exibido na App Store.

Para obter mais informações, consulte as [Diretrizes da interface humana do iOS](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556) da Apple e a descrição da seção de ícone grande do aplicativo na documentação de [Informações gerais do aplicativo](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7)

### <a name="app-id"></a>ID do aplicativo

Este é um número de identificação exclusivo atribuído ao seu aplicativo pela Apple quando o registro do iTunes Connect é criado. Você pode usar esse número ao chamar várias interfaces com base na Web que a Apple oferece, inclusive informações da App Store no seu site.

### <a name="version-number"></a>Número de versão

Essa é a versão atual e ativa do seu aplicativo, conforme exibido para o usuário na App Store.

### <a name="category-and-sub-category"></a>Categoria e subcategoria

Um aspecto importante da detectabilidade de seu aplicativo é a categoria que consta na App Store. As categorias permitem que usuários navegar em um conjunto de aplicativos e localizar aqueles em que estão interessados. O iTunes Connect permite que você atribua até duas categorias diferentes ao definir seu aplicativo. Certifique-se de escolher cuidadosamente as categorias que melhor descrevem a função principal do seu aplicativo.

### <a name="ratings"></a>Classificações

Todos os aplicativos precisam ter uma classificação na App Store. Essa classificação é usada para informar o controle dos pais e limitar o acesso aos filhos com base no tipo e conteúdo dos aplicativos. Ao definir seu aplicativo, o iTunes Connect fornece uma lista de descrições de conteúdo na qual você identifica a frequência com que o conteúdo é exibido em seu aplicativo. Essas seleções são convertidas na classificação que é exibida na App Store.

Ao criar aplicativos para crianças, a App Store tem uma categoria especial para crianças de até 11 anos. Mesmo que seu aplicativo não seja especificamente para crianças, ao fornecer classificações de conteúdo apropriadas você ajuda seus clientes a fazer boas escolhas.

> [!IMPORTANT]
> Observação: a Apple rejeitará envios de aplicativos que considere obscenos, pornográficos, ofensivos ou difamatórios.

### <a name="copyright-and-company-information"></a>Informações de direitos autorais e da empresa

A Apple permite que você forneça informações de direitos autorais sobre seu aplicativo e requer informações sobre a empresa lançando o aplicativo, assim como suas informações de endereço e de contato (que são necessárias para aplicativos lançados para a App Store coreana). Essas informações serão exibidas na App Store conforme necessário.

### <a name="editing-general-app-information-in-itunes-connect"></a>Editar informações gerais do aplicativo no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Versions (Versões)**.
4. Role até a seção **General App Information (Informações gerais do aplicativo)**.
5. Preencha todas as informações necessárias:

    [![](itunesconnect-images/general01.png "Editando Informações gerais do aplicativo no iTunes Connect")](itunesconnect-images/general01.png#lightbox)
6. Clique no botão **Editar** ao lado de **Classificação** para definir as informações de classificação:

    [![](itunesconnect-images/general02.png "Editando a Classificação")](itunesconnect-images/general02.png#lightbox)
6. Clique no botão **Save (Salvar)** na parte superior da tela para salvar suas alterações.

> [!NOTE]
> Observação: a Apple rejeitará o envio se as Categorias ou Classificações não corresponderem à funcionalidade atual em seu aplicativo.

<a name="game-center" />

## <a name="maintaining-game-center-information"></a>Manutenção de informações do Game Center

Para aplicativos de jogo do iOS que oferecem suporte Game Center da Apple, você pode fornecer informações como os quadros de líderes e conquistas em jogos que estão disponíveis para o usuário e se o aplicativo é compatível com múltiplos jogadores através de uma conexão de rede.

### <a name="editing-game-center-information-in-itunes-connect"></a>Editar informações do Game Center no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Versions (Versões)**.
4. Role até a seção **Game Center**.
5. Inverta o comutador na seção **Game Center** para a posição **On (Ligado)**.
5. Preencha todas as informações necessárias:

    [![](itunesconnect-images/gamecenter01.png "Editando as informações do Game Center no iTunes Connect")](itunesconnect-images/gamecenter01.png#lightbox)
6. Clique no botão **Save (Salvar)** na parte superior da tela para salvar suas alterações.

Use a guia **Game Center** para ativar o Game Center e manter quaisquer **Leaderboards (Placares de Líderes)** ou **Achievements (Conquistas)** disponíveis para este aplicativo:

[![](itunesconnect-images/gamecenter02.png "Ativar Game Center")](itunesconnect-images/gamecenter02.png#lightbox)

[![](itunesconnect-images/gamecenter03.png "Manter Placar de Líderes ou Conquistas disponíveis para este aplicativo")](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>Manutenção de informações de análise de aplicativos

Use esta seção para fornecer as informações necessárias à equipe da Apple que examinará seu aplicativo, como as informações de contato (se o técnico tiver qualquer dúvida), contas de demonstração que possam ser necessárias e anotações que possam ajudar o testador a examinar seu aplicativo com êxito.

### <a name="editing-app-review-information-in-itunes-connect"></a>Editar informações de análise do aplicativo no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Versions (Versões)**.
4. Role até a seção **App Review Information (Informações de análise do aplicativo)**.
5. Preencha todas as informações necessárias:

    [![](itunesconnect-images/review01.png "Editando Informações de Análise do Aplicativo no iTunes Connect")](itunesconnect-images/review01.png#lightbox)
6. Selecione como você deseja que o aplicativo seja liberado para a App Store depois de ter sido analisado com êxito:

    [![](itunesconnect-images/review02.png "Editando informações de versão no iTunes Connect")](itunesconnect-images/review02.png#lightbox)
6. Clique no botão **Save (Salvar)** na parte superior da tela para salvar suas alterações.


## <a name="maintaining-pricing-information"></a>Manutenção de informações de preço

Se você pretende liberar seu aplicativo para venda, você precisará definir o preço de venda selecionando um dos tipos de preço disponíveis da Apple e a data em que o preço determinado entra em vigor. Por exemplo, no momento da redação deste artigo, o **Tier 1 (Tipo 1)** de preço tem a seguinte aparência:

[![](itunesconnect-images/price01.png "Mantendo Informações de preço")](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>Desconto educacional

Marque esta caixa se desejar que seu aplicativo seja oferecido com desconto para instituições de ensino ao comprar várias cópias de uma vez. Os detalhes do desconto são encontrados no último **Contrato do aplicativo pago**, que você precisa assinar antes que este aplicativo esteja disponível para clientes que usufruem do desconto educacional.

### <a name="custom-business-to-business-application"></a>Aplicativo entre empresas personalizado

Um aplicativo que está configurado como um **Custom Business to Business Application (Aplicativo entre empresas personalizado)** só estará disponível para os clientes do **Volume Purchase Program** que você especificar no iTunes Connect e só estará disponível em regiões aplicáveis (por exemplo, clientes do Volume Purchase Program dos EUA devem usar o Volume Purchase Program for Business da App Store dos EUA).

Aplicativos entre empresas personalizados não estarão disponíveis para clientes de instituições educacionais ou clientes da App Store em geral. Para saber mais sobre o *Volume Purchase Program for Business da App Store*, visite a página [Perguntas frequentes](http://vpp.itunes.apple.com/faq) da Apple. Para saber mais sobre como seus clientes podem se inscrever para o **Volume Purchase Program**, visite a página [Programas de implantação](http://enroll.vpp.itunes.apple.com) da Apple.

### <a name="editing-pricing-information-in-itunes-connect"></a>Editar informações de preço no iTunes Connect

Faça o seguinte no [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa):

1. Clique em **My Apps (Meus Aplicativos)**.
2. Clique no **ícone** do seu aplicativo.
3. Selecione a guia **Preços**:

    [![](itunesconnect-images/price02.png "Editando informações de preço no iTunes Connect")](itunesconnect-images/price02.png#lightbox)
4. Configure um **Availability Date (Alerta de Disponibilidade)**.
5. Selecione o preço desejado da lista suspensa **Price Tier (Faixa de Preço)**.
5. Opcionalmente, habilite **Educational Discounts (Descontos Educacionais)**.
6. Opcionalmente, definir o aplicativo como um **Custom Business to Business Application (Aplicativo entre Empresas Personalizado)**.
6. Clique no botão **Save (Salvar)** para salvar suas alterações.

<a name="iap" />

## <a name="maintaining-in-app-purchase-information"></a>Manutenção de informações de compra no aplicativo

Se você pretende vender produtos no aplicativo virtuais do seu aplicativo (como recursos do aplicativo ou novos níveis de jogos) você usará esta seção para criar e manter esses itens de compra.

[![](itunesconnect-images/inapp01.png "Mantendo informações de Compra no aplicativo")](itunesconnect-images/inapp01.png#lightbox)

Para obter mais informações sobre como trabalhar com compras no aplicativo em um aplicativo Xamarin.iOS, consulte nossa documentação sobre [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md).

## <a name="viewing-application-reviews"></a>Exibição de análises do aplicativo

Depois que o aplicativo foi lançado para a App Store, os usuários que compram ou baixam o aplicativo gratuitamente podem escrever análises do aplicativo e deixar classificações por estrelas. Use esta seção para ver essas análises. Por exemplo:

[![](itunesconnect-images/reviews01.png "Exibindo Análises do Aplicativo")](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>Resumo

Este artigo descreve como usar o iTunes Connect para preparar um aplicativo Xamarin.iOS para liberação para a App Store e como manter todas as informações exibidas sobre seu aplicativo na loja.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Guia de fluxo de trabalho de desenvolvimento de Aplicativo iOS: distribuição de aplicativos](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Guia do Desenvolvedor do iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1)
