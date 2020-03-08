---
title: Provisionamento de dispositivos para o Xamarin.iOS
description: Este documento descreve como provisionar um dispositivo para que ele possa ser usado para testar um aplicativo. Ele também aborda como configurar um aplicativo para que ele possa usar recursos como notificações por push.
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: bb1ef1e948c796e6b0d91230be3c82c5a8c7d366
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910852"
---
# <a name="device-provisioning-for-xamarinios"></a>Provisionamento de dispositivos para o Xamarin.iOS

Ao desenvolver um aplicativo Xamarin.iOS, é essencial testá-lo implantando o aplicativo em um dispositivo físico, além do simulador. Problemas de desempenho e bugs exclusivos de dispositivos podem ocorrer durante a execução em um dispositivo, devido a limites de hardware como memória ou conectividade de rede. Para testar em um dispositivo físico, o dispositivo deve ser *provisionado* e a Apple deve ser informada de que o dispositivo será usado para teste.

As seções realçadas na imagem abaixo mostram as etapas necessárias para concluir a configuração para provisionamento do iOS:

[![](images/provisioningdiagram.png "The highlighted sections in this image show the steps required to get set up for iOS provisioning")](images/provisioningdiagram.png#lightbox)

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

## <a name="provisioning-your-device"></a>Provisionar o dispositivo

Há duas maneiras de provisionar o dispositivo iOS:

- **Automaticamente (Recomendado)** – Selecione o esquema **Provisionamento Automático** no projeto para que o Visual Studio crie e gerencie automaticamente as identidades de assinatura, as IDs de aplicativo e os perfis de provisionamento. Para obter informações sobre como gerenciar automaticamente o provisionamento, consulte o guia [Provisionamento automático](automatic-provisioning.md). Essa é a maneira recomendada para provisionar um dispositivo iOS.

- **Manualmente** – Identidades de assinatura, IDs do Aplicativo e Perfis de provisionamento podem ser criados e gerenciados por meio do Portal do Desenvolvedor da Apple, conforme descrito no guia [Provisionamento manual](manual-provisioning.md). Esses artefatos podem ser gerenciados conforme descrito no guia [Gerenciamento de Conta da Apple](~/cross-platform/macios/apple-account-management.md).

## <a name="provisioning-for-application-services"></a>Provisionando para Serviços de Aplicativos

A Apple fornece uma seleção de serviços de aplicativos especiais, também chamada de recursos, que podem ser ativados para o aplicativo Xamarin.iOS. Esses serviços de aplicativos devem ser configurados tanto no Portal de Provisionamento iOS, quando a **ID do aplicativo** é criada, quanto no arquivo **Entitlements.plist**, que é parte do projeto do aplicativo Xamarin.iOS. Para obter informações sobre como adicionar serviços de aplicativo no seu aplicativo, consulte os guias [Introdução aos recursos](~/ios/deploy-test/provisioning/capabilities/index.md) e [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).

- Crie uma ID do aplicativo com os serviços de aplicativo necessários.
- Crie um novo [perfil de provisionamento](#provisioning-your-device) que contenha essa ID do aplicativo.
- Definir direitos no projeto Xamarin.iOS

## <a name="related-links"></a>Links relacionados

- [Provisionamento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
- [Apple – Guia de distribuição de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
