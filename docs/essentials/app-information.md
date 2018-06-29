---
title: 'Xamarin.Essentials: Informações do aplicativo'
description: Este documento descreve a classe de AppInfo Xamarin.Essentials, que fornece informações sobre o seu aplicativo. Por exemplo, ele expõe o nome do aplicativo e a versão.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080268"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informações do aplicativo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **AppInfo** classe fornece informações sobre o seu aplicativo.

## <a name="using-appinfo"></a>Usando AppInfo

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtendo informações do aplicativo:

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

## <a name="displaying-application-settings"></a>Exibindo configurações de aplicativo

O **AppInfo** classe também pode exibir uma página de configurações mantidos pelo sistema operacional para o aplicativo:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Esta página de configurações permite ao usuário alterar permissões do aplicativo e executar outras tarefas de plataforma específica.

## <a name="api"></a>API

- [Código-fonte AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
