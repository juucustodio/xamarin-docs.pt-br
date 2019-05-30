---
title: 'Xamarin.Essentials: Monitoramento de versões'
description: A classe VersionTracking no Xamarin.Essentials permite que você verifique a versão e os números de build dos aplicativos, junto com informações adicionais, por exemplo, se é a primeira vez que o aplicativo é iniciado ou, para a versão atual, obter informações do build anterior e muito mais.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 3728a209c99712fad6b3dbf9bc59a2c1a3c7bcd5
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354119"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Monitoramento de versões

A classe **VersionTracking** permite que você verifique a versão e os números de build dos aplicativos, junto com informações adicionais, por exemplo, se é a primeira vez que o aplicativo é iniciado ou, para a versão atual, obter informações do build anterior e muito mais.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>Como usar Version Tracking

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A primeira vez que você usar a classe **VersionTracking**, ela começará a acompanhar a versão atual. Chame `Track` antecipadamente em seu aplicativo sempre que ele for carregado, a fim de garantir que as informações da versão atual sejam rastreadas:

```csharp
VersionTracking.Track();
```

Após a chamada da `Track` inicial, é possível ler as informações de versão:

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

Todas as informações de versão são armazenadas usando a API de [Preferências](preferences.md) no Xamarin.Essentials, são armazenadas com um nome de arquivo **[ID-DO-PACOTE-DO-SEU-APLICATIVO].xamarinessentials.versiontracking**, e seguem as mesmas persistência de dados descritas na documentação de [Preferences](preferences.md#persistence).

## <a name="api"></a>API

- [Código-fonte de Controle de Versão](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [Documentação da API de Controle de Versão](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
