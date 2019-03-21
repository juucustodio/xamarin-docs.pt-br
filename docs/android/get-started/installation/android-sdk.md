---
title: Configurando o SDK do Android para o Xamarin.Android
description: O Visual Studio inclui um Android SDK Manager que você pode usar para baixar ferramentas, plataformas e outros componentes do SDK do Android necessários para desenvolver aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: 66b9e89080d88fa6f8192b42bc8ab0a97c92c20c
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58071002"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurando o SDK do Android para o Xamarin.Android

_O Visual Studio inclui um Android SDK Manager que você pode usar para baixar ferramentas, plataformas e outros componentes do SDK do Android que são necessários para desenvolver aplicativos Xamarin.Android._


## <a name="overview"></a>Visão geral

Este guia explica como usar o Xamarin Android SDK Manager no Visual Studio e no Visual Studio para Mac.

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio 2017 e ao Visual Studio para Mac.  

O Xamarin Android SDK Manager (instalado como parte da carga de trabalho de **Desenvolvimento móvel com .NET**) ajuda você a baixar os componentes mais recentes do Android que são necessários para desenvolver aplicativos Xamarin.Android. Ele substitui o Gerenciador de SDK autônomo do Google, que foi preterido.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="requirements"></a>Requisitos

Para usar o Gerenciador de SDK do Android do Xamarin, serão necessários:

- Visual Studio 2017 (edição Community, Professional ou Enterprise). Visual Studio 2017 versão 15.7 ou posterior.

- As Ferramentas do Visual Studio para Xamarin versão 4.10.0 ou posterior (instaladas como parte da carga de trabalho de **Desenvolvimento mobile com .NET**). 

O Gerenciador de SDK do Xamarin Android também requer o Java Development Kit (que é instalado automaticamente com o Xamarin.Android). Existem várias alternativas de JDK disponíveis:

-   Por padrão, o Xamarin.Android usa o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24).

