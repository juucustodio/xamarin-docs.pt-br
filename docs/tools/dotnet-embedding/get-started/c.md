---
title: Introdução ao C
description: Este documento descreve como usar a inserção .NET para inserir o código .NET em um aplicativo C. Ele aborda como usar a incorporação .NET no Visual Studio 2019 e Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: conceptdev
ms.author: crdun
ms.date: 04/19/2018
ms.openlocfilehash: 1dc68a709f8e1f864961bbe87af112b648b0dd2a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278733"
---
# <a name="getting-started-with-c"></a>Introdução ao C

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com C, você precisará de um computador Mac ou Windows executando:

### <a name="macos"></a>macOS

* macOS 10,12 (Sierra) ou posterior
* Xcode 8.3.2 ou posterior
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 ou posterior
* Visual Studio 2015 ou posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalando a incorporação .NET do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a inserção do .net para seu projeto.

A invocação de comando que deve ser configurada será semelhante (possivelmente com números de versão e caminhos diferentes):

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

Se tudo correr bem, você receberá a seguinte saída:

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

Como o `--compile` sinalizador foi passado para a ferramenta, a incorporação do .net também deve ter compilado os arquivos de saída em uma biblioteca compartilhada, que pode ser encontrada ao lado dos arquivos gerados, um arquivo **libmanaged. dylib** no MacOS e o **Managed. dll** no Windows.

Para consumir a biblioteca compartilhada, você pode incluir o arquivo de cabeçalho **. h C gerenciado** , que fornece as declarações de C correspondentes às APIs da biblioteca gerenciada e o link com a biblioteca compartilhada compilada mencionada anteriormente.

## <a name="further-reading"></a>Leitura adicional

* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)
