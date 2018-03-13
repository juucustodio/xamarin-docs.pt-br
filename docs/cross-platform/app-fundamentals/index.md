---
title: "Conceitos básicos de aplicativo"
description: Conceitos fundamentais do aplicativo
ms.topic: article
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: c5b823370e5b65fbcf9ba366cb89c05e003b1a89
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="application-fundamentals"></a>Conceitos básicos de aplicativo

Esta seção fornece um guia sobre alguns dos conceitos que os desenvolvedores precisam estar atento ao desenvolvimento de aplicativos móveis ou tarefas de coisas mais comuns.

##  <a name="building-cross-platform-applicationscross-platformapp-fundamentalsbuilding-cross-platform-applicationsindexmd"></a>[Compilar aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)

Escolhendo o Xamarin e manter alguns pontos em mente ao projetar e desenvolver aplicativos móveis, você pode perceber um enorme código compartilhamento entre plataformas móveis, reduzir o tempo de entrega, aproveitar talent existente, atender à demanda de cliente de acesso móvel, e reduzir a complexidade de plataforma cruzada. &nbsp;Este documento descreve as diretrizes importantes para concretizar estas vantagens para aplicativos de produtividade e utilitário.

## <a name="code-sharing-optionscode-sharingmd"></a>[Opções de compartilhamento de código](code-sharing.md)

Saiba mais sobre o código de diferente opções disponíveis para projetos de Xamarin, incluindo bibliotecas de classes portáteis (PCLs), projetos compartilhados e as bibliotecas .NET padrão de compartilhamento.


## <a name="accessibilityaccessibilitymd"></a>[Acessibilidade](accessibility.md)

Dicas para a criação de aplicativos acessíveis.


## <a name="localizationlocalizationmd"></a>[Localização](localization.md)

Diretrizes para criação de aplicativos com reconhecimento de localidade que podem ser convertidos em vários idiomas.


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

##  <a name="cross-platform-data-accessxamarin-formsdata-cloudindexmd"></a>[Acesso de dados de plataforma entre](~/xamarin-forms/data-cloud/index.md)

A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados. iOS e Android tem o mecanismo de banco de dados SQLite "interno" e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. O [acesso a dados Android](~/android/data-cloud/data-access/index.md), [acesso a dados iOS](~/ios/data-cloud/data/index.md), e [acesso a dados xamarin. Forms](~/xamarin-forms/data-cloud/index.md) guias fornecem exemplos de como acessar SQLite em cada plataforma.


##  <a name="transport-layer-securitytransport-layer-securitymd"></a>[Segurança da camada de transporte](transport-layer-security.md)

Informações sobre selectingthe implementação correta de SSL/TLS para proteger a conectividade de rede do seu aplicativo.


##  <a name="notificationsxamarin-formsdata-cloudpush-notificationsindexmd"></a>[Notificações](~/xamarin-forms/data-cloud/push-notifications/index.md)

Aplicativos móveis usam notificações como uma maneira simples de informando ao usuário que ocorreu algum evento específico do aplicativo. As notificações normalmente são usadas para notificar o usuário sobre o status de um processo do aplicativo que está em execução em segundo plano. Um exemplo disso pode estar baixando um arquivo grande. Esse arquivo pode levar muito tempo para baixar, portanto essa atividade deve ocorrer em segundo plano. Quando o download for concluído, o usuário será informado de fato por uma notificação.
Além disso, notificação ares não limita apenas para aplicativos locais. Também é possível para aplicativos de servidor publicar as notificações de aplicativos móveis. Este artigo discutirá como usar notificações no Android e iOS.
