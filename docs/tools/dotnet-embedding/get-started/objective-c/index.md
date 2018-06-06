---
title: Guia de Introdução ao Objective-C
description: Este documento descreve como começar a usar a inserção do .NET com objetivo-C. Ele discute os requisitos, instalando o .NET incorporação do NuGet e plataformas com suporte.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c5db0a55cc1d2597837ae5feb2c5167a0a21b494
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792974"
---
# <a name="getting-started-with-objective-c"></a>Guia de Introdução ao Objective-C

Esta é a página de Introdução para Objective-C, que abrange as Noções básicas para todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com Objective-C, você precisará de um Mac executando:

* macOS 10.12 (Serra) ou posterior
* O Xcode 8.3.2 ou posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Você pode instalar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar e compilar o código c#.

> [!NOTE]
> * Versões anteriores do macOS, Xcode e Mono _pode_ funcionar, mas não testado e sem suporte
> * Geração de código pode ser feita no Windows, mas só é possível compilá-lo em um computador Mac onde o Xcode está instalado

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a incorporação do .NET para o seu projeto.

Uma invocação de comando de exemplo está listada no [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guias de Introdução.

## <a name="platforms"></a>Plataformas

Objective-C é uma linguagem que é mais comumente usada para escrever aplicativos para macOS, iOS, tvOS e watchOS; inserindo .NET oferece suporte a todas as plataformas. Trabalhar com cada plataforma implica um [principais diferenças e elas são explicadas aqui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Criando um aplicativo macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) é mais fácil, desde que ela não abrange como muitas etapas adicionais, como configurar a identidade, perfis de provisining, simuladores e dispositivos. Você é incentivado a iniciar com o documento macOS antes do iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verifique se você já estiver configurado para desenvolver aplicativos do iOS antes de tentar criar um usando a incorporação de .NET. O [seguindo instruções](~/tools/dotnet-embedding/get-started/objective-c/ios.md) pressupõem que você já com êxito compilado e implantado um aplicativo iOS do seu computador.

Suporte para tvOS é semelhante à maneira como funciona a iOS, usando apenas projetos tvOS nos IDEs (Visual Studio e Xcode) em vez de projetos do iOS.

> [!NOTE]
> Suporte para watchOS estarão disponíveis em uma versão futura e será muito semelhante para iOS/tvOS.

## <a name="further-reading"></a>Leitura adicional

* [.NET incorporar recursos específicos para Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Práticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitações de incorporação de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (iOS e macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
