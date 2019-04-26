---
title: Introdução ao Java
description: Este documento descreve como começar a usar a incorporação do .NET com o Java. Ele aborda os requisitos do sistema, instalação e plataformas com suporte.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: 79a483743946c4f7509833867f2afe4b1e055183
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61198936"
---
# <a name="getting-started-with-java"></a>Introdução ao Java

Isso é a página de Introdução para Java, que aborda os conceitos básicos para todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a incorporação do .NET com Java, você precisará de:

* Java 1.8 ou posterior
* [Mono 5.0](https://www.mono-project.com/download/)

Para Mac:

* 8.3.2 do Xcode ou posterior

Para Windows:

* Visual Studio 2017 com suporte do C++
* Windows 10 SDK

Para Android:

* [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) ou posterior
* [Android Studio 3.x](https://developer.android.com/studio/index.html) com Java 1.8

Você pode usar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar e compilar seu C# código.

> [!NOTE]
> Versões anteriores do Visual Studio, Xcode, xamarin. Android, Android Studio e Mono _talvez_ funcionar, mas são não testados e sem suporte.

## <a name="installation"></a>Instalação

Incorporação do .NET está disponível atualmente no [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Isso colocará **Embeddinator 4000.exe** para o **pacotes/Embeddinator-4000/tools** directory.

Além disso, você pode criar .NET incorporação de fonte, consulte nosso [repositório do git](https://github.com/mono/Embeddinator-4000/) e o [contribuindo](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) documento para obter instruções.

## <a name="platforms"></a>Plataformas

Java está atualmente em um estado de visualização para macOS, Windows e Android.

A plataforma é selecionada, passando o `--platform=<platform>` argumento de linha de comando para a ferramenta de incorporação do .NET. No momento `macOS`, `Windows`, e `Android` têm suporte.

### <a name="macos-and-windows"></a>macOS e Windows

Para o desenvolvimento, você deve ser capaz de usar qualquer IDE de Java que dá suporte a Java 1.8. Você pode até usar o Studio Android para que isso se desejar, [ver aqui](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Como você faria com qualquer arquivo de jar Java padrão, você pode usar a saída do arquivo JAR.

### <a name="android"></a>Android

Verifique se você já estiver configurado para desenvolver aplicativos Android antes de tentar criar um usando a incorporação do .NET. O [seguindo instruções](~/tools/dotnet-embedding/get-started/java/android.md) pressupõem que você já criada e implantada com sucesso um aplicativo Android do seu computador.

Android Studio é recomendado para implantação, mas outros IDEs deve funcionar, contanto que não há suporte para o [formato de arquivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Leitura adicional

* [Guia de Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Retornos de chamada no Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Pesquisa preliminar de Android](~/tools/dotnet-embedding/android/index.md)
* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
