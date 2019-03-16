---
title: Introdução à integração contínua com o Xamarin
description: 'Este documento leva a guias que descrevem a integração contínua com o Xamarin. Conteúdo vinculado fornece uma visão geral da integração contínua e discute a compilação do App Center, TeamCity e Jenkins.'
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: lobrien
ms.author: laobri
ms.date: 10/23/2018
---

# <a name="continuous-integration-with-xamarin"></a>Integração contínua com o Xamarin

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introdução à integração contínua](~/tools/ci/intro-to-ci.md)

Esta seção aborda os diferentes componentes envolvidos com a integração contínua e suas relações. Ele descreve os ambientes de integração contínua são discutidos nas seções específicas a seguir.

## <a name="devops-with-xamarintoolscidevopsmd"></a>[DevOps com o Xamarin](~/tools/ci/devops.md)

Esta seção identifica quais recursos de DevOps no Azure e o Visual Studio, você pode esperar que funcionem bem com um projeto Xamarin.

## <a name="working-with-continuous-integration-environments"></a>Trabalhando com ambientes de integração contínua

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[Compilar aplicativos Xamarim com Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

Use Pipelines do Azure para criar automaticamente os aplicativos Xamarin para Android e iOS.

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[Crie aplicativos do Xamarin usando o App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

Crie soluções xamarin. IOS e xamarin. Android com o App Center, diretamente do GitHub, Bitbucket ou do DevOps do Azure.

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[Compilar aplicativos Xamarim com TeamCity](~/tools/ci/teamcity.md)

Este guia aborda as etapas envolvidas com o uso TeamCity para compilar aplicativos móveis e, em seguida, enviá-los ao teste do App Center.

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[Compilar aplicativos Xamarim com Jenkins](~/tools/ci/jenkins-walkthrough.md)

Este guia mostra como configurar o Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com Xamarin. Ele descreve como instalar o Jenkins nos X, configurá-lo e configurar os trabalhos para compilar aplicativos xamarin. IOS e xamarin. Android quando as alterações são confirmadas no sistema de controle de versão.
