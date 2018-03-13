---
title: Trabalhando com grupos de aplicativos
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 9365bc2707876816419bb5d136a6a1011cf129d7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="working-with-app-groups"></a>Trabalhando com grupos de aplicativos


Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- Apple Watch [configurações](~/ios/watchos/app-fundamentals/settings.md).
- Compartilhado [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Compartilhado [arquivos](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

### <a name="provisioning"></a>Provisionamento

O grupo do aplicativo terá um identificador, que geralmente é a ID do pacote com um `group.` prefixo. Por exemplo, podemos usar a ID do pacote `com.xamarin.WatchSettings` e o grupo de aplicativo `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Use com.xamarin.WatchSettings a ID do pacote e o group.com.xamarin.WatchSettings de grupo do aplicativo")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Bem como configurar o perfil de provisionamento, **habilitar grupos de aplicativos** no **Entitlements.plist** e insira a ID que você escolheu:

[![](app-groups-images/entitlements-sml.png "Configurar o plist e insira a ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Implantação

Certifique-se de configurar o grupo de aplicativo corretamente em seu [implantação](~/ios/watchos/deploy-test/index.md#App_Groups) provisionamento.


Para obter mais informações, consulte o [recursos de grupo do aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentação.


## <a name="related-links"></a>Links relacionados

- [Apple compartilhamento de dados com seu aplicativo contém](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Doc de grupo do aplicativo da Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
