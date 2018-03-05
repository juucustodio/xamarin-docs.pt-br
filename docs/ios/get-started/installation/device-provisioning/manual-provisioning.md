---
title: Provisionamento manual
description: "Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia mostrará a solicitação de certificados de desenvolvimento e perfis, o trabalho com serviços de aplicativos e a implantação de um aplicativo no dispositivo."
ms.topic: article
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/15/2017
ms.openlocfilehash: 46d2f9f9f1a22ef13e994c91255f0fb29c52ca5d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="manual-provisioning"></a>Provisionamento manual

_Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia mostrará a solicitação de certificados de desenvolvimento e perfis, o trabalho com serviços de aplicativos e a implantação de um aplicativo no dispositivo._

<a name="signingidentity" />

## <a name="creating-a-signing-identity"></a>Criando uma Identidade de Assinatura

A primeira etapa na configuração de um dispositivo de desenvolvimento é criar uma identidade de assinatura. Uma identidade de assinatura consiste em duas coisas:

- Um certificado de desenvolvimento
- Uma chave privada

Certificados de desenvolvimento e as [chaves](#keypairs) associadas são essenciais para um desenvolvedor do iOS: eles estabelecem sua identidade com a Apple e associam você a um determinado dispositivo e um perfil para desenvolvimento, algo semelhante a colocar sua assinatura digital em seus aplicativos. A Apple busca certificados controlar o acesso aos dispositivos que você tem permissão para implantar.

Equipes de desenvolvimento, certificados e perfis podem ser gerenciados acessando a seção [Certificados, identificadores e perfis](https://developer.apple.com/account/overview.action) da Central de Membros da Apple. A Apple exige que você tenha uma identidade de assinatura para compilar seu código para o dispositivo ou simulador.  

> [!IMPORTANT]
> É importante observar que você só pode ter dois certificados de desenvolvimento do iOS em um determinado momento. Se você precisar criar mais, será necessário revogar um existente. Um computador usando um certificado revogado não poderá assinar seu aplicativo.

Para gerar uma identidade de assinatura, faça o seguinte:

1. Faça logon na [seção Certificados, Identificadores e Perfis do Portal do Desenvolvedor](https://developer.apple.com/account/overview.action) e selecione a seção **Certificados** da coluna **Aplicativos iOS**. Em seguida, pressione o **+** para criar um novo certificado:

    [ ![](manual-provisioning-images/cert-plus.png "Clique no + para criar um novo certificado")](manual-provisioning-images/cert-plus.png)

2. Selecione a opção **Desenvolvimento de Aplicativos do iOS** para o tipo de certificado e clique em **Continuar**. Essa tela pode ser diferente dependendo dos privilégios da sua conta:

    [ ![](manual-provisioning-images/cert-first.png "Selecione a opção Desenvolvimento de Aplicativos do iOS para o tipo de certificado")](manual-provisioning-images/cert-first.png)

3. Solicite uma solicitação de assinatura de certificado, que será carregada para gerar um certificado manualmente. Para fazer isso, inicie o **Acesso ao Conjunto de Chaves** em um Mac. Navegue até o menu principal e selecione **Assistente de Certificado** e **Solicitar um Certificado de uma Autoridade de Certificação...**, conforme ilustrado abaixo:

      [ ![](manual-provisioning-images/key-first.png "Faça uma Solicitação de Assinatura de Certificado")](manual-provisioning-images/key-first.png)

4. Preencha suas informações e selecione a opção **Salvar em disco**:

    [ ![](manual-provisioning-images/key-second.png "Preencha suas informações")](manual-provisioning-images/key-second.png)

5. Salve o CSR em um local em que ele possa ser facilmente encontrado:

    [ ![](manual-provisioning-images/cert-third.png "Salve o CSR")](manual-provisioning-images/cert-third.png)

6. Retorne ao Portal de Provisionamento, carregue o certificado no portal e envie-o:

    [ ![](manual-provisioning-images/cert-second.png "Carregue o certificado para o Portal")](manual-provisioning-images/cert-second.png)

    Se você não tiver privilégios de administrador, o certificado deverá ser aprovado por um agente de equipe ou administrador.

7. Uma vez que o certificado esteja aprovado, baixe-o do Portal de Provisionamento:

    [ ![](manual-provisioning-images/status-dev.png "Baixe o certificado do Portal de Provisionamento")](manual-provisioning-images/status-dev.png)

8. Clique duas vezes no certificado baixado para iniciar o Acesso ao Conjunto de Chaves e abrir o painel **Meus Certificados**, mostrando os novos certificados e a chave privada associada:

    [ ![](manual-provisioning-images/keychain.png "O Certificado no Acesso ao conjunto de chaves")](manual-provisioning-images/keychain.png)

<a name="keypairs" />

### <a name="understanding-certificate-key-pairs"></a>Entendendo pares de chaves de certificado

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Perfil do Desenvolvedor contém certificados, suas chaves associadas e eventuais perfis de provisionamento associados à conta. Existem na realidade duas versões de um Perfil do Desenvolvedor – uma está no Portal do Desenvolvedor, a outra reside em um Mac local. A diferença entre os dois é o tipo de chaves que contêm: _o perfil no Portal armazena todas as chaves públicas associadas com os certificados, enquanto a cópia no seu Mac local contém todas as chaves privadas_. Para que os certificados sejam válidos, os pares de chaves devem ser correspondentes. Mantenha um backup do perfil do desenvolvedor no Mac local, porque se as chaves privadas forem perdidas, todos os certificados e perfis de provisionamento precisarão ser regenerados.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O Perfil do Desenvolvedor contém certificados, suas chaves associadas e eventuais perfis de provisionamento associados à conta. Existem na realidade duas versões de um Perfil do Desenvolvedor – uma está no Portal do Desenvolvedor, a outra reside em um Mac. A diferença entre os dois é o tipo de chaves que contêm: _o perfil no Portal armazena todas as chaves públicas associadas com os certificados, enquanto a cópia no seu Mac contém todas as chaves privadas_. Para que os certificados sejam válidos, os pares de chaves devem ser correspondentes. Mantenha um backup do perfil do desenvolvedor no Mac do Host de Build do Xamarin, porque se as chaves privadas forem perdidas, todos os certificados e perfis de provisionamento precisarão ser regenerados.

-----

> [!WARNING]
> **Observação:** perder os certificados e chaves associadas tem um potencial enorme de causar interrupção, pois exigirá a revogação de certificados existentes e reprovisionamento dos dispositivos associados, incluindo aqueles registrados para implantação ad hoc. Após configurar com êxito os certificados de desenvolvimento, exporte uma cópia de backup e armazene-os em um local seguro. Para obter mais informações sobre como fazer isso, consulte a seção de Exportação e Importação de Certificados e Perfis da guia [Mantendo Certificados](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) nos documentos da Apple.

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>Provisionando um dispositivo iOS para desenvolvimento

Agora que você estabeleceu sua identidade com a Apple e tem um certificado de desenvolvimento, é necessário configurar um perfil de provisionamento e as entidades necessárias para que seja possível implantar um aplicativo em um dispositivo Apple. O dispositivo deve estar executando uma versão do iOS com suporte pelo Xcode – talvez seja necessário atualizar o dispositivo, o Xcode ou ambos.

<a name="adddevice" />

## <a name="add-a-device"></a>Adicionar um dispositivo

Ao criar um perfil de provisionamento para desenvolvimento, devemos especificar quais dispositivos podem executar o aplicativo. Para habilitar isso, até 100 dispositivos por ano podem ser adicionados a nosso Portal do Desenvolvedor e, daqui, podemos selecionar os dispositivos a serem adicionados a um determinado perfil de provisionamento. Siga as etapas abaixo em seu Mac para adicionar um dispositivo ao Portal do Desenvolvedor

1. Inicie o Xcode.
2. Conecte o dispositivo a ser provisionado para o Mac com seu cabo USB fornecido.
2. No menu **Janelas**, selecione **Dispositivos**:

  [ ![](manual-provisioning-images/add01.png "No menu Janelas selecione Dispositivos")](manual-provisioning-images/add01.png)

3. Selecione o dispositivo iOS desejado na lista **DISPOSITIVOS** no lado esquerdo da janela Dispositivos.
4. Realce a cadeia de caracteres do **Identificador** e copie-a para a área de transferência:

  [ ![](manual-provisioning-images/add02.png "Realce a cadeia de caracteres do Identificador")](manual-provisioning-images/add02.png)

5. No Safari, navegue até o [Apple Developer Center (Centro de Desenvolvedores da Apple)](https://developer.apple.com/membercenter/index.action) e faça logon.
6. Clique no link **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)**:

  [ ![](manual-provisioning-images/add03.png "Clique no link Certificados, identificadores, perfis")](manual-provisioning-images/add03.png)

7. Clique no link **Devices (Dispositivos)**:

  [ ![](manual-provisioning-images/add04.png "Clique no link Dispositivos")](manual-provisioning-images/add04.png)

8. Clique no botão **+**:

  [ ![](manual-provisioning-images/add05.png "Clique no botão +")](manual-provisioning-images/add05.png)

9. Forneça um nome para o novo dispositivo e cole o **Identifier (Identificador)** do dispositivo que copiamos acima no campo **UUID**:

  [ ![](manual-provisioning-images/add06.png "Fornecer um nome para o novo dispositivo e o Identificador do dispositivo")](manual-provisioning-images/add06.png)

10. Clique no botão **Continue (Continuar)**.
11. Por fim, examine as informações e clique no botão **Register (Registrar)**:

  [ ![](manual-provisioning-images/add07.png "Examine as informações")](manual-provisioning-images/add07.png)

Repita as etapas acima para qualquer dispositivo iOS que será usado para testar ou depurar um aplicativo Xamarin.iOS.

Depois de adicionar o dispositivo ao portal do desenvolvedor, é necessário criar um perfil de provisionamento e adicionar o dispositivo a ele.


<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>Criando um perfil de provisionamento de desenvolvimento

Como com o Certificado de Desenvolvimento, os Perfis de provisionamento podem ser criados manualmente por meio da seção [Certificados, identificadores e perfis](https://developer.apple.com/account/overview.action) da Central de Membros da Apple.

Antes de criar um perfil de provisionamento, deve ser criada uma *ID do aplicativo*. Uma ID do aplicativo é uma cadeia de caracteres no estilo DNS reverso que identifica um aplicativo de modo exclusivo. As etapas a seguir será demonstram como criar uma **ID do Aplicativo Curinga**, que pode ser usada para criar e instalar a maioria dos aplicativos. Cada uma das **IDs do aplicativo explícitas** só permite a instalação de um aplicativo (com a ID do pacote correspondente) e elas geralmente são usadas para determinadas funcionalidades do iOS, como Apple Pay e HealthKit. Para obter informações sobre como criar IDs do aplicativo explícitas, consulte o guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="app-id"></a>ID do aplicativo

1. No [portal do desenvolvedor](https://developer.apple.com/account/overview.action), navegue até a seção *Certificate, Identifiers and Profiles (Certificados, Identificadores e Perfis)* no Apple Developer Center (Centro de Desenvolvedores da Apple). Selecione **App IDs (IDs do Aplicativo)** em **Identifiers (Identificadores)**.
2. Clique no botão **+** e forneça um **Nome**:

    [ ![](manual-provisioning-images/appid05a.png "Forneça um nome")](manual-provisioning-images/appid05a.png)
3. O prefixo do aplicativo deve ser predefinido. Selecione a **Wildcard App ID (ID do Aplicativo Curinga)** para o sufixo do aplicativo. Insira uma ID do pacote no formato `com.[DomainName].*`:

  [ ![](manual-provisioning-images/appid05b.png "Insira uma ID do pacote")](manual-provisioning-images/appid05b.png)

3. Clique no botão **Continue (Continuar)** e siga as instruções na tela para criar a nova ID do Aplicativo.

### <a name="provisioning-profile"></a>Perfil de provisionamento

Quando a ID do aplicativo tiver sido criada, o perfil de provisionamento poderá ser produzido. Esse perfil de provisionamento contém informações sobre a *que* aplicativo (ou aplicativos, se for uma ID do aplicativo curinga) este perfil está relacionado, *quem* pode usar o perfil (dependendo de quais certificados de desenvolvedor são adicionados) e *que* dispositivos podem instalar o aplicativo.

Para criar manualmente um perfil de provisionamento para desenvolvimento, faça o seguinte:

1. Use o Safari para navegar até a [Apple Developers Member Center (Central de Associados de Desenvolvedores da Apple)](https://developer.apple.com/membercenter/index.action) e, na seção *Certificates, Identifiers & Profiles (Certificados, Identificadores e perfis)*, selecione Perfis de Provisionamento.
2. Clique no botão **+** no canto superior direito para criar um novo perfil.
3. Na seção **Desenvolvimento**, selecione o botão de opção ao lado de **Desenvolvimento de Aplicativos iOS** e pressione **Continuar**:

    [ ![](manual-provisioning-images/provisioning-profile01.png "Selecione o tipo de perfil a ser criado")](manual-provisioning-images/provisioning-profile01.png)
4. No menu suspenso, selecione a ID do aplicativo que será usada:

    [ ![](manual-provisioning-images/provisioning-profile02.png "Selecione a ID do aplicativo a ser usada")](manual-provisioning-images/provisioning-profile02.png)
5. Selecione os certificados para incluir no perfil de provisionamento e pressione **Continuar**:

    [ ![](manual-provisioning-images/provisioning-profile03.png "Selecione os Certificados a serem incluídos no perfil de provisionamento")](manual-provisioning-images/provisioning-profile03.png)
6. Selecione todos os dispositivos nos quais o aplicativo será instalado.

    [ ![](manual-provisioning-images/provisioning-profile04.png "Selecione todos os dispositivos nos quais o aplicativo será instalado")](manual-provisioning-images/provisioning-profile04.png)
7. Forneça um nome identificável ao perfil de provisionamento e pressione **Continuar** para criar o perfil:

    [ ![](manual-provisioning-images/provisioning-profile05.png "Forneça o Perfil de provisionamento com um nome identificável")](manual-provisioning-images/provisioning-profile05.png)
8. Pressione **Baixar** para baixar o perfil de provisionamento em um Mac:

    [ ![](manual-provisioning-images/provisioning-profile06.png "Baixe o perfil de provisionamento")](manual-provisioning-images/provisioning-profile06.png)
9. Clique duas vezes no arquivo para instalar o perfil de provisionamento no Xcode. Observe que o Xcode pode não mostrar indicações visuais de que o perfil está instalado, exceto ao abri-lo. Isso pode ser verificado navegando em **Xcode > Preferências > Contas**. Selecione sua ID Apple clique em **Exibir Detalhes...**. Seu novo perfil de provisionamento deve ser listado, conforme ilustrado abaixo:

      [ ![](manual-provisioning-images/provisioning-profile07.png "Exibindo o perfil no Xcode")](manual-provisioning-images/provisioning-profile07.png)

Após o perfil de provisionamento ter sido criado com êxito, pode ser necessário atualizar o Xcode para que todos os certificados de desenvolvimento fiquem disponíveis para o Visual Studio para Mac e o Visual Studio.

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>Baixando perfis e certificados no Xcode

Certificados e perfis de provisionamento que foram criados no Portal de Desenvolvedores da Apple podem não aparecer automaticamente no Xcode. Portanto, talvez seja necessário baixá-los para que possam ser acessados pelo Visual Studio para Mac e pelo Visual Studio. Para atualizar e baixar qualquer certificado criado no Portal de Desenvolvedores da Apple, faça o seguinte:

1.   Saia do Visual Studio para Mac ou Visual Studio.
2.   Inicie o Xcode.
3.   Escolha **Menu do Xcode > Preferências...**
4.   Clique na guia **Contas**.
5.   Selecione uma equipe e clique no botão **Baixar Perfis Manuais**: [ ![](manual-provisioning-images/selectteam1.png "Baixando perfis Manuais")](manual-provisioning-images/selectteam1.png)

6.   Feche o Xcode.
7.  Inicie o Visual Studio para Mac ou Visual Studio.

Os novos certificados ou perfis de provisionamento estarão disponíveis no Visual Studio para Mac ou Visual Studio e prontos para uso.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

> [!IMPORTANT]
> **Observação**: pode ser necessário parar e reiniciar o Visual Studio para Mac para que ele exiba certificados novos ou modificados ou perfis atualizados pelo Xcode.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

> [!IMPORTANT]
> **Observação**: pode ser necessário parar e reiniciar o Visual Studio para que ele exiba certificados novos ou modificados ou perfis atualizados pelo Xcode.

-----

<a name="appservices" />

## <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de serviços de aplicativos especiais, também chamada de recursos, que podem ser ativados para o aplicativo Xamarin.iOS. Esses serviços de aplicativos devem ser configurados tanto no Portal de Provisionamento iOS, quando a **ID do aplicativo** é criada, quanto no arquivo **Entitlements.plist**, que é parte do projeto do aplicativo Xamarin.iOS. Para obter informações sobre como adicionar serviços de aplicativo no seu aplicativo, consulte os guias [Introdução aos recursos](~/ios/deploy-test/provisioning/capabilities/index.md) e [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).

* Crie uma ID do aplicativo com os serviços de aplicativo necessários.
* Crie um novo [perfil de provisionamento](#provisioningprofile) que contenha essa ID do aplicativo.
* Definir direitos no projeto Xamarin.iOS

<a name="deploy" />

## <a name="deploying-to-a-device"></a>Implantação em um dispositivo

Neste momento, o provisionamento já deve estar concluído e o aplicativo pronto para ser implantado no dispositivo. Para fazer isso, siga as etapas abaixo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Certifique-se de definir o seletor de equipe para Nenhum nas Opções de Projeto > Assinatura do Pacote de iOS.

1. Conecte o dispositivo em um Mac.
2. No **Info.plist** do projeto, verifique se o identificador do pacote corresponde à ID do aplicativo (a menos que a ID do aplicativo seja um caractere curinga):

  ![](manual-provisioning-images/deploydevice01xs.png "Inserindo um identificador")

3. Clique com o botão direito do mouse no projeto para exibir a caixa de diálogo Opções do Projeto e vá até **Compilar > Assinatura do Pacote iOS**. Na lista suspensa ao lado de **Identidade de Assinatura** e **Perfil de Provisionamento**, verifique se o Visual Studio para Mac pode ver os perfis corretos e selecione uma identidade e um perfil específicos:

  ![](manual-provisioning-images/deploydevice02xs.png "Selecione uma identidade e um perfil específicos")

Se isso estiver definido como **Automático**, o Visual Studio para Mac selecionará a identidade e o perfil com base na ID do pacote que foi definida na etapa No. 2.

4. Certifique-se de definir a configuração de build para **iPhone** / **iPad**, em vez do simulador.
5. Clique em **Executar** no Visual Studio para Mac e exiba o aplicativo em execução no dispositivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Conecte o dispositivo em um Mac.
2. No **Info.plist** do projeto, verifique se o identificador do pacote corresponde à ID do aplicativo:

  ![](manual-provisioning-images/servicevs01.png "Inserindo um identificador")

3. Clique com o botão direito do mouse no projeto para exibir a caixa de diálogo Opções do Projeto e vá até **Compilar > Assinatura do Pacote iOS**. Na lista suspensa ao lado de **Identidade de Assinatura** e **Perfil de Provisionamento**, verifique se o Visual Studio pode ver os perfis corretos e selecione uma identidade e um perfil específicos.

    Se isso estiver definido como **Automático**, o Visual Studio selecionará a identidade e o perfil com base na ID do pacote que foi definida na etapa No. 2.

4. Certifique-se de definir a configuração de build para **iPhone** ou **iPad**, em vez do simulador.
5. Clique em **Executar** no Visual Studio e exiba o aplicativo em execução no dispositivo.


-----

## <a name="summary"></a>Resumo

Este artigo abordou as etapas necessárias para configurar o ambiente de desenvolvimento para Xamarin.iOS. Ele explorou como o código de um aplicativo é assinado com informações sobre o desenvolvedor, sua equipe, os dispositivos nos quais um aplicativo pode ser executado e a ID do aplicativo individual.


## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
