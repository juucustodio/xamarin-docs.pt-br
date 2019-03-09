---
title: Como faço para atualizar a versão do JDK (Java Development Kit)?
description: Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 290a7deef4fdc10163bdb09881382f011b0dcd32
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670831"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Como faço para atualizar a versão do JDK (Java Development Kit)?

_Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac._

## <a name="overview"></a>Visão geral

Xamarin. Android usa o Java Development Kit (JDK) para integrar com o SDK do Android para criar aplicativos Android e executar o designer do Android. As versões mais recentes do SDK do Android (API 24 e superior) exigem o JDK 8 (1.8). Como alternativa, você pode instalar o [versão prévia do Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). O Microsoft Mobile OpenJDK substituirá o JDK 8 para o desenvolvimento do xamarin. Android.

Para atualizar para o Microsoft Mobile OpenJDK, consulte [versão prévia do Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Para atualizar para o JDK 8, siga estas etapas:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Baixar o JDK 8 (1.8) do [site da Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela do JDK página de download no site da Oracle](update-jdk-images/image1.png)

2.  Selecione a versão de 64 bits para permitir que a renderização de [controles personalizados](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) no designer do Android do Xamarin:

    ![Selecionando o pacote JDK de x64 do Windows para fazer o download da página de download do JDK](update-jdk-images/image2.png)

3.  Execute o .exe e instale o **ferramentas de desenvolvimento**:

    ![Instalando as ferramentas de desenvolvimento no instalador do JDK](update-jdk-images/image3.png)

4.  Abra o Visual Studio e atualize o **local do Java Development Kit** para apontar para o novo JDK sob **Ferramentas > Opções > Xamarin > configurações do Android > local do Java Development Kit**:

    [![Configuração do caminho para o JDK na página de configurações do Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Certifique-se de reiniciar o Visual Studio depois de atualizar o local.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Baixar o JDK 8 (1.8) do [site da Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela do JDK página de download no site da Oracle](update-jdk-images/image1.png)

2.  Abra o arquivo. dmg e execute o instalador. pkg:

    ![Executar o instalador do JDK no macOS](update-jdk-images/image5.png)

Mac OS definirá automaticamente a nova versão do JDK como o padrão Atualizando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Você pode, em seguida, verifique novamente se a **SDK do Java (JDK)** local é definido como o padrão esperado de **/usr** sob **Visual Studio para Mac > Preferências > projetos > locais do SDK > Android > locais > local do Java SDK (JDK)**:

[![Definir o local do JDK na guia do Android locais](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

