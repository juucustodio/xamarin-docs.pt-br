---
title: "Distribuição Ad-Hoc"
description: "Este documento fornece uma visão geral das técnicas de distribuição Ad Hoc que são usados principalmente para testar aplicativos Xamarin.iOS com um grande grupo de pessoas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 3d01130989336ada855e936a6597b517fab5ee69
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="ad-hoc-distribution"></a>Distribuição Ad-Hoc

_Este documento fornece uma visão geral das técnicas de distribuição Ad Hoc que são usados principalmente para testar aplicativos Xamarin.iOS com um grande grupo de pessoas._

Depois que seu aplicativo Xamarin.iOS tiver sido desenvolvido, a próxima etapa do ciclo de vida de desenvolvimento de software é distribuir seu aplicativo aos usuários para teste.

O iTunes Connect é uma opção para gerenciar testes de aplicativo e é descrito mais detalhadamente na guia [TestFlight](~/ios/deploy-test/testflight.md). No entanto, os membros do Programa de Desenvolvedor Corporativo da Apple não têm acesso ao iTunes Connect, portanto a distribuição *Ad Hoc* é o melhor método para testas estes aplicativos.

Aplicativos Xamarin.iOS podem ser testados por usuário por meio da distribuição *ad-hoc*, que está disponível no Programa de Desenvolvedores da Apple e pelo Programa de Desenvolvedor Corporativo da Apple, permitindo que até 100 dispositivos iOS sejam testados.

A distribuição Ad hoc tem a vantagem de não exigir a aprovação da App Store e pode ser instalada via satélite de um servidor Web ou por meio do iTunes. No entanto, é limitada a **100** dispositivos por ano de associação, para o desenvolvimento e a distribuição, devendo ser adicionados manualmente na Central de Associados pelo UDID. Para obter mais informações sobre como adicionar dispositivos, visite a guia [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice).

A distribuição Ad Hoc requer que aplicativos sejam provisionados usando um *perfil de provisionamento* Ad Hoc que contém informações de assinatura de código, bem como a identidade do aplicativo e os dispositivos que podem instalar o aplicativo.

Este guia fornece informações sobre o provisionamento para distribuição Ad Hoc e informações sobre como distribuir um aplicativo Xamarin.iOS.

<a name="setup" />

## <a name="setting-up-for-distribution"></a>Configurar para distribuição

Mesmo se você pretende liberar um aplicativo Xamarin.iOS para implantação interna, para fins de teste, você precisará criar um Perfil de Provisionamento de Distribuição Ad Hoc específico para ele. Esse perfil permite que um aplicativo seja assinado digitalmente para lançamento de forma que ele pode ser instalado em um dispositivo iOS.

A próxima seção descreverá como configurar com um Certificado de Distribuição e um Perfil de Provisionamento de Distribuição.

> [!NOTE]
>  Observação: somente administradores e agentes de equipe podem criar certificados de distribuição e perfis de provisionamento.

<a name="createcertificate" />

## <a name="create-a-distribution-certificate"></a>Criar um certificado de distribuição


