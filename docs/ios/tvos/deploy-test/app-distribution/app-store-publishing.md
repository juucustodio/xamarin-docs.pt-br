---
title: Publicação para a Store de aplicativo da Apple TV
description: Este documento descreve como publicar um aplicativo para a Store de aplicativo da Apple TV. Ele discute como configurar, provisionar, compilar e enviar um aplicativo tvOS criado com o Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b941bcc8588e7fb0377430cca2829ad72ecbc8c6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122394"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publicação para a Store de aplicativo da Apple TV

Distribuir aplicativos para todos os dispositivos Apple TV, a Apple requer que os aplicativos sejam publicados por meio de *Apple TV App Store*, tornando a App Store o local de compra único para aplicativos de tvOS. Os desenvolvedores de muitos tipos de aplicativos pode tiraram proveito do enorme sucesso desse único ponto de distribuição. A Store de aplicativo da Apple TV é uma solução pronta para uso, oferecendo aos desenvolvedores de aplicativos em sistemas de distribuição e pagamento.

O processo de envio de um aplicativo para a Store do Apple TV App envolve:

1. Criar uma *ID do aplicativo* e a selecionar *Direitos*.
2. Criar um *Perfil de provisionamento de distribuição.*
3. Usando esse perfil para criar seu aplicativo.
4. Enviando seu aplicativo por meio *iTunes Connect*.


Neste artigo, abordaremos todas as etapas necessárias para provisionar, compilar e enviar um aplicativo para distribuição de Apple TV App Store.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar um aplicativo

