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
ms.openlocfilehash: 09d20ef2e4fece36ce67a7891892defb05a5f3df
ms.sourcegitcommit: ca1eca5bcf6c82bc4ec05db12ee1be585006d59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88514623"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionamento automático para o Xamarin.iOS

_Depois que o Xamarin. iOS tiver sido instalado com êxito, a próxima etapa no desenvolvimento do iOS será provisionar seu dispositivo iOS. Este guia explora o uso do provisionamento automático para solicitar perfis e certificados de desenvolvimento._

## <a name="requirements"></a>Requisitos

O provisionamento automático está disponível em Visual Studio para Mac, Visual Studio 2019 e Visual Studio 2017 (versão 15,7 e superior). 

> [!NOTE]
> Você também deve ter uma conta de desenvolvedor da Apple paga para usar esse recurso. Mais informações sobre as contas de desenvolvedor da Apple estão disponíveis no guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) .
> Se você não tiver uma conta de desenvolvedor da Apple paga, consulte o guia [provisionamento gratuito para Xamarin. iOS] (~/Ios/Get-Started/Installation/Device-Provisioning/Free-Provisioning.MD).

> [!NOTE]
> Antes de começar, certifique-se de primeiro aceitar todos os contratos de licença no [portal do desenvolvedor da Apple](https://developer.apple.com/account/) ou no [App Store Connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Habilitar o provisionamento automático

Antes de iniciar o processo de assinatura automático, verifique se você tem uma ID da Apple adicionada no Visual Studio, conforme descrito no guia de [Gerenciamento de conta da Apple](~/cross-platform/macios/apple-account-management.md) . 

Depois de adicionar um ID Apple, será possível usar qualquer _Equipe_ associada. Isso permite que certificados, perfis e outras IDs sejam feitas com relação à equipe. A ID de equipe também é usada para criar um prefixo para uma ID de aplicativo que será incluída no perfil de provisionamento. Isso permite que a Apple verifique que você é quem diz que ser.

Para assinar seu aplicativo automaticamente para implantação em um dispositivo iOS, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra o projeto iOS no Visual Studio para Mac.

2. Abra o arquivo **info. plist** .

3. Selecione a guia **aplicativo** .

4. Na seção **Assinatura**, selecione **Provisionamento automático**:

    ![Lista suspensa do seletor de equipe](automatic-provisioning-images/image2.png)

5. Selecione sua equipe na lista suspensa **Equipe**.

6. Depois de alguns segundos, um Certificado de Assinatura e um Perfil de Provisionamento serão criados:

    ![certificado e perfil criados com sucesso](automatic-provisioning-images/image5.png)

    Se a assinatura automática falhar, o **Painel de assinatura automática** exibirá o motivo do erro.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Se você estiver usando o Visual Studio 2017 ou o Visual Studio 2019 (versão 16,4 e mais antigo), você precisará ser [emparelhado a um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) antes de continuar.

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto do IOS e selecione **Propriedades**. Em seguida, navegue até a guia **assinatura do pacote do IOS** :

    ![Captura de tela da página de assinatura do pacote nas propriedades do iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Selecione o esquema de **provisionamento automático** .

3. Selecione sua equipe no menu suspenso da **equipe** para iniciar o processo de assinatura automática. O Visual Studio indicará se o processo foi concluído com êxito:

    ![Captura de tela da página de assinatura do pacote realçando a mensagem "provisionamento automático concluído com êxito".](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Executar provisionamento automático

Quando o provisionamento automático estiver habilitado, o Visual Studio executará novamente o processo, se necessário, quando qualquer uma das seguintes ações acontecer:

- Um dispositivo iOS é conectado ao seu Mac
  - Isso verifica automaticamente se o dispositivo está registrado no Portal do Desenvolvedor da Apple. Se não estiver, ele será adicionado e gerará um novo perfil de provisionamento que o contém.
- A ID do Pacote do seu aplicativo é alterada
  - Isso atualiza a ID do aplicativo. É criado um novo perfil de provisionamento que contém esta ID do aplicativo.
- Uma funcionalidade compatível é habilitada no arquivo Entitlements.plist.
  - Essa funcionalidade será adicionada à ID do aplicativo, e um novo perfil de provisionamento com a ID do aplicativo atualizada será gerado.
  - Nem todas as funcionalidades são compatíveis no momento. Para obter mais informações sobre as compatíveis, confira o guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>IDs de Aplicativo Curinga

No Visual Studio para Mac e no Visual Studio 2019 (versão 16,5 ou superior), o provisionamento automático, por padrão, tenta criar e usar uma ID de aplicativo curinga e um perfil de provisionamento em vez de uma ID de aplicativo explícita com base no **identificador de pacote** especificado em **info. plist**. IDs de Aplicativo Curinga reduzem o número de perfis e de IDs mantidos no Portal do Desenvolvedor da Apple.

Em alguns casos, os direitos do aplicativo exigem uma ID do Aplicativo explícita. Os seguintes direitos não dão suporte a IDs de Aplicativo curinga:

- Grupos de Aplicativos
- Domínios Associados
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- Ponto de acesso
- Compra no aplicativo
- Multipath
- NFC
- VPN pessoal
- Notificações por Push
- Configuração de Acessório sem Fio

Se seu aplicativo usar um desses direitos, o Visual Studio tentará criar uma ID de aplicativo explícita (em vez de uma curinga).

## <a name="troubleshoot"></a>Solucionar problemas 

- Pode levar várias horas para que uma nova conta de desenvolvedor da Apple seja aprovada. Você não poderá habilitar o provisionamento automático até que a conta tenha sido aprovada.
- Se o processo de provisionamento automático falhar com a mensagem de erro `Authentication Service Is Unavailable` , entre na [App Store Connect](https://appstoreconnect.apple.com/) ou [appleid.Apple.com](https://appleid.apple.com) para verificar se você aceitou os contratos de serviço mais recentes.
- Se você receber a mensagem de erro `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.` , verifique se a equipe selecionada tem uma associação paga ativa ao programa de desenvolvedor da Apple. Para usar uma conta de desenvolvedor da Apple paga, confira o guia de [provisionamento gratuito para aplicativos Xamarin. Ios](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="related-links"></a>Links Relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
