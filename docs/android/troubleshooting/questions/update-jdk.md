---
title: Como faço para atualizar a versão do Java Development Kit (JDK)?
description: Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/18/2018
ms.openlocfilehash: 979bf4572e0e0865c2254c3e1c2f707c8eecae8d
ms.sourcegitcommit: 57f9a9ba2f199697cb75e7be67f1a372c35a861b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36269654"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Como faço para atualizar a versão do Java Development Kit (JDK)?

_Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac._

## <a name="overview"></a>Visão geral

Xamarin usa Java Development Kit (JDK) para integrar o SDK do Android para a criação de aplicativos do Android e executando o Android designer. As versões mais recentes do SDK do Android (API 24 e superior) requerem o JDK 8 (1.8). Se você ainda não atualizou para JDK 8, siga estas etapas para instalar e habilitá-lo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Baixar o JDK 8 (1.8) da [site Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela do JDK página de download no site da Oracle](update-jdk-images/image1.png)

2.  Escolha a versão de 64 bits para permitir o processamento de [controles personalizados](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols) no designer de Xamarin Android:

    ![Selecionar o pacote JDK de x64 do Windows para fazer o download da página de download do JDK](update-jdk-images/image2.png)

3.  Execute o .exe e instale o **ferramentas de desenvolvimento**:

    ![Instalando as ferramentas de desenvolvimento no instalador do JDK](update-jdk-images/image3.png)

4.  Abra o Visual Studio e atualize o **local do Kit de desenvolvimento Java** para apontar para o novo JDK em **Ferramentas > Opções > Xamarin > configurações do Android > local do Java Development Kit > alteração**:

    [![Configuração do caminho para o JDK na página de configuração de Android de opções do IDE](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Certifique-se de reiniciar o Visual Studio depois de atualizar o local.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Baixar o JDK 8 (1.8) da [site Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de tela do JDK página de download no site da Oracle](update-jdk-images/image1.png)

2.  Abra o arquivo. dmg e execute o instalador. pkg:

    ![Executar o instalador do JDK no macOS](update-jdk-images/image5.png)

Mac OS definirá automaticamente a nova versão do JDK como padrão Atualizando **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Você pode verificar que o **SDK de Java (JDK)** local é definido como o padrão esperado de **em /usr.** em **Visual Studio para Mac > Preferências > projetos > SDK locais > Android > Java SDK (JDK) > local**:

![Definindo o local do JDK na página local do Android SDK](update-jdk-images/image6.png)

-----

