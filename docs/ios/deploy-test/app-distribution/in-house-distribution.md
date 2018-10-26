---
title: Distribuição interna para aplicativos Xamarin.iOS
description: Este documento fornece uma visão geral sobre a distribuição de aplicativos internamente, como membro do Programa de Desenvolvedores Corporativos da Apple.
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: c1e1b2d7bfb43c256c71abb62e7d85a05b096419
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103576"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Distribuição interna para aplicativos Xamarin.iOS

_Este documento fornece uma visão geral sobre a distribuição de aplicativos internamente, como membro do Programa de Desenvolvedores Corporativos da Apple._

Depois que seu aplicativo Xamarin.iOS tiver sido desenvolvido, a próxima etapa do ciclo de vida de desenvolvimento de software é distribuir seu aplicativo aos usuários. Aplicativos proprietários podem ser distribuídos *internamente* (anteriormente chamado de Empresariais) por meio de **Programa de Desenvolvedores Corporativos da Apple**, que oferece os seguintes benefícios:

- Seu aplicativo não precisa ser enviado para análise pela Apple.
- Não há limites de quantidade de dispositivos nos quais você pode implantar um aplicativo
    - É importante observar que a Apple deixa muito claro que aplicativos internos são somente para uso interno.

Também é importante observar que o programa Corporativo:

- Não fornece acesso ao iTunes Connect para distribuição ou teste (incluindo TestFlight).
- O custo de associação é US$ 299 por ano.

Todos os aplicativos ainda precisam ser assinados pela Apple.

<a name="testing" />

## <a name="testing-your-application"></a>Testando o Aplicativo

