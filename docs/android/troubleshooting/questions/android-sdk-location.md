---
title: Onde posso configurar os locais de SDK do Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/16/2017
ms.openlocfilehash: c004fb7717f78750e7ac1e8dc1856a32ba808638
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227656"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Onde posso configurar os locais de SDK do Android?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, navegue até **Ferramentas > Opções > Xamarin > configurações do Android** para exibir e definir o local do SDK do Android:

[![Guia de locais de exemplo nas preferências](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

O local padrão para cada caminho é da seguinte maneira:

- Localização do Java Development Kit: 

    **C:\\Program Files\\Java\\jdk1.8.0_131**

- Local do SDK do Android: 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Local do NDK do Android: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Observe que o número de versão do NDK pode variar. Por exemplo, em vez de **android-ndk-r13b**, ele pode ser uma versão anterior como **android-ndk-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório do SDK do Android para o **local do SDK do Android** caixa. Navegue até o local do SDK do Android no Explorador de arquivos, copie o caminho da barra de endereços e cole esse caminho para o **local do SDK do Android** caixa.
Por exemplo, se o local do SDK do Android é em **c:\\os usuários\\nome de usuário\\AppData\\Local\\Android\\Sdk**, limpar o caminho antigo no  **Local do SDK do Android** caixa, cole esse caminho e clique em **Okey**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, navegue até **Preferências > projetos > locais do SDK > Android**. No **Android** , clique no **locais** guia para exibir e definir o local do SDK:

[![Guia de locais de exemplo nas preferências](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

O local padrão para cada caminho é da seguinte maneira:

- Local do SDK do Android: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Local do NDK do Android: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Local do Java SDK (JDK): 

    **/usr**

Observe que o número de versão do NDK pode variar. Por exemplo, em vez de **android-ndk-r14b**, ele pode ser uma versão anterior como **android-ndk-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório do SDK do Android para o **local do SDK do Android** caixa. Você pode selecionar a pasta do SDK do Android no Finder, pressione **CTRL +&#8984;+ I** para exibir informações da pasta, clique e arraste o caminho para a direita de **em que:**, copie e cole-o para a **do SDK do Android Local** caixa de **locais** guia. Por exemplo, se o local do SDK do Android é em **~/Library/Developer/Android/Sdk**, desmarque o caminho antigo na **local do SDK do Android** caixa, cole esse caminho e clique em **Okey**.

-----