-   Você poderá continuar usando o [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

-   Se estiver usando o Visual Studio 15.8 Versão Prévia 5 ou posterior, você poderá tentar usar a [Distribuição do OpenJDK Móvel da Microsoft](openjdk.md) (atualmente em versão prévia) em vez do JDK 8.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

 
## <a name="sdk-manager"></a>Gerenciador de SDK 

Para iniciar o Gerenciador de SDK no Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**:

[![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

O Gerenciador do SDK do Android será aberto na tela **SDKs e Ferramentas do Android**. Essa tela mostra duas guias, &ndash; **Plataformas** e **Ferramentas**:

[![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

A tela **SDKs e Ferramentas do Android** será descrita em mais detalhes nas seções seguintes.


### <a name="android-sdk-location"></a>Local do SDK do Android

O local do SDK do Android está configurado na parte superior da tela **SDKs e Ferramentas do Android**, conforme mostrado na captura de tela anterior. Esse local deve ser configurado corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente. Talvez seja necessário definir o local do SDK do Android por um ou mais dos seguintes motivos:

1. O Gerenciador de SDK do Android não conseguiu localizar o SDK do Android. 

2. Você instalou o SDK do Android em um local alternativo (não padrão). 

Para definir o local do SDK do Android, clique no botão de reticências (&hellip;) à direita do **Local do SDK do Android**. A caixa de diálogo **Procurar Pasta** será aberta e pode ser usada para navegar até o local do SDK do Android. Na captura de tela abaixo, o SDK do Android em **Arquivos de Programa (x86)\\Android** é selecionado:

![Captura de tela da caixa de diálogo do Windows "Procurar Pasta" localizando o SDK do Android](android-sdk-images/win/05-browse-for-folder.png)

Ao clicar em **OK**, o Gerenciador do SDK gerenciará o SDK do Android instalado no local selecionado.


### <a name="tools-tab"></a>Guia Ferramentas

A guia **Ferramentas** mostra uma lista de _ferramentas_ e _adicionais_. Use essa guia para instalar as Android SDK Tools, as ferramentas de plataforma e as ferramentas de build.
Além disso, é possível instalar o Android Emulator, o depurador de baixo nível (LLDB), o NDK, a aceleração por HAXM e as bibliotecas do Google Play.


Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Aplicar Alterações**:

[![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

Deve ser exibida uma caixa de diálogo com a mensagem _O pacote a seguir exige que você aceite os termos de licença antes da instalação_:

![Tela de aceitação da licença](android-sdk-images/win/07-license-acceptance.png)

Clique em **Aceitar** se estiver de acordo com os termos e condições. Na parte inferior da janela, uma barra de progresso indica o andamento do download e da instalação. Após a conclusão da instalação, a guia **Ferramentas** mostrará que as ferramentas e adicionais foram instalados.

### <a name="platforms-tab"></a>Guia Plataformas

A guia **Plataformas** exibe uma lista de versões de SDK de plataforma junto com outros recursos (como imagens do sistema) para cada plataforma:

[![Captura de tela do painel Plataformas](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

Essa tela lista a versão do Android (como **Android 8.0**), o nome do código (**Oreo**), o nível da API (como **26**) e os tamanhos dos componentes dessa plataforma (como **1 GB**). A guia **Plataformas** pode ser usada para instalar componentes do nível da API do Android que você quer direcionar. Para saber mais sobre as versões do Android e os níveis da API, veja [Noções básicas sobre níveis da API do Android](~/android/app-fundamentals/android-api-levels.md).

Se todos os componentes de uma plataforma estiverem instalados, uma marca de seleção aparecerá ao lado do nome da plataforma. Se nem todos os componentes de uma plataforma estiverem instalados, a caixa da plataforma será preenchida. É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na caixa **+** à esquerda da plataforma.
Clique em **-** para recolher a lista de componentes de uma plataforma.

Para adicionar outra plataforma ao SDK, clique na caixa ao lado da plataforma até que a marca de seleção apareça para instalar todos os componentes e, em seguida, clique em **Aplicar Alterações**:

[![Exemplo de como adicionar componentes Android 7.1 Nougat ao SDK do Android](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

Para instalar somente componentes específicos, clique uma vez na caixa ao lado da plataforma. É possível selecionar quaisquer componentes individuais necessários:

[![Exemplo de como adicionar alguns componentes do Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida, como mostrado antes.
Clique em **Aceitar** se estiver de acordo com os termos e condições. Essa caixa de diálogo poderá ser exibida outras vezes quando houver diversos componentes a serem instalados. Na parte inferior da janela, uma barra de progresso indicará o andamento do download e da instalação. Quando o processo de download e instalação for concluído (isso pode levar vários minutos, de acordo com a quantidade de componentes que precisam ser baixados), os componentes adicionados serão marcados com uma marca de seleção e listados como **Instalados**.

### <a name="respository-selection"></a>Seleção de repositório

Por padrão, o Gerenciador do SDK do Android baixa os componentes e as ferramentas da plataforma de um repositório gerenciado pela Microsoft. Se você precisar acessar plataformas e ferramentas experimentais alfa/beta que ainda não estão disponíveis no repositório da Microsoft, poderá alterar o Gerenciador do SDK e fazer com que ele use o repositório do Google. Para fazer essa alteração, clique no ícone de engrenagem no canto inferior direito e selecione **Repositório > Google (Não compatível)**:

[![Selecionar repositório do Google](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

Quando o repositório do Google é selecionado, os pacotes adicionais que não estavam disponíveis anteriormente poderão aparecer na guia **Plataformas**. (Na captura de tela acima, a **Plataforma do SDK do Android 28** foi adicionada alterando para o repositório do Google.) O uso do repositório do Google não tem suporte e, portanto, não é recomendado para o desenvolvimento cotidiano.

Para voltar a usar o repositório compatível de plataformas e ferramentas, clique em **Microsoft (Recomendado)**. Assim, a lista de pacotes e ferramentas será restaurada para a seleção padrão.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="requirements"></a>Requisitos

Para usar o Gerenciador de SDK do Android do Xamarin, serão necessários:

-   Visual Studio para Mac 7.5 (ou posterior).

O Gerenciador de SDK do Xamarin Android também requer o Java Development Kit (que é instalado automaticamente com o Xamarin.Android). Existem várias alternativas de JDK disponíveis:

-   Por padrão, o Xamarin.Android usa o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24).

-   Você poderá continuar usando o [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

-   Se estiver usando o Visual Studio para Mac 7.7 ou posterior, você poderá tentar usar a [Distribuição do OpenJDK Móvel da Microsoft](openjdk.md) (atualmente em versão prévia) em vez do JDK 8.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.
 
## <a name="sdk-manager"></a>Gerenciador de SDK 

Para iniciar o Gerenciador de SDK no Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**:
 
[![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

O **Gerenciador de SDK do Android** será aberto na **janela Preferências**, que contém três guias, **Plataformas**, **Ferramentas** e **Locais**:

[![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

As guias do Gerenciador do SDK do Android do Xamarin serão descritas nas seções seguintes.


### <a name="locations-tab"></a>Guia Locais

A guia **Locais** tem três configurações para definir os locais do SDK do Android, o NDK do Android e o SDK do Java (JDK). Esses locais devem ser configurados corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente.

Ao ser iniciado, o Gerenciador de SDK determina automaticamente o caminho para cada pacote instalado e indica que foi **Localizado** colocando um ícone de marca de seleção verde ao lado do caminho:

[![Captura de tela da guia Locais](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

Clique no botão **Restaurar Padrões** para que o Gerenciador de SDK localize o SDK, o NDK e o JDK em seus locais padrão. 

Normalmente, utiliza-se a guia **Locais** para modificar o local do SDK do Android e/ou JDK do Java. Não é necessário instalar o NDK para desenvolver aplicativos Xamarin.Android &ndash;. O NDK é usado somente quando é preciso desenvolver partes do aplicativo usando linguagens de código nativo, como C e C++.

### <a name="tools-tab"></a>Guia Ferramentas

A guia **Ferramentas** mostra uma lista de _ferramentas_ e _adicionais_. Use essa guia para instalar as Android SDK Tools, as ferramentas de plataforma e as ferramentas de build.
Além disso, é possível instalar o Android Emulator, o depurador de baixo nível (LLDB), o NDK, a aceleração por HAXM e as bibliotecas do Google Play.

Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Aplicar Alterações**:

[![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

Deve ser exibida uma caixa de diálogo com a mensagem _O pacote a seguir exige que você aceite os termos de licença antes da instalação_:

[![Tela de aceitação da licença](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

Clique em **Aceitar** se estiver de acordo com os termos e condições. Na parte inferior da janela, uma barra de progresso indica o andamento do download e da instalação. Após a conclusão da instalação, a guia **Ferramentas** mostrará que as ferramentas e adicionais foram instalados.


### <a name="platforms-tab"></a>Guia Plataformas

A guia **Plataformas** exibe uma lista de versões de SDK de plataforma junto com outros recursos (como imagens do sistema) para cada plataforma:

[![Captura de tela do painel Plataformas](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

Essa tela lista a versão do Android (como **Android 8.1**), o nome do código (**Oreo**), o nível da API (como **27**) e os tamanhos dos componentes dessa plataforma (como **1 GB**). A guia **Plataformas** pode ser usada para instalar componentes do nível da API do Android que você quer direcionar. Para saber mais sobre as versões do Android e os níveis da API, veja [Noções básicas sobre níveis da API do Android](~/android/app-fundamentals/android-api-levels.md).

Se todos os componentes de uma plataforma estiverem instalados, uma marca de seleção aparecerá ao lado do nome da plataforma. Se nem todos os componentes de uma plataforma estiverem instalados, a caixa da plataforma será preenchida. É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na **seta** à esquerda da plataforma.
Clique na **seta para baixo** para recolher a lista de componentes de uma plataforma.

Para adicionar outra plataforma ao SDK, clique na caixa ao lado da plataforma até que a marca de seleção apareça para instalar todos os componentes e, em seguida, clique em **Aplicar Alterações**:

[![Exemplo de como adicionar todos os componentes de uma plataforma](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

Para instalar somente alguns componentes, clique uma vez na caixa ao lado da plataforma. É possível selecionar quaisquer componentes individuais necessários:

[![Exemplo de como adicionar alguns componentes](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida, como mostrado antes.
Clique em **Aceitar** se estiver de acordo com os termos e condições. Essa caixa de diálogo poderá ser exibida outras vezes quando houver diversos componentes a serem instalados. Na parte inferior da janela, uma barra de progresso indicará o andamento do download e da instalação. Quando o processo de download e instalação for concluído (isso pode levar vários minutos, de acordo com a quantidade de componentes que precisam ser baixados), os componentes adicionados serão marcados com uma marca de seleção e listados como **Instalados**.

### <a name="respository-selection"></a>Seleção de repositório

Por padrão, o Gerenciador do SDK do Android baixa os componentes e as ferramentas da plataforma de um repositório gerenciado pela Microsoft. Se você precisar acessar plataformas e ferramentas experimentais alfa/beta que ainda não estão disponíveis no repositório da Microsoft, poderá alterar o Gerenciador do SDK e fazer com que ele use o repositório do Google. Para fazer essa alteração, clique no ícone de engrenagem no canto inferior direito e selecione **Repositório > Google (Não compatível)**:

[![Selecionar repositório do Google](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

Quando o repositório do Google é selecionado, os pacotes adicionais que não estavam disponíveis anteriormente poderão aparecer na guia **Plataformas**. (Na captura de tela acima, a **Plataforma do SDK do Android 28** foi adicionada alterando para o repositório do Google.) O uso do repositório do Google não tem suporte e, portanto, não é recomendado para o desenvolvimento cotidiano.

Para voltar a usar o repositório compatível de plataformas e ferramentas, clique em **Microsoft (Recomendado)**. Assim, a lista de pacotes e ferramentas será restaurada para a seleção padrão.

-----

 
## <a name="summary"></a>Resumo

Este guia explicou como instalar e usar a ferramenta Xamarin Android SDK Manager no Visual Studio e no Visual Studio para Mac.


## <a name="related-links"></a>Links relacionados

- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
