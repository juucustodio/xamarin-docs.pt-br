---
title: Provisionamento automático
description: Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da Assinatura automática no Visual Studio para Mac para solicitar certificados e perfis de desenvolvimento.
ms.topic: article
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 11/17/2017
ms.openlocfilehash: 271d9e3f7ae04f03a132ae2fd0ebf531fe52578c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="automatic-provisioning"></a>Provisionamento automático

_Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da assinatura automática no Visual Studio para Mac para solicitar certificados e perfis de desenvolvimento._

## <a name="requirements"></a>Requisitos

- Visual Studio para Mac 7.3 ou posterior
- Xcode 9 ou posterior

> [!IMPORTANT]
> Este guia mostra como usar o Visual Studio para Mac para configurar um dispositivo da Apple para implantação e como implantar um aplicativo. Para obter etapas manuais de como fazer isso ou para fazer isso com o Visual Studio no Windows, é recomendável que você siga as etapas detalhadas no guia de [provisionamento manual](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

## <a name="enabling-automatic-signing"></a>Habilitando a Assinatura automática

Antes de iniciar o processo de assinatura automática, é necessário garantir que você tem uma ID Apple no Visual Studio para Mac, conforme descrito no guia [Gerenciamento da conta da Apple](~/cross-platform/macios/apple-account-management.md). Depois de adicionar um ID Apple, será possível usar qualquer _Equipe_ associada. Isso permite que certificados, perfis e outras IDs sejam feitas com relação à equipe. A ID da equipe também é usada para criar um prefixo para uma ID do aplicativo que será incluída no perfil de provisionamento. Isso permite que a Apple verifique que você é quem diz que ser.

Para assinar seu aplicativo automaticamente para implantação em um dispositivo iOS, faça o seguinte:

1. Abra o projeto iOS no Visual Studio para Mac.

2. Abra o arquivo **Info.plist**.

3. Na seção **Assinatura**, selecione **Provisionamento automático**:

    ![Lista suspensa do seletor de equipe](automatic-provisioning-images/image2.png)

4. Selecione sua equipe na lista suspensa **Equipe**.

6. Depois de alguns segundos, um Certificado de Assinatura e um Perfil de Provisionamento serão criados:

    ![certificado e perfil criados com sucesso](automatic-provisioning-images/image5.png)

    Se a assinatura automática falhar, o **Painel de assinatura automática** exibirá o motivo do erro.

## <a name="triggering-automatic-provisioning"></a>Disparando o provisionamento automático

Quando a assinatura automática tiver sido habilitada, o Visual Studio para Mac atualizará esses artefatos, se necessário, quando qualquer uma das seguintes ações ocorrem:

* Um dispositivo iOS for conectado ao seu Mac
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
