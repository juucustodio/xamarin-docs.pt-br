---
title: Introdução ao Java
description: Este documento descreve como começar a usar a inserção do .NET com o Java. Ele aborda os requisitos do sistema, a instalação e as plataformas com suporte.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 09ea33724c2b1184654ce7768ea1cb2525b62c28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007372"
---
# <a name="getting-started-with-java"></a>Introdução ao Java

Esta é a página de introdução para Java, que aborda os conceitos básicos de todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com Java, você precisará de:

* Java 1,8 ou posterior
* [Mono 5,0](https://www.mono-project.com/download/)

Para Mac:

* Xcode 8.3.2 ou posterior

Para Windows:

* Visual Studio 2017 com C++ suporte
* SDK do Windows 10

Para Android:

* [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou posterior
* [Android Studio 3. x](https://developer.android.com/studio/index.html) com Java 1,8

Você pode usar [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) para editar e compilar seu C# código.

> [!NOTE]
> Versões anteriores do Xcode, Visual Studio, Xamarin. Android, Android Studio e mono _podem_ funcionar, mas não são testadas e não suportadas.

## <a name="installation"></a>Instalação

Atualmente, a incorporação .NET está disponível no [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Isso irá posicionar o **Embeddinator-4000. exe** no diretório **Packages/Embeddinator-4000/Tools** .

Além disso, você pode criar a incorporação .NET da origem, ver nosso [repositório git](https://github.com/mono/Embeddinator-4000/) e o documento [colaborador](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) para obter instruções.

## <a name="platforms"></a>Plataformas

O Java está atualmente em um estado de visualização para macOS, Windows e Android.

A plataforma é selecionada passando o argumento de linha de comando `--platform=<platform>` para a ferramenta de inserção .NET. Atualmente `macOS`, `Windows`e `Android` têm suporte.

### <a name="macos-and-windows"></a>macOS e Windows

Para o desenvolvimento, você deve ser capaz de usar qualquer Java IDE que ofereça suporte a Java 1,8. Você pode até mesmo usar Android Studio para isso, se desejar, [Consulte aqui](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Você pode usar a saída do arquivo JAR como faria com qualquer arquivo JAR Java padrão.

### <a name="android"></a>Android

Verifique se você já está configurado para desenvolver aplicativos Android antes de tentar criar um usando a inserção .NET. As [instruções a seguir](~/tools/dotnet-embedding/get-started/java/android.md) pressupõem que você já criou e implantou com êxito um aplicativo Android do seu computador.

Android Studio é recomendado para o desenvolvimento, mas outras IDEs devem funcionar desde que haja suporte para o [formato de arquivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Leitura adicional

* [Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Retornos de chamada no Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Pesquisa do Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitações de incorporação do .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Códigos de erro e descrições](~/tools/dotnet-embedding/errors.md)
