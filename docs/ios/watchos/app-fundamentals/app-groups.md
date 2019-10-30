---
title: Trabalhando com grupos de aplicativos watchOS no Xamarin
description: Este documento descreve os grupos de aplicativos e seu uso em um aplicativo watchOS. Ele discute como configurar um grupo de aplicativos, requisitos de provisionamento, considerações de direitos. plist e implantação.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e117fce77e9cdc8d9e9dc8b9ed7b3aa22eca4e39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001707"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Trabalhando com grupos de aplicativos watchOS no Xamarin

Um grupo de aplicativos permite que diferentes aplicativos (ou um aplicativo e suas extensões) acessem um local de armazenamento de arquivo compartilhado. Grupos de aplicativo podem ser usados para dados como:

- [Configurações](~/ios/watchos/app-fundamentals/settings.md)de Apple Watch.
- [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)compartilhados.
- [Arquivos](~/ios/watchos/app-fundamentals/parent-app.md#files)compartilhados.

## <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

### <a name="provisioning"></a>Provisionamento

O grupo de aplicativos terá um identificador, que normalmente é a ID do pacote com um prefixo `group.`. Por exemplo, poderíamos usar a ID do pacote `com.xamarin.WatchSettings` e o grupo de aplicativos `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Use the Bundle ID com.xamarin.WatchSettings and the app group   group.com.xamarin.WatchSettings")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Além de configurar o perfil de provisionamento, **habilite grupos de aplicativos** no **intitulars. plist** e insira a ID que você escolheu:

[![](app-groups-images/entitlements-sml.png "Configure the plist and enter the ID")](app-groups-images/entitlements.png#lightbox)

### <a name="deployment"></a>Implantação

Certifique-se de configurar o grupo de aplicativos corretamente em seu provisionamento de [implantação](~/ios/watchos/deploy-test/index.md#App_Groups) .

Para obter mais informações, consulte a documentação de [recursos do grupo de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Links relacionados

- [Dados de compartilhamento da Apple com seu aplicativo recipiente](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Doc do grupo de aplicativos da Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
