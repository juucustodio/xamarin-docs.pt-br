---
title: Distribuição Ad Hoc para aplicativos Xamarin.iOS
description: Este documento fornece uma visão geral das técnicas de distribuição Ad Hoc que são usados principalmente para testar aplicativos Xamarin.iOS com um grande grupo de pessoas.
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: ae3c60699f7e31c66893723ac73248ced9a2d72a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567964"
---
# <a name="ad-hoc-distribution-for-xamarinios-apps"></a>Distribuição Ad Hoc para aplicativos Xamarin.iOS

_Este documento fornece uma visão geral das técnicas de distribuição Ad Hoc que são usados principalmente para testar aplicativos Xamarin.iOS com um grande grupo de pessoas._

Depois que seu aplicativo Xamarin.iOS tiver sido desenvolvido, a próxima etapa do ciclo de vida de desenvolvimento de software é distribuir seu aplicativo aos usuários para teste.

O iTunes Connect é uma opção para gerenciar testes de aplicativo e é descrito mais detalhadamente na guia [TestFlight](~/ios/deploy-test/testflight.md). No entanto, os membros do Programa de Desenvolvedor Corporativo da Apple não têm acesso ao iTunes Connect, portanto a distribuição *Ad Hoc* é o melhor método para testas estes aplicativos.

Os aplicativos Xamarin. iOS podem ser testados pelo usuário por meio da distribuição *ad hoc* , que está disponível tanto no Apple Developer Program quanto no programa Apple Developer Enterprise e permite que até 100 dispositivos IOS sejam testados.

A distribuição Ad hoc tem a vantagem de não exigir a aprovação da App Store e pode ser instalada via satélite de um servidor Web ou por meio do iTunes. No entanto, é limitada a **100** dispositivos por ano de associação, para o desenvolvimento e a distribuição, devendo ser adicionados manualmente na Central de Associados pelo UDID. Para obter mais informações sobre como adicionar dispositivos, visite a guia [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice).

A distribuição Ad Hoc requer que aplicativos sejam provisionados usando um *perfil de provisionamento* Ad Hoc que contém informações de assinatura de código, bem como a identidade do aplicativo e os dispositivos que podem instalar o aplicativo.

Este guia fornece informações sobre o provisionamento para distribuição Ad Hoc e informações sobre como distribuir um aplicativo Xamarin.iOS.

<a name="setup"></a>

## <a name="setting-up-for-distribution"></a>Configurar para distribuição

Mesmo se você pretende liberar um aplicativo Xamarin.iOS para implantação interna, para fins de teste, você precisará criar um Perfil de Provisionamento de Distribuição Ad Hoc específico para ele. Esse perfil permite que um aplicativo seja assinado digitalmente para lançamento de forma que ele pode ser instalado em um dispositivo iOS.

A próxima seção descreverá como configurar com um Certificado de Distribuição e um Perfil de Provisionamento de Distribuição.

> [!NOTE]
> Somente administradores e agentes de equipe podem criar certificados de distribuição e perfis de provisionamento.

<a name="createcertificate"></a>

## <a name="create-a-distribution-certificate"></a>Criar um certificado de distribuição

