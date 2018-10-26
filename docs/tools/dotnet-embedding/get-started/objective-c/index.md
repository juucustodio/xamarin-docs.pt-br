---
title: Introdução ao Objective-C
description: Este documento descreve como começar a usar a incorporação do .NET com Objective-C. Ele aborda os requisitos, instalando a incorporação do .NET do NuGet e plataformas com suporte.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c7bac0612679131383d3b89f24904c8083fa925b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103095"
---
# <a name="getting-started-with-objective-c"></a>Introdução ao Objective-C

Isso é a página de Introdução para Objective-C, que aborda os conceitos básicos para todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a incorporação do .NET com Objective-C, você precisará de um Mac executando:

* macOS 10.12 (Sierra) ou posterior
* 8.3.2 do Xcode ou posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Você pode instalar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar e compilar seu C# código.

> [!NOTE]
> * Versões anteriores do macOS, Xcode e Mono _talvez_ funcionar, mas são não testados e sem suporte
> * Geração de código pode ser feita no Windows, mas só é possível compilá-lo em um computador Mac onde o Xcode está instalado

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a incorporação do .NET para o seu projeto.

Uma invocação de comando de exemplo está listada na [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guias de Introdução.

## <a name="platforms"></a>Plataformas

Objective-C é uma linguagem que é mais comumente usada para escrever aplicativos para macOS, iOS, tvOS e watchOS; inserir .NET dá suporte a todas as plataformas. Trabalhar com cada plataforma implica alguns [as principais diferenças e elas são explicadas aqui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Criando um aplicativo macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) é mais fácil, pois ela não envolve quantas etapas adicionais, como configuração de identidade, perfis de provisionamento, simuladores e dispositivos. Você é incentivado a iniciar com o documento de macOS antes para iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verifique se você já estiver configurado para desenvolver aplicativos iOS antes de tentar criar um usando a incorporação do .NET. O [seguindo instruções](~/tools/dotnet-embedding/get-started/objective-c/ios.md) pressupõem que você já com êxito compilado e implantado um aplicativo iOS do seu computador.

Suporte para tvOS é análogo à maneira como funciona a iOS, usando apenas os projetos tvOS nos IDEs (Visual Studio e Xcode) em vez de projetos do iOS.

> [!NOTE]
> Suporte para o watchOS estará disponível em uma versão futura e será muito semelhante ao iOS/tvOS.

## <a name="further-reading"></a>Leitura adicional

* [Incorporação de .NET recursos específicos do Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Práticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (iOS e macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
