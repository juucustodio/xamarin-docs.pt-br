---
title: Introdução ao C
description: Este documento descreve como usar a incorporação do .NET para inserir o código .NET em um aplicativo C. Ele aborda como usar a incorporação do .NET no Visual Studio de 2019 e o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: 342ba2a6b51483983df7bd04034a4cef62fd57ff
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213586"
---
# <a name="getting-started-with-c"></a>Introdução ao C

## <a name="requirements"></a>Requisitos

Para usar a incorporação do .NET com C, você precisará de um máquina executando Mac ou Windows:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) ou posterior
* 8.3.2 do Xcode ou posterior
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 ou posterior
* Visual Studio 2015 ou posterior

## <a name="installing-net-embedding-from-nuget"></a>Instalando o .NET incorporação do NuGet

Siga estas [instruções](~/tools/dotnet-embedding/get-started/install/install.md) para instalar e configurar a incorporação do .NET para o seu projeto.

A invocação de comando, que você deve configurar terá aparência (possivelmente com números de versão diferentes e caminhos):

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>geração

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

Uma vez que o `--compile` sinalizador foi passado para a ferramenta, a incorporação do .NET deve também ter compilado os arquivos de saída em uma biblioteca compartilhada, que você pode encontrar ao lado dos arquivos gerados, um **libmanaged.dylib** arquivo no macOS e no **managed.dll** no Windows.

Para consumir a biblioteca compartilhada, você pode incluir a **managed.h** arquivo de cabeçalho C, que fornece as declarações de C correspondentes para os respectivos gerenciado a APIs da biblioteca e vincular com mencionado anteriormente compilados biblioteca compartilhada.

## <a name="further-reading"></a>Leitura adicional

* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