Depois de enviar um aplicativo para publicação de Store de aplicativo da Apple TV, ele passa por um processo de análise pela Apple para garantir que ele atende às diretrizes da Apple para qualidade e conteúdo. Se o aplicativo não atender a essas diretrizes, a Apple o rejeitará e, em seguida, você precisará resolver as não conformidades citadas pela Apple e reenviar o aplicativo.
Portanto, você terá mais chance de fazê-lo passar pela análise da Apple ao familiarizar-se com essas diretrizes e tentar adaptar seu aplicativo a elas. Diretrizes da Apple estão disponíveis em [diretrizes de revisão da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) e [preparar seu envio de aplicativo para o novo Apple TV](https://developer.apple.com/tvos/submit/).

Algumas coisas a observar ao enviar um aplicativo:

1. Verifique se que a funcionalidade incluída no aplicativo corresponde a descrição do aplicativo.
2. Testar para ver se o aplicativo não falha em uso normal. Isso inclui o uso em todos os dispositivos Apple TV que você oferece suporte.


Apple também mantém uma lista de dicas de envio de Apple TV App Store. Você pode ler essas dicas em [Distribuindo na App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurando seu aplicativo no iTunes Connect

[o iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) é um conjunto de ferramentas baseadas na web, para, entre outras coisas, gerenciar seus aplicativos de tvOS de Store de aplicativo da Apple TV. Seu aplicativo tvos precisará ser corretamente configurados e configurado no iTunes Connect antes que ele pode ser enviado à Apple para análise e, por fim, ser liberado para venda ou como um aplicativo gratuito de Store de aplicativo da Apple TV.

Faça o seguinte:

1. Verifique se os contratos apropriados estão em vigor e atualizados na seção **Agreements, Tax, and Banking (Contratos, impostos e transações bancárias)** do iTunes Connect para lançar um aplicativo iOS gratuitamente ou para venda.
2. Criar um novo **registro do iTunes Connect** para o aplicativo e especifique seus **nome de exibição** (como visto de Store de aplicativo da Apple TV).
3. Selecione um **Sale Price (Preço de venda)** ou especifique que o aplicativo será liberado gratuitamente.
4. Fornecer um **ícone da App Store** (ícone grande) e capturas de tela do seu aplicativo em ação nos dispositivos da Apple TV oferece suporte a ele. Consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) guia para obter mais detalhes.
5. Fornecem uma clara sucinta **descrição** do aplicativo, incluindo seus recursos e benefícios para o usuário final.
6. Fornecer **categorias**, **subcategorias**, e **palavras-chave** para ajudar o usuário a encontrar seu aplicativo em de Store de aplicativo da Apple TV.
7. Forneça URLs de **Contact (Contato)** e **Support (Suporte)** para o seu site exigido pela Apple.
8. Defina o aplicativo **classificação**, que é usado por controles dos pais de Store de aplicativo da Apple TV.
9. Configure tecnologias opcionais da App Store, como **Game Center** e **In-App Purchase (Compra no aplicativo)**.

Para obter mais detalhes, consulte nosso [configurar seu aplicativo tvOS no iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentação.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparando para a distribuição da App Store

Para publicar um aplicativo para a Store de aplicativo da Apple TV, primeiro você precisa compilá-lo para distribuição, que envolve várias etapas. As seções a seguir aborda tudo que é necessário preparar um aplicativo xamarin. tvos para publicação, para que ele pode ser compilado e enviá-lo para a Store de aplicativo de TV da Apple para análise e lançamento.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de serviços de aplicativos especiais, também chamada de direitos, que podem ser ativados para seu aplicativo tvOS, ao criar uma ID exclusiva para ele. Se você estiver usando direitos personalizados ou não, ainda precisará criar uma ID exclusiva para seu aplicativo tvos antes de ser publicado de Store de aplicativo da Apple TV.

A criação de uma ID do aplicativo e, opcionalmente, selecionar direitos, envolve as seguintes etapas usando o Portal de provisionamento iOS baseado na Web da Apple:

1. Selecione **provisionamento** > **desenvolvimento**.
2. Clique no botão **+** e forneça um **Name (Nome)** e uma **Bundle ID (ID do pacote)** para o novo aplicativo.
3. Role até a parte inferior da tela e selecione qualquer **serviços de aplicativos** que serão necessários pelo seu aplicativo xamarin. tvos.
4. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do aplicativo.

Além de selecionar e configurar os serviços de aplicativo necessários ao definir sua ID do aplicativo, você também precisa configurar a ID de aplicativo e os direitos em seu projeto xamarin. tvos, editando os a `Info.plist` e `Entitlements.plist` arquivos.

Faça o seguinte no Visual Studio para Mac:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. No **destino do aplicativo do tvOS** seção, preencha um nome para seu aplicativo e insira o **identificador de pacote** que você criou quando você definiu o ID. App
3. Salve as alterações no arquivo `Info.plist`.
4. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
5. Selecione e configure os direitos necessários para você aplicativo tvos para que eles correspondam à configuração que foi feita acima, quando você definiu o ID. App
6. Salve as alterações no arquivo `Entitlements.plist`.

Para obter instruções detalhadas, consulte a documentação [Provisionamento para Serviços de Aplicativo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices). Embora este documento foi escrito para iOS, as mesmas etapas são usadas para provisionar um aplicativo xamarin. tvos.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Definir os ícones de aplicativos, a imagem de inicialização e a imagem da prateleira superior

Para um aplicativo tvOS seja aceito pela Apple para inclusão de Store de aplicativo da Apple TV, ele requer ícones apropriados, inicialização e imagens de prateleira superior para todos os dispositivos Apple TV que ele será executado. Você precisa adicionar os ativos de imagem necessários que serão compilados em um `Assets.car` de arquivo e incluídos no pacote do seu aplicativo tvos antes de ser carregado no iTunes Connect.

Para obter instruções detalhadas, consulte nosso [trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) documentação.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Criando e instalando um perfil de distribuição

usa tvOS *perfis de provisionamento* para controlar como uma compilação de determinado aplicativo pode ser implantada. Esses são arquivos que contêm informações sobre o certificado usado para assinar um aplicativo, a *ID do aplicativo* e informações sobre onde o aplicativo pode ser instalado. Para desenvolvimento e distribuição ad hoc, o perfil de provisionamento também inclui a lista de dispositivos permitidos para os quais você pode implantar o aplicativo. No entanto, para distribuição de Apple TV App Store, somente informações de ID do aplicativo e de certificado são incluídos, como o único mecanismo de distribuição pública é por meio de Store de aplicativo da Apple TV.

O provisionamento envolve as seguintes etapas usando o Portal de provisionamento do iOS baseado na Web da Apple:

1.  Selecione **Provisionamento** > **Distribuição**.
2.  Clique o **+** botão e selecione o tipo de perfil de distribuição que você deseja criar como **Apple TV App Store**.
3.  Selecione na lista suspensa a **ID do aplicativo** para a qual você deseja criar um Perfil de distribuição.
4.  Selecione o certificado necessário para assinar o aplicativo.
5.  Insira um **Nome** para o novo **Perfil de distribuição** e gere o perfil.
6.  Atualize a lista de perfis disponíveis no Xcode.
7.  Selecione a perfil no Visual Studio para de provisionamento de distribuição do **App Store** _configuração de compilação_.

Para obter instruções detalhadas, consulte [Criando um perfil de distribuição](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) e [Selecionando um perfil de distribuição em um projeto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Novamente, ambos esses documentos são específicos para iOS, mas a mesma técnica é usada para aplicativos de tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Definindo a configuração de build para o seu aplicativo

Por padrão, quando você cria um novo aplicativo xamarin. tvos _configurações de Build_ são criadas automaticamente para as **Debug** e **versão** implantação. Antes de fazer a compilação final do seu aplicativo que será enviada à Apple, há algumas modificações que você precisará fazer na base **versão** configuração.

Faça o seguinte:

1. Com o botão direito no **nome do projeto** na **Gerenciador de soluções** e a seleção **opções** para abri-lo para edição.
2. Se você estiver selecionando uma versão específica do tvOS, selecioná-la sob **Build do tvOS** > **versão do SDK do iOS**. A versão de visualização do suporte de tvOS, deixe esse valor definido como **padrão**.
3. Vinculação reduz o tamanho geral do seu aplicativo distribuível, eliminando as Classes não utilizadas de métodos, propriedades, etc. e, na maioria dos casos o valor padrão devem ser deixados **Link somente para o framework SDK**. Em algumas situações, como no uso de algumas bibliotecas de terceiros específicas, você poderá ser forçado a definir esse valor como **Não vincular** para impedir que elementos necessários sejam removidos.
4. Para enviar um aplicativo xamarin. tvos, você precisará estar usando o compilador de otimização do LLVM. Certifique-se de que o **usar o compilador de otimização do LLVM** caixa está marcada sob o **versão** configuração.
5. Apple também necessário que os aplicativos de tvOS usam bitcode. Novamente sob o **versão** configuração, adicione `--bitcode=asmonly` para o **argumentos adicionais do mtouch** caixa.
6. O **arquivos de imagem PNG otimizar para iOS** caixa de seleção deve ser verificada, pois isso ajudará a diminuir ainda mais tamanho da entrega do seu aplicativo.
7. A depuração *não* deve ser habilitada, pois deixará o build desnecessariamente maior.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Criando e enviando o distribuível

Com o seu aplicativo tvos configurado corretamente, agora você está pronto para fazer a compilação de distribuição final que será enviada à Apple para análise e lançamento.

#### <a name="build-your-archive"></a>Compilar seu arquivo morto

1. Selecione a configuração **Versão | Dispositivo** no Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Selecione a configuração de versão")
2. No menu **Compilar**, selecione **Arquivo para Publicação**:

    [![](app-store-publishing-images/buildxs02new.png "Selecionar Arquivo Morto para Publicação")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Quando o arquivo tiver sido criado, a exibição **Arquivos Mortos** será exibida:

    [![](app-store-publishing-images/buildxs03new.png "O modo de exibição de arquivos mortos")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Assinar e distribuir seu aplicativo

Sempre que você compilar seu aplicativo para o Arquivo Morto, ele abrirá automaticamente a *Exibição de Arquivos Mortos*, exibindo todos os projetos arquivados, agrupados por solução. Por padrão esta exibição mostra apenas a solução atual aberta. Para ver todas as soluções que têm arquivos, clique na opção **Mostrar todos os arquivos mortos**.

É recomendável que os arquivos implantados para os clientes (implantações App Store ou Empresarial) sejam guardados, para que qualquer informação de depuração que for gerada posa ser indicada em uma data posterior.

Para assinar seu aplicativo e prepará-lo para distribuição:

1. Selecione o **assinar e distribuir...** , como ilustrado abaixo:

    [![](app-store-publishing-images/buildxs04new.png ", Selecione theSign e distribuir...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Isso abrirá o assistente de publicação. Selecione o canal de distribuição da **App Store** para criar um pacote e abra o Application Loader:

    [![](app-store-publishing-images/distribute01.png "Selecione o canal de distribuição da App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. Na tela de perfil de provisionamento, selecione sua identidade de assinatura e perfil de provisionamento correspondente ou assine novamente com outra identidade:

    [![](app-store-publishing-images/distribute02.png "Selecione a identidade de assinatura e perfil de provisionamento correspondente")](app-store-publishing-images/distribute02.png#lightbox)
4. Verifique os detalhes do seu pacote e, em seguida, clique em **Publicar** para salvar seu pacote `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verifique os detalhes do pacote")](app-store-publishing-images/distribute03.png#lightbox)
5. Uma vez que o `.ipa` tiver sido salvo, seu aplicativo está pronto para ser carregado no iTunes Connect através do Application Loader:

    [![](app-store-publishing-images/distribute04.png "Carregado no iTunes Connect através do Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Com a compilação de distribuição criada e arquivada, agora você está pronto para enviar seu aplicativo para o iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviando seu aplicativo para a Apple

Com a compilação de distribuição concluída, você está pronto para enviar o seu aplicativo iOS para a Apple para análise e lançamento na App Store.


O fluxo de trabalho de arquivamento no Visual Studio para Mac abrirá o Application Loader automaticamente, assim que você tiver salvo o `.ipa`:

2. Selecione *Entregar seu aplicativo* e clique no botão *Escolher*:

    [![](app-store-publishing-images/publishvs01.png "Selecionar Entregar seu aplicativo")](app-store-publishing-images/publishvs01.png#lightbox)

3. Selecione o arquivo zip ou IPA criado anteriormente e clique no botão **OK**.
4. O Application Loader validará o arquivo:

    [![](app-store-publishing-images/publishvs02.png "A tela de validação do carregador de aplicativos")](app-store-publishing-images/publishvs02.png#lightbox)
5. Clique no botão *Avançar* e o aplicativo será validados na App Store:

    [![](app-store-publishing-images/publishvs03.png "O aplicativo que está sendo validado em relação a App Store")](app-store-publishing-images/publishvs03.png#lightbox)
6. Clique no botão **Enviar** para enviar o aplicativo para a Apple para análise.
7. O Application Loader informará quando o arquivo for carregado com êxito.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Status de conexão do iTunes

Se você faça logon novamente no iTunes Connect e selecione seu aplicativo na lista de aplicativos disponíveis, o status no iTunes Connect agora deve mostrar que ele está **Aguardando revisão** (ele pode mostrar temporariamente **Upload recebido** durante o processamento):

[![](app-store-publishing-images/image21.png "O status no iTunes Connect mostrando Aguardando revisão")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solução de problemas

Se você estiver tendo problemas enviando seu aplicativo tvos para a Store de aplicativo da Apple TV, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) guia. Ele contém vários problemas conhecidos que podem ocorrer e como resolvê-los no xamarin. tvos.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou um guia passo a passo para configurar, compilar e enviar um aplicativo para publicação de Apple TV App Store. Primeiro, ele abordou as etapas necessárias para criar e instalar um perfil de provisionamento de distribuição. Em seguida, discorreu sobre como usar o Visual Studio para Mac para criar uma compilação de distribuição. Por fim, ele mostrou como usar o iTunes Connect e a ferramenta de arquivamento do Xcode para enviar um aplicativo para a Store de aplicativo da Apple TV.


## <a name="related-links"></a>Links relacionados

- [Trabalhar com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparar o envio de aplicativo para o novo Apple TV](https://developer.apple.com/tvos/submit/)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rejeições de Aplicativo Comuns](https://developer.apple.com/app-store/review/rejections/)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
