---
title: Guia de Introdução ao Objective-C
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 96afe6bbfd1d9c6f4fd5ca3b7358ef0b89da30bb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-objective-c"></a>Guia de Introdução ao Objective-C

Esta é a página de Introdução para Objective-C, que abrange as Noções básicas para todas as plataformas com suporte.


## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com Objective-C, você precisará um Mac executando:

* macOS 10.12 (Serra) ou posterior
* O Xcode 8.3.2 ou posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Você pode instalar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar e compilar o código c#.


Notas:

* Versões anteriores do macOS, Xcode e Mono _pode_ funcionar, mas não testado e sem suporte;
* Geração de código pode ser feita no Windows, mas só é possível compilá-lo em um computador Mac onde o Xcode está instalado;


## <a name="installation"></a>Instalação

A próxima etapa é baixar e instalar o .NET inserindo no seu Mac.

* [Package](https://dl.xamarin.com/embeddinator/Xamarin.Embeddinator-4000-0.2.0.79.pkg)
* [Notas de Versão](https://github.com/mono/Embeddinator-4000/tree/master/docs/releases)

Como alternativa, é possível compilá-lo do nosso [repositório git](https://github.com/mono/Embeddinator-4000/tree/objc), consulte o [contribuindo](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento para obter instruções.

O instalador é um instalador de pacote padrão com base em:

![Introdução de instalador](images/install1.png)
![tipo de instalação do instalador](images/install2.png)
![resumo de instalador](images/install3.png)

Depois de instalado por meio do instalador, depois de iniciar uma nova sessão de terminal, você pode usar o `objcgen` comando.
Caso contrário, você também pode executar a ferramenta por meio de seu caminho absoluto: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`.

## <a name="platforms"></a>Plataformas

Objective-C é uma linguagem que é mais comumente usada para escrever aplicativos para macOS, iOS, tvOS e watchOS; e o embeddinator oferece suporte a todas as plataformas. Trabalhar com cada plataforma implica algumas das principais diferenças e eles são explicados [aqui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Criando um aplicativo macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) é mais fácil, desde que ela não abrange como muitas etapas adicionais, como configurar a identidade, perfis de provisining, simuladores e dispositivos. Você é incentivado a iniciar com o documento macOS antes do iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verifique se que você já estiver configurado para desenvolver aplicativos do iOS antes de tentar criar um usando o embeddinator. O [seguindo instruções](~/tools/dotnet-embedding/get-started/objective-c/ios.md) pressupõem que você já com êxito compilado e implantado um aplicativo iOS do seu computador.

Suporte para tvOS é semelhante à maneira como funciona a iOS, usando apenas projetos tvOS nos IDEs (Visual Studio e Xcode) em vez de projetos do iOS.

> [!NOTE]
> Observação: O suporte para watchOS estarão disponível em uma versão futura e será muito semelhante para iOS/tvOS.


## <a name="further-reading"></a>Leitura adicional

* [.NET incorporar recursos específicos para Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Práticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitações de incorporação de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)


## <a name="related-links"></a>Links relacionados

- [Exemplo de clima (iOS e macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
