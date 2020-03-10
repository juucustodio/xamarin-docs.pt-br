---
title: Gerenciamento de contas da Apple
description: Este documento descreve como usar os recursos de gerenciamento de conta da Apple no Visual Studio para Mac e no Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: 17607e09a141fd29cd81cde93d812b20e62a9af8
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946242"
---
# <a name="apple-account-management"></a>Gerenciamento de contas da Apple

A interface de gerenciamento de conta da Apple no Visual Studio fornece uma maneira de exibir informações para equipes de desenvolvimento associadas a uma ID da Apple. Ele permite que você faça o seguinte:

- **Adicionar contas de desenvolvedor da Apple**
- **Exibir certificados de autenticação e perfis de provisionamento**
- **Criar novos certificados de assinatura**
- **Baixar perfis de provisionamento existentes**

> [!IMPORTANT]
> As ferramentas do Xamarin para o gerenciamento de conta da Apple exibem apenas informações sobre contas de desenvolvedor da Apple pagas. Para saber como testar um aplicativo em um dispositivo sem uma conta de desenvolvedor da Apple paga, consulte o guia de [provisionamento gratuito de aplicativos Xamarin. Ios](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="requirements"></a>Requisitos

O gerenciamento de conta da Apple está disponível em Visual Studio para Mac, Visual Studio 2019 e Visual Studio 2017 (versão 15,7 e superior). Você também deve ter uma conta de desenvolvedor da Apple paga para usar esse recurso. Mais informações sobre as contas de desenvolvedor da Apple estão disponíveis no guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .

> [!NOTE]
> Antes de começar, lembre-se de primeiro aceitar todos os contratos de licença de usuário no [portal do desenvolvedor da Apple](https://developer.apple.com/account/).

## <a name="add-an-apple-developer-account"></a>Adicionar uma conta de desenvolvedor da Apple

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Acesse o **Visual Studio > preferências > conta de desenvolvedor da Apple** e clique no botão **+** para abrir a caixa de diálogo entrar:

    ![AScreenshot da página de contas de desenvolvedor da Apple em preferências de Visual Studio para Mac.](apple-account-management-images/add-account-vsm.png)

2. Insira sua ID da Apple e senha e clique em **entrar**. Isso salvará suas credenciais no conjunto de chaves seguro neste computador.

3. Selecione **sempre permitir** na caixa de diálogo de alerta para permitir que o Visual Studio use suas credenciais:

    ![Caixa de diálogo sempre permitir alerta](apple-account-management-images/image4.png)

4. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e todas as equipes das quais sua ID da Apple faz parte:

    ![Caixa de diálogo da conta de desenvolvedor da Apple com contas adicionadas](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Se você estiver usando o Visual Studio 2017 ou o Visual Studio 2019 (versão 16,4 e mais antigo), você precisará ser [emparelhado a um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) antes de continuar.

1. Vá para **ferramentas > opções > Xamarin > contas da Apple** e clique em **Adicionar**:

    ![Captura de tela da página de contas da Apple nas opções do Visual Studio.](apple-account-management-images/add-account-vsw.png)

2. Insira sua ID da Apple e senha e clique em **logon**.

3. Depois que sua conta tiver sido adicionada com êxito, você verá sua ID da Apple e todas as equipes das quais sua ID da Apple faz parte:

    ![Captura de tela da página de conta de desenvolvedor com contas adicionadas.](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>Exibir certificados de autenticação e perfis de provisionamento

Selecione uma equipe e clique em **Exibir detalhes...** para abrir uma caixa de diálogo que exibe uma lista de identidades de assinatura e perfis de provisionamento instalados em seu computador.

A caixa de diálogo detalhes da equipe exibe uma lista de identidades de assinatura, organizadas por tipo. A coluna **status** aconselha você se o certificado for: 

- **Válido** – a identidade de assinatura (o certificado e a chave privada) está instalada em seu computador e não expirou.

- **Não está no** conjunto de chaves – há uma identidade de assinatura válida no servidor da Apple. Para instalar isso em seu computador, ele deve ser exportado de outro computador. Não é possível baixar a identidade de assinatura do portal do desenvolvedor da Apple, pois ela não conterá a chave privada.

- A **chave privada está ausente** – um certificado sem chave privada está instalado no conjunto de chaves.

- **Expirado** – o certificado expirou. Você deve remover isso do seu conjunto de chaves.

  ![informações da caixa de diálogo detalhes da equipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>Criar um certificado de autenticação

Para criar uma nova identidade de assinatura, clique em **criar certificado** para abrir o menu suspenso e selecione o [tipo de certificado](https://help.apple.com/xcode/mac/current/#/dev80c6204ec) que você deseja criar. Se você tiver as permissões corretas, uma nova identidade de assinatura será exibida após alguns segundos.

Se uma opção na lista suspensa estiver esmaecida e desmarcada, isso significa que você não tem as permissões de equipe corretas para criar esse tipo de certificado.

## <a name="download-provisioning-profiles"></a>Baixar perfis de provisionamento

A caixa de diálogo detalhes da equipe também exibe uma lista de todos os perfis de provisionamento conectados à sua conta de desenvolvedor. Você pode baixar todos os perfis de provisionamento para o computador local clicando em **baixar todos os perfis**.


## <a name="troubleshoot"></a>Solução de problemas

- Pode levar várias horas para que uma nova conta de desenvolvedor da Apple seja aprovada. Você não poderá habilitar o provisionamento automático até que a conta tenha sido aprovada.

- Se a adição de contas de desenvolvedor da Apple falhar com a mensagem `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, verifique se a ID da Apple que você está usando tem uma associação paga ativa ao programa de desenvolvedor da Apple. Para usar uma conta de desenvolvedor da Apple paga, confira o guia de [provisionamento gratuito para aplicativos Xamarin. Ios](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

- Se a tentativa de criar um novo certificado de autenticação falhar com o erro `You have reached the limit for certificates of this type`, o número máximo de certificados permitidos terá sido gerado. Para corrigir isso, navegue até o [centro de desenvolvedores da Apple](https://developer.apple.com/account/ios/certificate/distribution) e revogue um dos certificados de produção.

- Se você estiver tendo problemas ao fazer logon em sua conta no Visual Studio para Mac, uma possível correção é abrir o aplicativo de conjunto de chaves e, em **categoria** , selecionar **senhas**. Procure `deliver.` e exclua todas as entradas encontradas.

## <a name="known-issues"></a>Problemas conhecidos

- Por padrão, os perfis de provisionamento de distribuição direcionarão a Loja de Aplicativos. Perfis Internos ou Ad Hoc devem ser criados manualmente.
