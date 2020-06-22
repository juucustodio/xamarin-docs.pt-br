---
title: 'Xamarin.Essentials: Informações do aplicativo'
description: Este documento descreve a classe AppInfo no Xamarin.Essentials , que fornece informações sobre seu aplicativo. Por exemplo, ela expõe o nome e a versão do aplicativo.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 01/29/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a8673e7c405660355e3a849e6ef1709fd2980a4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802514"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informações do aplicativo

A classe **AppInfo** fornece informações sobre seu aplicativo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Como usar AppInfo

Adicione uma referência a Xamarin.Essentials em sua classe:

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

# <a name="android"></a>[Android](#tab/android)

Informações do aplicativo são obtidas do `AndroidManifest.xml` para os seguintes campos:

- **Compilação** – `android:versionCode` no `manifest` nó
- **Nome do**  -  `android:label` no `application` nó
- **PackageName**: `package` no `manifest` nó
- **VersionString** – `android:versionName` no `application` nó

# <a name="ios"></a>[iOS](#tab/ios)

Informações do aplicativo são obtidas do `Info.plist` para os seguintes campos:

- **Compilação** –`CFBundleVersion`
- **Nome do**  -  `CFBundleDisplayName` Se definido, senão`CFBundleName`
- **PackageName**:`CFBundleIdentifier`
- **Versãostring** –`CFBundleShortVersionString`

# <a name="uwp"></a>[UWP](#tab/uwp)

Informações do aplicativo são obtidas do `Package.appxmanifest` para os seguintes campos:

- **Build** – usa o `Build` da `Version` no nó `Identity`
- **Nome do**  -  `DisplayName` no `Properties` nó
- **PackageName**: `Name` no `Identity` nó
- **VersionString** – `Version` no `Identity` nó

--------------

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
