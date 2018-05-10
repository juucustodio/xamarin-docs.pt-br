---
title: Guia de Introdução com Java
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: e88e3c8d2dbabebcfff22687d8ea341233a776b5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-java"></a>Guia de Introdução com Java

Esta é a página de Introdução para Java, que abrange as Noções básicas para todas as plataformas com suporte.

## <a name="requirements"></a>Requisitos

Para usar a inserção do .NET com Java, você precisará de:

* Java 1.8 ou posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Para Mac:

* O Xcode 8.3.2 ou posterior

Para Windows:

* Visual Studio de 2017 com suporte do C++
* Windows 10 SDK

Para o Android:

* [Xamarin 7.5](https://www.visualstudio.com/xamarin/) ou posterior
* [O Android Studio 3. x](https://developer.android.com/studio/index.html) com Java 1.8

Você pode usar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar e compilar o código c#.

> [!NOTE]
> Versões anteriores do Xcode, Visual Studio, xamarin, Android Studio e Mono _pode_ funcionar, mas não testado e sem suporte.

## <a name="installation"></a>Instalação

Inserindo .NET está disponível atualmente no [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Isso colocará **Embeddinator 4000.exe** para o **pacotes/Embeddinator-4000/ferramentas** directory.

Além disso, você pode criar .NET incorporação de fonte, consulte nosso [repositório git](https://github.com/mono/Embeddinator-4000/) e [contribuindo](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) documento para obter instruções.

## <a name="platforms"></a>Plataformas

Java está atualmente em um estado de visualização para macOS, Android e Windows.

A plataforma está selecionada, passando o `--platform=<platform>` argumento de linha de comando para a ferramenta de inserção de .NET. No momento `macOS`, `Windows`, e `Android` têm suporte.

### <a name="macos-and-windows"></a>macOS e Windows

Para o desenvolvimento, você deve ser capaz de usar qualquer IDE de Java que dá suporte ao Java 1.8. Você pode até usar Android Studio para isso se desejar, [ver aqui](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Como você faria com qualquer arquivo de jar Java padrão, você pode usar a saída do arquivo JAR.

### <a name="android"></a>Android

Verifique se você já estiver configurado para desenvolver aplicativos do Android antes de tentar criar um usando a incorporação de .NET. O [seguindo instruções](~/tools/dotnet-embedding/get-started/java/android.md) pressupõem que você já com êxito compilado e implantado um aplicativo Android do seu computador.

O Android Studio é recomendado para desenvolvimento, mas outros IDEs deve funcionar, contanto que não há suporte para o [formato de arquivo AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Leitura adicional

* [Guia de Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Retornos de chamada no Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Pesquisa Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitações de incorporação de .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