1. Navegue até a seção *Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)* do Apple Developer Member Center (Central de Associados do Desenvolvedor da Apple).
2. Em *Certificates (Certificados)*, selecione **Production (Produção)**.
3. Clique no **+** botão para criar um novo certificado.
4. Sob o título *Production (Produção)*, selecione **In-House and Ad Hoc (Interna e Ad Hoc)** ou **App Store and Ad Hoc (App Store e Ad Hoc)**, dependendo da sua associação no programa: 

   [![Selecione interno e ad hoc, ou loja de aplicativos e ad hoc](ad-hoc-distribution-images/cert-first-small.png)](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. Clique em Continuar e siga as instruções para criar uma Solicitação de Assinatura de Certificado por meio do Acesso do Conjunto de Chaves:

   [![Criar uma solicitação de assinatura de certificado por meio do acesso ao conjunto de chaves](ad-hoc-distribution-images/createcertmanually02.png)](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. Depois de criar o CSR conforme a instrução, clique em Continuar e carregue o CSR na Central de Membros:

   [![Carregar o CSR no centro de membros](ad-hoc-distribution-images/createcertmanually03.png)](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. Clique em Gerar para criar o certificado.
8. Por fim, escolha Download (Baixar) o certificado concluído e clique duas vezes no arquivo para instalá-lo.
9. Neste ponto, o certificado deve ser instalado no computador, mas talvez você precise [Refresh your profiles (Atualizar seus perfis)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) para garantir que eles estejam visíveis no Xcode.

Também é possível solicitar um Certificado usando a caixa de diálogo Preferências no Xcode. Para fazer isso, siga as etapas abaixo:

1. Selecione sua equipe e clique em **gerenciar certificados...**: [ ![ selecionando a equipe](ad-hoc-distribution-images/selectteam.png)](ad-hoc-distribution-images/selectteam.png#lightbox)

2. Em seguida, clique no botão **mais (+)** e selecione **iOS App Store**: [ ![ selecionando iOS App Store](ad-hoc-distribution-images/selectcert.png)](ad-hoc-distribution-images/selectcert.png#lightbox)

<a name="createprofile"></a>

## <a name="create-a-distribution-provisioning-profile"></a>Criar um perfil de provisionamento de distribuição

<a name="createappid"></a>

### <a name="create-an-app-id"></a>Criar uma ID do Aplicativo
Como com qualquer outro Perfil de Provisionamento que você criar, uma ID do Aplicativo será necessária para identificar o Aplicativo que você distribuirá para o dispositivo do usuário. Se ainda não tiver criado uma, siga as etapas abaixo para criá-la:

1. No [Apple Developer Center (Centro de Desenvolvedores da Apple)](https://developer.apple.com/account/overview.action), navegue até a seção *Certificate, Identifiers and Profiles (Certificados, Identificadores e Perfis)*. Selecione **App IDs (IDs do Aplicativo)** em **Identifiers (Identificadores)**.
2. Clique no **+** botão e forneça um **nome** que o identificará no Portal.
3. O prefixo do aplicativo já deve estar definido como sua ID de Equipe e não pode ser alterado. Selecione uma ID do Aplicativo Explícita ou Curinga e insira uma ID de Pacote no formato DNS inverso, como:
    - **Explícito**:`com.[DomainName].[AppName]`
    - **Curinga**:`com.[DomainName].*`
4. Selecione [App Services (Serviços de Aplicativos)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services) que seu aplicativo solicitar.
5. Clique no botão **Continuar** e siga as instruções na tela para criar a nova ID do Aplicativo.

Depois de obter os componentes obrigatórios necessários para criar um Perfil de Distribuição, siga as etapas abaixo para criá-lo:

1. Retorne ao portal de provisionamento da Apple e selecione **provisionamento > distribuição**: [ ![ selecione provisionamento > distribuição](ad-hoc-distribution-images/distribute01.png)](ad-hoc-distribution-images/distribute01.png#lightbox)

2. Clique no **+** botão e selecione o tipo de perfil de distribuição que você deseja criar como **ad-hoc**:

    [![Criar um tipo de distribuição ad hoc](ad-hoc-distribution-images/distribute02.png)](ad-hoc-distribution-images/distribute02.png#lightbox)

3. Clique no botão **Continue (Continuar)** e selecione a ID do Aplicativo na lista suspensa para qual você deseja criar um Perfil de Distribuição: 

    [![Selecione a ID do aplicativo na lista suspensa](ad-hoc-distribution-images/distribute03.png)](ad-hoc-distribution-images/distribute03.png#lightbox)

4. Clique no botão **Continue (Continuar)** e selecione o certificado de distribuição necessário para assinar o aplicativo:

    [![Selecione o certificado de distribuição necessário para assinar o aplicativo](ad-hoc-distribution-images/distribute04.png)](ad-hoc-distribution-images/distribute04.png#lightbox)

5. Clique no botão **Continuar** e digite um **Nome** para o novo Perfil de Distribuição:

    [![Insira um nome para o novo perfil de distribuição](ad-hoc-distribution-images/distribute06.png)](ad-hoc-distribution-images/distribute06.png#lightbox)

6. Clique no botão **Generate (Gerar)** para criar o novo perfil e finalizar o processo.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Talvez você precise fechar Visual Studio para Mac e fazer com que o Xcode atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Baixando Perfis e Certificados no Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio para Mac.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 Talvez você precise fechar Visual Studio e fazer com que o Xcode (no Mac do Host de Build) atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Baixando Perfis e Certificados no Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio.

-----

<a name="selectprofile"></a>

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selecionando um perfil de distribuição em um projeto Xamarin.iOS

Quando você estiver pronto para fazer um build final de um aplicativo Xamarin.iOS, selecione o perfil de distribuição criado acima.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

 No Visual Studio para Mac, faça o seguinte:

1. Clique duas vezes no nome do projeto no **Gerenciador de Soluções** para abri-lo para edição.
2. Selecione **Assinatura do pacote iOS** e o tipo de build na lista suspensa **Configuração**:  

    ![Selecione o tipo de build da lista suspensa Configuração](ad-hoc-distribution-images/releasexs01.png)
3. Na maioria dos casos, a **Identidade de Assinatura** e o **Perfil de Provisionamento** podem ser deixados com um valor padrão de **Automático** e o Visual Studio para Mac escolherá o perfil correto com base no Identificador de Pacote na Info.plist:

    ![A Identidade de assinatura e o Perfil de provisionamento definidos como os valores padrão de Automático](ad-hoc-distribution-images/releasexs02.png)
4. Se necessário, selecione a Identidade de Assinatura e o Perfil de Distribuição (aquele criado acima) nas listas suspensas:

    ![Selecione a Identidade de assinatura e o Perfil de distribuição](ad-hoc-distribution-images/releasexs03.png)
5. Clique no botão **OK** para salvar as alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)
 No Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse no nome do projeto no **Gerenciador de Soluções** e selecione **Propriedades** para abri-lo para edição.
2. Selecione **Assinatura do pacote iOS** e o tipo de build na lista suspensa **Configuração**:  

    ![Selecione o tipo de build da lista suspensa Configuração](ad-hoc-distribution-images/releasevs01.png)
3. Na maioria dos casos, a **Identidade de Assinatura** e o **Perfil de Provisionamento** podem ser deixados com um valor padrão de **Automático** e o Visual Studio escolherá o perfil correto com base no Identificador de Pacote na Info.plist:

    ![A Identidade de assinatura e o Perfil de provisionamento definidos como os valores padrão de Automático](ad-hoc-distribution-images/releasevs02.png)
4. Se necessário, selecione a Identidade de Assinatura e o Perfil de Distribuição (aquele criado acima) nas listas suspensas:

    ![Selecione a Identidade de assinatura e o Perfil de distribuição](ad-hoc-distribution-images/releasevs03.png)
5. Salve suas alterações nas Propriedades do projeto.

-----

<a name="adhoc"></a>

## <a name="ad-hoc-distribution"></a>Distribuição Ad Hoc

Enquanto [TestFlight](~/ios/deploy-test/testflight.md) é um meio popular de teste beta e distribuição, ele faz parte do iTunes Connect e, portanto, não está disponível para membros do **Programa de Desenvolvedor Corporativo da Apple**.

A Distribuição Ad Hoc permite que os desenvolvedores de aplicativos executem teste beta em uma grande variedade de dispositivos quando se o iTunes Connect não é uma opção. Ad-Hoc funciona de maneira semelhante à Distribuição Interna e requer que um IPA seja criado, que pode ser distribuído via satélite ou manualmente por meio do iTunes.

<a name="IPA_Creation"></a>

### <a name="ipa-support-for-ad-hoc-deployment"></a>Suporte de IPA para Implantação Ad Hoc

Depois de provisionados, aplicativos podem ser empacotados em um arquivo conhecido como um *IPA*. Este é um arquivo zip que contém o aplicativo, juntamente com metadados adicionais e ícones. O IPA é usado para adicionar um aplicativo localmente no iTunes para que ele pode ser sincronizado diretamente em um dispositivo que está incluído no perfil de provisionamento.

Para obter mais informações sobre como criar um IPA, consulte Guia de [suporte do IPA](~/ios/deploy-test/app-distribution/ipa-support.md) .

## <a name="summary"></a>Resumo

Este artigo explicou os mecanismos de distribuição Ad Hoc, que são necessários para testar aplicativos Xamarin.iOS.

## <a name="related-links"></a>Links relacionados

- [Distribuição da loja de aplicativos](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [O arquivo iTunesMetadata. plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Suporte do IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
