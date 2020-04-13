---
title: Provisionamento automático para o Xamarin.iOS
description: Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso da assinatura automática para solicitar certificados e perfis de desenvolvimento.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510673"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionamento automático para o Xamarin.iOS

_Uma vez que o Xamarin.iOS tenha sido instalado com sucesso, o próximo passo no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia explora o uso de provisionamento automático para solicitar certificados de desenvolvimento e perfis._

## <a name="requirements"></a>Requisitos

O provisionamento automático está disponível no Visual Studio para Mac, Visual Studio 2019 e Visual Studio 2017 (Versão 15.7 ou superior). 

> [!NOTE]
> Você também deve ter uma conta paga do Apple Developer para usar esse recurso. Mais informações sobre as contas de desenvolvedores da Apple estão disponíveis no guia [de provisionamento de dispositivos.](~/ios/get-started/installation/device-provisioning/index.md)
> Se você não tiver uma conta de desenvolvedor apple paga, consulte o guia [Provisionamento gratuito para Xamarin.iOS] (~/ios/get-started/installation/device-provisioning/free-provisioning.md).

> [!NOTE]
> Antes de começar, certifique-se de primeiro aceitar quaisquer contratos de licença no [portal Apple Developer](https://developer.apple.com/account/) ou app store [connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Habilitar o provisionamento automático

Antes de iniciar o processo de assinatura automática, você deve garantir que você tenha um ID apple adicionado no Visual Studio, conforme descrito no guia [de gerenciamento de contas da Apple.](~/cross-platform/macios/apple-account-management.md) 

Depois de adicionar um ID Apple, será possível usar qualquer _Equipe_ associada. Isso permite que certificados, perfis e outras IDs sejam feitas com relação à equipe. O ID da equipe também é usado para criar um prefixo para um ID de aplicativo que será incluído no perfil de provisionamento. Isso permite que a Apple verifique que você é quem diz que ser.

Para assinar seu aplicativo automaticamente para implantação em um dispositivo iOS, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra o projeto iOS no Visual Studio para Mac.

2. Abra o arquivo **Info.plist.**

3. Na seção **Assinatura**, selecione **Provisionamento automático**:

    ![Lista suspensa do seletor de equipe](automatic-provisioning-images/image2.png)

4. Selecione sua equipe na lista suspensa **Equipe**.

5. Depois de alguns segundos, um Certificado de Assinatura e um Perfil de Provisionamento serão criados:

    ![certificado e perfil criados com sucesso](automatic-provisioning-images/image5.png)

    Se a assinatura automática falhar, o **Painel de assinatura automática** exibirá o motivo do erro.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Se você estiver usando o Visual Studio 2017 ou o Visual Studio 2019 (versão 16.4 ou mais velha), você precisará ser [emparelhado a um host de construção Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) antes de prosseguir.

1. No **Solution Explorer,** clique com o botão direito do mouse no nome do projeto do iOS e selecione **Propriedades**. Em seguida, navegue até a guia **de assinatura do pacote do iOS:**

    ![Captura de tela da página de assinatura do pacote nas propriedades do iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Selecione o **esquema de provisionamento automático.**

3. Selecione sua equipe no menu suspenso **da Equipe** para iniciar o processo de assinatura automática. O Visual Studio indicará se o processo foi concluído com sucesso:

    ![Captura de tela da página de assinatura do pacote destacando a mensagem "Provisionamento automático concluído com sucesso".](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Executar provisionamento automático

Quando o provisionamento automático estiver ativado, o Visual Studio reexecutará o processo se necessário quando alguma das seguintes coisas acontecer:

- Um dispositivo iOS é conectado ao seu Mac
  - Isso verifica automaticamente se o dispositivo está registrado no Portal do Desenvolvedor da Apple. Se não for, ele irá adicioná-lo e gerar um novo perfil de provisionamento que o contenha.
- A ID do Pacote do seu aplicativo é alterada
  - Isso atualiza a ID do aplicativo. É criado um novo perfil de provisionamento que contém esta ID do aplicativo.
- Uma funcionalidade compatível é habilitada no arquivo Entitlements.plist.
  - Essa funcionalidade será adicionada à ID do aplicativo, e um novo perfil de provisionamento com a ID do aplicativo atualizada será gerado.
  - Nem todas as funcionalidades são compatíveis no momento. Para obter mais informações sobre as compatíveis, confira o guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>IDs de Aplicativo Curinga

No Visual Studio para Mac e Visual Studio 2019 (versão 16.5 ou superior), o provisionamento automático será por padrão tentar criar e usar um perfil de id e provisionamento de um aplicativo curinga em vez de um ID de aplicativo explícito com base no **Identificador de Pacote** especificado no **Info.plist**. IDs de Aplicativo Curinga reduzem o número de perfis e de IDs mantidos no Portal do Desenvolvedor da Apple.

Em alguns casos, os direitos do aplicativo exigem uma ID do Aplicativo explícita. Os seguintes direitos não dão suporte a IDs de Aplicativo curinga:

- Grupos de Aplicativos
- Domínios Associados
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- Ponto de acesso
- Compra no aplicativo
- Múltiplos caminhos
- NFC
- VPN pessoal
- Notificações por Push
- Configuração de Acessório sem Fio

Se o seu aplicativo usar um desses direitos, o Visual Studio tentará criar um ID de aplicativo explícito (em vez de um curinga).

## <a name="troubleshoot"></a>Solucionar problemas 

- Pode levar várias horas para que uma nova conta de desenvolvedor da Apple seja aprovada. Você não poderá habilitar o provisionamento automático até que a conta seja aprovada.
- Se o processo de provisionamento `Authentication Service Is Unavailable`automático falhar com a mensagem de erro, entre no [App Store Connect](https://appstoreconnect.apple.com/) ou [appleid.apple.com](https://appleid.apple.com) para verificar se você aceitou os últimos contratos de serviço.
- Se você receber `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`a mensagem de erro, certifique-se de que a equipe selecionada tenha uma associação paga ativa para o Programa de Desenvolvedor da Apple. Para usar uma conta paga do desenvolvedor da Apple, consulte o guia [de provisionamento gratuito para aplicativos Xamarin.iOS.](~/ios/get-started/installation/device-provisioning/free-provisioning.md)

## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativos](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
