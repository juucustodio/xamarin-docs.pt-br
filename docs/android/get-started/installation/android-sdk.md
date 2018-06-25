---
title: Configurando o SDK do Android para o Xamarin.Android
description: O Visual Studio tem um Gerenciador de SDK do Android que substitui o Gerenciador de SDK autônomo do Google. Este guia explica como usar o Gerenciador de SDK para baixar Android SDK Tools, plataformas e outros componentes necessários para desenvolver aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 0af0ef56630103498041ad61f7c5ce900358b055
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732847"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurando o SDK do Android para o Xamarin.Android

_O Visual Studio tem um Gerenciador de SDK do Android que substitui o Gerenciador de SDK autônomo do Google. Este guia explica como usar o Gerenciador de SDK para baixar Android SDK Tools, plataformas e outros componentes necessários para desenvolver aplicativos Xamarin.Android._


## <a name="overview"></a>Visão geral

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Este guia explica como instalar e utilizar o Gerenciador de SDK do Android do Xamarin para Visual Studio no Windows (ou [Mac](?tabs=vsmac)).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Este guia explica como instalar e utilizar o Gerenciador de SDK do Android do Xamarin para Visual Studio para Mac (ou [Windows](?tabs=vswin)).

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio 2017 e ao Visual Studio para Mac.  

-----

O Gerenciador de SDK do Android do Xamarin ajuda a baixar os componentes mais recentes do Android necessários para desenvolver aplicativos Xamarin.Android.
Ele substitui o Gerenciador de SDK autônomo do Google, que foi preterido.

Por que usar o Gerenciador de SDK do Android do Xamarin em vez do Gerenciador de SDK incluído no SDK do Android? Na versão 25.2.3 do pacote Android SDK Tools, o Google apresentou uma nova ferramenta para manter o SDK do Android. Essa nova ferramenta, o **[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)**, é um utilitário de linha de comando que substitui o gerenciador de interface do usuário autônomo do SDK do Android. Portanto, se você atualizar para a versão 26.0.1 ou posterior do Android SDK Tools (exigido para o Android 8.0) e quiser continuar a gerenciar o SDK do Android por meio de uma interface do usuário, é necessário usar o Gerenciador de SDK do Android do Xamarin.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para usar o Gerenciador de SDK do Android do Xamarin, serão necessários:

- Visual Studio 2017 (edição Community, Professional ou Enterprise). Visual Studio 2017 versão 15.5 ou posterior.

- Ferramentas do Visual Studio para Xamarin versão 4.5.0 ou posterior. 

O Gerenciador de SDK do Android do Xamarin não é compatível com o Visual Studio
2015. Os usuários do Visual Studio 2015 devem usar as ferramentas do Gerenciador de SDK fornecidas pelo Google no SDK do Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   Visual Studio para Mac 7.0.0.3146 (ou posterior).

-----

