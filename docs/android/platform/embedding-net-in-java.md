---
title: Inserindo .NET em Java
description: Como usar uma biblioteca .NET Xamarin em um projeto do Android Native baseados em Java
ms.topic: article
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/28/2018
ms.openlocfilehash: f0da12d739c6003257d3acf9ccefdec7e36f5349
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="embedding-net-in-java"></a>Inserindo .NET em Java

Em alguns casos, convém adicionar uma biblioteca .NET Xamarin a um projeto Android nativo existente. Para fazer isso, você pode usar o [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/) ferramenta transformar sua biblioteca do .NET em uma biblioteca nativa que pode ser incorporada em um aplicativo do Android native baseados em Java.

 
## <a name="requirements"></a>Requisitos

Para usar o 4000 Embeddinator com Java no Android, você precisará do seguinte:

-   **O Android Studio** &ndash; [Android Studio 3. x](https://developer.android.com/studio/preview/index.html) ou posterior deve ser instalado.

-   **Xamarin** &ndash; [xamarin 7.5](https://www.visualstudio.com/xamarin/) ou posterior deve ser instalado.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior deve ser instalado.

-   **Mono** &ndash; [5.0 Mono](http://www.mono-project.com/download/) ou posterior deve ser instalado.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Você pode usar o Visual Studio para editar e compilar o código c#.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Você pode usar o Visual Studio para Mac para editar e compilar o código c#.

-----

 
## <a name="using-the-embeddinator-4000"></a>Usando o Embeddinator-4000

Para consumir uma biblioteca .NET em um projeto nativo do Android, você deve usar as seguintes etapas:

1.  Crie um projeto de biblioteca Android c#.

2.  Instale Embeddinator-4000 por meio do NuGet.

3.  Execute Embeddinator no assembly de biblioteca Android.

4.  Use o arquivo AAR gerado em um projeto Java no Android Studio.

Essas etapas são descritas detalhadamente o [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/getting-started-java-android.html) documentação.
