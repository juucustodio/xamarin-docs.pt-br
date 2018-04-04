---
title: Gerenciamento de conta da Apple
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/05/2017
ms.openlocfilehash: 21af0ef09644f39f9be42788b3d8f4977a2143d3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="apple-account-management"></a>Gerenciamento de conta da Apple

A interface de gerenciamento de conta da Apple fornece uma maneira de exibir todas as equipes de desenvolvimento associadas com uma ID Apple. Ele também permite que você exibir mais detalhes sobre cada equipe exibindo uma lista de _assinatura identidades_ e _perfis de provisionamento_ que estão instalados em seu computador.

Autenticação de sua ID da Apple é executada na linha de comando com [fastlane](https://fastlane.tools/). FastLane deve ser instalado em seu computador para que você possa ser autenticado com êxito. Para obter mais informações sobre fastlane e como instalá-lo são detalhadas no [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guias.

A caixa de diálogo da conta da Apple no Visual Studio para Mac permite que você faça o seguinte:

* **Criar e gerenciar certificados** 
* **Criar e gerenciar perfis de provisionamento** 

Obter informações sobre como fazer isso são descritas neste guia.

Você também pode usar as ferramentas de assinatura de pacote do iOS para fazer o seguinte:

* **Adicionar uma nova identidade de autenticação em um perfil existente** 
* **Provisionar novos dispositivos** 

Para obter mais informações sobre como usar esses recursos, consulte o [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.
️
## <a name="requirements"></a>Requisitos

Gerenciamento de conta do Apple está disponível no Visual Studio para Mac. Ele não está disponível atualmente no Visual Studio para Windows.

Você deve ter uma conta de desenvolvedor da Apple para usar esse recurso. Para obter mais informações sobre contas de desenvolvedor da Apple estão disponíveis na [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

- Certifique-se de que você está conectado à internet. Isso ocorre porque fastlane se comunica diretamente com o portal do desenvolvedor da Apple.
- Verifique se você tem [fastlane ferramentas instaladas](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verifique se você tem as ferramentas mais recentes de fastlane de [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Antes de começar, certifique-se de aceitar os contratos de licença de usuário no [portal do desenvolvedor](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Adicionando uma conta de desenvolvedor da Apple

1. Para abrir a caixa de diálogo de gerenciamento de conta, vá para **Visual Studio > Preferências > conta de desenvolvedor da Apple**:

    ![Opções de conta de desenvolvedor da Apple](apple-account-management-images/image1.png)

2. Pressione a **+** botão para exibir a entrada na caixa de diálogo, conforme descrito abaixo: 

    ![caixa de diálogo FastLane.](apple-account-management-images/image2.png)

4. Insira sua ID Apple e a senha e clique no **entrar** botão. Isso salvará suas credenciais no conjunto de chaves seguro neste computador. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) é usado para lidar com suas credenciais com segurança e passá-las para o portal do desenvolvedor da Apple.
 
5. Selecione **sempre permitir** na caixa de diálogo alerta para permitir que o Visual Studio usar suas credenciais:

    ![](apple-account-management-images/image4.png)

6. Quando sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e qualquer equipes que sua ID Apple faz parte.

    ![](apple-account-management-images/image5.png)

7. Selecione qualquer equipe e pressione a **exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que estão instalados em seu computador:

    ![](apple-account-management-images/image6.png)


<a name="managing" />


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gerenciamento de identidades de assinatura e perfis de provisionamento

A caixa de diálogo de detalhes de equipe exibe uma lista de identidades de assinatura, organizados por tipo. O **Status** coluna avisa se o certificado é: 

* **Válido** – a identidade de assinatura (o certificado e a chave privada) é instalada em seu computador e não expirou.

* **Não no conjunto de chaves** – há uma identidade de assinatura válida no servidor da Apple. Para instalá-lo em seu computador, ele deve ser exportado de outro computador. Não é possível baixar a identidade de assinatura do Portal do desenvolvedor da Apple, pois ele não conterá a chave privada.

* **Chave privada está ausente** – um certificado sem chave privada está instalado no conjunto de chaves.

* **Expirado** – o certificado expirou. Você deve remover essa do seu conjunto de chaves.

  ![](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Criar uma assinatura de identidades

Para criar uma nova identidade da assinatura, selecione o **criar um novo certificado** botão suspenso e selecione o tipo que você precisa. Se você tiver as permissões corretas uma assinatura nova identidade aparecerá após alguns segundos.

Se uma opção na lista suspensa é esmaecida e não selecionada, conforme ilustrado abaixo, isso significa que você não tem as permissões de equipe correta para criar esse tipo de certificado.

![](apple-account-management-images/image8.png)

## <a name="download-provisioning-profiles"></a>Baixar perfis de provisionamento

A caixa de diálogo de detalhes do team também exibe uma lista de todos os perfis de provisionamento, conectado à sua conta de desenvolvedor. Você pode baixar todos os perfis de provisionamento para seu computador local, pressionando a **baixar todos os perfis** botão

![](apple-account-management-images/image9.png)

## <a name="ios-bundle-signing"></a>Assinatura de pacote de iOS

Para obter informações sobre como implantar seu aplicativo em um dispositivo, consulte o [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="view-details-dialog-is-empty"></a>Caixa de diálogo Exibir detalhes está vazia

Atualmente, esta é um problema conhecido, relacionadas ao bug [&#53906;](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se você estiver tendo problemas para conectar-se em sua conta, tente o seguinte:

* Abra o aplicativo de conjunto de chaves e na categoria, selecione *senhas*. Procurar `deliver.`e exclua todas as entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Erro ao adicionar a conta. Faça logon com uma senha específicos do aplicativo"

Isso ocorre porque a autenticação de fator 2 está habilitado em sua conta. Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>Falha ao criar o novo certificado
"Você atingiu o limite para certificados desse tipo"

![](apple-account-management-images/image10.png)

O número máximo de certificados permitido ter sido gerado. Para corrigir isso, navegue até o [central do desenvolvedor Apple](https://developer.apple.com/account/ios/certificate/distribution) e revogar um dos certificados de produção.

## <a name="known-issues"></a>Problemas Conhecidos

* Às vezes, a caixa de diálogo Exibir detalhes pode levar muito tempo para buscar as identidades de autenticação e perfis.
* Geralmente o foco pode não retornar para o Visual Studio para Mac depois de inserir os detalhes, fazendo com que sua conta não deve ser adicionado. Se esse for o caso, tente o processo novamente.
* Os perfis de provisionamento criados no Visual Studio para Mac não levarão em conta os direitos selecionados nos projetos (Entitlements.plist). Essa funcionalidade será adicionada em versões futuras do IDE.
* Por padrão, os perfis de provisionamento de distribuição direcionarão a Loja de Aplicativos. Perfis Internos ou Ad Hoc devem ser criados manualmente.
