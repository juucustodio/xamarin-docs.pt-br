---
title: Introdução ao C
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: topgenorth
ms.author: toopge
ms.date: 04/19/2018
ms.openlocfilehash: f3c238dc9805dafa922f8e32fb4b1935a3fa152c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-c"></a>Introdução ao C

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com C, você precisará de um máquina executando Mac ou Windows:

### <a name="macos"></a>macOS

* macOS 10.12 (Serra) ou posterior
* O Xcode 8.3.2 ou posterior
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 ou posterior
* Visual Studio 2015 ou posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a incorporação do .NET para o seu projeto.

Você deve configurar a chamada do comando será semelhante (possivelmente com números de versão diferentes e caminhos):

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Geração

### <a name="output-files"></a>Arquivos de saída

Se tudo correr bem, você verá a seguinte saída:

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Como o `--compile` sinalizador foi passado para a ferramenta, inserindo .NET deve também ter compilado os arquivos de saída em uma biblioteca compartilhada, você pode encontrar ao lado de arquivos gerados, uma **libmanaged.dylib** arquivo macOS e **managed.dll** no Windows.

Para consumir a biblioteca compartilhada, você pode incluir o **managed.h** arquivo de cabeçalho C, que fornece as declarações de C correspondente para o respectivo gerenciados biblioteca de APIs e vincular com mencionadas anteriormente compilados biblioteca compartilhada.

## <a name="further-reading"></a>Leitura adicional

* [Limitações de incorporação de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
