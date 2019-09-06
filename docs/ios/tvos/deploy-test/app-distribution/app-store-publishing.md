---
title: Como publicar na Apple TV App Store
description: Este documento descreve como publicar um aplicativo na loja de aplicativos da Apple TV. Ele aborda como configurar, provisionar, compilar e enviar um aplicativo tvOS criado com o Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 350cd7e2d0dd79b876a6c1277d40a9c96d97c102
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282712"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Como publicar na Apple TV App Store

Para distribuir aplicativos para todos os dispositivos da Apple TV, a Apple exige que os aplicativos sejam publicados por meio da *loja de aplicativos da Apple TV*, fazendo com que o aplicativo armazene o local de compras One-stop para aplicativos tvOS. Os desenvolvedores de muitos tipos de aplicativos podem ser capitalizados em grande quantidade de sucesso desse único ponto de distribuição. A Apple TV App Store é uma solução completa, oferecendo aos desenvolvedores de aplicativos sistemas de distribuição e de pagamento.

O processo de envio de um aplicativo para a loja de aplicativos da Apple TV envolve:

1. Criar uma *ID do aplicativo* e a selecionar *Direitos*.
2. Criar um *Perfil de provisionamento de distribuição.*
3. Usando esse perfil para compilar seu aplicativo.
4. Enviando seu aplicativo por meio do *iTunes Connect*.


Neste artigo, abordaremos todas as etapas necessárias para provisionar, compilar e enviar um aplicativo para a distribuição da loja de aplicativos da Apple TV.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar um aplicativo

