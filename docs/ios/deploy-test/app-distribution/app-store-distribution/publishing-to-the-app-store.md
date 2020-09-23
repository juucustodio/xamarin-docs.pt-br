---
title: Publicar aplicativos Xamarin.iOS na App Store
description: Este documento descreve como configurar, compilar e publicar um aplicativo Xamarin.iOS para distribuição na App Store.
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 1f556e7b63cabcb2a0909927df4970d367338694
ms.sourcegitcommit: 2d41e287ccf937efa24813151e371298b2b4a582
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102278"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>Publicar aplicativos Xamarin.iOS na App Store

Para publicar um aplicativo na [App Store](https://www.apple.com/ios/app-store/), primeiro, o desenvolvedor precisa enviá-lo – com capturas de tela, uma descrição, ícones e outras informações – para a análise da Apple. Após aprovar o aplicativo, a Apple o coloca na App Store e os usuários poderão comprá-lo e instalá-lo diretamente em seus dispositivos iOS.

Este guia descreve as etapas a serem seguidas para preparar um aplicativo para a App Store e enviá-lo à Apple para análise. Em especial, ele descreve como:

> [!div class="checklist"]
>
> - Seguir as Diretrizes de Análise da App Store
> - Configuração de uma ID e os direitos do aplicativo
> - Fornecer um ícone da App Store e ícones do aplicativo
> - Configurar um perfil de provisionamento da App Store
> - Atualização da configuração de build da **Versão**
> - Configurar um aplicativo no iTunes Connect
> - Criar um aplicativo e enviá-lo para a Apple

> [!IMPORTANT]
> A Apple [indicou](https://developer.apple.com/ios/submit/) que, a partir de março de 2019, todos os aplicativos e atualizações enviados à App Store deverão ter sido criados com o SDK do iOS 12.1 ou posterior, incluído no Xcode 10.1 ou posterior.
> Aplicativos também devem dar suporte aos tamanhos de tela do iPhone XS e de 12,9 pol. do iPad Pro.

## <a name="app-store-guidelines"></a>Diretrizes da App Store

Antes de enviar um aplicativo para publicação na App Store, verifique se ele atende aos padrões definidos pelas [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) da Apple.
Quando você envia um aplicativo para a App Store, a Apple o analisa para se certificar de que ele atende a esses requisitos. Em caso negativo, a Apple o rejeitará – e você precisará resolver os problemas citados e enviá-lo novamente.
Portanto, é uma boa ideia conhecer as diretrizes o quanto antes no processo de desenvolvimento.

Algumas coisas a observar ao enviar um aplicativo:

1. Verificar se a descrição do aplicativo corresponde à sua funcionalidade.
2. Testar para ver se o aplicativo não falha em uso normal. Isso inclui o uso em cada dispositivo iOS compatível.

Além disso, verificar os [recursos relacionados com a App Store](https://developer.apple.com/app-store/resources/) fornecidos pela Apple.

## <a name="set-up-an-app-id-and-entitlements"></a>Configurar uma ID e os direitos do aplicativo

Todos os aplicativos iOS têm uma ID exclusiva, que tem um conjunto de serviços de aplicativo associado chamado *direitos*. Os direitos permitem que os aplicativos executem várias ações, como receber notificações por push, acessar recursos do iOS como o HealthKit, e muito mais.

Para criar uma ID do aplicativo e selecionar os direitos necessários, acesse o [Apple Developer Portal](https://developer.apple.com/account/) e siga estas etapas:

1. Faça logon no [portal do desenvolvedor da Apple](https://developer.apple.com/account/). Se você ainda não tiver uma ID da Apple, [crie uma ID da Apple](https://appleid.apple.com/account?appId=632&returnUrl=https%3A%2F%2Fdeveloper.apple.com%2Faccount%2F#!&page=create) primeiro.
2. Vá para a seção **certificados, IDs & perfis** e, em seguida, para a seção **identificadores** .
3. Clique no **+** botão ao lado do cabeçalho identificadores na parte superior da página.
4. Selecione **IDs do aplicativo** e clique em **continuar**. 
5. Selecione **aplicativo** e clique em **continuar**.
6. Insira uma **Descrição** e uma **ID de pacote** para o novo aplicativo e selecione quaisquer **recursos** que serão exigidos pelo seu aplicativo Xamarin. Ios. Os Serviços de Aplicativos serão descritos mais adiante no guia [Trabalhar com funcionalidades no Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md). Ao terminar de fazer suas seleções, clique em **continuar**.
7. Clique no botão **registrar** para concluir o processo de criação da nova ID do aplicativo.

Além de selecionar e configurar os serviços de aplicativo necessários ao definir a ID do aplicativo, os direitos também devem ser configurados no projeto Xamarin.iOS editando os arquivos **Info.plist** e **Entitlements.plist**. Para saber mais, confira o guia [Trabalhar com direitos no Xamarin.iOS](~/ios/deploy-test/provisioning/entitlements.md), que descreve como criar um arquivo **Entitlements.plist** e o significado de várias configurações de direitos que ele contém.

## <a name="include-an-app-store-icon"></a>Incluir um ícone da App Store

Quando você envia um aplicativo para a Apple, verifique se ele inclui um catálogo de ativos que contém um ícone da App Store. Para saber como fazer isso, confira o guia [Ícones da App Store no Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-store-icon.md).

## <a name="set-the-apps-icons-and-launch-screens"></a>Configurar ícones e telas de inicialização dos aplicativos

Para a Apple disponibilizar um aplicativo iOS na App Store, ele precisa ter ícones e telas de inicialização apropriados para todos os dispositivos iOS nos quais ele pode ser executado. Para saber mais sobre a configuração de ícones e telas de inicialização de aplicativos, leia os seguintes guias:

- [Ícones de aplicativo no Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Telas de inicialização para aplicativos Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>Criar e instalar um perfil de provisionamento da App Store

O iOS usa *perfis de provisionamento* para controlar como a compilação de um determinado aplicativo pode ser implantada. Esses são arquivos que contêm informações sobre o certificado usado para assinar um aplicativo, a ID do aplicativo e informações sobre onde o aplicativo pode ser instalado. Para desenvolvimento e distribuição ad hoc, o perfil de provisionamento também inclui a lista de dispositivos permitidos nos quais você pode implantar o aplicativo. No entanto, para a distribuição da App Store, somente informações de certificado e ID do aplicativo são incluídas, pois o único mecanismo de distribuição pública é a App Store.

Para criar e instalar um perfil de provisionamento da App Store, siga estas etapas:

1. Faça logon no [portal do desenvolvedor da Apple](https://developer.apple.com/account/).
2. Vá para a seção **certificados, IDs & perfis** e, em seguida, para a seção de **perfis** .
3. Clique no **+** botão, selecione **desenvolvimento de aplicativo do IOS** e **loja de aplicativos**e clique em **continuar**.
4. Selecione a **ID do aplicativo** do seu aplicativo na lista e clique em **Continue** (Continuar).
5. Selecione um certificado de autenticação e clique em **Continue** (Continuar).
6. Selecione os dispositivos a serem incluídos neste perfil e clique em **continuar**.
7. Insira um **nome de perfil de provisionamento** e clique em **gerar** para gerar o perfil.
8. Use as ferramentas do [Apple Account Management](~/cross-platform/macios/apple-account-management.md) do Xamarin para baixar o perfil de provisionamento recém-criado para o seu Mac. Se você estiver usando um Mac, também será possível baixar o perfil de provisionamento diretamente do Apple Developer Portal e clicar nele duas vezes para instalar.

Para obter instruções detalhadas, confira [Criar um perfil de distribuição](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) e [Selecionar um perfil de distribuição em um projeto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile).

## <a name="update-the-release-build-configuration"></a>Atualizar a configuração de build da Versão

Os novos projetos do Xamarin. iOS definem automaticamente **Release** _as configurações_de **depuração** e versão de compilação. Para configurar corretamente a compilação **Versão**, siga estas etapas:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, abra o arquivo **info. plist** localizado dentro do projeto do Ios. 
2. Verifique se você está na exibição do **aplicativo** . Isso pode ser definido clicando na opção na barra de navegação inferior.
3. Selecione **provisionamento manual** como a opção de assinatura. Salve e feche o arquivo.
4. Clique com o botão direito do mouse no **Nome do projeto** no **Painel de Soluções**, selecione **Opções** e navegue até a guia **Compilação do iOS**.
5. Defina a **Configuração** como **Versão** e **Plataforma** como **iPhone**.
6. Para compilar com um SDK específico do iOS, selecione-o na lista **Versão do SDK**. Caso contrário, deixe esse valor como **Padrão**.
7. A vinculação reduz o tamanho total do aplicativo ao eliminar o código não utilizado.
   * Na maioria dos casos, o **comportamento do vinculador** deve ser definido como o valor padrão **somente dos SDKs do link Framework**.
   * Usar a opção **não vincular** pode fazer com que a Apple rejeite o aplicativo devido à presença de APIs do IOS não públicas no Xamarin. Ios que seria vinculada com a opção **link Framework somente SDKs**
   * **Vincular tudo** deve ser usado com cuidado, pois ele removerá o código de todos os assemblies no projeto, incluindo as bibliotecas de terceiros. Ele pode distribuir o código que a biblioteca de terceiros só pode usar por meio de reflexão que o vinculador não pode detectar, como faz a análise de código estático para determinar qual código de biblioteca está sendo usado. Use **vincular tudo** com cuidado, pois você pode ter que preservar manualmente algumas classes e/ou métodos para evitar falhas de tempo de execução devido ao código ausente.
   * Para saber mais, consulte o guia [Vincular aplicativos do Xamarin.iOS](~/ios/deploy-test/linker.md).
8. Para o iOS 11, selecione uma das arquiteturas de dispositivo compatíveis com **ARM64**. Para saber mais sobre a compilação para dispositivos iOS de 64 bits, consulte a seção **Habilitar compilações de 64 bits de aplicativos Xamarin.iOS** da documentação [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md).
9. Talvez você queira usar o compilador **LLVM** para compilar código menor e mais rápido. No entanto, essa opção aumenta o tempo de compilação.
10. Confira **Otimizar imagens PNG** para diminuir ainda mais o tamanho do aplicativo.
11. A depuração _não_ deve ser habilitada, pois deixará o build maior, sem necessidade.
12. Com base nas necessidades do seu aplicativo, você também pode ajustar o tipo de **Coleta de Lixo** que está sendo usado e configurar para a **Internacionalização**.
    
    Depois de definir as opções conforme descrito acima, as configurações de Build devem ser semelhantes a esta:

    ![configurações de Build do iOS](publishing-to-the-app-store-images/build-m157.png "configurações de Build do iOS")

    Veja também o guia de [mecânica de Build do IOS](~/ios/deploy-test/ios-build-mechanics.md) , que descreve ainda mais as configurações de compilação.

13. Navegue até a guia **assinatura do pacote do IOS** . Se as opções aqui não forem editáveis, verifique se o **provisionamento manual** está selecionado no arquivo **info. plist** .
14. Verifique se a **Configuração** está definida como **Versão** e se a **Plataforma** está definida como **iPhone**.
15. Defina **Identidade de assinatura** como **Distribuição (automática)**.
16. Selecione o **Perfil de provisionamento** da App Store [criado acima](#create-and-install-an-app-store-provisioning-profile).

    As opções de assinatura de pacote do projeto serão parecidas com isto:

    ![Assinatura de pacote do iOS](publishing-to-the-app-store-images/bundleSigning-m157.png "Assinatura de pacote do iOS")

17. Clique em **OK** para salvar as alterações nas propriedades do projeto.

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

1. Verifique se o Visual Studio 2019 foi [emparelhado a um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Clique com o botão direito do mouse em **Nome do Projeto** no **Gerenciador de Soluções** e selecione **Propriedades**.
3. Navegue até a guia **iOS Build** e defina **Configuração** como **Versão** e **Plataforma** como **iPhone**.
4. Para criar com um SDK do iOS específico, selecione-o na lista **versão do SDK** . Caso contrário, deixe esse valor como **Padrão**.
5. A vinculação reduz o tamanho total do aplicativo ao eliminar o código não utilizado.
   * Na maioria dos casos, o **comportamento do vinculador** deve ser definido como o valor padrão **somente dos SDKs do link Framework**.
   * Usar a opção **não vincular** pode fazer com que a Apple rejeite o aplicativo devido à presença de APIs do IOS não públicas no Xamarin. Ios que seria vinculada com a opção **link Framework somente SDKs**
   * **Vincular tudo** deve ser usado com cuidado, pois ele removerá o código de todos os assemblies no projeto, incluindo as bibliotecas de terceiros. Ele pode distribuir o código que a biblioteca de terceiros só pode usar por meio de reflexão que o vinculador não pode detectar, como faz a análise de código estático para determinar qual código de biblioteca está sendo usado. Use **vincular tudo** com cuidado, pois você pode ter que preservar manualmente algumas classes e/ou métodos para evitar falhas de tempo de execução devido ao código ausente.
   * Para saber mais, consulte o guia [Vincular aplicativos do Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Para o iOS 11, selecione uma das arquiteturas de dispositivo compatíveis com **ARM64**. Para saber mais sobre a compilação para dispositivos iOS de 64 bits, consulte a seção **Habilitar compilações de 64 bits de aplicativos Xamarin.iOS** da documentação [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md).
7. Talvez você queira usar o compilador **LLVM** para compilar código menor e mais rápido. No entanto, essa opção aumenta o tempo de compilação.
8. Confira **Otimizar imagens PNG** para diminuir ainda mais o tamanho do aplicativo.
9. A depuração _não_ deve ser habilitada, pois deixará o build maior, sem necessidade.
10. Com base nas necessidades do seu aplicativo, você também pode ajustar o tipo de **Coleta de Lixo** que está sendo usado e configurar para a **Internacionalização**.

    Depois de definir as opções descritas acima, as configurações de build devem ser semelhantes a isto:

    ![configurações de Build do iOS](publishing-to-the-app-store-images/build-w157.png "configurações de Build do iOS")

    Veja também o guia de [mecânica de Build do IOS](~/ios/deploy-test/ios-build-mechanics.md) , que descreve ainda mais as configurações de compilação.

11. Navegue até a guia **assinatura do pacote do IOS** . Verifique se a **configuração** está definida como **versão**, a **plataforma** está definida como **iPhone**e se o **provisionamento manual** está selecionado.
12. Defina **Identidade de assinatura** como **Distribuição (automática)**.
13. Selecione o **Perfil de provisionamento** da App Store [criado acima](#create-and-install-an-app-store-provisioning-profile).

    As opções de assinatura de pacote do projeto serão parecidas com isto:

    ![configurações de assinatura de pacote do iOS](publishing-to-the-app-store-images/bundleSigning-w157.png "configurações de assinatura de pacote do iOS")

14. Salve a configuração de build e feche-a.

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

1. Verifique se o Visual Studio 2017 foi [emparelhado com um host de build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Clique com o botão direito do mouse em **Nome do Projeto** no **Gerenciador de Soluções** e selecione **Propriedades**.
3. Navegue até a guia **iOS Build** e defina **Configuração** como **Versão** e **Plataforma** como **iPhone**.
4. Para criar com um SDK do iOS específico, selecione-o na lista **versão do SDK** . Caso contrário, deixe esse valor como **Padrão**.
5. A vinculação reduz o tamanho total do aplicativo ao eliminar o código não utilizado.
   * Na maioria dos casos, o **comportamento do vinculador** deve ser definido como o valor padrão **somente dos SDKs do link Framework**.
   * Usar a opção **não vincular** pode fazer com que a Apple rejeite o aplicativo devido à presença de APIs do IOS não públicas no Xamarin. Ios que seria vinculada com a opção **link Framework somente SDKs**
   * **Vincular tudo** deve ser usado com cuidado, pois ele removerá o código de todos os assemblies no projeto, incluindo as bibliotecas de terceiros. Ele pode distribuir o código que a biblioteca de terceiros só pode usar por meio de reflexão que o vinculador não pode detectar, como faz a análise de código estático para determinar qual código de biblioteca está sendo usado. Use **vincular tudo** com cuidado, pois você pode ter que preservar manualmente algumas classes e/ou métodos, etc., para evitar falhas de tempo de execução devido a código ausente.
   * Para saber mais, consulte o guia [Vincular aplicativos do Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Para o iOS 11, selecione uma das arquiteturas de dispositivo compatíveis com **ARM64**. Para saber mais sobre a compilação para dispositivos iOS de 64 bits, consulte a seção **Habilitar compilações de 64 bits de aplicativos Xamarin.iOS** da documentação [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md).
7. Talvez você queira usar o compilador **LLVM** para compilar código menor e mais rápido. No entanto, essa opção aumenta o tempo de compilação.
8. Confira **Otimizar imagens PNG** para diminuir ainda mais o tamanho do aplicativo.
9. A depuração _não_ deve ser habilitada, pois deixará o build maior, sem necessidade.
10. Com base nas necessidades do seu aplicativo, você também pode ajustar o tipo de **Coleta de Lixo** que está sendo usado e configurar para a **Internacionalização**.

    Depois de definir as opções descritas acima, as configurações de build devem ser semelhantes a isto:

    ![configurações de Build do iOS](publishing-to-the-app-store-images/build-w157.png "configurações de Build do iOS")

    Veja também o guia de [mecânica de Build do IOS](~/ios/deploy-test/ios-build-mechanics.md) , que descreve ainda mais as configurações de compilação.

11. Navegue até a guia **assinatura do pacote do IOS** . Verifique se a **configuração** está definida como **versão**, a **plataforma** está definida como **iPhone**e se o **provisionamento manual** está selecionado.
12. Defina **Identidade de assinatura** como **Distribuição (automática)**.
13. Selecione o **Perfil de provisionamento** da App Store [criado acima](#create-and-install-an-app-store-provisioning-profile).

    As opções de assinatura de pacote do projeto serão parecidas com isto:

    ![configurações de assinatura de pacote do iOS](publishing-to-the-app-store-images/bundleSigning-w157.png "configurações de assinatura de pacote do iOS")

14. Navegue até a guia **Opções de IPA do iOS**.
15. Verifique se a **Configuração** está definida como **Versão** e se a **Plataforma** está definida como **iPhone**.
16. Marque a caixa de seleção **Compilar IPA (iTunes Package Archive)**. Essa configuração fará com que cada **Versão** de build (já que é a configuração selecionada) gere um arquivo .ipa. Esse arquivo pode ser enviado à Apple para lançamento na App Store.

    > [!NOTE]
    > **Metadados do iTunes** e **Arte do iTunes** não são necessários para as versões da App Store. Para saber mais, confira [O arquivo iTunesMetadata.plist em aplicativos Xamarin.iOS](~/ios/deploy-test/app-distribution/itunesmetadata.md) e [Arte do iTunes](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork).

17. Para especificar um nome de arquivo .ipa diferente do nome do projeto do Xamarin.iOS, insira-o no campo **Nome do pacote**.

    ![configurações de assinatura de pacote do iOS](publishing-to-the-app-store-images/ipaOptions-w157.png "configurações de assinatura de pacote do iOS")

18. Salve a configuração de build e feche-a.

-----

## <a name="configure-your-app-in-itunes-connect"></a>Configurar seu aplicativo no iTunes Connect

O [iTunes Connect](https://itunesconnect.apple.com) é um pacote de ferramentas baseadas na Web para gerenciar aplicativos iOS na App Store. Seu aplicativo Xamarin.iOS precisa ser definido e configurado corretamente no iTunes Connect antes de ser enviado à Apple para análise e ser liberado na App Store.

Para saber como fazer isso, leia o guia [Configurar um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="build-and-submit-your-app"></a>Criar e enviar seu aplicativo

Com as configurações de build configuradas corretamente e o iTunes Connect aguardando seu envio, agora você pode criar seu aplicativo e enviá-lo para a Apple.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No Visual Studio para Mac, selecione a configuração de build **Versão** e um dispositivo (não um simulador) no qual compilar.

    ![Configuração de compilação e seleção de plataforma](publishing-to-the-app-store-images/chooseConfig-m157.png "Configuração de compilação e seleção de plataforma")

2. No menu **Compilar** , selecione **arquivo morto para publicação**.
3. Depois que o arquivo for criado, a exibição de **arquivos mortos** será exibida. Clique em **Assinar e Distribuir...** para abrir o assistente de publicação.

    ![Captura de tela do local do botão assinar e distribuir na exibição de arquivos mortos.](publishing-to-the-app-store-images/archives-mac.png "Captura de tela do local do botão assinar e distribuir na exibição de arquivos mortos.")

    > [!NOTE]
    > Por padrão, a exibição **Arquivos** mostra somente os arquivos da solução aberta. Para ver todas as soluções que têm arquivos, marque a opção **Mostrar todos os arquivos** na caixa de seleção. É uma boa ideia manter arquivos antigos de forma que as informações de depuração que eles contêm possam ser usadas para simbolizar relatórios de falha, se necessário.

4. Selecione o canal de distribuição **App Store**. Clique em **Avançar**.

5. Selecione **carregar** como o destino. Clique em **Avançar**.

6. Na janela **Perfil de provisionamento**, selecione sua identidade de assinatura, aplicativo e perfil de provisionamento. Clique em **Avançar**.

    ![Captura de tela da página do assistente de perfil de provisionamento mostrando uma identidade de assinatura válida, aplicativo e seleção de perfil de provisionamento.](publishing-to-the-app-store-images/provProfileSelect-mac.png "Captura de tela da página do assistente de perfil de provisionamento com uma identidade de assinatura, aplicativo e perfil de provisionamento válidos selecionados.")

7. Na janela **informações de conexão da loja de aplicativos** , selecione um nome de usuário de ID da Apple no menu e insira [uma senha específica do aplicativo](https://support.apple.com/ht204397). Clique em **Avançar**.

    ![Captura de tela da página do assistente de informações do App Store Connect mostrando um nome de usuário da ID da Apple selecionado.](publishing-to-the-app-store-images/connectInfo-mac.png "Captura de tela da página do assistente de informações do App Store Connect mostrando um nome de usuário da ID da Apple selecionado.")

8. Verifique os detalhes do pacote e clique em **publicar**. Depois de selecionar um local para salvar o arquivo. ipa, o assistente carregará seu aplicativo para o App Store Connect.

    > [!NOTE]
    > A Apple pode rejeitar aplicativos com o **iTunesMetadata.plist** incluído no arquivo .ipa, o que resulta em um erro como este:
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Para conferir uma solução alternativa para esse erro, veja [esta postagem nos Fóruns do Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

> [!NOTE]
> A publicação na loja de aplicativos tem suporte no Visual Studio 2019 versão 16,3 e superior.

1. Verifique se o Visual Studio 2019 está [emparelhado a um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Selecione **liberar** na lista suspensa **configurações da solução** e **iPhone** na lista suspensa plataformas de **solução** .

    ![Captura de tela da barra de ferramentas do Visual Studio mostrando configuração de solução definida como versão, plataforma de solução definida como iPhone e destino definida como dispositivo.](publishing-to-the-app-store-images/chooseConfig-w157.png "Captura de tela da barra de ferramentas do Visual Studio mostrando configuração de solução definida como versão, plataforma de solução definida como iPhone e destino definida como dispositivo.")

3. No menu **Compilar** , selecione **arquivo morto...**. Isso abrirá o **Gerenciador de arquivos** e começará a criar um arquivo morto.

4. Depois que o arquivo for criado, clique em **distribuir...** para abrir o assistente de publicação.

    ![Captura de tela do local do botão distribuir na exibição do Gerenciador de arquivos.](publishing-to-the-app-store-images/archives-win.png "Captura de tela do local do botão distribuir na exibição do Gerenciador de arquivos.")

5. Selecione o canal de distribuição **App Store**.

6. Selecione sua identidade de assinatura e o perfil de provisionamento. Clique em **carregar para armazenar**.

    ![Captura de tela do assistente de publicação mostrando uma identidade de assinatura válida e seleção de perfil de provisionamento.](publishing-to-the-app-store-images/provProfileSelect-win.png "Captura de tela do assistente de publicação mostrando uma identidade de assinatura válida e seleção de perfil de provisionamento.")

7. Insira sua ID da Apple e [uma senha específica do aplicativo](https://support.apple.com/ht204397). Clique em **OK** para começar a carregar seu aplicativo para o App Store Connect.

    ![Captura de tela da janela pop-up para inserir sua ID da Apple e a senha específica do aplicativo.](publishing-to-the-app-store-images/connectInfo-win.png "Captura de tela da janela pop-up para inserir sua ID da Apple e a senha específica do aplicativo.")

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

> [!NOTE]
> O Visual Studio 2017 não oferece suporte ao fluxo de trabalho de publicação completo encontrado em Visual Studio para Mac e no Visual Studio 2019.
>
> As etapas a seguir são para o Xcode 10.
>
> Você ainda pode seguir as etapas abaixo para criar um. Arquivo IPA, mas para implantar na App Store usando o Xcode 11 (que é necessário para o suporte do iOS 13), você deve [usar Visual Studio para Mac](?tabs=macos#build-and-submit-your-app).

1. Verifique se o Visual Studio 2017 foi [emparelhado com um host de build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Selecione **Versão** no menu suspenso **Configurações da solução** do Visual Studio 2017, e **iPhone** no menu suspenso **Plataformas da solução**.

    ![Configuração de compilação e seleção de plataforma](publishing-to-the-app-store-images/chooseConfig-w157.png "Configuração de compilação e seleção de plataforma")

3. Compile o projeto. Isso cria um arquivo .ipa.

    > [!NOTE]
    > A seção [Configuração de build Atualizar a versão](#update-the-release-build-configuration) deste documento configurou as configurações de build do aplicativo para criar um arquivo .ipa para cada build de **Versão**.

4. Para localizar o arquivo .ipa no computador Windows, clique com o botão direito do mouse no nome do projeto Xamarin.iOS no **Gerenciador de Soluções** do Visual Studio 2019 ou do Visual Studio 2017 e escolha **Abrir Pasta no Explorador de Arquivos**. Em seguida, no **Explorador de Arquivos** do Windows, navegue até o subdiretório **bin/iPhone/Release**. A menos que você tenha [personalizado o local de saída de arquivo .ipa](#customize-the-ipa-location), ele deve estar nesse diretório.
5. Para, em vez disso, exibir o arquivo .ipa no host de build do Mac, clique com o botão direito do mouse no nome do projeto Xamarin.iOS no **Gerenciador de Soluções** no Visual Studio 2019 ou no Visual Studio 2017 (no Windows) e selecione **Mostrar arquivo IPA no servidor de build**. Isso abrirá uma janela **Localizador** no host de build do Mac com o arquivo .ipa selecionado.

    > [!TIP]
    >
    > As etapas a seguir só serão válidas se você estiver usando o Xcode 10 e Compilando para o iOS 12 e anterior.
    >
    > Para implantar na App Store usando o Xcode 11 (para iOS 13), você deve [usar Visual Studio para Mac](?tabs=macos#build-and-submit-your-app) para compilar e carregar seu aplicativo. O **carregador de aplicativos** não estará disponível para o Xcode 11.

6. No host de build do Mac, abra o **Carregador de Aplicativos**. No Xcode, selecione **Xcode > Abrir Ferramenta de Desenvolvedor > Carregador de Aplicativos**.

    > [!NOTE]
    > Para saber mais sobre a ferramenta, confira os [documentos da Apple sobre o Carregador de Aplicativo](https://help.apple.com/itc/apploader/#/apdS673accdb).

7. Faça logon no Carregador de Aplicativos (observe que é necessário [criar uma senha específica do aplicativo](https://support.apple.com/ht204397) para sua ID Apple).
8. Selecione **Entregar seu aplicativo** e clique no botão **Escolher**: 

    ![Selecionar Entregar seu aplicativo](publishing-to-the-app-store-images/publishvs01.png "Selecionar Entregar seu aplicativo")

9. Selecione o arquivo .ipa criado anteriormente e clique em **OK**.
10. O Application Loader validará o arquivo:

    ![A tela de validação](publishing-to-the-app-store-images/publishvs02.png "A tela de validação")

11. Clique no botão **Avançar** e o aplicativo será validados na App Store: 

    ![Validação na App Store](publishing-to-the-app-store-images/publishvs03.png "Validação na App Store")

12. Clique no botão **Enviar** para enviar o aplicativo para a Apple para análise.
13. O Application Loader informará quando o arquivo for carregado com êxito.

    > [!NOTE]
    > A Apple pode rejeitar aplicativos com o **iTunesMetadata.plist** incluído no arquivo .ipa, o que resulta em um erro como este:
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Para conferir uma solução alternativa para esse erro, veja [esta postagem nos Fóruns do Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

-----

## <a name="itunes-connect-status"></a>Status do iTunes Connect

Para ver o status de envio do aplicativo, faça logon no iTunes Connect e selecione seu aplicativo. O status inicial deve ser **Aguardando análise**, embora ele possa mostrar temporariamente **Upload recebido** enquanto está sendo processado.

![Aguardando revisão](publishing-to-the-app-store-images/image21.png "Aguardando revisão")

## <a name="tips-and-tricks"></a>Dicas e truques

### <a name="customize-the-ipa-location"></a>Personalizar o local do .ipa

Uma propriedade **MSBuild**, `IpaPackageDir`, possibilita a personalização do local de saída do arquivo .ipa. Se `IpaPackageDir` estiver definido como um local personalizado, o arquivo .ipa será colocado nesse local em vez de no subdiretório padrão com carimbo de data/hora. Isso pode ser útil ao criar builds automatizados que dependem de um caminho de diretório específico para funcionarem corretamente, como aqueles usados para builds de CI (Integração Contínua).

Há várias maneiras possíveis de se usar a nova propriedade. Por exemplo, para fazer a saída do arquivo .ipa no antigo diretório padrão (como no Xamarin.iOS 9.6 e inferior), você pode definir a propriedade `IpaPackageDir` como `$(OutputPath)` usando uma das abordagens a seguir. As duas abordagens são compatíveis com todos os builds do Xamarin.iOS de API Unificada, incluindo builds do IDE, bem como builds de linha de comando que usam **msbuild** ou **mdtool**:

- A primeira opção é definir a propriedade `IpaPackageDir` dentro de um elemento `<PropertyGroup>` em um arquivo **MSBuild**. Por exemplo, você poderia adicionar o seguinte `<PropertyGroup>` à parte inferior do arquivo .csproj do projeto de aplicativo do iOS (logo antes da marca de fechamento `</Project>`):

    ```xml
    <PropertyGroup>
      <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Uma abordagem melhor seria adicionar um elemento `<IpaPackageDir>` à parte inferior do `<PropertyGroup>` existente que corresponde à configuração usada para compilar o arquivo .ipa. Ela é melhor porque preparará o projeto para compatibilidade futura com uma configuração planejada na página de propriedades do projeto Opções do IPA do iOS. Se você estiver usando a configuração `Release|iPhone` para compilar o arquivo .ipa no momento, o grupo de propriedades completo atualizado poderá ficar parecido com o seguinte:

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone'">
       <Optimize>true</Optimize>
       <OutputPath>bin\iPhone\Release</OutputPath>
       <ErrorReport>prompt</ErrorReport>
       <WarningLevel>4</WarningLevel>
       <ConsolePause>false</ConsolePause>
       <CodesignKey>iPhone Developer</CodesignKey>
       <MtouchUseSGen>true</MtouchUseSGen>
       <MtouchUseRefCounting>true</MtouchUseRefCounting>
       <MtouchFloat32>true</MtouchFloat32>
       <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
       <MtouchLink>SdkOnly</MtouchLink>
       <MtouchArch>ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

Uma técnica alternativa para builds de linha de comando **msbuild** é adicionar um argumento de linha de comando `/p:` para definir a propriedade `IpaPackageDir`. Neste caso, observe que **msbuild** não expande as expressões `$()` passadas na linha de comando, portanto, não é possível usar a sintaxe `$(OutputPath)`. Em vez disso, você deve fornecer um nome de caminho completo.

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

Ou semelhante ao seguinte no Mac:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Com a compilação de distribuição criada e arquivada, agora você está pronto para enviar seu aplicativo para o iTunes Connect.

## <a name="summary"></a>Resumo

Este artigo descreveu como configurar, compilar e enviar um aplicativo iOS para lançamento na App Store.

## <a name="related-links"></a>Links relacionados

- [Apple Developer Portal (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [App Store Review Guidelines (Apple) (Diretrizes de análise da App Store [Apple])](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Common App Rejections (Apple) (Rejeições de aplicativo comuns [Apple])](https://developer.apple.com/app-store/review/rejections/)
- [Trabalhar com recursos no Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md)
- [Trabalhando com direitos no Xamarin. iOS](~/ios/deploy-test/provisioning/entitlements.md)
- [Configurando um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Ícones de aplicativo no Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Telas de inicialização para aplicativos Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
