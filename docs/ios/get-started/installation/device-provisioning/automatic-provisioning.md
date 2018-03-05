---
title: "Provisionamento automático"
description: "Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da Assinatura automática no Visual Studio para Mac para solicitar certificados e perfis de desenvolvimento."
ms.topic: article
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/17/2017
ms.openlocfilehash: d7532d052c57ad46caca0cd6d6ce26d0e77dc05f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="automatic-provisioning"></a>Provisionamento automático

_Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da assinatura automática no Visual Studio para Mac para solicitar certificados e perfis de desenvolvimento._

## <a name="requirements"></a>Requisitos

- Visual Studio para Mac 7.3 ou posterior
- Xcode 9 ou posterior

> [!IMPORTANT]
>  Este guia mostra como usar o Visual Studio para Mac para configurar um dispositivo da Apple para implantação e como implantar um aplicativo. Para obter etapas manuais de como fazer isso ou para fazer isso com o Visual Studio no Windows, é recomendável que você siga as etapas detalhadas no guia de [provisionamento manual](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

## <a name="enabling-automatic-signing"></a>Habilitando a Assinatura automática

Antes de iniciar o processo de assinatura automática, é necessário garantir que você tem uma ID Apple no Visual Studio para Mac, conforme descrito no guia [Gerenciamento da conta da Apple](~/cross-platform/macios/apple-account-management.md). Depois de adicionar um ID Apple, será possível usar qualquer _Equipe_ associada. Isso permite que certificados, perfis e outras IDs sejam feitas com relação à equipe. A ID da equipe também é usada para criar um prefixo para uma ID do aplicativo que será incluída no perfil de provisionamento. Isso permite que a Apple verifique que você é quem diz que ser.

Para assinar seu aplicativo automaticamente para implantação em um dispositivo iOS, faça o seguinte:

1. Abra o projeto iOS no Visual Studio para Mac.

2. Abra o arquivo **Info.plist**:

    ![Arquivo Info.plist](automatic-provisioning-images/image1.png)

3. No seção **Assinatura**, selecione sua equipe na lista suspensa **Equipe**:

    ![Lista suspensa do seletor de equipe](automatic-provisioning-images/image2.png)

4. Marque a caixa de seleção **Gerenciar assinatura automaticamente**, conforme ilustrado abaixo. Isso inicia o processo de assinatura automática, em que o Visual Studio para Mac tentará gerar uma ID do aplicativo, um perfil de provisionamento e uma identidade de assinatura e definir esses artefatos para serem usados para assinatura. Quando a caixa de seleção tiver sido marcada, os controles manuais para selecionar identidades de assinatura serão desabilitados.

    ![Opção de gerenciar equipes automaticamente](automatic-provisioning-images/image3.png)

5. A seguinte caixa de diálogo será exibida, informando que o arquivo de projeto será modificado para usar o certificado e o perfil de provisionamento recém-gerados:

    ![caixa de diálogo que avisa que o arquivo de projeto será modificado](automatic-provisioning-images/image4.png)

6. Depois de alguns segundos, um Certificado de Assinatura e um Perfil de Provisionamento serão criados e exibidos:

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
