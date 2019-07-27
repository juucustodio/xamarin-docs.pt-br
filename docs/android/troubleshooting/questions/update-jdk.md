---
title: Como faço para atualizar a versão do JDK (Java Development Kit)?
description: Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e no Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 02768bfcae9da06d8fd86ada63dd2d463245d254
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510459"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Como faço para atualizar a versão do JDK (Java Development Kit)?

_Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e no Mac._

## <a name="overview"></a>Visão geral

O Xamarin. Android usa o Java Development Kit (JDK) para integrar com o SDK do Android para a criação de aplicativos Android e a execução do Android designer. As versões mais recentes do SDK do Android (API 24 e superior) exigem o JDK 8 (1,8). Como alternativa, você pode instalar a versão [prévia do Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Eventualmente, o Microsoft Mobile OpenJDK substituirá o JDK 8 para desenvolvimento do Xamarin. Android.

Para atualizar para o Microsoft Mobile OpenJDK, consulte [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Para atualizar para o JDK 8, siga estas etapas:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Baixe o JDK 8 (1,8) do [site da Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela da página de download do JDK no site da Oracle](update-jdk-images/image1.png)

2.  Escolha a versão de 64 bits para permitir a renderização de [controles personalizados](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) no Xamarin Android designer:

    ![Selecionando o pacote do JDK do Windows x64 a ser baixado na página de download do JDK](update-jdk-images/image2.png)

3.  Execute o. exe e instale as **ferramentas de desenvolvimento**:

    ![Instalando as ferramentas de desenvolvimento no instalador do JDK](update-jdk-images/image3.png)

4.  Abra o Visual Studio e atualize o **local do Java Development Kit** para apontar para o novo JDK em **ferramentas > opções > Xamarin > configurações Android > local do Java Development Kit**:

    [![Configuração de caminho para o JDK na página de configurações do Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Certifique-se de reiniciar o Visual Studio depois de atualizar o local.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Baixe o JDK 8 (1,8) do [site da Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela da página de download do JDK no site da Oracle](update-jdk-images/image1.png)

2.  Abra o arquivo. dmg e execute o instalador. pkg:

    ![Executando o instalador do JDK no macOS](update-jdk-images/image5.png)

Mac OS irá definir automaticamente a nova versão do JDK como o padrão atualizando **/System/Library/frameworks/JavaVM.Framework/Versions/Current**. Em seguida, você pode verificar se o local do **Java SDK (JDK)** está definido como o padrão esperado de **/usr** em **preferências de > de Visual Studio para Mac > projetos > locais do SDK > locais de > do Android > Localização do Java SDK (JDK)** :

[![Configurando o local do JDK na guia locais do Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

