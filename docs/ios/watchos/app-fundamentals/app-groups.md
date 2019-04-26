---
title: Trabalhando com grupos de aplicativos no Xamarin do watchOS
description: Este documento descreve os grupos de aplicativos e seu uso em um aplicativo do watchOS. Ele aborda como configurar um grupo de aplicativos, implantação, Entitlements. plist considerações e requisitos de provisionamento.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 78f6c03f73f0e4d8a74f826dd7bc25bbe325d545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61411665"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Trabalhando com grupos de aplicativos no Xamarin do watchOS


Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- Apple Watch [configurações](~/ios/watchos/app-fundamentals/settings.md).
- Compartilhado [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Compartilhado [arquivos](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

### <a name="provisioning"></a>Provisionamento

O grupo de aplicativos terá um identificador, que normalmente é sua ID do pacote com um `group.` prefixo. Por exemplo, poderíamos usar a ID do pacote `com.xamarin.WatchSettings` e o grupo de aplicativos `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Use com.xamarin.WatchSettings a ID do pacote e o group.com.xamarin.WatchSettings de grupo do aplicativo")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Bem como configurar o perfil de provisionamento **habilitar grupos de aplicativos** na **Entitlements. plist** e insira a ID que você escolheu:

[![](app-groups-images/entitlements-sml.png "Configurar o plist e insira a ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Implantação

Certifique-se de configurar o grupo de aplicativo corretamente em seu [implantação](~/ios/watchos/deploy-test/index.md#App_Groups) provisionamento.


Para obter mais informações, consulte o [recursos do grupo de aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentação.


## <a name="related-links"></a>Links relacionados

- [Apple compartilhamento de dados com seu aplicativo contém](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Doc do grupo de aplicativos da Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
