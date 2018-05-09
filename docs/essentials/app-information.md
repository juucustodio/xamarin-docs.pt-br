---
title: Informações do aplicativo Xamarin.Essentials
description: A classe AppInfo fornece informações sobre o seu aplicativo.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5b235fdeb666c3f8f2c1b52a9691c931724ce2b8
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-app-information"></a>Informações do aplicativo Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **AppInfo** classe fornece informações sobre o seu aplicativo.

## <a name="using-appinfo"></a>Usando AppInfo

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

As informações a seguir são exibidas por meio da API:

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

## <a name="api"></a>API

- [Código-fonte AppInfo](https://github.com/xamarin/Essentials/tree/master/Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
