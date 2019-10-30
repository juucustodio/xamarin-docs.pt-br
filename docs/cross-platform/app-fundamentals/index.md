---
title: Compartilhando código em várias plataformas
description: Este documento contém links para vários guias que descrevem técnicas para o compartilhamento de código, incluindo bibliotecas de classes portáteis, projetos compartilhados, .NET Standard e NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016821"
---
# <a name="sharing-code-on-multiple-platforms"></a>Compartilhando código em várias plataformas

Estes artigos explicam as diferentes opções disponíveis para o compartilhamento de código entre plataformas, incluindo Windows, Android, iOS e muito mais.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Visão geral de compartilhamento de código](code-sharing.md)

Saiba mais sobre as diferentes opções de compartilhamento de código disponíveis para projetos do Xamarin, incluindo bibliotecas de .NET Standard e projetos compartilhados. As bibliotecas de classes portáteis também têm suporte, no entanto, elas são consideradas preteridas em favor da .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard é a opção preferida para compartilhar código entre plataformas. O código é criado com base em uma versão específica (2,0 fornece a melhor compatibilidade de API com o código de .NET Framework existente) e, em seguida, pode ser consumido por outros projetos que dão suporte a esse nível ou mais. .NET Standard projetos têm suporte no Visual Studio 2019 e no Visual Studio 2019 para Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)

Projetos compartilhados permitem que você escreva código comum que é referenciado por vários projetos de aplicativo diferentes. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica da plataforma na base de código compartilhada. Este artigo discute como os projetos compartilhados funcionam e como criá-los e usá-los com projetos do Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)

Os projetos de biblioteca de classes portáteis permitem criar e distribuir assemblies que contêm código compartilhado para serem executados em várias plataformas. Para criar uma biblioteca de classes portátil (ou "PCL"), primeiro selecione as plataformas a serem direcionadas e, em seguida, escreva o código em um subconjunto do .NET Framework que está disponível no perfil definido para essas plataformas. PCLs são considerados preteridos nas versões mais recentes do Visual Studio; os desenvolvedores são incentivados a usar o .NET Standard 2,0 em vez disso.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Projetos do NuGet: bibliotecas multiplataforma para o compartilhamento de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Os pacotes NuGet podem ser gerados automaticamente de projetos PCL ou .NET Standard; e os projetos compartilhados podem ser empacotados em pacotes NuGet "bait and switch" usando o tipo de projeto do NuGet separado. Esta seção explica como criar pacotes NuGet para cada cenário de compartilhamento de código.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Criando manualmente pacotes NuGet para o Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Dicas para criar pacotes NuGet que funcionam com a plataforma Xamarin.

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[Usar C/C++ Libraries em projetos Xamarin de plataforma cruzada](~/cross-platform/cpp/index.md)

Essa técnica permite que você Desassocie a evolução de suas bibliotecas CC++ /, uma C# associação em um NuGet e seus aplicativos Xamarin. A funcionalidade é fornecida pela plataforma C/C++ biblioteca nativa, mas todo o código específico da plataforma é isolado do (s) aplicativo (es) final do Xamarin, permitindo o melhor desempenho possível sem duplicação de código. 
