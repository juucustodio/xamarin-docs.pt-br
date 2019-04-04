---
title: .NET incorporação no Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 6917267896cff796af4e5cff095720eaeccc7652
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671152"
---
# <a name="net-embedding-on-android"></a>.NET incorporação no Android

Em alguns casos, você talvez queira adicionar uma biblioteca .NET do Xamarin para um projeto existente do Android nativo. Para fazer isso, você pode usar o [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) ferramenta transformar sua biblioteca do .NET em uma biblioteca nativa que pode ser incorporada a um aplicativo Android nativo baseado em Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisitos do xamarin. Android

Para xamarin. Android trabalhar com a inserção de .NET, você precisará do seguinte:

-   **Xamarin. Android** &ndash; [xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve ser instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) ou posterior deve ser instalado.

-   **Java Developer Kit** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve ser instalado.


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Para consumir uma biblioteca do .NET em um projeto do Android nativo, use as seguintes etapas:

1.  Criar um C# projeto de biblioteca Android.

2.  Instale [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Localize **Embeddinator 4000.exe** e adicioná-lo ao seu **caminho**. Por exemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Execute Embeddinator 4000 no assembly de biblioteca. Por exemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Use o arquivo AAR gerado em um projeto Java no Android Studio.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisitos do xamarin. Android

Para xamarin. Android trabalhar com a inserção de .NET, você precisará do seguinte:

-   **Xamarin. Android** &ndash; [xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve ser instalado.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) ou posterior deve ser instalado.

-   **Java Developer Kit** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve ser instalado.

-   **Mono** &ndash; [5.0 Mono](https://www.mono-project.com/download/) ou posterior deve ser instalado (mono está instalado com o Visual Studio para Mac).


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Para consumir uma biblioteca do .NET em um projeto do Android nativo, use as seguintes etapas:

1.  Criar um C# projeto de biblioteca Android.

2.  Instale [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

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

Opções de uso e de linha de comando são descritas na [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentação.


## <a name="callbacks"></a>Retornos de chamada

Saiba mais sobre [fazer chamadas entre C# e Java](callbacks.md).

## <a name="samples"></a>Exemplos

* [Aplicativo de exemplo de clima](https://github.com/jamesmontemagno/embeddinator-weather)
