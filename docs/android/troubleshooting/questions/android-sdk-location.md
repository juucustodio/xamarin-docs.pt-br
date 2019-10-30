---
title: Onde posso configurar os locais de SDK do Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027020"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Onde posso configurar os locais de SDK do Android?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, navegue até **ferramentas > opções > Xamarin > configurações do Android** para exibir e definir o local do SDK do Android:

[guia ![exemplo de locais em preferências](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

O local padrão para cada caminho é o seguinte:

- Local do Java Development Kit: 

    **C:\\arquivos de programas\\Java\\JDK 1.8.0 _131**

- SDK do Android local: 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Local de NDK do Android: 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\Android-NDK-r13b**

Observe que o número de versão do NDK pode variar. Por exemplo, em vez de **Android-NDK-r13b**, pode ser uma versão anterior, como **Android-NDK-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório SDK do Android na caixa **local do SDK do Android** . Você pode navegar até o local do SDK do Android no explorador de arquivos, copiar o caminho da barra de endereços e colar esse caminho na caixa **SDK do Android local** .
Por exemplo, se seu local de SDK do Android estiver em **C:\\usuários\\nome de usuário\\AppData\\local\\Android\\SDK**, desmarque o caminho antigo na caixa **local do SDK do Android** , Cole neste caminho e clique em **OK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Em Visual Studio para Mac, navegue até **preferências > projetos > locais do SDK > Android**. Na página do **Android** , clique na guia **locais** para exibir e definir o local do SDK:

[guia ![exemplo de locais em preferências](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

O local padrão para cada caminho é o seguinte:

- SDK do Android local: 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Local de NDK do Android: 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Localização do Java SDK (JDK): 

    **/usr**

Observe que o número de versão do NDK pode variar. Por exemplo, em vez de **Android-NDK-r14b**, pode ser uma versão anterior, como **Android-NDK-r10e**.

Para definir o local do SDK do Android, insira o caminho completo do diretório SDK do Android na caixa **local do SDK do Android** . Você pode selecionar a pasta SDK do Android no localizador, pressionar **Ctrl +&#8984;+ I** para exibir informações da pasta, clicar e arrastar o caminho para a direita de **onde:** , copiar e, em seguida, colá-lo na caixa **SDK do Android local** na guia **locais** . Por exemplo, se seu local de SDK do Android estiver em **~/library/Developer/Android/SDK**, desmarque o caminho antigo na caixa **SDK do Android local** , Cole esse caminho e clique em **OK**.

-----
