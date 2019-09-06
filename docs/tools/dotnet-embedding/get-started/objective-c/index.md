---
title: Introdução ao Objective-C
description: Este documento descreve como começar a usar a inserção do .NET com o Objective-C. Ele aborda os requisitos, a instalação da incorporação .NET do NuGet e das plataformas com suporte.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: 66b99e0da574c50df32afedb1dd6dc9de315b347
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278461"
---
# <a name="getting-started-with-objective-c"></a>Introdução ao Objective-C

Esta é a página de introdução do Objective-C, que abrange os fundamentos de todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com o Objective-C, você precisará de um Mac em execução:

- macOS 10,12 (Sierra) ou posterior
- Xcode 8.3.2 ou posterior
- [Mono 5,0](https://www.mono-project.com/download/)

Você pode instalar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar e compilar seu C# código.

> [!NOTE]
> - Versões anteriores do macOS, Xcode e mono _podem_ funcionar, mas não são testadas e não suportadas
> - A geração de código pode ser feita no Windows, mas só é possível compilá-la em um computador Mac em que o Xcode está instalado

## <a name="installing-net-embedding-from-nuget"></a>Instalando a incorporação .NET do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a inserção do .net para seu projeto.

Uma invocação de comando de exemplo está listada nos guias de introdução do [MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e do [Ios](~/tools/dotnet-embedding/get-started/objective-c/ios.md) .

## <a name="platforms"></a>Plataformas

Objective-C é uma linguagem usada com mais frequência para escrever aplicativos para macOS, iOS, tvOS e watchOS; a inserção do .NET oferece suporte a todas essas plataformas. Trabalhar com cada plataforma implica algumas [diferenças importantes e elas são explicadas aqui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[A criação de um aplicativo MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) é mais fácil, pois não envolve tantas etapas adicionais, como configurar a identidade, perfis de Continue, simuladores e dispositivos. Você é incentivado a começar com o documento do macOS antes daquele para iOS.

### <a name="ios--tvos"></a>iOS/tvOS

Verifique se você já está configurado para desenvolver aplicativos iOS antes de tentar criar um usando a inserção .NET. As [instruções a seguir](~/tools/dotnet-embedding/get-started/objective-c/ios.md) pressupõem que você já criou e implantou com êxito um aplicativo IOS do seu computador.

O suporte para tvOS é análogo à forma como o iOS funciona, usando apenas projetos tvOS nos IDEs (Visual Studio e Xcode) em vez de projetos do iOS.

> [!NOTE]
> O suporte para watchOS estará disponível em uma versão futura e será muito semelhante ao iOS/tvOS.

## <a name="further-reading"></a>Leitura adicional

- [Recursos de incorporação do .NET específicos do Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
- [Práticas recomendadas para o Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)
- [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
