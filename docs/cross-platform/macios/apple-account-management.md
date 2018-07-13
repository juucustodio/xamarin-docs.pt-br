---
title: Gerenciamento de conta da Apple
description: Este documento descreve como usar os recursos de gerenciamento de conta da Apple no Visual Studio para Mac e Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 4557d3b055e5c49842b9fdcff1dac9ee996e8bab
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986012"
---
# <a name="apple-account-management"></a>Gerenciamento de conta da Apple

A interface de gerenciamento de conta da Apple fornece uma maneira de exibir todas as equipes de desenvolvimento associadas com uma ID Apple. Ele também permite que você exibir mais detalhes sobre cada equipe, exibindo uma lista dos _identidades de assinatura_ e _perfis de provisionamento_ que estão instaladas em seu computador.

Autenticação de sua ID da Apple é executada na linha de comando com [fastlane](https://fastlane.tools/). o FastLane deve ser instalado em seu computador para que você possa ser autenticado com êxito. Para obter mais informações sobre o fastlane e como instalá-lo é descrito em detalhes os [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guias.

A caixa de diálogo da conta da Apple permite que você faça o seguinte:

* **Criar e gerenciar certificados**
* **Criar e gerenciar perfis de provisionamento**

Obter informações sobre como fazer isso são descritas neste guia.

> [!NOTE]
> Ferramentas do Xamarin para gerenciamento de conta da Apple só exibem informações sobre contas de desenvolvedor da Apple pagas. Para saber como testar um aplicativo em um dispositivo sem uma conta de desenvolvedor da Apple paga, consulte o [provisionamento gratuito para aplicativos xamarin. IOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) guia.

Você também pode usar o provisionamento automático de ferramentas do iOS para automaticamente criar e gerenciar identidades de assinatura, IDs do aplicativo e perfis de provisionamento. Para obter mais informações sobre como usar esses recursos, consulte a [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

## <a name="requirements"></a>Requisitos

Gerenciamento de conta da Apple está disponível no Visual Studio para Mac e Visual Studio 2017 (versão 15.7 e superior)

Você deve ter uma conta de desenvolvedor da Apple para usar esse recurso. Obter mais informações sobre contas de desenvolvedor da Apple estão disponíveis na [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

- Certifique-se de que você está conectado à internet. Isso ocorre porque o fastlane se comunica diretamente com o portal do desenvolvedor da Apple.
- Verifique se você tem [as ferramentas do fastlane instaladas](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verifique se você tem as ferramentas do fastlane de mais recente [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Antes de começar, certifique-se aceitar quaisquer contratos de licença de usuário na [portal do desenvolvedor](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Adicionando uma conta de desenvolvedor da Apple

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Para abrir a caixa de diálogo de gerenciamento de conta, acesse **Visual Studio > Preferências > conta de desenvolvedor da Apple**:

    ![Opções de conta de desenvolvedor da Apple](apple-account-management-images/image1.png)

2. Pressione a **+** botão para exibir o símbolo na caixa de diálogo, conforme descrito abaixo: 

    ![caixa de diálogo do FastLane.](apple-account-management-images/image2.png)

4. Insira sua Apple ID e senha e clique no **entrar** botão. Isso salvará suas credenciais no conjunto de chaves seguro neste computador. [o FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) é usado para lidar com suas credenciais com segurança e passá-los para o portal do desenvolvedor da Apple.
 
5. Selecione **sempre permitir** na caixa de diálogo alerta para permitir que o Visual Studio para usar suas credenciais:

    ![Sempre permitir a caixa de diálogo alerta](apple-account-management-images/image4.png)

6. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e qualquer equipes que sua ID da Apple é parte do.

    ![Caixa de diálogo do Apple developer conta com as contas adicionadas](apple-account-management-images/image5.png)

7. Selecione qualquer equipe e pressione a **exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que são instalados em seu computador:

    ![Exibição mostrando de tela de detalhes identidades de assinatura e provisionamento perfis em seu computador](apple-account-management-images/image6.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Antes de começar a adicionar sua ID da Apple ao Visual Studio 2017, certifique-se de que seu ambiente de desenvolvimento está [emparelhado com um host de build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Para abrir a janela de gerenciamento de conta, acesse **Ferramentas > Opções > Xamarin > contas da Apple**:

    ![Tela de opções de contas da Apple](apple-account-management-images/prov1.png)

1. Selecione o **adicionar** botão e insira sua Apple ID e senha:

    ![caixa de diálogo de nome de usuário e senha](apple-account-management-images/prov1a.png)

1. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e qualquer equipes que sua ID da Apple é parte do.
 
1. Selecione qualquer equipe e pressione a **exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que são instalados em seu computador:

    ![caixa de diálogo de nome de usuário e senha](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gerenciar identidades de assinatura e perfis de provisionamento

A caixa de diálogo de detalhes de equipe exibe uma lista de identidades de assinatura, organizado por tipo. O **Status** coluna avisa se o certificado é: 

* **Válido** – a identidade de assinatura (o certificado e a chave privada) é instalada em seu computador e não expirou.

* **Não no conjunto de chaves** – há uma identidade de assinatura válida no servidor da Apple. Para instalá-lo em seu computador, ele deve ser exportado de outro computador. Não é possível baixar a identidade de assinatura do Portal do desenvolvedor da Apple, pois ele não conterá a chave privada.

* **A chave privada está ausente** – um certificado sem chave privada está instalado no conjunto de chaves.

* **Expirado** – o certificado expirou. Você deve remova-o de seu conjunto de chaves.

  ![informações de caixa de diálogo de detalhes de equipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Criar identidades de assinatura

Para criar uma nova identidade de assinatura, selecione a **criar um certificado** botão suspenso e selecione o tipo que você precisa. Se você tiver as permissões corretas uma assinatura nova identidade será exibida após alguns segundos.

Se uma opção na lista suspensa estiver esmaecida e desmarcada, isso significa que você não tem as permissões de equipe correta para criar esse tipo de certificado.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Criar opções de certificado](apple-account-management-images/image8.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Criar opções de certificado](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Baixar perfis de provisionamento

A caixa de diálogo de detalhes de equipe também exibe uma lista de todos os perfis de provisionamento, conectado à sua conta de desenvolvedor. Você pode baixar todos os perfis de provisionamento em seu computador local, pressionando as **baixar todos os perfis** botão

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Seção de perfis de provisionamento de download](apple-account-management-images/image9.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Seção de perfis de provisionamento de download](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Assinatura do pacote iOS

Para obter informações sobre como implantar seu aplicativo em um dispositivo, consulte a [aprovisionamento](~/ios/get-started/installation/device-provisioning/index.md) guia.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="view-details-dialog-is-empty"></a>Caixa de diálogo Exibir detalhes está vazia

Atualmente, isso é um problema conhecido, relacionados ao bug [53906 #](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se você estiver tendo problemas para fazer logon em sua conta, tente o seguinte:

* Abra o aplicativo de conjunto de chaves e na categoria, selecione *senhas*. Pesquise `deliver.`e exclua todas as entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Erro ao adicionar a conta. Faça logon com uma senha específicos do aplicativo"

Isso ocorre porque a autenticação de fator de 2 está habilitado em sua conta. Certifique-se de que você estiver usando a versão estável mais recente do Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>Falha ao criar o novo certificado
"Você atingiu o limite para certificados desse tipo"

![caixa de diálogo de limite de certificado](apple-account-management-images/image10.png)

O número máximo de certificados permitido ter sido gerado. Para corrigir isso, navegue até a [Apple Developer Center](https://developer.apple.com/account/ios/certificate/distribution) e revogar um dos certificados de produção.

## <a name="known-issues"></a>Problemas Conhecidos

* Por padrão, os perfis de provisionamento de distribuição direcionarão a Loja de Aplicativos. Perfis Internos ou Ad Hoc devem ser criados manualmente.
