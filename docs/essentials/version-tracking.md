---
title: 'Xamarin.Essentials: Controle de versão'
description: A classe VersionTracking Xamarin.Essentials lhe permite verificar a versão de aplicativos e números de compilação junto com a ver informações adicionais tais como se fosse a primeira vez o aplicativo aberto nunca ou para a versão atual, obtém a compilação anterior informações e muito mais.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c092d6767045f0af956c5dab74801077dadb51f
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815642"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Controle de versão

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **VersionTracking** classe permite que você verifique a versão de aplicativos e números de compilação junto com a ver informações adicionais tais como se fosse a primeira vez que o aplicativo aberto nunca ou para a versão atual, obtém anterior informações de compilação e muito mais.

## <a name="using-version-tracking"></a>Usando o controle de versão

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Na primeira vez que você usar o **VersionTracking** classe iniciará a versão atual do controle. Você deve chamar `Track` antecipada somente em seu aplicativo sempre que ele é carregado para garantir que as informações de versão atual são rastreadas:

```csharp
VersionTracking.Track();
```

Após a inicial `Track` é chamado de informações de versão podem ser lidos:

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

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

Todas as informações de versão são armazenadas usando o [preferências](preferences.md) API no Xamarin.Essentials e é armazenado com um nome de arquivo do **.xamarinessentials [YOUR-APP-pacote-ID]**.

Desinstalar o aplicativo fará com que o _LocalSettings_e a versão de todas as informações a serem removidos do controle.

## <a name="api"></a>API

- [Código de origem de controle de versão](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [Documentação da API de controle de versão](xref:Xamarin.Essentials.VersionTracking)
