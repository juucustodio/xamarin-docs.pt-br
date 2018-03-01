---
title: Inserindo .NET em Java
description: Como usar uma biblioteca .NET Xamarin em um projeto do Android Native baseados em Java
ms.topic: article
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1a25f4bc39e39ce58a07ed399082bf13284c16e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="embedding-net-in-java"></a>Inserindo .NET em Java

Em alguns casos, convém adicionar uma biblioteca .NET Xamarin a um projeto Android nativo existente. Para fazer isso, você pode usar o [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/) ferramenta transformar sua biblioteca do .NET em uma biblioteca nativa que pode ser incorporada em um aplicativo do Android native baseados em Java.

 
## <a name="requirements"></a>Requisitos

Para usar o 4000 Embeddinator com Java no Android, você precisará do seguinte:

-   **O Android Studio** &ndash; [Android Studio 3. x](https://developer.android.com/studio/preview/index.html) ou posterior deve ser instalado.

-   **Xamarin** &ndash; [xamarin 7.4.99](https://jenkins.mono-project.com/view/Xamarin.Android/job/xamarin-android/lastSuccessfulBuild/Azure/) ou posterior deve ser instalado.

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
