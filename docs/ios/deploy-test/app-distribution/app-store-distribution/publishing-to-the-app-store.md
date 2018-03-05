---
title: Publicando na App Store
description: "Este artigo mostra como configurar, compilar e publicar um aplicativo Xamarin.iOS para distribuição através da App Store. Ele inclui um guia passo a passo que aborda como preparar seu aplicativo para distribuição, como usar ferramentas da Apple para enviar seu aplicativo para análise e, finalmente, como publicar seu aplicativo na App Store."
ms.topic: article
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: 84db17ede0019e1134b65edaca85ef2401fb3bc0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="publishing-to-the-app-store"></a>Publicando na App Store

_Este artigo mostra como configurar, compilar e publicar um aplicativo Xamarin.iOS para distribuição através da App Store. Ele inclui um guia passo a passo que aborda como preparar seu aplicativo para distribuição, como usar ferramentas da Apple para enviar seu aplicativo para análise e, finalmente, como publicar seu aplicativo na App Store._

Para distribuir aplicativos para todos os dispositivos iOS, a Apple exige que os aplicativos sejam publicados por meio da *App Store*, tornando a App Store o local de compra único para aplicativos iOS. Com mais de 500.000 aplicativos na loja, os desenvolvedores de muitos tipos de aplicativos tiraram proveito do enorme sucesso desse único ponto de distribuição. A App Store é uma solução pronta para uso, que oferece sistemas de distribuição e pagamento aos desenvolvedores de aplicativos.

O processo de envio de um aplicativo para a App Store envolve:

1. Criar uma **ID do aplicativo** e a selecionar **Direitos**.
2. Criando um **Perfil de Provisionamento de Distribuição**.
3. Usar esse perfil para compilar seu aplicativo.
4. Enviar seu aplicativo por meio do **iTunes Connect**.


Este artigo aborda todas as etapas necessárias para provisionar, compilar e enviar um aplicativo para a distribuição da App Store.

## <a name="before-you-submit-an-application"></a>Antes de enviar um aplicativo

Depois de enviar um aplicativo para publicação na App Store, ele passa por um processo de análise pela Apple para garantir que ele atende às diretrizes de qualidade e conteúdo da Apple. Se o aplicativo não atender a essas diretrizes, a Apple o rejeitará e, em seguida, você precisará resolver as não conformidades citadas pela Apple e reenviar o aplicativo.
Portanto, você terá mais chance de fazê-lo passar pela análise da Apple ao familiarizar-se com essas diretrizes e tentar adaptar seu aplicativo a elas. As diretrizes da Apple estão disponíveis em: [Diretrizes de análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html).

Algumas coisas a observar ao enviar um aplicativo:

1. Verifique se a descrição do aplicativo corresponde à funcionalidade incluída no aplicativo.
2. Teste para ver se o aplicativo não falha em uso normal. Isso inclui o uso em cada dispositivo iOS que você der suporte.


