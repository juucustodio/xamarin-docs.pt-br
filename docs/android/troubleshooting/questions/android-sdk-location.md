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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027020"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Onde posso configurar os locais de SDK do Android?

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

No Visual Studio, navegue até **ferramentas > opções > Xamarin > configurações** do Android para visualizar e definir a localização do Android SDK:

[![Guia De exemplos de localização em Preferências](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

O local padrão de cada caminho é o seguinte:

- Localização do kit de desenvolvimento Java: 

    **C:\\Arquivos\\\\do programa Java jdk1.8.0_131**

- Localização do Android SDK: 

    **C:\\Arquivos do programa\\(x86) Android\\android-sdk**

- Localização do Android NDK: 

    **C:\\\\ProgramData\\Microsoft AndroidNDK64\\android-ndk-r13b**

Observe que o número da versão do NDK pode variar. Por exemplo, em vez de **android-ndk-r13b,** poderia ser uma versão anterior, como **android-ndk-r10e**.

Para definir a localização do Android SDK, digite o caminho completo do diretório Android SDK na caixa **de localização do Android SDK.** Você pode navegar até a localização do Android SDK no File Explorer, copiar o caminho da barra de endereços e colar esse caminho na caixa **de localização do Android SDK.**
Por exemplo, se a sua localização do Android SDK estiver em **\\C:\\Usuários nome de\\usuário AppData\\Local\\Android\\Sdk**, limpar o caminho antigo na caixa de localização do Android **SDK,** colar neste caminho e clicar em **OK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, navegue até **preferências > projetos > locais sdk > Android**. Na página do **Android,** clique na guia **Locais** para visualizar e definir a localização do SDK:

[![Guia De exemplos de localização em Preferências](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

O local padrão de cada caminho é o seguinte:

- Localização do Android SDK: 

    **~/Biblioteca/Desenvolvedor/Xamarin/android-sdk-macosx**

- Localização do Android NDK: 

    **~/Biblioteca/Desenvolvedor/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK (JDK) Localização: 

    **/usr**

Observe que o número da versão do NDK pode variar. Por exemplo, em vez de **android-ndk-r14b,** poderia ser uma versão anterior, como **android-ndk-r10e**.

Para definir a localização do Android SDK, digite o caminho completo do diretório Android SDK na caixa **de localização do Android SDK.** Você pode selecionar a pasta Android SDK no Finder, pressionar **CTRL+&#8984;+I** para visualizar informações da pasta, clicar e arrastar o caminho para a direita de **Onde:**, copie, em seguida, cole-o na caixa **De localização do Android SDK** na guia **Locais.** Por exemplo, se a localização do Android SDK estiver em **~/Biblioteca/Desenvolvedor/Android/Sdk,** limpe o caminho antigo na caixa **de localização do Android SDK,** cole neste caminho e clique em **OK**.

-----