1. Navegue até a seção *Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)* do Apple Developer Member Center (Central de Associados do Desenvolvedor da Apple).
2. Em *Certificates (Certificados)*, selecione **Production (Produção)**.
3. Clique no botão **+** para criar um novo Certificado.
4. Sob o título *Produção*, selecione **Interna e Ad Hoc** ou **Loja de Aplicativos e Ad Hoc**, dependendo da sua associação ao programa:

  [![](ad-hoc-distribution-images/cert-first-small.png "Selecionar Interna e Ad Hoc ou App Store e Ad Hoc")](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. Clique em Continuar e siga as instruções para criar uma Solicitação de Assinatura de Certificado por meio do Acesso do Conjunto de Chaves:

  [![](ad-hoc-distribution-images/createcertmanually02.png "Criar uma Solicitação de Assinatura de Certificado por meio do Acesso do Conjunto de Chaves")](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. Depois de criar o CSR conforme a instrução, clique em Continuar e carregue o CSR na Central de Membros:

  [![](ad-hoc-distribution-images/createcertmanually03.png "Carregar o CSR para a Central de Membros")](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. Clique em Gerar para criar o certificado.
8. Por fim, escolha Download (Baixar) o certificado concluído e clique duas vezes no arquivo para instalá-lo.
9. Neste ponto, o certificado deve ser instalado no computador, mas talvez você precise [Refresh your profiles (Atualizar seus perfis)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) para garantir que eles estejam visíveis no Xcode.

Também é possível solicitar um Certificado usando a caixa de diálogo Preferências no Xcode. Para fazer isso, siga as etapas abaixo:

1.   Selecione sua equipe e clique em **Gerenciar Certificados...**: [![](ad-hoc-distribution-images/selectteam.png "Selecionar a equipe")](ad-hoc-distribution-images/selectteam.png#lightbox)

2.   Em seguida, clique no botão de **mais (+)** e selecione **iOS App Store**: [![](ad-hoc-distribution-images/selectcert.png "Selecionando a App Store do iOS")](ad-hoc-distribution-images/selectcert.png#lightbox)

<a name="createprofile" />

## <a name="create-a-distribution-provisioning-profile"></a>Criar um perfil de provisionamento de distribuição

<a name="createappid" />

### <a name="create-an-app-id"></a>Criar uma ID do aplicativo
Como com qualquer outro Perfil de Provisionamento que você criar, uma ID do Aplicativo será necessária para identificar o Aplicativo que você distribuirá para o dispositivo do usuário. Se ainda não tiver criado uma, siga as etapas abaixo para criá-la:


1. No [Apple Developer Center (Centro de Desenvolvedores da Apple)](https://developer.apple.com/account/overview.action), navegue até a seção *Certificate, Identifiers and Profiles (Certificados, Identificadores e Perfis)*. Selecione **App IDs (IDs do Aplicativo)** em **Identifiers (Identificadores)**.
2. Clique no botão **+** e forneça um **Name (Nome)** que o identificará no Portal.
3. O prefixo do aplicativo já deve estar definido como sua ID de Equipe e não pode ser alterado. Selecione uma ID do Aplicativo Explícita ou Curinga e insira uma ID de Pacote no formato DNS inverso, como:
    - **Explícito**: `com.[DomainName].[AppName]`
    - **Curinga**: `com.[DomainName].*`
4. Selecione [App Services (Serviços de Aplicativos)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) que seu aplicativo solicitar.
5. Clique no botão **Continuar** e siga as instruções na tela para criar a nova ID do Aplicativo.

Depois de obter os componentes obrigatórios necessários para criar um Perfil de Distribuição, siga as etapas abaixo para criá-lo:

1. Volte para o Portal de Provisionamento da Apple e selecione **Provisionamento > Distribuição**: [![](ad-hoc-distribution-images/distribute01.png "Selecionar Provisionamento > Distribuição")](ad-hoc-distribution-images/distribute01.png#lightbox)

2. Clique no botão **+** e selecione o tipo de Perfil de Distribuição que você deseja criar como **Ad Hoc**:

    [![](ad-hoc-distribution-images/distribute02.png "Criar um Tipo de distribuição Ad-Hoc")](ad-hoc-distribution-images/distribute02.png#lightbox)

3. Clique no botão **Continuar** e selecione a ID do Aplicativo na lista suspensa para a qual você deseja criar um Perfil de Distribuição:

    [![](ad-hoc-distribution-images/distribute03.png "Selecione a ID do aplicativo na lista suspensa")](ad-hoc-distribution-images/distribute03.png#lightbox)

4. Clique no botão **Continuar** e selecione o certificado de distribuição necessário para assinar o aplicativo:

    [![](ad-hoc-distribution-images/distribute04.png "Selecione o certificado de distribuição necessário para assinar o aplicativo")](ad-hoc-distribution-images/distribute04.png#lightbox)

6. Clique no botão **Continuar** e digite um **Nome** para o novo Perfil de Distribuição:

    [![](ad-hoc-distribution-images/distribute06.png "Insira um nome para o novo Perfil de Distribuição")](ad-hoc-distribution-images/distribute06.png#lightbox)

7. Clique no botão **Generate (Gerar)** para criar o novo perfil e finalizar o processo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Talvez você precise fechar Visual Studio para Mac e fazer com que o Xcode atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Baixando Perfis e Certificados no Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio para Mac.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Talvez você precise fechar Visual Studio e fazer com que o Xcode (no Mac do Host de Build) atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Baixando Perfis e Certificados no Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio.

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selecionando um perfil de distribuição em um projeto Xamarin.iOS

Quando você estiver pronto para fazer um build final de um aplicativo Xamarin.iOS, selecione o perfil de distribuição criado acima.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

 No Visual Studio para Mac, faça o seguinte:

1. Clique duas vezes no nome do projeto no **Gerenciador de Soluções** para abri-lo para edição.
2. Selecione **Assinatura do pacote iOS** e o tipo de build na lista suspensa **Configuração**:

    ![](ad-hoc-distribution-images/releasexs01.png "Selecione o tipo de build da lista suspensa Configuração")
3. Na maioria dos casos, a **Identidade de Assinatura** e o **Perfil de Provisionamento** podem ser deixados com um valor padrão de **Automático** e o Visual Studio para Mac escolherá o perfil correto com base no Identificador de Pacote na Info.plist:

    ![](ad-hoc-distribution-images/releasexs02.png "A Identidade de assinatura e o Perfil de provisionamento definidos como os valores padrão de Automático")
4. Se necessário, selecione a Identidade de Assinatura e o Perfil de Distribuição (aquele criado acima) nas listas suspensas:

    ![](ad-hoc-distribution-images/releasexs03.png "Selecione a Identidade de assinatura e o Perfil de distribuição")
5. Clique no botão **OK** para salvar as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
 No Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse no nome do projeto no **Gerenciador de Soluções** e selecione **Propriedades** para abri-lo para edição.
2. Selecione **Assinatura do pacote iOS** e o tipo de build na lista suspensa **Configuração**:

    ![](ad-hoc-distribution-images/releasevs01.png "Selecione o tipo de build da lista suspensa Configuração")
3. Na maioria dos casos, a **Identidade de Assinatura** e o **Perfil de Provisionamento** podem ser deixados com um valor padrão de **Automático** e o Visual Studio escolherá o perfil correto com base no Identificador de Pacote na Info.plist:

    ![](ad-hoc-distribution-images/releasevs02.png "A Identidade de assinatura e o Perfil de provisionamento definidos como os valores padrão de Automático")
4. Se necessário, selecione a Identidade de Assinatura e o Perfil de Distribuição (aquele criado acima) nas listas suspensas:

    ![](ad-hoc-distribution-images/releasevs03.png "Selecione a Identidade de assinatura e o Perfil de distribuição")
5. Salve suas alterações nas Propriedades do projeto.

-----

<a name="adhoc" />

## <a name="ad-hoc-distribution"></a>Distribuição Ad Hoc

Enquanto [TestFlight](~/ios/deploy-test/testflight.md) é um meio popular de teste beta e distribuição, ele faz parte do iTunes Connect e, portanto, não está disponível para membros do **Programa de Desenvolvedor Corporativo da Apple**.

A Distribuição Ad Hoc permite que os desenvolvedores de aplicativos executem teste beta em uma grande variedade de dispositivos quando se o iTunes Connect não é uma opção. Ad-Hoc funciona de maneira semelhante à Distribuição Interna e requer que um IPA seja criado, que pode ser distribuído via satélite ou manualmente por meio do iTunes.

<a name="IPA_Creation" />

### <a name="ipa-support-for-ad-hoc-deployment"></a>Suporte de IPA para Implantação Ad Hoc

Depois de provisionados, aplicativos podem ser empacotados em um arquivo conhecido como um *IPA*. Este é um arquivo zip que contém o aplicativo, juntamente com metadados adicionais e ícones. O IPA é usado para adicionar um aplicativo localmente no iTunes para que ele pode ser sincronizado diretamente em um dispositivo que está incluído no perfil de provisionamento.

Para obter mais informações sobre como criar um IPA, consulte [Suporte do IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="summary"></a>Resumo

Este artigo explicou os mecanismos de distribuição Ad Hoc, que são necessários para testar aplicativos Xamarin.iOS.


## <a name="related-links"></a>Links relacionados

- [Distribuição da App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [O arquivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Suporte a IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
