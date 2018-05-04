---
title: Compartilhando código
description: Conceitos fundamentais do aplicativo
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 01116a35dca80cd92ea16232a2abb127f60d9f0a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="sharing-code"></a>Compartilhando código

Esta seção fornece um guia sobre alguns dos conceitos que os desenvolvedores precisam estar atento ao desenvolvimento de aplicativos móveis ou tarefas de coisas mais comuns.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Visão geral do compartilhamento de código](code-sharing.md)

Saiba mais sobre o código de diferente opções disponíveis para projetos de Xamarin, incluindo bibliotecas de classes portáteis (PCLs), projetos compartilhados e as bibliotecas .NET padrão de compartilhamento.


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)

Projetos de biblioteca de classes portátil permitem que você crie e distribua assemblies que contêm código compartilhado para ser executado em várias plataformas. Para criar uma biblioteca de classes portátil (ou "PCL"), você primeiro selecionar quais plataformas de destino, em seguida, escrever código contra um sub conjunto do .NET Framework que está disponível no perfil definido para essas plataformas. Este documento descreve como criar e usar PCLs com Xamarin.

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)

Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica de plataforma da base de código compartilhado. Este artigo aborda como projetos compartilhados funcionam e como criar e usá-los com Xamarin projetos.

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET padrão é uma nova opção para compartilhar código entre as plataformas. Ele funciona de maneira semelhante para bibliotecas de classes portáteis; código foi desenvolvido com uma versão específica (atualmente 1.0 por meio de 1.6) e, em seguida, pode ser consumida por outros projetos que oferecem suporte a esse nível ou superior. Projetos .NET padrão têm suporte no Xamarin Studio 6.2, o Visual Studio para Windows e o Visual Studio para Mac.

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Projetos do NuGet: Bibliotecas multiplataforma para compartilhamento de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Pacotes do NuGet podem ser gerados automaticamente de projetos de padrão PCL ou .NET; e projetos compartilhados podem ser empacotados em pacotes do NuGet "isco" usando o tipo de projeto separado do NuGet. Esta seção explica como criar pacotes do NuGet para cada cenário de compartilhamento de código.

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Criar manualmente os pacotes do NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Dicas para criar pacotes do NuGet que funcionam com a plataforma do Xamarin.
