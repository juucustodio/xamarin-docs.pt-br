---
title: Introdução à integração contínua com o Xamarin
description: Integração contínua é uma prática de engenharia de software no qual uma compilação automatizada compila e, opcionalmente, testa um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo discutirá os conceitos gerais de integração contínua e algumas das opções disponíveis para a integração contínua com Xamarin projetos.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/04/2017
ms.openlocfilehash: 54f3d3c475e506e7d451af5125e90a0f51aa7374
ms.sourcegitcommit: c9ebf456e1c6924956bedb13f4ea78ff09f7b1a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introdução à integração contínua com o Xamarin

_Integração contínua é uma prática de engenharia de software no qual uma compilação automatizada compila e, opcionalmente, testa um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo discutirá os conceitos gerais de integração contínua e algumas das opções disponíveis para a integração contínua com Xamarin projetos._

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]


##  <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introdução à integração contínua](~/tools/ci/intro-to-ci.md)

Esta seção aborda os diferentes componentes envolvidos com a integração contínua e suas relações. Descreve os ambientes de integração contínua são discutidos nas seções específicas a seguir.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="working-with-continuous-integration-environments"></a>Trabalhando com os ambientes de integração contínua


### <a name="using-app-center-build-with-xamarinappcenterbuildxamarin"></a>[Usar a compilação do App Center com o Xamarin](/appcenter/build/xamarin/)

Crie soluções xamarin e xamarin com o Centro de aplicativo, diretamente do GitHub, VSTS ou Bitbucket.

### <a name="using-teamcity-with-xamarintoolsciteamcitymd"></a>[Usar TeamCity com Xamarin](~/tools/ci/teamcity.md)

Este guia aborda as etapas envolvidas com o uso de TeamCity para compilar aplicativos móveis e, em seguida, enviá-los para teste do Centro de aplicativo.

### <a name="using-jenkins-with-xamarintoolscijenkins-walkthroughmd"></a>[Usar Jenkins com Xamarin](~/tools/ci/jenkins-walkthrough.md)

Este guia mostra como configurar Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com o Xamarin. Descreve como instalar Jenkins nos X, configurá-lo e configurar os trabalhos para compilar aplicativos xamarin e xamarin quando as alterações são confirmadas no sistema de controle de versão.
