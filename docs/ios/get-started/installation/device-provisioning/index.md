---
title: Provisionamento de Dispositivo
description: "Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia mostrará a solicitação de certificados de desenvolvimento e perfis, o trabalho com serviços de aplicativos e a implantação de um aplicativo no dispositivo."
ms.topic: article
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 07/15/2017
ms.openlocfilehash: 3ff77e909c86e627a6a1ea4875d8bef7db3f8d63
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="device-provisioning"></a>Provisionamento de Dispositivo

_Após o Xamarin.iOS ter sido instalado com êxito, a próxima etapa no desenvolvimento do iOS é provisionar seu dispositivo iOS. Este guia mostrará a solicitação de certificados de desenvolvimento e perfis, o trabalho com serviços de aplicativos e a implantação de um aplicativo no dispositivo._

Ao desenvolver um aplicativo Xamarin.iOS, é essencial testá-lo implantando o aplicativo em um dispositivo físico, além do simulador. Problemas de desempenho e bugs exclusivos de dispositivos podem ocorrer durante a execução em um dispositivo, devido a limites de hardware como memória ou conectividade de rede. Para testar em um dispositivo físico, o dispositivo deve ser *provisionado* e a Apple deve ser informada de que o dispositivo será usado para teste.

As seções realçadas na imagem abaixo mostram as etapas necessárias para concluir a configuração para provisionamento do iOS:

[![](images/provisioningdiagram.png "As seções realçadas nesta imagem mostram as etapas necessárias para concluir a configuração para provisionamento do iOS")](images/provisioningdiagram.png#lightbox)

Depois disso, a próxima etapa é distribuir o aplicativo. Para obter mais informações sobre a implantação, acesse os guias de [Distribuição de aplicativos](~/ios/deploy-test/app-distribution/index.md).

Antes de implantar o aplicativo em um dispositivo, você precisa ter uma assinatura ativa para o Programa de Desenvolvedores da Apple *ou* usar o [provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md). A Apple oferece duas opções de programa:

- **Programa de Desenvolvedores da Apple** – independentemente de você ser um indivíduo ou representante de uma organização, o Programa de Desenvolvedores da Apple permite a você desenvolver, testar e distribuir aplicativos.
- **Programa Corporativo de Desenvolvedores da Apple** – o programa corporativo é mais adequado para organizações que desejam desenvolver e distribuir aplicativos apenas internamente. Membros do programa Enterprise não têm acesso ao iTunes Connect e os aplicativos criados não podem ser publicados na App Store.


Para se registrar para qualquer um desses programas, visite o [Portal de Desenvolvedores da Apple](https://developer.apple.com/programs/enroll/). Observe que, para se registrar como um desenvolvedor da Apple, é necessário ter uma [ID Apple](https://appleid.apple.com/). Este guia foi criado assumindo que você **é** um membro de um Programa de Desenvolvedores da Apple.

Como alternativa, a Apple introduziu o [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) no Xcode 7, que permite que um aplicativo seja executado em um dispositivo *sem* ser membro do Programa de Desenvolvedores da Apple. Há uma série de limitações ao provisionar desse modo, conforme detalhado [aqui](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations).

Qualquer aplicativo que é executado em um dispositivo deve conter um conjunto de metadados (ou *impressão digital*) contendo informações sobre o aplicativo e o desenvolvedor. Apple usa essa impressão digital para certificar-se de que o aplicativo não seja adulterado durante a implantação ou a execução em um dispositivo do usuário. Isso é obtido ao exigir que os desenvolvedores de aplicativo registrem sua ID Apple como um desenvolvedor e, para configurar uma ID do aplicativo, solicitem um certificado e registrem o dispositivo no qual o aplicativo será implantado.

Ao implantar um aplicativo em um dispositivo, um perfil de provisionamento também é instalado no dispositivo iOS. O perfil de provisionamento existe para verificar as informações com as quais o aplicativo foi assinado no momento do build e é assinado criptograficamente pela Apple. Juntas, as verificações de perfil de provisionamento e 'impressão digital' determinam se um aplicativo pode ser implantado em um dispositivo ao verificar:

- **Quem** (Certificados – o aplicativo foi assinado com uma chave privada, que tem uma chave pública correspondente no perfil de provisionamento? O certificado também associa o desenvolvedor a uma equipe de desenvolvimento)
- **O que** (ID do aplicativo individual – o identificador do pacote definido em Info.plist corresponde à ID do aplicativo no perfil de provisionamento?)
- **Onde** (Dispositivos – o dispositivo está contido no perfil de provisionamento?)

Essas etapas garantem que tudo o que é criado ou usado durante o processo de desenvolvimento, incluindo os aplicativos e dispositivos, pode ser rastreado de volta a uma conta de Desenvolvedor da Apple.

<a name="Provisioning_Profile" />

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="provisioning-your-device"></a>Provisionando o dispositivo

Há duas maneiras de provisionar seu dispositivo iOS com o Visual Studio para Mac:

* **Automaticamente (Recomendado)** – Selecione a opção **Gerenciar assinatura automaticamente** no arquivo Info.plist para que o Visual Studio para Mac crie e gerencie automaticamente suas Identidades de assinatura, IDs do Aplicativo e Perfis de provisionamento.  Para obter informações sobre como gerenciar automaticamente o provisionamento, consulte o guia [Provisionamento automático](automatic-provisioning.md). Essa é a maneira recomendada para provisionar um dispositivo iOS.

* **Manualmente** – Identidades de assinatura, IDs do Aplicativo e Perfis de provisionamento podem ser criados e gerenciados por meio do Portal do Desenvolvedor da Apple, conforme descrito no guia [Provisionamento manual](manual-provisioning.md). Esses artefatos podem ser gerenciados conforme descrito no guia [Gerenciamento de Conta da Apple](~/cross-platform/macios/apple-account-management.md).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="provisioning-your-device"></a>Provisionando o dispositivo

Para obter as etapas sobre como configurar um dispositivo Apple para implantação e implantar um aplicativo com o Visual Studio no Windows, é recomendado seguir as etapas detalhadas no guia de [provisionamento manual](manual-provisioning.md).

-----

<a name="appservices" />

## <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de serviços de aplicativos especiais, também chamada de recursos, que podem ser ativados para o aplicativo Xamarin.iOS. Esses serviços de aplicativos devem ser configurados tanto no Portal de Provisionamento iOS, quando a **ID do aplicativo** é criada, quanto no arquivo **Entitlements.plist**, que é parte do projeto do aplicativo Xamarin.iOS. Para obter informações sobre como adicionar serviços de aplicativo no seu aplicativo, consulte os guias [Introdução aos recursos](~/ios/deploy-test/provisioning/capabilities/index.md) e [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).

* Crie uma ID do aplicativo com os serviços de aplicativo necessários.
* Crie um novo [perfil de provisionamento](#Provisioning_Profile) que contenha essa ID do aplicativo.
* Definir direitos no projeto Xamarin.iOS

> [!NOTE]
> Atualmente, os perfis de provisionamento criados no Visual Studio para Mac não levarão em conta os direitos selecionados nos projetos (Entitlements.plist). Essa funcionalidade será adicionada em versões futuras do IDE. Se você precisar usar os Serviços de Aplicativos, é recomendável que você siga as instruções no guia de [Provisionamento Manual](manual-provisioning.md).

## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