A Apple também mantém uma lista de dicas de envio da App Store. Você pode ler essas dicas em [Distribuindo na App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

## <a name="configuring-your-application-in-itunes-connect"></a>Configurando seu aplicativo no iTunes Connect

O [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) é um pacote de ferramentas baseadas na Web para gerenciamento dos seus aplicativos iOS na App Store, entre outras coisas. Seu aplicativo Xamarin.iOS precisará ser definido e configurado corretamente no iTunes Connect antes de ser enviado à Apple para análise e, por fim, ser liberado para venda ou como um aplicativo gratuito na App Store.

Faça o seguinte:

1. Verifique se os contratos apropriados estão em vigor e atualizados na seção **Agreements, Tax, and Banking (Contratos, impostos e transações bancárias)** do iTunes Connect para lançar um aplicativo iOS gratuitamente ou para venda.
2. Crie um novo **iTunes Connect Record (Registro do iTunes Connect)** para o aplicativo e especifique seu **Display Name (Nome de exibição)** (como visto na App Store).
3. Selecione um **Sale Price (Preço de venda)** ou especifique que o aplicativo será liberado gratuitamente.
5. Forneça uma **Description (Descrição)** clara e sucinta do aplicativo, incluindo seus recursos e benefícios para o usuário final.
6. Forneça **Categories (Categorias)**, **Sub Categories (Subcategorias)** e **Keywords (Palavras-chave)** para ajudar o usuário a localizar o aplicativo na App Store.
7. Forneça URLs de **Contact (Contato)** e **Support (Suporte)** para o seu site exigido pela Apple.
8. Defina a **Rating (Classificação)** do aplicativo, que é usada pelos controles dos pais na App Store.
9. Configure tecnologias opcionais da App Store, como **Game Center** e **In-App Purchase (Compra no aplicativo)**.

Para obter mais detalhes, consulte a documentação [Configurando um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="preparing-for-app-store-distribution"></a>Preparando para a distribuição da App Store

Para publicar um aplicativo na App Store, primeiro, é necessário compilá-lo para distribuição, o que envolve várias etapas. As seções a seguir abordam tudo o que é necessário para preparar um aplicativo Xamarin.iOS para a publicação, para que ele seja compilado e enviado à App Store para análise e lançamento.

### <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de Serviços de aplicativos especiais, também chamada de direitos, que podem ser ativados para o seu aplicativo iOS quando você cria uma ID exclusiva para ele. Se estiver ou não estiver usando os direitos personalizados, ainda assim é necessário criar uma ID exclusiva para seu aplicativo Xamarin.iOS antes de publicá-lo na App Store.

A criação de uma ID do aplicativo e, opcionalmente, selecionar direitos, envolve as seguintes etapas usando o Portal de provisionamento iOS baseado na Web da Apple:

1. Na seção **Certificates, Identifiers & Profiles (Certificados, identificadores e perfis)** selecione **Identifiers (Identificadores)** > **App ID (ID do aplicativo)**.
2. Clique no botão **+** e forneça um **Name (Nome)** e uma **Bundle ID (ID do pacote)** para o novo aplicativo.
3. Role até a parte inferior da tela e selecione os **App Services (Serviços de aplicativos)** que serão necessários ao seu aplicativo Xamarin.iOS.
4. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do aplicativo.

Além de selecionar e configurar os Serviços de aplicativos necessários ao definir a ID do aplicativo, você também precisa configurar a ID e os direitos do aplicativo no seu projeto Xamarin.iOS, editando os arquivos `Info.plist` e `Entitlements.plist`.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Info.plist` para abri-lo para edição.
2. Na seção **Destino do aplicativo iOS**, preencha um nome para o seu aplicativo e insira o **Identificador de pacote** que você criou quando definiu a ID do aplicativo.
3. Salve as alterações no arquivo `Info.plist`.
4. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Entitlements.plist` para abri-lo para edição.
5. Selecione e configure os direitos necessários para o aplicativo Xamarin.iOS de maneira que correspondam à configuração que foi feita acima, quando você definiu a ID do aplicativo.
6. Salve as alterações no arquivo `Entitlements.plist`.

Para obter instruções detalhadas, consulte a documentação [Provisionamento para Serviços de Aplicativo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices).

### <a name="setting-the-store-icons"></a>Configurar os Ícones de Loja

Os ícones de Lojas de Aplicativos devem ser entregues agora por um catálogo de ativos. Para adicionar um ícone da App Store, primeiro localize a imagem **AppIcon** definida no arquivo **Assets.xcassets** do seu projeto.

O ícone necessário no Catálogo de Ativos é chamado **App Store** e deve ter **1024 x 1024** de tamanho. A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

Para obter informações sobre como configurar o ícone da loja, confira o guia [Ícone da App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md).

### <a name="setting-the-apps-icons-and-launch-screens"></a>Configurando ícones e telas de inicialização dos aplicativos

Para que um aplicativo iOS seja aceito pela Apple para inclusão na App Store, são necessários ícones e telas de inicialização apropriados para todos os dispositivos iOS nos quais o aplicativo será executado. Os ícones de aplicativos são adicionados aos seus projetos em um catálogo de ativos por meio de uma imagem **AppIcon** definida no arquivo **Assets.xcassets**. As telas de inicialização são adicionadas por meio de um Storyboard.

Para obter instruções detalhadas sobre a criação de ícones de aplicativos e telas de inicialização, confira os guias [Ícone do Aplicativo](~/ios/app-fundamentals/images-icons/app-icons.md) e [Telas de Inicialização](~/ios/app-fundamentals/images-icons/launch-screens.md).

### <a name="creating-and-installing-a-distribution-profile"></a>Criando e instalando um perfil de distribuição

O iOS usa *perfis de provisionamento* para controlar como a compilação de um determinado aplicativo pode ser implantada. Esses são arquivos que contêm informações sobre o certificado usado para assinar um aplicativo, a *ID do aplicativo* e informações sobre onde o aplicativo pode ser instalado. Para desenvolvimento e distribuição ad hoc, o perfil de provisionamento também inclui a lista de dispositivos permitidos para os quais você pode implantar o aplicativo. No entanto, para a distribuição da App Store, somente informações de certificado e ID do aplicativo são incluídas, pois o único mecanismo de distribuição pública é através da App Store.

O provisionamento envolve as seguintes etapas usando o Portal de provisionamento do iOS baseado na Web da Apple:

1.  Selecione **Provisionamento** > **Distribuição**.
2.  Clique no botão **+** e selecione o tipo de Perfil de distribuição que você deseja criar como **App Store**.
3.  Selecione na lista suspensa a **ID do aplicativo** para a qual você deseja criar um Perfil de distribuição.
4.  Selecione um certificado válido de Produção (Distribuição) para assinar o aplicativo.
5.  Insira um **Nome** para o novo **Perfil de distribuição** e gere o perfil.
6.  No Mac, abra o Xcode e navegue até **Preferências > [selecione sua ID da Apple] > Exibir detalhes...**. Baixar todos os perfis disponíveis no Xcode no Mac.
7.  Retorne ao IDE e, nas opções **Assinatura do Pacote iOS**, selecione o Perfil de Provisionamento de Distribuição para a correta _Configuração de build_ (que será **App Store** ou **Lançamento**).

Para obter instruções detalhadas, consulte [Criando um perfil de distribuição](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) e [Selecionando um perfil de distribuição em um projeto Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile).

## <a name="setting-the-build-configuration-for-your-application"></a>Definindo a configuração de build para o seu aplicativo

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Faça o seguinte:

1. Clique com o botão direito do mouse no **Nome do Projeto** no **Painel de Soluções** e selecione **Opção** para abri-lo para edição.
2. Selecione **Build do iOS** e **Versão | iPhone** no menu suspenso **Configuração**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selecione a AppStore na lista suspensa Configuração")

3. Se você tiver uma versão de destino específica do iOS, você poderá selecioná-la na lista suspensa **Versão do SDK** ou deixar esse valor na configuração padrão.
4. A vinculação reduz o tamanho geral do distribuível do seu aplicativo, eliminando métodos, propriedades, classes, etc. não utilizados e, na maioria dos casos, deve ser deixada no valor padrão de **Vincular somente assemblies do SDK**. Em algumas situações, como ao usar algumas bibliotecas de terceiros específicas, você poderá ser forçado a definir esse valor como **Não vincular** para impedir que elementos necessários sejam removidos. Para obter mais informações, consulte o guia [iOS Build Mechanics](~/ios/deploy-test/ios-build-mechanics.md) (Mecânica de build do iOS).
5. A caixa de seleção **Otimizar arquivos de imagem PNG para iOS** deve ser marcada, pois isso ajudará a diminuir ainda mais tamanho da entrega do seu aplicativo.
6. A depuração _não_ deve ser habilitada, pois deixará o build maior, sem necessidade.
8. Para o iOS 11, é preciso selecionar uma das arquiteturas de dispositivos com suporte para **ARM64**. Para obter mais informações sobre como compilar para dispositivos iOS de 64 bits, consulte a seção **Habilitando compilações de 64 bits de aplicativos Xamarin.iOS** da documentação [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64.md).
9. Você pode optar por usar o compilador **LLVM**, que cria um código menor e mais rápido, mas ele levará mais tempo para compilar.
10. Com base nas necessidades do seu aplicativo, você também pode ajustar o tipo de **Coleta de Lixo** que está sendo usado e configurar para a **Internacionalização**.
11. Salve as alterações na configuração de build.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por padrão, quando você cria um novo aplicativo Xamarin.iOS no Visual Studio, as _Configurações de build_ são criadas automaticamente para as implantações **Ad Hoc** e **App Store**. Antes de fazer o build final do aplicativo que será enviado para a Apple, há algumas modificações que você precisará fazer na configuração básica.

Faça o seguinte:

1. Clique com o botão direito do mouse no **Nome do projeto** no **Gerenciador de Soluções** e selecione **Propriedades**, para abri-lo para edição.
2. Selecione **Build do iOS** e **AppStore** (ou **Versão** se AppStore não estiver disponível) no menu suspenso **Configuração**:

    ![](publishing-to-the-app-store-images/configurevs01.png "Selecione a AppStore na lista suspensa Configuração")

3. Se você tiver uma versão de destino específica do iOS, você poderá selecioná-la na lista suspensa **Versão do SDK** ou deixar esse valor na configuração padrão.
4. A vinculação reduz o tamanho geral do distribuível do seu aplicativo, eliminando métodos, propriedades, classes, etc. não utilizados e, na maioria dos casos, deve ser deixada no valor padrão de **Vincular somente assemblies do SDK**. Em algumas situações, como ao usar algumas bibliotecas de terceiros específicas, você poderá ser forçado a definir esse valor como **Não vincular** para impedir que elementos necessários sejam removidos. Para obter mais informações, consulte o guia [iOS Build Mechanics](~/ios/deploy-test/ios-build-mechanics.md) (Mecânica de build do iOS).
5. A caixa de seleção **Otimizar arquivos de imagem PNG para iOS** deve ser marcada, pois isso ajudará a diminuir ainda mais tamanho da entrega do seu aplicativo.
6. A depuração _não_ deve ser habilitada, pois deixará o build maior, sem necessidade.
7. Clique na guia **Avançado**:

    ![](publishing-to-the-app-store-images/configurevs02.png "A guia Avançado")

8. Se seu aplicativo Xamarin.iOS destina-se aos dispositivos iOS 8 e iOS 64 bits, será necessário selecionar uma das arquiteturas de dispositivo que oferece suporte a **ARM64**. Para obter mais informações sobre como compilar para dispositivos iOS de 64 bits, consulte a seção **Habilitando compilações de 64 bits de aplicativos Xamarin.iOS** da documentação [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64.md).
9. Você pode optar por usar o compilador **LLVM**, que cria um código menor e mais rápido, mas ele levará mais tempo para compilar.
10. Com base nas necessidades do seu aplicativo, você também pode ajustar o tipo de **Coleta de Lixo** que está sendo usado e configurar para a **Internacionalização**.
11. Salve as alterações na configuração de build.

-----

## <a name="building-and-submitting-the-distributable"></a>Criando e enviando o distribuível

Agora que o seu aplicativo Xamarin.iOS configurado corretamente, é possível fazer o build final de distribuição que será enviada para a Apple para análise e lançamento.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="build-your-archive"></a>Compilar seu arquivo morto

1. Selecione a configuração **Versão | Dispositivo** no Visual Studio para Mac:

    ![](publishing-to-the-app-store-images/buildxs01new.png "Selecione a versão | Configuração do dispositivo")
1. No menu **Compilar**, selecione **Arquivo para Publicação**:

    ![](publishing-to-the-app-store-images/buildxs02new.png "Selecione Arquivo Morto para Publicação")

1. Quando o arquivo tiver sido criado, a exibição **Arquivos Mortos** será exibida:

    ![](publishing-to-the-app-store-images/buildxs03new.png "O modo de exibição de Arquivo morto será exibido")


> [!NOTE]
> Observação: embora as configurações antigas _App Store_ e _Ad Hoc_ tenham sido removidas de todos os projetos de modelo do Visual Studio para Mac, você pode achar que projetos mais antigos ainda incluam essas configurações. Se esse for o caso, você poderá continuar a usar a configuração **App Store | Dispositivo** na etapa 1 da lista acima.

### <a name="sign-and-distribute-your-app"></a>Assinar e distribuir seu aplicativo

 Sempre que você compilar seu aplicativo para o Arquivo Morto, ele abrirá automaticamente a **Exibição de Arquivos Mortos**, exibindo todos os projetos arquivados, agrupados por solução. Por padrão esta exibição mostra apenas a solução atual aberta. Para ver todas as soluções que têm arquivos, clique na opção **Mostrar todos os arquivos mortos**.

 É recomendável que os arquivos implantados para os clientes (implantações App Store ou Empresarial) sejam guardados, para que qualquer informação de depuração que for gerada posa ser indicada em uma data posterior.

 Para assinar seu aplicativo e prepará-lo para distribuição:


1. Selecione **Assinar e Distribuir...**, como ilustrado abaixo:

    ![](publishing-to-the-app-store-images/buildxs04new.png "Selecione Assinar e Distribuir...")

1. Isso abrirá o assistente de publicação. Selecione o canal de distribuição da **App Store** para criar um pacote e abra o Application Loader:

    ![](publishing-to-the-app-store-images/distribute01.png "Abra o Carregador de Aplicativos")

1. Na tela Perfil de Provisionamento, selecione sua identidade de assinatura e o perfil de provisionamento correspondente ou repita a assinatura com outra identidade:

    ![](publishing-to-the-app-store-images/distribute02.png "Selecione a identidade de assinatura e o perfil de provisionamento correspondente")

1. Verifique os detalhes do seu pacote e, em seguida, clique em **Publicar** para salvar seu pacote `.ipa`:

    ![](publishing-to-the-app-store-images/distribute03.png "Verifique os detalhes do pacote")

1. Uma vez que o `.ipa` tiver sido salvo, seu aplicativo está pronto para ser carregado no iTunes Connect através do Application Loader:

    ![](publishing-to-the-app-store-images/distribute04.png "A tela Publicação bem-sucedida")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No momento, o plug-in Xamarin para o Visual Studio não dá suporte ao fluxo de trabalho Arquivamento para a publicação de aplicativos iOS na App Store. Como resultado, você carregou um IPA criado por meio do comando **Compilar IPA Ad hoc**, como descrito abaixo.


## <a name="build-an-ipa"></a>Compilar um IPA

 Esta seção descreve a compilação de um IPA, com fluxo de trabalho semelhante a quando se usa a distribuição Ad Hoc ou distribuição Corporativa. No entanto, ele será assinado usando o perfil de provisionamento da App Store que foi criado acima.

 Faça o seguinte:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto Xamarin.iOS e selecione **Propriedades** para abri-lo para edição:

    ![](publishing-to-the-app-store-images/imagevs01.png "Selecione Propriedades")
1. Selecione **Assinatura do Pacote do iOS** e altere o perfil de provisionamento para um perfil de provisionamento da Loja de Aplicativos:

    ![](publishing-to-the-app-store-images/ipa01.png "Selecione Assinatura de Pacote do iOS e altere o perfil de provisionamento para um perfil de provisionamento da App Store")
1. Selecione **Opções de IPA de iOS** e selecione **Ad-Hoc** na lista suspensa **Configuração** (se Ad-Hoc não for exibido, selecione **Lançar** em vez disso):

    ![](publishing-to-the-app-store-images/imagevs02.png "Selecione Ad-Hoc na lista suspensa Configuração")

1. Opcionalmente, você pode especificar um **Nome do Pacote** para o IPA. Se não for especificado, ele terá o mesmo nome que o projeto Xamarin.iOS.
1. Salve suas alterações às Propriedades do Projeto.
1. Selecione **Ad Hoc** na lista suspensa **Configuração de build** do Visual Studio para Mac:

    ![](publishing-to-the-app-store-images/imagevs05.png "Selecione Ad Hoc na lista suspensa Configuração de Build")
1. Compile o projeto para criar o pacote IPA.
1. O IPA será compilado na pasta `Bin` > _Dispositivo iOS_ > `Ad Hoc`:

    ![](publishing-to-the-app-store-images/imagevs06.png "O IPA mostrado no explorador de arquivos")

-----


## <a name="customizing-the-ipa-location"></a>Personalizando o local de IPA

Uma nova propriedade `IpaPackageDir` do **MSBuild** foi adicionada para facilitar a personalização do local de saída do arquivo `.ipa`. Se `IpaPackageDir` for definida para um local personalizado, o arquivo `.ipa` será colocado nesse local, em vez do subdiretório padrão de carimbo de data/hora. Isso pode ser útil ao criar builds automatizados que dependem de um caminho de diretório específico para funcionarem corretamente, como aqueles usados para builds de CI (Integração Contínua).

Há várias maneiras possíveis de se usar a nova propriedade:

Por exemplo, para fazer a saída do arquivo `.ipa` no antigo diretório padrão (como no Xamarin.iOS 9.6 e inferior), você pode definir a propriedade `IpaPackageDir` como `$(OutputPath)`, usando uma das abordagens a seguir. As duas abordagens são compatíveis com todas as compilações de API Unificada do Xamarin.iOS, incluindo compilações do IDE, bem como as compilações de linha de comando que usam `xbuild`, `msbuild` ou `mdtool`:

  - A primeira opção é definir a propriedade `IpaPackageDir` dentro de um elemento `<PropertyGroup>` em um arquivo **MSBuild**. Por exemplo, você poderia adicionar o seguinte `<PropertyGroup>` na parte inferior do arquivo `.csproj` do projeto de aplicativo do iOS (um pouco antes da marca de fechamento `</Project>`):

      ```xml
        <PropertyGroup>
            <IpaPackageDir>$(OutputPath)</IpaPackageDir>
        </PropertyGroup>
      ```
  - Uma abordagem melhor seria adicionar um elemento `<IpaPackageDir>` na parte inferior do `<PropertyGroup>` existente que corresponde à configuração usada para criar o arquivo `.ipa`. Ela é melhor porque preparará o projeto para compatibilidade futura com uma configuração planejada na página de propriedades do projeto Opções do IPA do iOS. Se você estiver usando a configuração `Release|iPhone` para compilar o arquivo `.ipa`, o grupo de propriedades completo atualizadas poderá ficar parecido com o seguinte:

      ```xml
      <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' )
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
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
      </PropertyGroup>
      ```
Uma técnica alternativa para compilações de linha de comando `msbuild` ou `xbuild` é adicionar um argumento de linha de comando `/p:` para definir a propriedade `IpaPackageDir`. Neste caso, observe que `msbuild` não expande as expressões `$()` passadas na linha de comando, portanto, não é possível usar a sintaxe `$(OutputPath)`. Em vez disso, você deve fornecer um nome de caminho completo. O comando `xbuild` do Mono expande as expressões `$()`, mas ainda é melhor usar um nome de caminho completo, pois o `xbuild` eventualmente será preterido em favor da [versão de plataforma cruzada do `msbuild`](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x) em versões futuras. Um exemplo completo que usa essa abordagem pode ser semelhante ao seguinte no Windows:

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

Ou semelhante ao seguinte no Mac:

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Com a compilação de distribuição criada e arquivada, agora você está pronto para enviar seu aplicativo para o iTunes Connect.

### <a name="automatically-copy-app-bundles-back-to-windows"></a>Copiar automaticamente os pacotes do .app para o Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="submitting-your-app-to-apple"></a>Enviando seu aplicativo para a Apple

> [!NOTE]
> Observação: a Apple alterou recentemente seu processo de verificação de aplicativos iOS e pode rejeitar aplicativos com o `iTunesMetadata.plist` incluído no IPA. Se você encontrar o erro `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`, a solução alternativa descrita [aqui](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) deverá resolver o problema.

Com a compilação de distribuição concluída, você está pronto para enviar o seu aplicativo iOS para a Apple para análise e lançamento na App Store.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 Faça o seguinte:

1. Inicie o **Xcode**.
2. No menu **Janela**, selecione **Organizador**.
3. Clique na guia **Arquivos** e selecione o arquivo que foi compilado acima:

    ![](publishing-to-the-app-store-images/publishxs01.png "Selecione o arquivo a ser enviado")
4. Clique no botão **Validar...**.
5. Selecione a conta a ser verificada e clique no botão **Escolher**:

    ![](publishing-to-the-app-store-images/publishxs02.png "Selecione a conta a ser verificada")
6. Clique no botão **Validar**:

    ![](publishing-to-the-app-store-images/publishxs03.png "A caixa de diálogo de resumo do arquivo")
7. Se houver problemas com o pacote, eles serão exibidos.
8. Corrija todos os problemas e recompile o arquivo no Visual Studio para Mac.
9. Clique no botão **Enviar...**.
10. Selecione a conta a ser verificada e clique no botão **Escolher**:

    ![](publishing-to-the-app-store-images/publishxs04.png "Selecione a conta a ser verificada")
11. Clique no botão **Enviar**:

    ![](publishing-to-the-app-store-images/publishxs05.png "A caixa de diálogo de resumo do arquivo")
12. O Xcode informará quando o carregamento do arquivo no iTunes Connect for concluído.


O fluxo de trabalho de arquivo no Visual Studio para Mac abrirá o Application Loader automaticamente, assim que você tiver salvo o _.ipa_:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O envio do aplicativo à Apple para análise é feito usando o aplicativo Carregador de Aplicativos. Essas etapas devem ser feitas no host de build do Mac. Você pode baixar uma cópia do Application Loader [aqui](https://itunesconnect.apple.com/apploader/ApplicationLoader_3.0.dmg).

-----

1. Selecione *Entregar seu aplicativo* e clique no botão *Escolher*:

    [ ![](publishing-to-the-app-store-images/publishvs01.png "Selecione Entregar seu aplicativo")](publishing-to-the-app-store-images/publishvs01.png)

2. Selecione o arquivo zip ou IPA criado anteriormente e clique no botão **OK**.

3. O Application Loader validará o arquivo:

    [ ![](publishing-to-the-app-store-images/publishvs02.png "A tela de validação")](publishing-to-the-app-store-images/publishvs02.png)
4. Clique no botão *Avançar* e o aplicativo será validados na App Store:

    [ ![](publishing-to-the-app-store-images/publishvs03.png "Validação em relação à App Store")](publishing-to-the-app-store-images/publishvs03.png)
5. Clique no botão **Enviar** para enviar o aplicativo para a Apple para análise.
6. O Application Loader informará quando o arquivo for carregado com êxito.

## <a name="itunes-connect-status"></a>Status de conexão do iTunes

Se você efetuar logon novamente no iTunes Connect e selecionar o seu aplicativo na lista de aplicativos disponíveis, o status no iTunes Connect agora deve mostrar que o aplicativo está **Aguardando Análise** (ele pode mostrar temporariamente **Upload recebido** durante o processamento):

[ ![](publishing-to-the-app-store-images/image21.png "Agora o status no iTunes Connect deve mostrar que está Aguardando Análise")](publishing-to-the-app-store-images/image21.png)

## <a name="summary"></a>Resumo

Este artigo apresentou um guia passo a passo para configurar, compilar e enviar um aplicativo para publicação na App Store. Primeiro, ele abordou as etapas necessárias para criar e instalar um perfil de provisionamento de distribuição. Em seguida, discorreu sobre como usar o Visual Studio e o Visual Studio para Mac para criar uma compilação de distribuição. Por fim, mostrou como usar iTunes Connect e a ferramenta para enviar um aplicativo para a App Store.


## <a name="related-links"></a>Links relacionados

- [Trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md)
- [Guia de fluxo de trabalho de desenvolvimento de Aplicativo iOS: distribuição de aplicativos](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rejeições de Aplicativo Comuns](https://developer.apple.com/app-store/review/rejections/)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
