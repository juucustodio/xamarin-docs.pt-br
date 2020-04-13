---
title: Como faço para atualizar a versão do JDK (Java Development Kit)?
description: Este artigo ilustra como atualizar a versão Java Development Kit (JDK) no Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019524"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Como faço para atualizar a versão do JDK (Java Development Kit)?

_Este artigo ilustra como atualizar a versão Java Development Kit (JDK) no Windows e Mac._

## <a name="overview"></a>Visão geral

Xamarin.Android usa o Java Development Kit (JDK) para integrar com o Android SDK para construir aplicativos Android e executar o designer Android. As versões mais recentes do Android SDK (API 24 ou superior) exigem JDK 8 (1.8). Alternativamente, você pode instalar o [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). O Microsoft Mobile OpenJDK eventualmente substituirá o JDK 8 pelo desenvolvimento do Xamarin.Android.

Para atualizar para o Microsoft Mobile OpenJDK, consulte [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Para atualizar para JDK 8, siga estas etapas:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Baixe JDK 8 (1.8) no site da [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela da página de download do JDK no site da Oracle](update-jdk-images/image1.png)

2. Escolha a versão de 64 [bits](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) para permitir a renderização de controles personalizados no designer Android Xamarin:

    ![Selecionando o pacote Windows x64 JDK para baixar na página de download do JDK](update-jdk-images/image2.png)

3. Execute o .exe e instale as **Ferramentas de Desenvolvimento**:

    ![Instalando as Ferramentas de Desenvolvimento no instalador JDK](update-jdk-images/image3.png)

4. Abra o Visual Studio e atualize o **Java Development Kit Location** para apontar para o novo JDK em Ferramentas > **Opções > Xamarin > Configurações do Android > Localização do Kit de Desenvolvimento Java**:

    [![Configuração do caminho para o JDK na página Configurações do Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Certifique-se de reiniciar o Visual Studio depois de atualizar o local.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Baixe JDK 8 (1.8) no site da [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela da página de download do JDK no site da Oracle](update-jdk-images/image1.png)

2. Abra o arquivo .dmg e execute o instalador .pkg:

    ![Executando o instalador JDK no macOS](update-jdk-images/image5.png)

O Mac OS definirá automaticamente a nova versão JDK como padrão atualizando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Em seguida, você pode verificar duas vezes se o local **do Java SDK (JDK)** está definido para o padrão esperado de **/usr** no **Visual Studio para Projetos > de Preferências de Mac > > > Locais > Android > Locais > Java SDK (JDK):**

[![Configuração da localização jdk na guia Localizações do Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
