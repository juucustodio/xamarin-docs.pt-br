---
title: Trabalhando com watchOS grupos de aplicativos em Xamarin
description: Este documento descreve os grupos de aplicativos e seu uso em um aplicativo watchOS. Ele discute como configurar um grupo de aplicativos, implantação, Entitlements.plist considerações e requisitos de provisionamento.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 5736b25af3993e2da794422a1a6f040461532497
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790673"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Trabalhando com watchOS grupos de aplicativos em Xamarin


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
