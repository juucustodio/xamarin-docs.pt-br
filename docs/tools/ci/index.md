---
title: Introdução à integração contínua com o Xamarin
description: Este documento contém links para guias que descrevem a integração contínua com o Xamarin. Conteúdo vinculado fornece uma visão geral da integração contínua e discute App Center Build, TeamCity e Jenkins.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: davidortinau
ms.author: daortin
ms.date: 10/23/2018
ms.openlocfilehash: c59667213f7ecbe5603e638c4b19d54ffdf6dd01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029823"
---
# <a name="continuous-integration-with-xamarin"></a>Integração contínua com o Xamarin

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introdução à integração contínua](~/tools/ci/intro-to-ci.md)

Esta seção aborda os diferentes componentes envolvidos na integração contínua e suas relações. Ele descreve os ambientes de integração contínua que são discutidos nas seções específicas abaixo.

## <a name="devops-with-xamarintoolscidevopsmd"></a>[DevOps com o Xamarin](~/tools/ci/devops.md)

Esta seção identifica quais recursos do DevOps no Azure e no Visual Studio você pode esperar funcionar bem com um projeto do Xamarin.

## <a name="working-with-continuous-integration-environments"></a>Trabalhando com ambientes de integração contínua

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[Compilar aplicativos Xamarin com Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

Use Azure Pipelines para criar automaticamente aplicativos Xamarin para Android e iOS.

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[Compilar aplicativos Xamarin usando App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

Crie soluções Xamarin. iOS e Xamarin. Android com App Center, diretamente do GitHub, do Azure DevOps ou do bitbucket.

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[Compilar aplicativos Xamarin com TeamCity](~/tools/ci/teamcity.md)

Este guia discute as etapas envolvidas no uso do TeamCity para compilar aplicativos móveis e, em seguida, enviá-los para App Center teste.

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[Compilar aplicativos Xamarin com Jenkins](~/tools/ci/jenkins-walkthrough.md)

Este guia ilustra como configurar o Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com o Xamarin. Ele descreve como instalar o Jenkins no OS X, configurá-lo e configurar trabalhos para compilar aplicativos Xamarin. iOS e Xamarin. Android quando as alterações são confirmadas no sistema de controle de versão.
