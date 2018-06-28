---
title: .NET inserindo no Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067263"
---
# <a name="net-embedding-on-android"></a>.NET inserindo no Android

Em alguns casos, convém adicionar uma biblioteca .NET Xamarin a um projeto Android nativo existente. Para fazer isso, você pode usar o [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) ferramenta transformar sua biblioteca do .NET em uma biblioteca nativa que pode ser incorporada em um aplicativo do Android native baseados em Java.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Requisitos do xamarin

Xamarin trabalhar com a inserção do .NET, você precisará do seguinte:

-   **Xamarin** &ndash; [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve ser instalado.

-   **O Android Studio** &ndash; [Android Studio 3. x](https://developer.android.com/studio/) ou posterior deve ser instalado.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve ser instalado.


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Para consumir uma biblioteca .NET em um projeto Android nativo, use as seguintes etapas:

1.  Crie um projeto de biblioteca Android c#.

2.  Instalar [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Localize **Embeddinator 4000.exe** e adicioná-lo ao seu **caminho**. Por exemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Execute Embeddinator 4000 no assembly de biblioteca. Por exemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Use o arquivo AAR gerado em um projeto Java no Android Studio.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Requisitos do xamarin

Xamarin trabalhar com a inserção do .NET, você precisará do seguinte:

-   **Xamarin** &ndash; [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve ser instalado.

-   **O Android Studio** &ndash; [Android Studio 3. x](https://developer.android.com/studio/) ou posterior deve ser instalado.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve ser instalado.

-   **Mono** &ndash; [5.0 Mono](http://www.mono-project.com/download/) ou posterior deve ser instalado (mono é instalado com o Visual Studio para Mac).


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Para consumir uma biblioteca .NET em um projeto Android nativo, use as seguintes etapas:

1.  Crie um projeto de biblioteca Android c#.

2.  Instalar [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Localize **Embeddinator 4000.exe** e adicione **mono** ao seu caminho. Por exemplo:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Execute Embeddinator 4000 no assembly de biblioteca. Por exemplo:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Use o arquivo AAR gerado em um projeto Java no Android Studio.

-----

Opções de linha de comando e de uso são descritas no [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentação.


## <a name="callbacks"></a>Retornos de chamada

Saiba mais sobre [fazer chamadas entre c# e Java](callbacks.md).

## <a name="samples"></a>Exemplos

* [Aplicativo de exemplo de previsão do tempo](https://github.com/jamesmontemagno/embeddinator-weather)
