---
title: Gerenciamento de conta da Apple
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: a4ca803085f31ff0db5dd4f194b705d765447c9d
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="apple-account-management"></a>Gerenciamento de conta da Apple

A interface de gerenciamento de conta da Apple fornece uma maneira de exibir todas as equipes de desenvolvimento associadas com uma ID Apple. Ele também permite que você exibir mais detalhes sobre cada equipe exibindo uma lista de _assinatura identidades_ e _perfis de provisionamento_ que estão instalados em seu computador.

Autenticação de sua ID da Apple é executada na linha de comando com [fastlane](https://fastlane.tools/). FastLane deve ser instalado em seu computador para que você possa ser autenticado com êxito. Para obter mais informações sobre fastlane e como instalá-lo são detalhadas no [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guias.

A caixa de diálogo Apple conta permite que você faça o seguinte:

* **Criar e gerenciar certificados** 
* **Criar e gerenciar perfis de provisionamento** 

Obter informações sobre como fazer isso são descritas neste guia.

Você também pode usar as ferramentas de provisionamento automático do iOS para automaticamente criar e gerenciar sua assinatura de identidades, IDs de aplicativo e perfis de provisionamento.

Para obter mais informações sobre como usar esses recursos, consulte o [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.
️
## <a name="requirements"></a>Requisitos

Gerenciamento de conta do Apple está disponível no Visual Studio para Mac e Visual Studio 2017 (versão 15.7 e posterior)

Você deve ter uma conta de desenvolvedor da Apple para usar esse recurso. Para obter mais informações sobre contas de desenvolvedor da Apple estão disponíveis na [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

- Certifique-se de que você está conectado à internet. Isso ocorre porque fastlane se comunica diretamente com o portal do desenvolvedor da Apple.
- Verifique se você tem [fastlane ferramentas instaladas](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verifique se você tem as ferramentas mais recentes de fastlane de [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Antes de começar, certifique-se de aceitar os contratos de licença de usuário no [portal do desenvolvedor](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Adicionando uma conta de desenvolvedor da Apple

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Para abrir a caixa de diálogo de gerenciamento de conta, vá para **Visual Studio > Preferências > conta de desenvolvedor da Apple**:

    ![Opções de conta de desenvolvedor da Apple](apple-account-management-images/image1.png)

2. Pressione a **+** botão para exibir a entrada na caixa de diálogo, conforme descrito abaixo: 

    ![caixa de diálogo FastLane.](apple-account-management-images/image2.png)

4. Insira sua ID Apple e a senha e clique no **entrar** botão. Isso salvará suas credenciais no conjunto de chaves seguro neste computador. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) é usado para lidar com suas credenciais com segurança e passá-las para o portal do desenvolvedor da Apple.
 
5. Selecione **sempre permitir** na caixa de diálogo alerta para permitir que o Visual Studio usar suas credenciais:

    ![Sempre permitir que a caixa de diálogo alerta](apple-account-management-images/image4.png)

6. Quando sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e qualquer equipes que sua ID Apple faz parte.

    ![Caixa de diálogo de conta de desenvolvedor a Apple com adicionados](apple-account-management-images/image5.png)

7. Selecione qualquer equipe e pressione a **exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que estão instalados em seu computador:

    ![Tela mostrando do modo de exibição detalhes identidades de autenticação e provisionamento perfis em seu computador](apple-account-management-images/image6.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Antes de começar a adicionar sua ID da Apple para Visual Studio de 2017, certifique-se de que seu ambiente de desenvolvimento é [emparelhada com a um host de compilação do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Para abrir a janela de gerenciamento de conta, vá para **Ferramentas > Opções > Xamarin > contas Apple**:

    ![Tela de opções de contas da Apple](apple-account-management-images/prov1.png)

1. Selecione o **adicionar** botão e insira sua ID Apple e a senha:

    ![caixa de diálogo nome de usuário e senha](apple-account-management-images/prov1a.png)

1. Quando sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e qualquer equipes que sua ID Apple faz parte.
 
1. Selecione qualquer equipe e pressione a **exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que estão instalados em seu computador:

    ![caixa de diálogo nome de usuário e senha](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gerenciamento de identidades de assinatura e perfis de provisionamento

A caixa de diálogo de detalhes de equipe exibe uma lista de identidades de assinatura, organizados por tipo. O **Status** coluna avisa se o certificado é: 

* **Válido** – a identidade de assinatura (o certificado e a chave privada) é instalada em seu computador e não expirou.

* **Não no conjunto de chaves** – há uma identidade de assinatura válida no servidor da Apple. Para instalá-lo em seu computador, ele deve ser exportado de outro computador. Não é possível baixar a identidade de assinatura do Portal do desenvolvedor da Apple, pois ele não conterá a chave privada.

* **Chave privada está ausente** – um certificado sem chave privada está instalado no conjunto de chaves.

* **Expirado** – o certificado expirou. Você deve remover essa do seu conjunto de chaves.

  ![informações de caixa de diálogo de detalhes de equipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Criar uma assinatura de identidades

Para criar uma nova identidade da assinatura, selecione o **Create Certificate** botão suspenso e selecione o tipo que você precisa. Se você tiver as permissões corretas uma assinatura nova identidade aparecerá após alguns segundos.

Se uma opção na lista suspensa é esmaecida e desmarcada, isso significa que você não tem as permissões de equipe correta para criar esse tipo de certificado.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Criar opções de certificado](apple-account-management-images/image8.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Criar opções de certificado](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Baixar perfis de provisionamento

A caixa de diálogo de detalhes do team também exibe uma lista de todos os perfis de provisionamento, conectado à sua conta de desenvolvedor. Você pode baixar todos os perfis de provisionamento para seu computador local, pressionando a **baixar todos os perfis** botão

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Seção de perfis de provisionamento de download](apple-account-management-images/image9.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Seção de perfis de provisionamento de download](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Assinatura de pacote de iOS

Para obter informações sobre como implantar seu aplicativo em um dispositivo, consulte o [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="view-details-dialog-is-empty"></a>Caixa de diálogo Exibir detalhes está vazia

Atualmente, esta é um problema conhecido, relacionadas ao bug [53906 #](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se você estiver tendo problemas para conectar-se em sua conta, tente o seguinte:

* Abra o aplicativo de conjunto de chaves e na categoria, selecione *senhas*. Procurar `deliver.`e exclua todas as entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Erro ao adicionar a conta. Faça logon com uma senha específicos do aplicativo"

Isso ocorre porque a autenticação de fator 2 está habilitado em sua conta. Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>Falha ao criar o novo certificado
"Você atingiu o limite para certificados desse tipo"

![caixa de diálogo de limite de certificado](apple-account-management-images/image10.png)

O número máximo de certificados permitido ter sido gerado. Para corrigir isso, navegue até o [central do desenvolvedor Apple](https://developer.apple.com/account/ios/certificate/distribution) e revogar um dos certificados de produção.

## <a name="known-issues"></a>Problemas Conhecidos

* Por padrão, os perfis de provisionamento de distribuição direcionarão a Loja de Aplicativos. Perfis Internos ou Ad Hoc devem ser criados manualmente.
