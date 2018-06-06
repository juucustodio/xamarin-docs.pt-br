---
title: 'Xamarin.Essentials: Informações do aplicativo'
description: Este documento descreve a classe de AppInfo Xamarin.Essentials, que fornece informações sobre o seu aplicativo. Por exemplo, ele expõe o nome do aplicativo e a versão.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 25765fbbcbd2475bfcbc72ca5c4feefa8ef19d08
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782099"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informações do aplicativo

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

- [Código-fonte AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
