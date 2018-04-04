---
title: Publicação para Apple TV App Store
description: Este artigo mostra como configurar, criar e publicar um aplicativo Xamarin.tvOS para distribuição por meio da Apple TV App Store. Ele inclui um guia passo a passo que aborda como preparar seu aplicativo para distribuição, como usar ferramentas da Apple para enviar seu aplicativo para revisão e, finalmente, como publicar seu aplicativo à loja de aplicativos Apple TV.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c72b161845f6d8492a47d95e51a80cf0b4f30271
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publicação para Apple TV App Store

_Este artigo mostra como configurar, criar e publicar um aplicativo Xamarin.tvOS para distribuição por meio da Apple TV App Store. Ele inclui um guia passo a passo que aborda como preparar seu aplicativo para distribuição, como usar ferramentas da Apple para enviar seu aplicativo para revisão e, finalmente, como publicar seu aplicativo à loja de aplicativos Apple TV._

Em ordem distribuir aplicativos para todos os dispositivos da Apple TV, Apple requer que os aplicativos a serem publicadas por meio de *Apple TV App Store*, fazendo o local de compra de um único para aplicativos tvOS de loja de aplicativos. Os desenvolvedores de muitos tipos de aplicativos pode em letras maiusculas no sucesso em grandes quantidades desse ponto de distribuição. Aplicativo da loja da Apple TV é uma solução completa, oferecendo os desenvolvedores de aplicativos sistemas de distribuição e o pagamento.

O processo de envio de um aplicativo à loja de aplicativos Apple TV envolve:

1. Criar uma *ID do aplicativo* e a selecionar *Direitos*.
2. Criar um *Perfil de provisionamento de distribuição.*
3. Usando este perfil para criar seu aplicativo.
4. Enviando seu aplicativo por meio de *iTunes conectar*.


Neste artigo, abordaremos todas as etapas necessárias para provisionar, criar e enviar um aplicativo para distribuição Apple TV App Store.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar um aplicativo

