---
title: 'Xamarin.Essentials: Acompanhamento de versão'
description: A classe VersionTracking no Xamarin.Essentials permite que você verifique a versão dos aplicativos e os números de compilação, juntamente com informações adicionais, como se fosse a primeira vez em que o aplicativo foi iniciado ou para a versão atual, obtenha as informações de compilação anteriores e muito mais.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20819d76c23ca43f60073bcc2cd762abda280374
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802042"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Acompanhamento de versão

A classe **VersionTracking** permite que você verifique a versão e os números de build dos aplicativos, junto com informações adicionais, por exemplo, se é a primeira vez que o aplicativo é iniciado ou, para a versão atual, obter informações do build anterior e muito mais.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>Como usar Version Tracking

Adicione uma referência a Xamarin.Essentials em sua classe:

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

Todas as informações de versão são armazenadas usando a API de [preferências](preferences.md) no Xamarin.Essentials e são armazenadas com um nome de arquivo **[Your-app-Package-ID]. xamarinessentials. versiontracking** e segue a mesma persistência de dados descrita na documentação de [preferências](preferences.md#persistence) .

## <a name="api"></a>API

- [Código-fonte de Controle de Versão](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/VersionTracking)
- [Documentação da API de Controle de Versão](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
