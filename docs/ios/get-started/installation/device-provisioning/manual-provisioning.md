---
title: Provisionamento manual para o Xamarin.iOS
description: Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso do provisionamento manual para configurar certificados e perfis de desenvolvimento.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 04cb1b9303e571b2a10cdfa621dcd312162e2893
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304859"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisionamento manual para o Xamarin.iOS

_Uma vez que o Xamarin.iOS tenha sido instalado com sucesso, o próximo passo no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso de provisionamento manual para configurar certificados de desenvolvimento e perfis._

> [!NOTE]
> As instruções nesta página são relevantes para desenvolvedores que têm acesso pago ao Programa de Desenvolvedor da Apple. Se tiver uma conta gratuita, confira o guia de [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) para obter mais informações sobre testes no dispositivo.

## <a name="create-a-development-certificate"></a>Criar um certificado de desenvolvimento

O primeiro passo para configurar um dispositivo de desenvolvimento é criar um certificado de assinatura. Um certificado de assinatura consiste em duas coisas:

- Um certificado de desenvolvimento
- Uma chave privada

Certificados de desenvolvimento e as [chaves](#understanding-certificate-key-pairs) associadas são essenciais para um desenvolvedor do iOS: eles estabelecem sua identidade com a Apple e associam você a um determinado dispositivo e um perfil para desenvolvimento, algo semelhante a colocar sua assinatura digital em seus aplicativos. A Apple busca certificados controlar o acesso aos dispositivos que você tem permissão para implantar.

É possível gerenciar equipes, certificados e perfis de desenvolvimento acessando a seção [Certificates, Identifiers & Profiles (Certificados, identificadores e perfis)](https://developer.apple.com/account/resources/certificates/list) (logon necessário) da Member Center (Central de Associados) da Apple. A Apple exige que você tenha uma identidade de assinatura para compilar seu código para o dispositivo ou simulador.  

> [!IMPORTANT]
> É importante observar que você só pode ter dois certificados de desenvolvimento do iOS em um determinado momento. Se você precisar criar mais, será necessário revogar um existente. Um computador usando um certificado revogado não poderá assinar seu aplicativo.

Antes de iniciar o processo de provisionamento manual, você deve garantir que você tenha uma conta de desenvolvedor apple adicionada no Visual Studio, conforme descrito no guia [de gerenciamento de contas da Apple.](~/cross-platform/macios/apple-account-management.md) Depois de adicionar sua conta de desenvolvedor apple, faça o seguinte para gerar um certificado de assinatura:

1. Vá para a janela de contas de desenvolvedores da Apple no Visual Studio.
    1. Mac: **Visual Studio > Preferências > Conta do Desenvolvedor Apple**
    2. Windows: **Ferramentas > opções > Contas Xamarin > Apple**

2. Selecione uma equipe e clique **em Exibir detalhes...**
3. Clique **em Criar certificado** e selecione Desenvolvimento **Apple** ou Desenvolvimento **do iOS**. Se você tiver as permissões corretas, uma nova identidade de assinatura aparecerá após alguns segundos.

### <a name="understanding-certificate-key-pairs"></a>Entendendo pares de chaves de certificado

O Perfil do Desenvolvedor contém certificados, suas chaves associadas e eventuais perfis de provisionamento associados à conta. Existem na realidade duas versões de um Perfil do Desenvolvedor – uma está no Portal do Desenvolvedor, a outra reside em um Mac local. A diferença entre os dois é o tipo de chaves que contêm: _o perfil no Portal armazena todas as chaves públicas associadas com os certificados, enquanto a cópia no seu Mac local contém todas as chaves privadas_. Para que os certificados sejam válidos, os pares de chaves devem ser correspondentes.

> [!WARNING]
> Perder o certificado e as chaves associadas pode ser incrivelmente perturbador, pois exigirá a revogação dos certificados existentes e o reprovisionamento de quaisquer dispositivos associados, incluindo aqueles registrados para implantação ad-hoc. Após configurar com êxito os certificados de desenvolvimento, exporte uma cópia de backup e armazene-os em um local seguro. Para obter mais informações sobre como fazer isso, consulte a seção de Exportação e Importação de Certificados e Perfis da guia [Mantendo Certificados](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) nos documentos da Apple.

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>Provisão de um dispositivo iOS para desenvolvimento

Agora que você estabeleceu sua identidade com a Apple e tem um certificado de desenvolvimento, você deve configurar um perfil de provisionamento e as entidades necessárias para que seja possível implantar um aplicativo em um dispositivo Apple. O dispositivo deve estar executando uma versão do iOS com suporte pelo Xcode – talvez seja necessário atualizar o dispositivo, o Xcode ou ambos.

<a name="adddevice" />

## <a name="add-a-device"></a>Adicionar um dispositivo

Ao criar um perfil de provisionamento para desenvolvimento, devemos especificar quais dispositivos podem executar o aplicativo. Para habilitar isso, até 100 dispositivos por ano podem ser adicionados a nosso Portal do Desenvolvedor e, daqui, podemos selecionar os dispositivos a serem adicionados a um determinado perfil de provisionamento. Siga as etapas abaixo em seu Mac para adicionar um dispositivo ao Portal do Desenvolvedor

1. Conecte o dispositivo a ser provisionado para o Mac com seu cabo USB fornecido.
2. Abra o Xcode e vá para **Dispositivos e Simuladores de > de Janela**.
3. Na guia **Dispositivos,** selecione o dispositivo no menu do lado esquerdo.
4. Realce a cadeia de caracteres do **Identificador** e copie-a para a área de transferência:

   ![Dispositivos Xcode e janela do simulador com a localização da seqüência de identificador do iOS destacada.](manual-provisioning-images/xcode-devices.png)

5. Em um navegador da Web, vá para a [seção Dispositivos no portal do desenvolvedor](https://developer.apple.com/account/resources/devices/list) e clique no **+** botão:

   ![Captura de tela da página dos dispositivos no site do Desenvolvedor Apple com o botão adicionar destacado.](manual-provisioning-images/developer-portal-devices.png)

6. Defina a **plataforma** correta e forneça um nome para o novo dispositivo. Cole o identificador copiado anteriormente no campo ID do **dispositivo:**

    ![Captura de tela da nova página de registro do dispositivo com os campos preenchidos corretamente.](manual-provisioning-images/new-device-info.png)

7. Clique em **Continuar**.
8. Revise as informações e clique **em Registrar**.

Repita as etapas acima para qualquer dispositivo iOS que será usado para testar ou depurar um aplicativo Xamarin.iOS.

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>Crie um perfil de provisionamento de desenvolvimento

Depois de adicionar o dispositivo ao portal do desenvolvedor, é necessário criar um perfil de provisionamento e adicionar o dispositivo a ele. 

Antes de criar um perfil de provisionamento, deve ser criada uma *ID do aplicativo*. Uma ID do aplicativo é uma cadeia de caracteres no estilo DNS reverso que identifica um aplicativo de modo exclusivo. As etapas a seguir será demonstram como criar uma **ID do Aplicativo Curinga**, que pode ser usada para criar e instalar a maioria dos aplicativos. Cada uma das **IDs do aplicativo explícitas** só permite a instalação de um aplicativo (com a ID do pacote correspondente) e elas geralmente são usadas para determinadas funcionalidades do iOS, como Apple Pay e HealthKit. Para obter informações sobre como criar IDs do aplicativo explícitas, consulte o guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="new-wildcard-app-id"></a>Novo curinga App ID

1. Vá para a [seção Identificadores no portal do desenvolvedor](https://developer.apple.com/account/resources/identifiers/list) e clique no **+** botão.
2. Selecione **IDs de aplicativos** e clique em **Continuar**.
3. Forneça uma **descrição**. Em seguida, defina **o ID do Bundle** como **Curinga** e digite um ID no formato `com.[DomainName].*`:

   ![Captura de tela da nova página de registro de ID do aplicativo com campos necessários preenchidos.](manual-provisioning-images/new-app-id.png)

4. Clique em **Continuar**.
5. Revise as informações e clique **em Registrar**.

### <a name="new-provisioning-profile"></a>Novo perfil de provisionamento

Uma vez criado o ID do aplicativo, o perfil de provisionamento pode ser criado. Este perfil de provisionamento contém informações sobre *qual* aplicativo (ou aplicativos, se é uma ID de aplicativo curinga) este perfil se relaciona, *quem* pode usar o perfil (dependendo dos certificados de desenvolvedor são adicionados) e *quais* dispositivos podem instalar o aplicativo.

Para criar manualmente um perfil de provisionamento para desenvolvimento, faça o seguinte:

1. Vá para a [seção Perfis no](https://developer.apple.com/account/resources/profiles/list) **+** portal do desenvolvedor e clique no botão.

2. Em **Desenvolvimento,** selecione **desenvolvimento de aplicativos para iOS** e clique em **Continuar**.

3. Selecione o ID do aplicativo para usar no menu suspenso e clique em **Continuar**.

4. Selecione os Certificados para incluir no perfil de provisionamento e clique em **Continuar**.

5. Selecione todos os dispositivos em que o aplicativo será instalado e clique em **Continuar**.

6. Forneça **o nome do perfil de provisionamento** e clique em **Gerar**.

7. Você pode, opcionalmente, clicar em **Baixar** na próxima página para baixar o perfil de provisionamento no Mac.

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>Baixe perfis de provisionamento no Visual Studio

Depois de criar um novo perfil de provisionamento no Portal do Desenvolvedor da Apple, use o Visual Studio para baixá-lo para que ele esteja disponível para assinatura de pacotes em seu aplicativo.

1. Vá para a janela de contas de desenvolvedores da Apple no Visual Studio.
    1. Mac: **Visual Studio > Preferências > Conta do Desenvolvedor Apple**
    2. Windows: **Ferramentas > opções > Contas Xamarin > Apple**

2. Selecione a equipe e clique **em Exibir detalhes...**
3. Verifique se o novo perfil aparece na lista **Perfis de Provisionamento.** Talvez seja necessário reiniciar o Visual Studio para atualizar a lista. 
4. Clique **em Baixar todos os perfis**.

O novo perfil de provisionamento estará agora disponível no Visual Studio e pronto para uso.

## <a name="deploy-to-a-device"></a>Implantar em um dispositivo

Neste momento, o provisionamento já deve estar concluído e o aplicativo pronto para ser implantado no dispositivo. Para fazer isso, siga estas etapas:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Conecte seu dispositivo ao Mac.
2. Abra **o Info.plist** e certifique-se de que o **Identificador de Pacote corresponda** ao ID do aplicativo criado anteriormente (a menos que o ID do aplicativo seja um curinga).
3. Na seção **Assinatura,** selecione **Provisionamento Manual** como **esquema**:

    ![Captura de tela do Info.plist no Visual Studio para Mac com provisionamento manual selecionado](manual-provisioning-images/vsm-info-plist.png)

4. Clique **em Opções de assinatura de pacotes...**
5. Certifique-se de que a configuração de compilação está definida **como Debug|iPhone**. Abra os menus suspensos **de identidade** de assinatura e de perfil **de provisionamento** para verificar se os certificados e perfis de provisionamento corretos estão listados: 

   ![página de assinatura de pacote do iOS com o menu suspenso do perfil de provisionamento aberto listando todos os perfis de provisionamento disponíveis para o aplicativo.](manual-provisioning-images/vsm-bundle-signing.png)

6. Selecione uma identidade e perfil específicos para usar ou deixe-o como **Automático**. Quando definido **como Automático,** o Visual Studio for Mac selecionará a identidade e o perfil com base no **Identificador de Pacote** **sin.** 
7. Clique em **OK**.
8. Clique **em Executar** para implantar o aplicativo no seu dispositivo.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Conecte seu dispositivo ao host de compilação Mac.
2. Abra **o Info.plist** e certifique-se de que o **Identificador de Pacote corresponda** ao ID do aplicativo criado anteriormente (a menos que o ID do aplicativo seja um curinga).
3. No **Solution Explorer**, clique com o botão direito do mouse no nome do projeto do iOS, selecione **Propriedades** e navegue até a guia Assinatura de pacotes do **iOS.**
4. Certifique-se de que a configuração de compilação está definida **como Debug|iPhone**. Em **Assinatura de pacotes,** **selecione Provisionamento manual** como o **Esquema:**

    ![Captura de tela do Info.plist no Visual Studio para Mac com provisionamento manual selecionado](manual-provisioning-images/vs-bundle-signing.png)

5. Abra os menus suspensos **de identidade de assinatura** e de perfil de **provisionamento** para verificar se os certificados e perfis de provisionamento corretos estão listados.
6. Selecione uma identidade e perfil específicos para usar ou deixe-o como **Automático**. Quando definido **como Automático,** o Visual Studio selecionará a identidade e o perfil com base no **Identificador de Pacote** sind.plist . **Info.plist** 
7. Clique **em Executar** para implantar o aplicativo no seu dispositivo.

-----

## <a name="provisioning-for-application-services"></a>Provisionamento para serviços de aplicativo

A Apple fornece uma seleção de serviços de aplicativos especiais, também chamada de recursos, que podem ser ativados para o aplicativo Xamarin.iOS. Esses serviços de aplicativos devem ser configurados tanto no Portal de Provisionamento iOS, quando a **ID do aplicativo** é criada, quanto no arquivo **Entitlements.plist**, que é parte do projeto do aplicativo Xamarin.iOS. Para obter informações sobre como adicionar serviços de aplicativo no seu aplicativo, consulte os guias [Introdução aos recursos](~/ios/deploy-test/provisioning/capabilities/index.md) e [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).

- Crie uma ID do aplicativo com os serviços de aplicativo necessários.
- Crie um novo [perfil de provisionamento](#provisioningprofile) que contenha essa ID do aplicativo.
- Definir direitos no projeto Xamarin.iOS

## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativos](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
