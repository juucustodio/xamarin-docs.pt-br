---
title: Introdução ao C
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8dff45de6de7c9492b199f323656778ac5c34d57
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-c"></a>Introdução ao C


## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com C você precisará de um máquina executando Mac ou Windows:

* macOS 10.12 (Serra) ou posterior
* O Xcode 8.3.2 ou posterior

* Windows 7, 8, 10 ou posterior
* Visual Studio 2015 ou posterior

* [Mono](http://www.mono-project.com/download/)


## <a name="installation"></a>Instalação

A próxima etapa é baixar e instalar as ferramentas de incorporação do .NET em seu computador.

Compilações de binárias para os geradores de C e Java ainda não estão disponíveis, mas serão disponibilizados em breve.

Como alternativa, é possível criar do nosso repositório git, consulte o [contribuindo](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento para obter instruções.


## <a name="generation"></a>Geração

Para gerar o código C, invocar a ferramenta .NET inserindo passando os sinalizadores de direito para direcionar a linguagem C:

### <a name="windows"></a>Windows:

```csharp
$ build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

Certifique-se de chamada de um shell de comando do Visual Studio específicas para a versão do Visual Studio está direcionando.

### <a name="macos"></a>macOS

```csharp
$ mono build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="output-files"></a>Arquivos de saída

Se tudo correr bem, você verá a seguinte saída:

```csharp
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

Como o `--compile` sinalizador foi passado para a ferramenta, inserindo .NET deve também ter compilado os arquivos de saída em uma biblioteca compartilhada, você pode encontrar ao lado de arquivos gerados, uma `libmanaged.dylib` arquivo macOS, e `managed.dll` no Windows.

Para consumir a biblioteca compartilhada, você pode incluir o `managed.h` arquivo de cabeçalho C, que fornece as declarações de C correspondente para o respectivo gerenciados biblioteca de APIs e vincular com mencionadas anteriormente compilados biblioteca compartilhada.

## <a name="further-reading"></a>Leitura adicional

* [Limitações de Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
