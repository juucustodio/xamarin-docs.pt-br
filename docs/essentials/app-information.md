---
title: 'Xamarin.Essentials: Informações do aplicativo'
description: Este documento descreve a classe AppInfo no Xamarin.Essentials, que fornece informações sobre o aplicativo. Por exemplo, ela expõe o nome e a versão do aplicativo.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 00419fb746609464b49be343938905614c59ab29
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691757"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informações do aplicativo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

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
AppInfo.OpenSettings();
```

Esta página de configurações permite ao usuário alterar permissões do aplicativo e executar outras tarefas específicas à plataforma.

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