Depois que você enviar um aplicativo para a publicação para a loja de aplicativos Apple TV, ele passa por meio de um processo de revisão pela Apple para verificar se ele atende às diretrizes da Apple para qualidade e conteúdo. Se o aplicativo não atender a essas diretrizes, a Apple o rejeitará e, em seguida, você precisará resolver as não conformidades citadas pela Apple e reenviar o aplicativo.
Portanto, você terá mais chance de fazê-lo passar pela análise da Apple ao familiarizar-se com essas diretrizes e tentar adaptar seu aplicativo a elas. Diretrizes da Apple estão disponíveis em [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparar seu aplicativo envio para o novo Apple TV](https://developer.apple.com/tvos/submit/).

Algumas coisas a observar ao enviar um aplicativo:

1. Certifique-se de que descrição do aplicativo corresponde a funcionalidade incluída no aplicativo.
2. Teste o aplicativo não falha em uso normal. Isso inclui o uso em cada dispositivo da Apple TV que você oferece suporte.


Apple também mantém uma lista de dicas de envio da Apple TV App Store. Você pode ler essas dicas em [Distribuindo na App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurando seu aplicativo no iTunes Connect

[conectar-se de iTunes](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) é um conjunto de ferramentas com base na web, entre outras coisas, gerenciar seus aplicativos tvOS na loja de aplicativos Apple TV. Seu aplicativo Xamarin.tvOS precisará estar configurado corretamente e configurado no iTunes Connect antes que ele pode ser enviado para a Apple para revisão e por fim, a ser liberado para venda ou como um aplicativo gratuito na loja de aplicativos Apple TV.

Faça o seguinte:

1. Verifique se os contratos apropriados estão em vigor e atualizados na seção **Agreements, Tax, and Banking (Contratos, impostos e transações bancárias)** do iTunes Connect para lançar um aplicativo iOS gratuitamente ou para venda.
2. Criar um novo **iTunes conectar registro** para o aplicativo e especificar seu **nome de exibição** (como visto na loja de aplicativos Apple TV).
3. Selecione um **Sale Price (Preço de venda)** ou especifique que o aplicativo será liberado gratuitamente.
4. Fornecer um **App Store ícone** (ícone grande) e capturas de tela de seu aplicativo em ação, nos dispositivos Apple TV, ele oferece suporte. Consulte nossa [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) guia para obter mais detalhes.
5. Forneça uma clara sucinta **descrição** do aplicativo, incluindo seus recursos e benefícios para o usuário final.
6. Fornecer **categorias**, **subcategorias**, e **palavras-chave** para ajudar o usuário a localizar seu aplicativo na loja de aplicativos Apple TV.
7. Forneça URLs de **Contact (Contato)** e **Support (Suporte)** para o seu site exigido pela Apple.
8. Defina o aplicativo **classificação**, que é usada pelo controle dos pais na loja de aplicativos Apple TV.
9. Configure tecnologias opcionais da App Store, como **Game Center** e **In-App Purchase (Compra no aplicativo)**.

Para obter mais detalhes, consulte nosso [configurar seu aplicativo tvOS no iTunes conectar](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentação.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparando para a distribuição da App Store

Para publicar um aplicativo à loja de aplicativos Apple TV, primeiro você precisa compilá-lo para distribuição, que envolve muitas etapas. As seções a seguir aborda tudo necessárias para preparar um aplicativo Xamarin.tvOS publicação para que ele pode ser criado e enviá-lo à loja de aplicativos Apple TV, de revisão e versão.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

Apple fornece uma seleção de serviços de aplicativo especial, também chamado de direitos, que podem ser ativados para seu aplicativo tvOS quando você criar uma ID exclusiva para ele. Se você estiver usando os direitos personalizados ou não, ainda será necessário criar uma ID exclusiva para seu aplicativo Xamarin.tvOS antes de ser publicado na loja de aplicativos Apple TV.

A criação de uma ID do aplicativo e, opcionalmente, selecionar direitos, envolve as seguintes etapas usando o Portal de provisionamento iOS baseado na Web da Apple:

1. Selecione **provisionamento** > **desenvolvimento**.
2. Clique no botão **+** e forneça um **Name (Nome)** e uma **Bundle ID (ID do pacote)** para o novo aplicativo.
3. Role até a parte inferior da tela e selecione qualquer **serviços de aplicativos** que será exigida por seu aplicativo Xamarin.tvOS.
4. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do aplicativo.

Além de selecionar e configurar os serviços de aplicativo necessários ao definir sua ID do aplicativo, também precisará configurar o ID do aplicativo e os direitos em seu projeto Xamarin.tvOS editando ambos o `Info.plist` e `Entitlements.plist` arquivos.

Faça o seguinte no Visual Studio para Mac:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. No **tvOS aplicativo destino** seção, insira um nome para seu aplicativo e insira o **identificador de pacote** criada quando você definiu o App ID.
3. Salve as alterações no arquivo `Info.plist`.
4. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
5. Selecionar e configurar os direitos necessários Xamarin.tvOS aplicativo para que correspondam a configuração executada acima, quando você definiu o App ID.
6. Salve as alterações no arquivo `Entitlements.plist`.

Para obter instruções detalhadas, consulte a documentação [Provisionamento para Serviços de Aplicativo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Embora este documento foi escrito para iOS, as mesmas etapas são usadas para provisionar um aplicativo Xamarin.tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Definindo os ícones de aplicativos, a imagem de inicialização e a imagem de prateleira superior

Para um aplicativo tvOS sejam aceitos pela Apple para inclusão na loja de aplicativos Apple TV, ele requer ícones apropriados, inicialização e imagens de validade da parte superior para todos os dispositivos da Apple TV que serão executados no. Você precisa adicionar os ativos de imagem necessário que serão compilados em um `Assets.car` de arquivo e incluídos no pacote do aplicativo Xamarin.tvOS antes de serem carregado para conectar-se de iTunes.

Para obter instruções detalhadas, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Criando e instalando um perfil de distribuição

usa tvOS *perfis de provisionamento* para controlar como uma compilação de determinado aplicativo pode ser implantada. Esses são arquivos que contêm informações sobre o certificado usado para assinar um aplicativo, a *ID do aplicativo* e informações sobre onde o aplicativo pode ser instalado. Para desenvolvimento e distribuição ad hoc, o perfil de provisionamento também inclui a lista de dispositivos permitidos para os quais você pode implantar o aplicativo. No entanto, para distribuição de loja de aplicativos do Apple TV, somente informações de ID de certificado e o aplicativo são incluídas, como o único mecanismo para a distribuição de público é por meio da Apple TV App Store.

O provisionamento envolve as seguintes etapas usando o Portal de provisionamento do iOS baseado na Web da Apple:

1.  Selecione **Provisionamento** > **Distribuição**.
2.  Clique o **+** botão e selecione o tipo de perfil de distribuição que você deseja criar como **Apple TV App Store**.
3.  Selecione na lista suspensa a **ID do aplicativo** para a qual você deseja criar um Perfil de distribuição.
4.  Selecione o certificado necessário para assinar o aplicativo.
5.  Insira um **Nome** para o novo **Perfil de distribuição** e gere o perfil.
6.  Atualize a lista de perfis disponíveis no Xcode.
7.  Selecione a perfil no Visual Studio para o provisionamento de distribuição de **App Store** _configuração de compilação_.

Para obter instruções detalhadas, consulte [Criando um perfil de distribuição](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) e [Selecionando um perfil de distribuição em um projeto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Novamente, ambos esses documentos são específicos para iOS, mas a mesma técnica é usada para tvOS aplicativos.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Definindo a configuração de build para o seu aplicativo

Por padrão, quando você cria um novo aplicativo Xamarin.tvOS, _Build Configurations_ são criados automaticamente para ambos **depurar** e **versão** implantação. Antes de fazer a compilação de final de seu aplicativo que você envie para a Apple, há alguns modificação que você precisará fazer a base de **versão** configuração.

Faça o seguinte:

1. Com o botão direito no **nome do projeto** no **Solution Explorer** e seleção **opções** para abri-los para edição.
2. Se você estiver direcionando uma versão específica do tvOS, selecione-o em **tvOS Build** > **iOS SDK versão**. A versão de visualização do tvOS suporte, deixe esse valor é definido **padrão**.
3. Vinculando reduz o tamanho geral do seu aplicativo distribuível retirando as Classes de métodos, propriedades, não utilizados, etc. e, na maioria dos casos devem ser deixados com o valor padrão de **Link SDK framework somente**. Em algumas situações, como no uso de algumas bibliotecas de terceiros específicas, você poderá ser forçado a definir esse valor como **Não vincular** para impedir que elementos necessários sejam removidos.
4. Para enviar um aplicativo Xamarin.tvOS, você precisará estar usando o compilador de otimização LLVM. Certifique-se de que o **usar o compilador de otimização de LLVM** caixa está marcada com o **versão** configuração.
5. Apple também necessário tvOS aplicativos usam bitcode. Novamente sob o **versão** configuração, adicionar `--bitcode=asmonly` para o **argumentos adicionais mtouch** caixa.
6. O **arquivos de imagem PNG otimizar para iOS** caixa de seleção deve ser verificada porque isso ajudará a mais diminuir tamanho do produto do seu aplicativo.
7. A depuração *não* deve ser habilitada, pois deixará o build desnecessariamente maior.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Criando e enviando o distribuível

Com seu aplicativo Xamarin.tvOS configurado corretamente, você está pronto para fazer a compilação de final de distribuição que você envie para a Apple para revisão e versão.

#### <a name="build-your-archive"></a>Compilar seu arquivo morto

1. Selecione a configuração **Versão | Dispositivo** no Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Selecione a configuração de versão")
2. No menu **Compilar**, selecione **Arquivo para Publicação**:

    [![](app-store-publishing-images/buildxs02new.png "Selecionar Arquivo Morto para Publicação")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Quando o arquivo tiver sido criado, a exibição **Arquivos Mortos** será exibida:

    [![](app-store-publishing-images/buildxs03new.png "O modo de exibição de arquivos")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Assinar e distribuir seu aplicativo

Sempre que você compilar seu aplicativo para o Arquivo Morto, ele abrirá automaticamente a *Exibição de Arquivos Mortos*, exibindo todos os projetos arquivados, agrupados por solução. Por padrão esta exibição mostra apenas a solução atual aberta. Para ver todas as soluções que têm arquivos, clique na opção **Mostrar todos os arquivos mortos**.

É recomendável que os arquivos implantados para os clientes (implantações App Store ou Empresarial) sejam guardados, para que qualquer informação de depuração que for gerada posa ser indicada em uma data posterior.

Para assinar seu aplicativo e prepará-lo para distribuição:

1. Selecione o **assinar e distribuir...** , ilustrado abaixo:

    [![](app-store-publishing-images/buildxs04new.png ", Selecione theSign e distribuir...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Isso abrirá o assistente de publicação. Selecione o canal de distribuição da **App Store** para criar um pacote e abra o Application Loader:

    [![](app-store-publishing-images/distribute01.png "Selecione o canal de distribuição de loja de aplicativos")](app-store-publishing-images/distribute01.png#lightbox)
3. Na tela de perfil de provisionamento, selecione sua assinatura de identidade e correspondente de perfil de provisionamento ou assinar novamente com outra identidade:

    [![](app-store-publishing-images/distribute02.png "Selecione a assinatura de identidade e correspondente de perfil de provisionamento")](app-store-publishing-images/distribute02.png#lightbox)
4. Verifique os detalhes do seu pacote e, em seguida, clique em **Publicar** para salvar seu pacote `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verifique os detalhes do pacote")](app-store-publishing-images/distribute03.png#lightbox)
5. Uma vez que o `.ipa` tiver sido salvo, seu aplicativo está pronto para ser carregado no iTunes Connect através do Application Loader:

    [![](app-store-publishing-images/distribute04.png "Carregado iTunes conectar via o carregador do aplicativo")](app-store-publishing-images/distribute04.png#lightbox)

Com a compilação de distribuição criada e arquivada, agora você está pronto para enviar seu aplicativo para o iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviando seu aplicativo para a Apple

Com a compilação de distribuição concluída, você está pronto para enviar o seu aplicativo iOS para a Apple para análise e lançamento na App Store.


O fluxo de trabalho do arquivo no Visual Studio para Mac será aberto carregador do aplicativo automaticamente, assim que você salvou o `.ipa`:

2. Selecione *Entregar seu aplicativo* e clique no botão *Escolher*:

    [![](app-store-publishing-images/publishvs01.png "Selecionar Entregar seu aplicativo")](app-store-publishing-images/publishvs01.png#lightbox)

3. Selecione o arquivo zip ou IPA criado anteriormente e clique no botão **OK**.
4. O Application Loader validará o arquivo:

    [![](app-store-publishing-images/publishvs02.png "A tela de validação do carregador do aplicativo")](app-store-publishing-images/publishvs02.png#lightbox)
5. Clique no botão *Avançar* e o aplicativo será validados na App Store:

    [![](app-store-publishing-images/publishvs03.png "O aplicativo que está sendo validado em relação ao armazenamento de aplicativo")](app-store-publishing-images/publishvs03.png#lightbox)
6. Clique no botão **Enviar** para enviar o aplicativo para a Apple para análise.
7. O Application Loader informará quando o arquivo for carregado com êxito.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Status de conexão do iTunes

Se você efetuar login novamente na iTunes conectar e seleciona seu aplicativo na lista de aplicativos disponíveis, o status na iTunes conectar agora deve mostrar que é **aguardando examine** (ela pode ler temporariamente **carregar recebida** durante o processamento):

[![](app-store-publishing-images/image21.png "O status na iTunes conectar mostrando Aguardando revisão")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

Se você estiver tendo problemas para enviar seu aplicativo Xamarin.tvOS a loja de aplicativos Apple TV, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) guia. Ele contém vários problemas conhecidos que podem ocorrer e como resolvê-los no Xamarin.tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentado um guia passo a passo para configurar, criar e enviar um aplicativo para a publicação da Apple TV App Store. Primeiro, ele abordou as etapas necessárias para criar e instalar um perfil de provisionamento de distribuição. Em seguida, movimentada como usar o Visual Studio para Mac para criar uma compilação de distribuição. Finalmente, ele mostrou como usar iTunes conectar e a ferramenta de arquivamento do Xcode para enviar um aplicativo para Apple TV App Store.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparar o envio de aplicativos para o novo Apple TV](https://developer.apple.com/tvos/submit/)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rejeições de Aplicativo Comuns](https://developer.apple.com/app-store/review/rejections/)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
