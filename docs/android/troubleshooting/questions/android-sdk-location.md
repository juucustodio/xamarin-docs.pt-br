---
title: Onde posso configurar meu locais do SDK do Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: 0113cc15bf1de5e0e668b05c2b0288a6ead141b5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Onde posso configurar meu locais do SDK do Android?

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, navegue até **Ferramentas > Opções > Xamarin > configurações do Android** para exibir e definir o local do SDK do Android:

[![Guia de exemplo nas preferências](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

O local padrão para cada caminho é o seguinte:

- Local do Kit de desenvolvimento Java: 

    **C:\\arquivos de programa\\Java\\jdk1.8.0_131**

- Local do SDK do Android: 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Local do Android NDK: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

Observe que o número de versão de no NDK pode variar. Por exemplo, em vez de **android-ndk-r13b**, pode ser uma versão anterior, como **android-ndk-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório do SDK do Android para o **local do SDK do Android** caixa. Navegue até o local do SDK do Android no Explorador de arquivos, copie o caminho da barra de endereços e cole esse caminho para o **local do SDK do Android** caixa.
Por exemplo, se o local do SDK do Android estiver no **c:\\usuários\\username\\AppData\\Local\\Android\\Sdk**, limpe o caminho antigo no  **Local do SDK do Android** caixa, cole nesse caminho e clique em **Okey**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac, navegue até **Preferências > projetos > locais SDK > Android**. No **Android** , clique no **locais** guia para exibir e definir o local do SDK:

[![Guia de exemplo nas preferências](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

O local padrão para cada caminho é o seguinte:

- Local do SDK do Android: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Local do Android NDK: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Local do Java SDK (JDK): 

    **/usr**

Observe que o número de versão de no NDK pode variar. Por exemplo, em vez de **android-ndk-r14b**, pode ser uma versão anterior, como **android-ndk-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório do SDK do Android para o **local do SDK do Android** caixa. Você pode selecionar a pasta do SDK do Android no localizador, pressione **CTRL +&#8984;+ I** para exibir informações da pasta, clique e arraste o caminho à direita do **onde:**, copie e cole-o **SDK do Android Local** caixa o **locais** guia. Por exemplo, se o local do SDK do Android estiver no **~/Library/Developer/Android/Sdk**, limpe o caminho antigo no **local do SDK do Android** caixa, cole nesse caminho e clique em **Okey**.

-----
