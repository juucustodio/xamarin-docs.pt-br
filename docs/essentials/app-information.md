---
title: 'Xamarin.Essentials: Informações do aplicativo'
description: Este documento descreve a classe AppInfo no Xamarin.Essentials, que fornece informações sobre o aplicativo. Por exemplo, ela expõe o nome e a versão do aplicativo.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 01/29/2019
ms.custom: video
ms.openlocfilehash: 69d0cb503d329ccfb4c29fb6cc4a589bef97e893
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756988"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informações do aplicativo

A classe **AppInfo** fornece informações sobre seu aplicativo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Como usar AppInfo

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obter informações do aplicativo:

As informações a seguir são expostas por meio da API:

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Exibir as configurações do aplicativo

A classe **AppInfo** também pode exibir uma página de configurações mantidas pelo sistema operacional para o aplicativo:

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

Esta página de configurações permite ao usuário alterar permissões do aplicativo e executar outras tarefas específicas à plataforma.

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Informações do aplicativo são obtidas do `AndroidManifest.xml` para os seguintes campos:

- **Build** – `android:versionCode` no nó `manifest`
- **Name** - `android:label` no nó `application`
- **PackageName**: `package` no nó `manifest`
- **VersionString** – `android:versionName` no nó `application`

# <a name="iostabios"></a>[iOS](#tab/ios)

Informações do aplicativo são obtidas do `Info.plist` para os seguintes campos:

- **Build** – `CFBundleVersion`
- **Name** - `CFBundleDisplayName` se definido, caso contrário `CFBundleName`
- **PackageName**: `CFBundleIdentifier`
- **VersionString** – `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Informações do aplicativo são obtidas do `Package.appxmanifest` para os seguintes campos:

- **Build** – usa o `Build` da `Version` no nó `Identity`
- **Name** - `DisplayName` no nó `Properties`
- **PackageName**: `Name` no nó `Identity`
- **VersionString** – `Version` no nó `Identity`

--------------

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
