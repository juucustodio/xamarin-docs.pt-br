---
title: Compartilhamento de código em várias plataformas
description: Este documento leva a vários guias que descrevem as técnicas para o compartilhamento de código, incluindo bibliotecas de classes portáteis, projetos compartilhados, .NET Standard e NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 35c210cc75033f2ad2c83ed6f5196f71d26d000f
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854178"
---
# <a name="sharing-code-on-multiple-platforms"></a>Compartilhamento de código em várias plataformas

Estes artigos explicam as diferentes opções disponíveis para compartilhar código entre plataformas, incluindo Windows, Android, iOS e muito mais.

## [<a name="code-sharing-overview"></a>Visão geral do compartilhamento de código](code-sharing.md)

Saiba mais sobre o opções disponíveis para projetos do Xamarin, incluindo bibliotecas do .NET Standard e projetos compartilhados de compartilhamento de código diferente. Também há suporte para bibliotecas de classes portáteis, no entanto, eles são considerados preteridos em favor do .NET Standard.

## [<a name="net-standard"></a>.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard é a opção preferencial para compartilhar código entre plataformas. Código é compilado em relação a uma versão específica (2.0 fornece a melhor compatibilidade de API com o código existente do .NET Framework) e, em seguida, pode ser consumida por outros projetos que dão suporte a esse nível ou superior. Projetos do .NET standard têm suporte no Visual Studio de 2019 e 2019 do Visual Studio para Mac.

## [<a name="shared-projects"></a>Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)

Projetos compartilhados permitem que você escreva código comum que é referenciado por um número de projetos de aplicativo diferente. O código é compilado como parte de cada projeto de referência e pode incluir diretivas de compilador para ajudar a incorporar a funcionalidade específica da plataforma na base de código compartilhado. Este artigo discute como projetos compartilhados funcionam e como criar e usá-las com projetos do Xamarin.

## [<a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis](~/cross-platform/app-fundamentals/pcl.md)

Projetos de biblioteca de classes portáteis permitem que você compilar e distribuir assemblies que contêm o código compartilhado para ser executado em várias plataformas. Para criar uma biblioteca de classes portátil (ou "PCL"), você primeiro selecionar quais plataformas de destino e, em seguida, escrever código contra um sub conjunto do .NET Framework que está disponível no perfil definido para essas plataformas. PCLs são consideradas preterida nas versões mais recentes do Visual Studio; os desenvolvedores são incentivados a usar em vez disso, o .NET Standard 2.0.

## [<a name="nuget-projects-multiplatform-libraries-for-code-sharing"></a>Projetos do NuGet: Bibliotecas multiplataforma para o compartilhamento de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Pacotes do NuGet podem ser gerados automaticamente de projetos PCL ou .NET standard; e projetos compartilhados podem ser empacotados em pacotes do NuGet "de isca" usando o tipo de projeto NuGet separado. Esta seção explica como criar pacotes do NuGet para cada cenário de compartilhamento de código.

## [<a name="manually-creating-nuget-packages-for-xamarin"></a>Criação manual de pacotes do NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Dicas para a criação de pacotes do NuGet que funcionam com a plataforma Xamarin.

## [<a name="use-cc-libraries-in-cross-platform-xamarin-projects"></a>Usar bibliotecas de C/C++ em projetos do Xamarin de plataforma cruzada](~/cross-platform/cpp/index.md)

Essa técnica permite que você separe a evolução das suas bibliotecas do C/C++, um C# de associação em um NuGet e seus aplicativos Xamarin. Funcionalidade é fornecida pela biblioteca em C/C++ de plataforma nativa, mas todo o código específico da plataforma é isolado de aplicativos Xamarin finais, permitindo que o desempenho mais alto possível com nenhuma eliminação de duplicação de código. 