Depois de enviar um aplicativo para publicação na loja de aplicativos da Apple TV, ele passa por um processo de revisão da Apple para garantir que ele atenda às diretrizes da Apple para qualidade e conteúdo. Se o aplicativo não atender a essas diretrizes, a Apple o rejeitará e, em seguida, você precisará resolver as não conformidades citadas pela Apple e reenviar o aplicativo.
Portanto, você terá mais chance de fazê-lo passar pela análise da Apple ao familiarizar-se com essas diretrizes e tentar adaptar seu aplicativo a elas. As diretrizes da Apple estão disponíveis em [diretrizes de revisão da loja de aplicativos](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparam o envio de seu aplicativo para a nova Apple TV](https://developer.apple.com/tvos/submit/).

Algumas coisas a observar ao enviar um aplicativo:

1. Verifique se a descrição do aplicativo corresponde à funcionalidade incluída no aplicativo.
2. Testar para ver se o aplicativo não falha em uso normal. Isso inclui o uso em todos os dispositivos de TV da Apple aos quais você dá suporte.


A Apple também mantém uma lista de dicas de envio da loja de aplicativos da Apple TV. Você pode ler essas dicas em [Distribuindo na App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurando seu aplicativo no iTunes Connect

o [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) é um pacote de ferramentas baseadas na Web para o, entre outras coisas, Gerenciando seus aplicativos TvOS na loja de aplicativos da Apple TV. Seu aplicativo Xamarin. tvOS precisará ser instalado e configurado corretamente no iTunes Connect antes que possa ser enviado à Apple para revisão e, por fim, seja liberado para venda ou como um aplicativo gratuito na loja de aplicativos da Apple TV.

Faça o seguinte:

1. Verifique se os contratos apropriados estão em vigor e atualizados na seção **Agreements, Tax, and Banking (Contratos, impostos e transações bancárias)** do iTunes Connect para lançar um aplicativo iOS gratuitamente ou para venda.
2. Crie um novo **registro do iTunes Connect** para o aplicativo e especifique seu **nome de exibição** (como visto na loja de aplicativos da Apple TV).
3. Selecione um **Sale Price (Preço de venda)** ou especifique que o aplicativo será liberado gratuitamente.
4. Forneça um **ícone de loja de aplicativos** (ícone grande) e capturas de tela do seu aplicativo em ação, nos dispositivos Apple TV aos quais ele dá suporte. Veja nosso guia de [trabalho com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) para obter mais detalhes.
5. Forneça uma **Descrição** clara e sucinta do aplicativo, incluindo seus recursos e benefícios para o usuário final.
6. Forneça **categorias**, **subcategorias**e **palavras-chave** para ajudar o usuário a localizar seu aplicativo na loja de aplicativos da Apple TV.
7. Forneça URLs de **Contact (Contato)** e **Support (Suporte)** para o seu site exigido pela Apple.
8. Defina a **classificação**do aplicativo, que é usada pelos controles dos pais na loja de aplicativos da Apple TV.
9. Configure tecnologias opcionais da App Store, como **Game Center** e **In-App Purchase (Compra no aplicativo)** .

Para obter mais detalhes, consulte nossa documentação [configurar seu aplicativo tvOS na iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) .

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparando para a distribuição da App Store

Para publicar um aplicativo na loja de aplicativos da Apple TV, primeiro você precisa compilá-lo para distribuição, o que envolve várias etapas. As seções a seguir abordam tudo o que é necessário para preparar um aplicativo Xamarin. tvOS para publicação para que ele possa ser compilado e enviado para a loja de aplicativos da Apple TV para revisão e liberação.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de Serviços de Aplicativos especiais, também chamados de direitos, que podem ser ativados para seu aplicativo tvOS quando você cria uma ID exclusiva para ele. Se você estiver usando direitos personalizados ou não, ainda precisará criar uma ID exclusiva para seu aplicativo Xamarin. tvOS antes que ele possa ser publicado na loja de aplicativos da Apple TV.

A criação de uma ID do aplicativo e, opcionalmente, selecionar direitos, envolve as seguintes etapas usando o Portal de provisionamento iOS baseado na Web da Apple:

1. Selecione**desenvolvimento**de **provisionamento** > .
2. Clique no botão **+** e forneça um **Name (Nome)** e uma **Bundle ID (ID do pacote)** para o novo aplicativo.
3. Role até a parte inferior da tela e selecione os **serviços de aplicativo** que serão exigidos pelo seu aplicativo Xamarin. tvOS.
4. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do aplicativo.

Além de selecionar e configurar o serviços de aplicativos necessário ao definir a ID do aplicativo, você também precisa configurar a ID do aplicativo e os direitos no projeto Xamarin. tvOS editando os `Info.plist` arquivos e. `Entitlements.plist`

Faça o seguinte no Visual Studio para Mac:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. Na seção **destino do aplicativo tvOS** , preencha um nome para seu aplicativo e insira o **identificador do pacote** que você criou quando definiu a ID do aplicativo.
3. Salve as alterações no arquivo `Info.plist`.
4. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
5. Selecione e configure os direitos necessários para seu aplicativo Xamarin. tvOS para que eles correspondam à configuração que você executou acima quando você definiu a ID do aplicativo.
6. Salve as alterações no arquivo `Entitlements.plist`.

Para obter instruções detalhadas, consulte a documentação [Provisionamento para Serviços de Aplicativo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Embora este documento tenha sido escrito para iOS, as mesmas etapas são usadas para provisionar um aplicativo Xamarin. tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Configurando os ícones de aplicativos, imagem de inicialização e imagem de prateleira superior

Para que um aplicativo tvOS seja aceito pela Apple para inclusão na loja de aplicativos da Apple TV, ele requer ícones adequados, lançamento e imagens de prateleira superior para todos os dispositivos da Apple TV nos quais ele será executado. Você precisará adicionar os ativos de imagem necessários que serão compilados em `Assets.car` um arquivo e incluídos no pacote do aplicativo Xamarin. tvOS antes que ele seja carregado no iTunes Connect.

Para obter instruções detalhadas, consulte nossa documentação sobre como [trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Criando e instalando um perfil de distribuição

o tvOS usa *perfis de provisionamento* para controlar como uma compilação de aplicativo específica pode ser implantada. Esses são arquivos que contêm informações sobre o certificado usado para assinar um aplicativo, a *ID do aplicativo* e informações sobre onde o aplicativo pode ser instalado. Para desenvolvimento e distribuição ad hoc, o perfil de provisionamento também inclui a lista de dispositivos permitidos para os quais você pode implantar o aplicativo. No entanto, para a distribuição da loja de aplicativos da Apple TV, somente as informações de ID de certificado e aplicativo são incluídas, pois o único mecanismo para distribuição pública é por meio da loja de aplicativos da Apple TV.

O provisionamento envolve as seguintes etapas usando o Portal de provisionamento do iOS baseado na Web da Apple:

1. Selecione **Provisionamento** > **Distribuição**.
2. Clique no **+** botão e selecione o tipo de perfil de distribuição que você deseja criar como a **loja de aplicativos da Apple TV**.
3. Selecione na lista suspensa a **ID do aplicativo** para a qual você deseja criar um Perfil de distribuição.
4. Selecione o certificado necessário para assinar o aplicativo.
5. Insira um **Nome** para o novo **Perfil de distribuição** e gere o perfil.
6. Atualize a lista de perfis disponíveis no Xcode.
7. Selecione o perfil de provisionamento de distribuição no Visual Studio para a _configuração de compilação_da loja de aplicativos.

Para obter instruções detalhadas, consulte [Criando um perfil de distribuição](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) e [Selecionando um perfil de distribuição em um projeto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Novamente, esses dois documentos são específicos do iOS, mas a mesma técnica é usada para aplicativos tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Definindo a configuração de build para o seu aplicativo

Por padrão, quando você cria um novo aplicativo Xamarin. tvOS, _as configurações de compilação_ são criadas automaticamente para implantação de **depuração** e **versão** . Antes de fazer a compilação final do seu aplicativo que você enviará para a Apple, há algumas modificações que você precisará fazer na configuração da **versão** base.

Faça o seguinte:

1. Clique com o botão direito do mouse no **nome do projeto** nas **Opções** de **Gerenciador de soluções** e seleção para abri-los para edição.
2. Se você estiver direcionando a uma versão específica do tvOS, selecione-a em **tvOS Build** > **Ios SDK versão**. Para obter a versão de visualização do suporte do tvOS, deixe esse valor definido como **padrão**.
3. A vinculação reduz o tamanho geral do distribuível do seu aplicativo removendo métodos, propriedades, classes, etc. e, na maioria dos casos, deve ser deixado no valor padrão **somente do SDK do link Framework**. Em algumas situações, como no uso de algumas bibliotecas de terceiros específicas, você poderá ser forçado a definir esse valor como **Não vincular** para impedir que elementos necessários sejam removidos.
4. Para enviar um aplicativo Xamarin. tvOS, você precisará usar o compilador de otimização do LLVM. Verifique se a caixa **usar o compilador de otimização de LLVM** está marcada sob a configuração de **versão** .
5. A Apple também exigiu que os aplicativos tvOS usem o BitCode. Novamente na configuração da **versão** , adicione `--bitcode=asmonly` à caixa **argumentos adicionais do mTouch** .
6. A caixa de seleção **otimizar arquivos de imagem png para IOS** deve ser marcada, pois isso ajudará a diminuir ainda mais o tamanho de entrega do aplicativo.
7. A depuração *não* deve ser habilitada, pois deixará o build desnecessariamente maior.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Criando e enviando o distribuível

Com seu aplicativo Xamarin. tvOS configurado corretamente, agora você está pronto para fazer o Build de distribuição final que será enviado à Apple para revisão e liberação.

#### <a name="build-your-archive"></a>Compilar seu arquivo morto

1. Selecione a configuração **Versão | Dispositivo** no Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Selecionar a configuração de versão")
2. No menu **Compilar**, selecione **Arquivo para Publicação**:

    [![](app-store-publishing-images/buildxs02new.png "Selecionar Arquivo Morto para Publicação")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Quando o arquivo tiver sido criado, a exibição **Arquivos Mortos** será exibida:

    [![](app-store-publishing-images/buildxs03new.png "A exibição de arquivos mortos")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Assinar e distribuir seu aplicativo

Sempre que você compilar seu aplicativo para o Arquivo Morto, ele abrirá automaticamente a *Exibição de Arquivos Mortos*, exibindo todos os projetos arquivados, agrupados por solução. Por padrão esta exibição mostra apenas a solução atual aberta. Para ver todas as soluções que têm arquivos, clique na opção **Mostrar todos os arquivos mortos**.

É recomendável que os arquivos implantados para os clientes (implantações App Store ou Empresarial) sejam guardados, para que qualquer informação de depuração que for gerada posa ser indicada em uma data posterior.

Para assinar seu aplicativo e prepará-lo para distribuição:

1. Selecione **assinar e distribuir...** , ilustrado abaixo:

    [![](app-store-publishing-images/buildxs04new.png ", Selecione assinar e distribuir...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Isso abrirá o assistente de publicação. Selecione o canal de distribuição da **App Store** para criar um pacote e abra o Application Loader:

    [![](app-store-publishing-images/distribute01.png "Selecione o canal de distribuição da loja de aplicativos")](app-store-publishing-images/distribute01.png#lightbox)
3. Na tela perfil de provisionamento, selecione sua identidade de assinatura e o perfil de provisionamento correspondente ou assine novamente com outra identidade:

    [![](app-store-publishing-images/distribute02.png "Selecionar a identidade de assinatura e o perfil de provisionamento correspondente")](app-store-publishing-images/distribute02.png#lightbox)
4. Verifique os detalhes do seu pacote e, em seguida, clique em **Publicar** para salvar seu pacote `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verificar os detalhes do pacote")](app-store-publishing-images/distribute03.png#lightbox)
5. Uma vez que o `.ipa` tiver sido salvo, seu aplicativo está pronto para ser carregado no iTunes Connect através do Application Loader:

    [![](app-store-publishing-images/distribute04.png "Carregado no iTunes Connect por meio do carregador de aplicativo")](app-store-publishing-images/distribute04.png#lightbox)

Com a compilação de distribuição criada e arquivada, agora você está pronto para enviar seu aplicativo para o iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviando seu aplicativo para a Apple

Com a compilação de distribuição concluída, você está pronto para enviar o seu aplicativo iOS para a Apple para análise e lançamento na App Store.


O fluxo de trabalho de arquivamento no Visual Studio para Mac abrirá o carregador de aplicativos automaticamente `.ipa`depois que você salvar o:

1. Selecione *Entregar seu aplicativo* e clique no botão *Escolher*:

    [![](app-store-publishing-images/publishvs01.png "Selecionar Entregar seu aplicativo")](app-store-publishing-images/publishvs01.png#lightbox)

2. Selecione o arquivo zip ou IPA criado anteriormente e clique no botão **OK**.
3. O Application Loader validará o arquivo:

    [![](app-store-publishing-images/publishvs02.png "A tela de validação do carregador de aplicativos")](app-store-publishing-images/publishvs02.png#lightbox)
4. Clique no botão *Avançar* e o aplicativo será validados na App Store:

    [![](app-store-publishing-images/publishvs03.png "O aplicativo que está sendo validado em relação à loja de aplicativos")](app-store-publishing-images/publishvs03.png#lightbox)
5. Clique no botão **Enviar** para enviar o aplicativo para a Apple para análise.
6. O Application Loader informará quando o arquivo for carregado com êxito.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Status de conexão do iTunes

Se você fizer logon novamente no iTunes Connect e selecionar seu aplicativo na lista de aplicativos disponíveis, o status no iTunes Connect agora deverá mostrar que ele está **aguardando a revisão** (ele pode ler temporariamente o **carregamento recebido** enquanto ele é processado):

[![](app-store-publishing-images/image21.png "O status no iTunes Connect mostrando aguardando a revisão")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

Se você estiver tendo problemas para enviar seu aplicativo Xamarin. tvOS para a loja de aplicativos da Apple TV, consulte nosso guia de [solução de problemas](~/ios/tvos/troubleshooting.md) . Ele contém vários problemas conhecidos que você pode encontrar e como solucioná-los no Xamarin. tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou um guia passo a passo para configurar, compilar e enviar um aplicativo para publicação da loja de aplicativos da Apple TV. Primeiro, ele abordou as etapas necessárias para criar e instalar um perfil de provisionamento de distribuição. Em seguida, ele analisou como usar Visual Studio para Mac para criar uma compilação de distribuição. Por fim, ele mostrou como usar o iTunes Connect e a ferramenta de arquivamento Xcode para enviar um aplicativo para a loja de aplicativos da Apple TV.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparar o envio de seu aplicativo para a nova Apple TV](https://developer.apple.com/tvos/submit/)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rejeições de Aplicativo Comuns](https://developer.apple.com/app-store/review/rejections/)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
