---
title: Gerenciamento de contas da Apple
description: Este documento descreve como usar os recursos de gerenciamento de conta da Apple no Visual Studio para Mac e no Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 9629d775b45951279178dffa3600e7cd5073dd38
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290624"
---
# <a name="apple-account-management"></a>Gerenciamento de contas da Apple

A interface de gerenciamento de conta da Apple fornece uma maneira de exibir todas as equipes de desenvolvimento associadas a uma ID da Apple. Ele também permite que você exiba mais detalhes sobre cada equipe, exibindo uma lista de _identidades de assinatura_ e _perfis de provisionamento_ que estão instalados em seu computador.

A autenticação da sua ID da Apple é executada na linha de comando com [Fastlane](https://fastlane.tools/). o Fastlane deve ser instalado em seu computador para que você seja autenticado com êxito. Mais informações sobre o Fastlane e como instalá-lo são detalhadas nos guias do [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) .

A caixa de diálogo conta da Apple permite que você faça o seguinte:

- **Criar e gerenciar certificados**
- **Criar e gerenciar perfis de provisionamento**

Informações sobre como fazer isso são descritas neste guia.

> [!NOTE]
> As ferramentas do Xamarin para o gerenciamento de conta da Apple exibem apenas informações sobre contas de desenvolvedor da Apple pagas. Para saber como testar um aplicativo em um dispositivo sem uma conta de desenvolvedor da Apple paga, consulte o guia de [provisionamento gratuito de aplicativos Xamarin. Ios](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

Você também pode usar as ferramentas de provisionamento automático do iOS para criar e gerenciar automaticamente suas identidades de assinatura, IDs de aplicativo e perfis de provisionamento. Para obter mais informações sobre como usar esses recursos, consulte o guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="requirements"></a>Requisitos

O gerenciamento de conta da Apple está disponível em Visual Studio para Mac, Visual Studio 2019 e Visual Studio 2017 (versão 15,7 e superior).

Você deve ter uma conta de desenvolvedor da Apple para usar esse recurso. Mais informações sobre as contas de desenvolvedor da Apple estão disponíveis no guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

- Verifique se você está conectado à Internet. Isso ocorre porque o Fastlane se comunica diretamente com o portal do desenvolvedor da Apple.
- Verifique se você tem as [Ferramentas do Fastlane instaladas](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Verifique se você tem as ferramentas Fastlane mais [https://download.fastlane.tools](https://download.fastlane.tools)recentes do.
- Antes de começar, certifique-se de aceitar os contratos de licença de usuário no [portal do desenvolvedor](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Adicionando uma conta de desenvolvedor da Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Para abrir a caixa de diálogo gerenciamento de contas, acesse o **Visual Studio > preferências > conta de desenvolvedor da Apple**:

    ![Opções de conta de desenvolvedor da Apple](apple-account-management-images/image1.png)

2. Pressione o **+** botão para exibir a caixa de diálogo de entrada, conforme descrito abaixo: 

    ![caixa de diálogo fastlane.](apple-account-management-images/image2.png)

3. Insira sua ID da Apple e senha e clique no botão **entrar** . Isso salvará suas credenciais no conjunto de chaves seguro neste computador. o [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) é usado para tratar suas credenciais com segurança e passá-las para o portal do desenvolvedor da Apple.
 
4. Selecione **sempre permitir** na caixa de diálogo de alerta para permitir que o Visual Studio use suas credenciais:

    ![Caixa de diálogo sempre permitir alerta](apple-account-management-images/image4.png)

5. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e todas as equipes das quais sua ID da Apple faz parte.

    ![Caixa de diálogo da conta de desenvolvedor da Apple com contas adicionadas](apple-account-management-images/image5.png)

6. Selecione qualquer equipe e pressione a **Exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que estão instalados em seu computador:

    ![Exibir a tela de detalhes mostrando identidades de assinatura e perfis de provisionamento em seu computador](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Antes de começar a adicionar sua ID da Apple ao Visual Studio 2019, certifique-se de que seu ambiente de desenvolvimento esteja [emparelhado com um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Para abrir a janela Gerenciamento de contas, vá para **ferramentas > opções > Xamarin > contas da Apple**:

    ![Tela de opções de contas da Apple](apple-account-management-images/prov1.png)

1. Selecione o botão **Adicionar** e insira sua ID da Apple e senha:

    ![caixa de diálogo nome de usuário e senha](apple-account-management-images/prov1a.png)

1. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e todas as equipes das quais sua ID da Apple faz parte.
 
1. Selecione qualquer equipe e pressione a **Exibir detalhes...** . Isso exibirá uma lista de todas as identidades de assinatura e perfis de provisionamento que estão instalados em seu computador:

    ![caixa de diálogo nome de usuário e senha](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gerenciando identidades de assinatura e perfis de provisionamento

A caixa de diálogo detalhes da equipe exibe uma lista de identidades de assinatura, organizadas por tipo. A coluna **status** aconselha você se o certificado for: 

- **Válido** – a identidade de assinatura (o certificado e a chave privada) está instalada em seu computador e não expirou.

- **Não está no** conjunto de chaves – há uma identidade de assinatura válida no servidor da Apple. Para instalar isso em seu computador, ele deve ser exportado de outro computador. Não é possível baixar a identidade de assinatura do portal do desenvolvedor da Apple, pois ela não conterá a chave privada.

- A **chave privada está ausente** – um certificado sem chave privada está instalado no conjunto de chaves.

- **Expirado** – o certificado expirou. Você deve remover isso do seu conjunto de chaves.

  ![informações da caixa de diálogo detalhes da equipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Criar identidades de assinatura

Para criar uma nova identidade de assinatura, selecione o botão suspenso **criar certificado** e selecione o tipo que você precisa. Se você tiver as permissões corretas, uma nova identidade de assinatura será exibida após alguns segundos.

Se uma opção na lista suspensa estiver esmaecida e desmarcada, isso significa que você não tem as permissões de equipe corretas para criar esse tipo de certificado.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![criar opções de certificado](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![criar opções de certificado](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Baixar perfis de provisionamento

A caixa de diálogo detalhes da equipe também exibe uma lista de todos os perfis de provisionamento conectados à sua conta de desenvolvedor. Você pode baixar todos os perfis de provisionamento para seu computador local pressionando o botão **baixar todos os perfis**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Baixar seção de perfis de provisionamento](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Baixar seção de perfis de provisionamento](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Assinatura de pacote do iOS

Para obter informações sobre como implantar seu aplicativo em um dispositivo, consulte o guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="troubleshooting"></a>Solução de problemas

### <a name="view-details-dialog-is-empty"></a>A caixa de diálogo Exibir detalhes está vazia

Atualmente, esse é um problema conhecido, relacionado ao bug [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Verifique se você está usando a versão estável mais recente do Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Se você estiver tendo problemas ao fazer logon em sua conta, tente o seguinte:

- Abra o aplicativo de conjunto de chaves e, em categoria, selecione *senhas*. `deliver.`Pesquise e exclua todas as entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Erro ao adicionar a conta. Entre com uma senha específica ao aplicativo "

Isso ocorre porque a autenticação de dois fatores está habilitada em sua conta. Verifique se você está usando a versão estável mais recente do Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>Falha ao criar novo certificado
"Você atingiu o limite de certificados deste tipo"

![caixa de diálogo limite de certificado](apple-account-management-images/image10.png)

O número máximo de certificados permitidos foi gerado. Para corrigir isso, navegue até o [centro de desenvolvedores da Apple](https://developer.apple.com/account/ios/certificate/distribution) e revogue um dos certificados de produção.

## <a name="known-issues"></a>Problemas Conhecidos

- Por padrão, os perfis de provisionamento de distribuição direcionarão a Loja de Aplicativos. Perfis Internos ou Ad Hoc devem ser criados manualmente.