O Gerenciador de SDK do Xamarin Android também requer o Java Development Kit (que é instalado automaticamente com o Xamarin.Android).
O Xamarin.Android usa o [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24). Você poderá continuar usando o [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installation"></a>Instalação

O Gerenciador de SDK do Xamarin pode ser adicionado ao Visual Studio 2017 no momento da instalação. Ao instalar o Visual Studio, clique em **Componentes Individuais** e role para baixo, até a seção **Atividades de desenvolvimento**.
Habilite o **Gerenciador de SDK do Xamarin** se ele não estiver marcado:

![Habilitar o Gerenciador de SDK do Xamarin em Componentes Individuais](android-sdk-images/win/01-sdk-manager-install.png)

Se o Visual Studio 2017 já estiver instalado, consulte [Modificar o Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/modify-visual-studio) para obter instruções sobre como modificar o Visual Studio e, depois, siga o procedimento mencionado anteriormente para habilitar o Gerenciador de SDK do Xamarin. Se uma solicitação para atualizar o Gerenciador de SDK for exibida, será possível usar esse mesmo procedimento para instalar o Gerenciador de SDK do Xamarin.

Ao clicar em **Ferramentas > Android > Gerenciador de SDK do Android** (conforme explicado a seguir), o Gerenciador de SDK do Android do Xamarin será iniciado em vez do Gerenciador de SDK do Android do Google. Caso você esteja usando uma versão anterior do SDK do Android que ofereça suporte ao Gerenciador de SDK do Android autônomo do Google, instalar o Gerenciador de SDK do Android do Xamarin não criará conflitos.&ndash; Ainda é possível iniciar o Gerenciador de SDK autônomo do Google fora do Visual Studio, a fim de gerenciar o SDK do Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
-----

 
## <a name="sdk-manager"></a>Gerenciador de SDK 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para iniciar o Gerenciador de SDK no Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**:

[![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

O **Gerenciador do SDK do Android do Xamarin** será aberto na tela **SDKs e Ferramentas do Android**. Essa tela mostra duas guias, &ndash; **Plataformas** e **Ferramentas**:

[![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

A tela **SDKs e Ferramentas do Android** será descrita em mais detalhes nas seções seguintes.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para iniciar o Gerenciador de SDK no Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**:
 
![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/mac/sdkmanager-01.png )

O **Gerenciador de SDK do Android** será aberto na **janela Preferências**, que contém três guias, **Plataformas**, **Ferramentas** e **Locais**:

![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/mac/sdkmanager-02.png)

As guias do Gerenciador do SDK do Android do Xamarin serão descritas nas seções seguintes.

-----



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-location"></a>Local do SDK do Android

O local do SDK do Android está configurado na parte superior da tela **SDKs e Ferramentas do Android**, conforme mostrado na imagem anterior. Esse local deve ser configurado corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente. Talvez seja necessário definir o local do SDK do Android por um ou mais dos seguintes motivos:

1. O Gerenciador de SDK do Xamarin não pôde localizar o SDK do Android. 

2. Você instalou o SDK do Android em um local alternativo (não padrão). 

Para definir o local do SDK do Android, clique no botão &hellip; à direita do **Local do SDK do Android**. A caixa de diálogo **Procurar Pasta** será aberta e pode ser usada para navegar até o local do SDK do Android. Na captura de tela abaixo, o SDK do Android em **Arquivos de Programa (x86)\\Android** é selecionado:

![Captura de tela da caixa de diálogo do Windows "Procurar Pasta" localizando o SDK do Android](android-sdk-images/win/05-browse-for-folder.png)

Ao clicar em **OK**, o Gerenciador do SDK do Android do Xamarin gerenciará o SDK do Android instalado no local selecionado.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="locations-tab"></a>Guia Locais

A guia **Locais** tem três configurações para definir os locais do SDK do Android, o NDK do Android e o SDK do Java (JDK). Esses locais devem ser configurados corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente.

Ao ser iniciado, o Gerenciador de SDK determina automaticamente o caminho para cada pacote instalado e indica que foi **Localizado** colocando um ícone de marca de seleção verde ao lado do caminho:

![Captura de tela da guia Locais](android-sdk-images/mac/sdkmanager-03.png)

Clique no botão **Restaurar Padrões** para que o Gerenciador de SDK localize o SDK, o NDK e o JDK em seus locais padrão. 

Normalmente, utiliza-se a guia **Locais** para modificar o local do SDK do Android e/ou JDK do Java. Não é necessário instalar o NDK para desenvolver aplicativos Xamarin.Android &ndash;. O NDK é usado somente quando é preciso desenvolver partes do aplicativo usando linguagens de código nativo, como C e C++.

-----


### <a name="tools-tab"></a>Guia Ferramentas

A guia **Ferramentas** mostra uma lista de _ferramentas_ e _adicionais_. Use essa guia para instalar as Android SDK Tools, as ferramentas de plataforma e as ferramentas de build.
Além disso, é possível instalar o Android Emulator, o depurador de baixo nível (LLDB), o NDK, a aceleração por HAXM e as bibliotecas do Google Play.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Aplicar Alterações**:

[![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Instalar Atualizações**:

![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/mac/sdkmanager-08.png)

-----


Uma caixa de diálogo podem ser exibida com a mensagem, _Alguns componentes podem ser atualizados. Deseja atualizá-los agora?_ Clique em **Sim**. Em seguida, é mostrada uma caixa de diálogo de aceitação da Licença:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Tela de aceitação da licença](android-sdk-images/win/07-license-acceptance.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Tela de aceitação da licença](android-sdk-images/mac/sdkmanager-09.png)

-----

Clique em **Aceitar** se estiver de acordo com os termos e condições. Na parte inferior da janela, uma barra de progresso indica o andamento do download e da instalação. Após a conclusão da instalação, a guia **Ferramentas** mostrará que as ferramentas e adicionais foram instalados.



### <a name="platforms-tab"></a>Guia Plataformas

A guia **Plataformas** exibe uma lista de versões de SDK de plataforma junto com outros recursos (como imagens do sistema) para cada plataforma.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Captura de tela do painel Plataformas](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Captura de tela do painel Plataformas](android-sdk-images/mac/sdkmanager-11.png)

-----

Essa tela lista a versão do Android (como **Android 7.0**), o nome do código (**Nougat**), o nível da API (como **24**) e o status (**Instalado**, se a plataforma estiver instalada). A guia **Plataformas** é utilizada para instalar componentes do nível da API do Android que você tem como alvo. Para saber mais sobre as versões e níveis de API do Android, consulte [Noções Básicas sobre os Níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

Se todos os componentes de uma plataforma estiverem instalados, uma marca de seleção aparecerá ao lado do nome da plataforma. Se nem todos os componentes de uma plataforma estiverem instalados, a caixa da plataforma será preenchida. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na caixa **+** à esquerda da plataforma.
Clique em **-** para recolher a lista de componentes de uma plataforma.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na **seta** à esquerda da plataforma.
Clique na **seta para baixo** para recolher a lista de componentes de uma plataforma.

-----

Para adicionar outra plataforma ao SDK, clique na caixa ao lado da plataforma até que a marca de seleção apareça para instalar todos os componentes e, em seguida, clique em **Aplicar Alterações**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Exemplo de como adicionar componentes Android 7.1 Nougat ao SDK do Android](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Exemplo de como adicionar componentes Android 4.4 ao SDK do Android](android-sdk-images/mac/sdkmanager-12.png)

-----

Para instalar somente o SDK, clique uma vez na caixa ao lado da plataforma. É possível selecionar quaisquer componentes individuais necessários:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Exemplo de como adicionar alguns componentes do Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Exemplo de como adicionar alguns componentes do Android 4.4](android-sdk-images/mac/sdkmanager-13.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. No exemplo acima, seis componentes estão prontos para serem instalados. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida:

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Caixa de diálogo Aceitação da Licença na guia Plataformas](android-sdk-images/win/11-license-screen.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Caixa de diálogo Aceitação da Licença na guia Plataformas](android-sdk-images/mac/sdkmanager-14.png)

-----

Clique em **Aceitar** se estiver de acordo com os termos e condições. Essa caixa de diálogo poderá ser exibida outras vezes quando houver diversos componentes a serem instalados. Na parte inferior da janela, uma barra de progresso indicará o andamento do download e da instalação. Quando o processo de download e instalação for concluído (isso pode levar vários minutos, de acordo com a quantidade de componentes que precisam ser baixados), os componentes adicionados serão marcados com uma marca de seleção e listados como **Instalados**.

Agora você está pronto para desenvolver aplicativos no maior e mais recente nível da API!


 
## <a name="summary"></a>Resumo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Este guia explicou como instalar e utilizar a ferramenta Gerenciador de SDK do Android do Xamarin no Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Este guia explicou como utilizar a ferramenta Gerenciador de SDK do Android do Xamarin no Visual Studio para Mac.

-----


## <a name="related-links"></a>Links relacionados

- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
