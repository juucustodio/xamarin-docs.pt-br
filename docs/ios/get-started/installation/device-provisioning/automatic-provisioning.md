---
title: Provisionamento automático para o Xamarin.iOS
description: Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da assinatura automática para solicitar certificados e perfis de desenvolvimento.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 05/22/2018
ms.openlocfilehash: a0c3179dc8e349c23d5521230e0957d1be9384ec
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986181"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionamento automático para o Xamarin.iOS

_Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da assinatura automática para solicitar certificados e perfis de desenvolvimento._

## <a name="requirements"></a>Requisitos

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

- Visual Studio para Mac 7.3 ou posterior
- Xcode 9 ou posterior

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- Visual Studio 2017 versão 15.7 (ou superior)

Você também precisa estar emparelhado com um host de build do Mac que tem o seguinte:

- Xcode 9 ou posterior

-----

## <a name="enabling-automatic-signing"></a>Habilitando a Assinatura automática

Antes de iniciar o processo de assinatura automática, você deve adicionar uma ID Apple ao Visual Studio, conforme descrito no guia [Gerenciamento de conta da Apple](~/cross-platform/macios/apple-account-management.md). Depois de adicionar um ID Apple, será possível usar qualquer _Equipe_ associada. Isso permite que certificados, perfis e outras IDs sejam feitas com relação à equipe. A ID da equipe também é usada para criar um prefixo para uma ID do aplicativo que será incluída no perfil de provisionamento. Isso permite que a Apple verifique que você é quem diz que ser.

> [!IMPORTANT]
> Para começar, entre no [iTunes Connect](https://itunesconnect.apple.com/) ou no site do [appleid.apple.com](https://appleid.apple.com) e confirme se você aceitou as políticas de conta mais recentes da Apple. Quando solicitado, conclua as etapas para aceitar novos contratos de conta da Apple. Se não aceitar o contrato de privacidade de maio de 2018, você receberá o seguinte alerta quando tentar provisionar o dispositivo:
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
>}
>```

Para assinar seu aplicativo automaticamente para implantação em um dispositivo iOS, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra o projeto iOS no Visual Studio para Mac.

2. Abra o arquivo **Info.plist**.

3. Na seção **Assinatura**, selecione **Provisionamento automático**:

    ![Lista suspensa do seletor de equipe](automatic-provisioning-images/image2.png)

4. Selecione sua equipe na lista suspensa **Equipe**.

6. Depois de alguns segundos, um Certificado de Assinatura e um Perfil de Provisionamento serão criados:

    ![certificado e perfil criados com sucesso](automatic-provisioning-images/image5.png)

    Se a assinatura automática falhar, o **Painel de assinatura automática** exibirá o motivo do erro.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Emparelhe o Visual Studio 2017 com um Mac conforme descrito no guia [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Propriedades**. Em seguida, navegue até a guia **Assinatura do Pacote do iOS**.

3. Selecione o esquema **Provisionamento Automático**:

    ![Seleção do esquema automático](automatic-provisioning-images/prov4.png)

4. Selecione sua equipe na caixa de combinação **Equipe** para iniciar o processo de assinatura automática.

    ![Seleção da equipe](automatic-provisioning-images/prov3.png)

4. Isso inicia o processo de assinatura automática. Em seguida, o Visual Studio tenta gerar a ID do aplicativo, o perfil de provisionamento e a identidade de assinatura para usar esses artefatos para a assinatura. É possível ver o processo de geração na saída do build:

    ![Saída do build mostrando a geração de artefatos](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>Disparando o provisionamento automático

Quando a assinatura automática tiver sido habilitada, o Visual Studio para Mac atualizará esses artefatos, se necessário, quando qualquer uma das seguintes ações ocorrem:

* Um dispositivo iOS é conectado ao seu Mac
    - Isso verifica automaticamente se o dispositivo está registrado no Portal do Desenvolvedor da Apple. Se ele não estiver, será adicionado, e um novo perfil de provisionamento que o contenha será gerado.
* A ID do Pacote do seu aplicativo é alterada
    - Isso atualiza a ID do aplicativo. É criado um novo perfil de provisionamento que contém esta ID do aplicativo.
* Uma funcionalidade compatível é habilitada no arquivo Entitlements.plist.
    - Essa funcionalidade será adicionada à ID do aplicativo, e um novo perfil de provisionamento com a ID do aplicativo atualizada será gerado.
    - Nem todas as funcionalidades são compatíveis no momento. Para obter mais informações sobre as compatíveis, confira o guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md).


## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
