---
title: Inserção do .NET no Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007334"
---
# <a name="net-embedding-on-android"></a>Inserção do .NET no Android

Em alguns casos, talvez você queira adicionar uma biblioteca do Xamarin .NET a um projeto Android nativo existente. Para fazer isso, você pode usar a ferramenta [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/) para transformar sua biblioteca do .net em uma biblioteca nativa que pode ser incorporada em um aplicativo Android baseado em Java nativo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisitos do Xamarin. Android

Para que o Xamarin. Android funcione com a inserção do .NET, você precisará do seguinte:

- O **xamarin. android** &ndash;[xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve estar instalado.

- **Android Studio** &ndash;   [Android Studio 3. x](https://developer.android.com/studio/) ou posterior deve estar instalado.

- O Java **Developer Kit** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve estar instalado.

## <a name="using-embeddinator-4000"></a>Usando Embeddinator-4000

Para consumir uma biblioteca .NET em um projeto nativo do Android, use as seguintes etapas:

1. Crie um C# projeto de biblioteca do Android.

2. Instale o [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Localize **Embeddinator-4000. exe** e adicione-o ao seu **caminho**. Por exemplo:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. Execute Embeddinator-4000 no assembly da biblioteca. Por exemplo:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Use o arquivo AAR gerado em um projeto Java no Android Studio.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisitos do Xamarin. Android

Para que o Xamarin. Android funcione com a inserção do .NET, você precisará do seguinte:

- O **xamarin. android** &ndash;[xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou posterior deve estar instalado.

- **Android Studio** &ndash;   [Android Studio 3. x](https://developer.android.com/studio/) ou posterior deve estar instalado.

- O Java **Developer Kit** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve estar instalado.

- O mono &ndash;[mono 5,0](https://www.mono-project.com/download/) ou posterior deve estar instalado ( **o mono é** instalado com Visual Studio para Mac).

## <a name="using-embeddinator-4000"></a>Usando Embeddinator-4000

Para consumir uma biblioteca .NET em um projeto nativo do Android, use as seguintes etapas:

1. Crie um C# projeto de biblioteca do Android.

2. Instale o [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Localize **Embeddinator-4000. exe** e adicione **mono** ao seu caminho. Por exemplo:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. Execute Embeddinator-4000 no assembly da biblioteca. Por exemplo:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Use o arquivo AAR gerado em um projeto Java no Android Studio.

-----

As opções de uso e linha de comando são descritas na documentação do [Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) .

## <a name="callbacks"></a>Retornos

Saiba como [fazer chamadas entre C# o e o Java](callbacks.md).

## <a name="samples"></a>Exemplos

- [Aplicativo de exemplo meteorológico](https://github.com/jamesmontemagno/embeddinator-weather)