O teste do seu aplicativo é realizado usando a distribuição Ad Hoc. Para obter mais informações sobre o teste, siga as etapas no guia [Distribuição Ad-Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Lembre-se de que você só pode testar até 100 dispositivos.

<a name="setup" />

## <a name="getting-set-up-for-distribution"></a>Configurando para Distribuição

Assim como acontece com outros Programas de Desenvolvedor da Apple, no Programa de Desenvolvedores Corporativos da Apple, somente Administradores e Agentes de Equipe podem criar Certificados de Distribuição e Perfis de Provisionamento.

Certificados do Programa de Desenvolvedores Corporativos da Apple durão três anos e perfis de provisionamento expiram após um ano.

É importante observar que certificados expirados não podem ser renovados e, em vez disso, você terá que substituir o certificado expirado por um novo, conforme detalhado [abaixo](#certificate).

<a name="certificate" />

## <a name="creating-a-distribution-certificate"></a>Criando um certificado de distribuição

1. Navegue até a seção *Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)* do Apple Developer Member Center (Central de Associados do Desenvolvedor da Apple).
2. Em *Certificates (Certificados)*, selecione **Production (Produção)**.
3. Clique no botão **+** para criar um novo Certificado.
4. No título *Produção*, selecione **Interna e Ad Hoc**:

   [![](in-house-distribution-images/createcertmanually01.png "Selecionar Interna e Ad Hoc")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. Clique em Continuar e siga as instruções para criar uma Solicitação de Assinatura de Certificado por meio do Acesso do Conjunto de Chaves:

   [![](in-house-distribution-images/createcertmanually02.png "Criar uma Solicitação de Assinatura de Certificado por meio do Acesso do Conjunto de Chaves")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. Depois de criar seu CSR, conforme instruído, clique em Continuar e carregue o CSR na Central de Associados:

   [![](in-house-distribution-images/createcertmanually03.png "Carregar o CSR para a Central de Membros")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. Clique em Generate (Gerar) para criar o certificado.
8. Baixe o certificado concluído e clique duas vezes no arquivo para instalá-lo.
9. Neste ponto, o certificado deve ser instalado no computador, mas talvez você precise atualizar seus perfis para garantir que eles estejam visíveis no Xcode.

Também é possível solicitar um Certificado usando a caixa de diálogo Preferências no Xcode. Para fazer isso, siga as etapas abaixo:

1. Selecione a equipe e clique em *Exibir Detalhes*:

    [![](in-house-distribution-images/selectteam.png "Selecionar sua equipe")](in-house-distribution-images/selectteam.png#lightbox)

2. Em seguida, clique no botão **Criar** próximo ao **Certificado de Distribuição iOS**:

   [![](in-house-distribution-images/selectcert.png "Criar o Certificado de distribuição iOS")](in-house-distribution-images/selectcert.png#lightbox)

2.   Em seguida, clique no botão de **mais (+)** e selecione **iOS App Store**:

   [![](in-house-distribution-images/selectcert.png "Selecione a App Store do iOS")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile" />

## <a name="creating-a-distribution-provisioning-profile"></a>Criando um Perfil de Provisionamento de Distribuição

<a name="appid" />

### <a name="creating-an-app-id"></a>Criando uma ID do aplicativo

Como com qualquer outro Perfil de Provisionamento que você criar, uma ID do Aplicativo será necessária para identificar o Aplicativo que você distribuirá para o dispositivo do usuário. Se ainda não tiver criado uma, siga as etapas abaixo para criá-la:


1. No [Apple Developer Center (Centro de Desenvolvedores da Apple)](https://developer.apple.com/account/overview.action), navegue até a seção *Certificate, Identifiers and Profiles (Certificados, Identificadores e Perfis)*. Selecione **App IDs (IDs do Aplicativo)** em **Identifiers (Identificadores)**.
2. Clique no botão **+** e forneça um **Name (Nome)** que o identificará no Portal.
3. O prefixo do aplicativo já deve estar definido como sua ID de Equipe e não pode ser alterado. Selecione uma ID do Aplicativo Explicit (Explícita) ou Wildcard (Curinga) e insira uma ID de Pacote em um formato DNS inverso, como: **Explicit (Explícita)**: com.[DomainName].[AppName] **Wildcard (Curinga)**:com.[DomainName].*
4. Selecione [App Services (Serviços de Aplicativos)](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services) que seu aplicativo solicitar.
5. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do Aplicativo.

Depois de obter os componentes obrigatórios necessários para criar um Perfil de Distribuição, siga as etapas abaixo para criá-lo:

1. Volte para o Portal de Provisionamento da Apple e selecione **Provisionamento** > **Distribuição**:

   [![](in-house-distribution-images/distribute01.png "Selecione Provisionamento > Distribuição")](in-house-distribution-images/distribute01.png#lightbox)

2. Clique no botão **+** e selecione o tipo de Perfil de Distribuição que você deseja criar como **Interna**:

   [![](in-house-distribution-images/distribute02.png "Crie um Perfil de Distribuição Interna")](in-house-distribution-images/distribute02.png#lightbox)

3. Clique no botão **Continuar** e selecione a ID do Aplicativo na lista suspensa para a qual você deseja criar um Perfil de Distribuição:

   [![](in-house-distribution-images/distribute03.png "Selecione a ID do aplicativo na lista suspensa")](in-house-distribution-images/distribute03.png#lightbox)

4. Clique no botão **Continuar** e selecione o certificado de distribuição necessário para assinar o aplicativo:

   [![](in-house-distribution-images/distribute04.png "Selecione o certificado de distribuição necessário para assinar o aplicativo")](in-house-distribution-images/distribute04.png#lightbox)

6. Clique no botão **Continuar** e digite um **Nome** para o novo Perfil de Distribuição:

   [![](in-house-distribution-images/distribute06.png "Insira um nome para o novo Perfil de Distribuição")](in-house-distribution-images/distribute06.png#lightbox)

7. Clique no botão **Generate (Gerar)** para criar o novo perfil e finalizar o processo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 Talvez você precise fechar o Visual Studio para Mac e fazer com que o Xcode atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Solicitando Identidades de Assinatura](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Talvez você precise fechar o Visual Studio e fazer com que o Xcode (no Mac do Host do Build) atualize sua lista de Identidades de Assinatura e Perfis de Provisionamento disponíveis (seguindo as instruções na seção [Solicitando Identidades de Assinatura](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) antes de um novo perfil de distribuição ser disponibilizado no Visual Studio.

-----

<a name="inhouse" />

## <a name="distributing-your-app-in-house"></a>Distribuindo o aplicativo internamente

Com o Programa de Desenvolvedor Corporativo da Apple, o Licenciado é a pessoa responsável por distribuir o aplicativo e por cumprir as [diretrizes](http://adcdownload.apple.com/Documentation/License_Agreements__Apple_Developer_Enterprise_Program/Apple_Developer_Program_Enterprise_Agreement_20150608.pdf) definidas pela Apple.

Seu aplicativo pode ser distribuído com segurança usando uma variedade de formas diferentes, como:

- Localmente por meio do iTunes
- Servidor MDM
- Um servidor Web interno e seguro
- Email

Para distribuir seu aplicativo em qualquer uma das seguintes maneiras, você deve primeiro criar um arquivo IPA, conforme explicado na próxima seção.


### <a name="creating-an-ipa-for-in-house-deployment"></a>Criando um IPA para Implantação Interna

Depois de provisionados, aplicativos podem ser empacotados em um arquivo conhecido como um *IPA*. Este é um arquivo zip que contém o aplicativo, juntamente com metadados adicionais e ícones. O IPA é usado para adicionar um aplicativo localmente no iTunes para que ele pode ser sincronizado diretamente em um dispositivo que está incluído no perfil de provisionamento.

Para obter mais informações sobre como criar um IPA, consulte [Suporte do IPA](~/ios/deploy-test/app-distribution/ipa-support.md).


## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral sobre a distribuição de aplicativos do Xamarin.iOS internamente.

## <a name="related-links"></a>Links relacionados

- [Distribuição da App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuição Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [O arquivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Suporte a IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
