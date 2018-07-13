---
title: Configurando o SDK do Android para o Xamarin.Android
description: O Visual Studio inclui um Android SDK Manager que você pode usar para baixar ferramentas, plataformas e outros componentes do SDK do Android necessários para desenvolver aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/10/2018
ms.openlocfilehash: 895496f6a198f679ce08322ae48fe88e03b85629
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947264"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configurando o SDK do Android para o Xamarin.Android

_O Visual Studio inclui um Android SDK Manager que você pode usar para baixar ferramentas, plataformas e outros componentes do SDK do Android que são necessários para desenvolver aplicativos Xamarin.Android._


## <a name="overview"></a>Visão geral

Este guia explica como usar o Xamarin Android SDK Manager no Visual Studio e no Visual Studio para Mac.

> [!NOTE]
> Este guia se aplica apenas ao Visual Studio 2017 e ao Visual Studio para Mac.  

O Xamarin Android SDK Manager (instalado como parte da carga de trabalho de **Desenvolvimento móvel com .NET**) ajuda você a baixar os componentes mais recentes do Android que são necessários para desenvolver aplicativos Xamarin.Android. Ele substitui o Gerenciador de SDK autônomo do Google, que foi preterido.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="requirements"></a>Requisitos

Para usar o Gerenciador de SDK do Android do Xamarin, serão necessários:

- Visual Studio 2017 (edição Community, Professional ou Enterprise). Visual Studio 2017 versão 15.7 ou posterior.

- Ferramentas do Visual Studio para Xamarin versão 4.10.0 ou posterior. 

O Gerenciador de SDK do Android do Xamarin não é compatível com o Visual Studio
2015. Os usuários do Visual Studio 2015 devem usar as ferramentas do Gerenciador de SDK fornecidas pelo Google no SDK do Android.


O Gerenciador de SDK do Xamarin Android também requer o Java Development Kit (que é instalado automaticamente com o Xamarin.Android).
O Xamarin.Android usa o [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24). Você poderá continuar usando o [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

 
## <a name="sdk-manager"></a>Gerenciador de SDK 

Para iniciar o Gerenciador de SDK no Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**:

[![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

O **Gerenciador do SDK do Android do Xamarin** será aberto na tela **SDKs e Ferramentas do Android**. Essa tela mostra duas guias, &ndash; **Plataformas** e **Ferramentas**:

[![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

A tela **SDKs e Ferramentas do Android** será descrita em mais detalhes nas seções seguintes.


### <a name="android-sdk-location"></a>Local do SDK do Android

O local do SDK do Android está configurado na parte superior da tela **SDKs e Ferramentas do Android**, conforme mostrado na imagem anterior. Esse local deve ser configurado corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente. Talvez seja necessário definir o local do SDK do Android por um ou mais dos seguintes motivos:

1. O Gerenciador de SDK do Xamarin não pôde localizar o SDK do Android. 

2. Você instalou o SDK do Android em um local alternativo (não padrão). 

Para definir o local do SDK do Android, clique no botão &hellip; à direita do **Local do SDK do Android**. A caixa de diálogo **Procurar Pasta** será aberta e pode ser usada para navegar até o local do SDK do Android. Na captura de tela abaixo, o SDK do Android em **Arquivos de Programa (x86)\\Android** é selecionado:

![Captura de tela da caixa de diálogo do Windows "Procurar Pasta" localizando o SDK do Android](android-sdk-images/win/05-browse-for-folder.png)

Ao clicar em **OK**, o Gerenciador do SDK do Android do Xamarin gerenciará o SDK do Android instalado no local selecionado.



### <a name="tools-tab"></a>Guia Ferramentas

A guia **Ferramentas** mostra uma lista de _ferramentas_ e _adicionais_. Use essa guia para instalar as Android SDK Tools, as ferramentas de plataforma e as ferramentas de build.
Além disso, é possível instalar o Android Emulator, o depurador de baixo nível (LLDB), o NDK, a aceleração por HAXM e as bibliotecas do Google Play.


Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Aplicar Alterações**:

[![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)



Uma caixa de diálogo podem ser exibida com a mensagem, _Alguns componentes podem ser atualizados. Deseja atualizá-los agora?_ Clique em **Sim**. Em seguida, é mostrada uma caixa de diálogo de aceitação da Licença:


![Tela de aceitação da licença](android-sdk-images/win/07-license-acceptance.png)


Clique em **Aceitar** se estiver de acordo com os termos e condições. Na parte inferior da janela, uma barra de progresso indica o andamento do download e da instalação. Após a conclusão da instalação, a guia **Ferramentas** mostrará que as ferramentas e adicionais foram instalados.



### <a name="platforms-tab"></a>Guia Plataformas

A guia **Plataformas** exibe uma lista de versões de SDK de plataforma junto com outros recursos (como imagens do sistema) para cada plataforma.


[![Captura de tela do painel Plataformas](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)


Essa tela lista a versão do Android (como **Android 7.0**), o nome do código (**Nougat**), o nível da API (como **24**) e o status (**Instalado**, se a plataforma estiver instalada). A guia **Plataformas** é utilizada para instalar componentes do nível da API do Android que você tem como alvo. Para saber mais sobre as versões e níveis de API do Android, consulte [Noções Básicas sobre os Níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

Se todos os componentes de uma plataforma estiverem instalados, uma marca de seleção aparecerá ao lado do nome da plataforma. Se nem todos os componentes de uma plataforma estiverem instalados, a caixa da plataforma será preenchida. 


É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na caixa **+** à esquerda da plataforma.
Clique em **-** para recolher a lista de componentes de uma plataforma.


Para adicionar outra plataforma ao SDK, clique na caixa ao lado da plataforma até que a marca de seleção apareça para instalar todos os componentes e, em seguida, clique em **Aplicar Alterações**:


[![Exemplo de como adicionar componentes Android 7.1 Nougat ao SDK do Android](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)


Para instalar somente o SDK, clique uma vez na caixa ao lado da plataforma. É possível selecionar quaisquer componentes individuais necessários:


[![Exemplo de como adicionar alguns componentes do Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)




Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. No exemplo acima, seis componentes estão prontos para serem instalados. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida:



![Caixa de diálogo Aceitação da Licença na guia Plataformas](android-sdk-images/win/11-license-screen.png)


Clique em **Aceitar** se estiver de acordo com os termos e condições. Essa caixa de diálogo poderá ser exibida outras vezes quando houver diversos componentes a serem instalados. Na parte inferior da janela, uma barra de progresso indicará o andamento do download e da instalação. Quando o processo de download e instalação for concluído (isso pode levar vários minutos, de acordo com a quantidade de componentes que precisam ser baixados), os componentes adicionados serão marcados com uma marca de seleção e listados como **Instalados**.



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


## <a name="requirements"></a>Requisitos

Para usar o Gerenciador de SDK do Android do Xamarin, serão necessários:

-   Visual Studio para Mac 7.5 (ou posterior).

O Gerenciador de SDK do Xamarin Android também requer o Java Development Kit (que é instalado automaticamente com o Xamarin.Android).
O Xamarin.Android usa o [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24). Você poderá continuar usando o [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

 
## <a name="sdk-manager"></a>Gerenciador de SDK 

Para iniciar o Gerenciador de SDK no Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**:
 
![Local do item de menu do Gerenciador de SDK do Android](android-sdk-images/mac/sdkmanager-01.png )

O **Gerenciador de SDK do Android** será aberto na **janela Preferências**, que contém três guias, **Plataformas**, **Ferramentas** e **Locais**:

![Captura de tela do Gerenciador de SDK do Android aberto na guia Plataformas](android-sdk-images/mac/sdkmanager-02.png)

As guias do Gerenciador do SDK do Android do Xamarin serão descritas nas seções seguintes.


### <a name="locations-tab"></a>Guia Locais

A guia **Locais** tem três configurações para definir os locais do SDK do Android, o NDK do Android e o SDK do Java (JDK). Esses locais devem ser configurados corretamente antes das guias **Plataformas** e **Ferramentas** funcionarem adequadamente.

Ao ser iniciado, o Gerenciador de SDK determina automaticamente o caminho para cada pacote instalado e indica que foi **Localizado** colocando um ícone de marca de seleção verde ao lado do caminho:

![Captura de tela da guia Locais](android-sdk-images/mac/sdkmanager-03.png)

Clique no botão **Restaurar Padrões** para que o Gerenciador de SDK localize o SDK, o NDK e o JDK em seus locais padrão. 

Normalmente, utiliza-se a guia **Locais** para modificar o local do SDK do Android e/ou JDK do Java. Não é necessário instalar o NDK para desenvolver aplicativos Xamarin.Android &ndash;. O NDK é usado somente quando é preciso desenvolver partes do aplicativo usando linguagens de código nativo, como C e C++.

### <a name="tools-tab"></a>Guia Ferramentas

A guia **Ferramentas** mostra uma lista de _ferramentas_ e _adicionais_. Use essa guia para instalar as Android SDK Tools, as ferramentas de plataforma e as ferramentas de build.
Além disso, é possível instalar o Android Emulator, o depurador de baixo nível (LLDB), o NDK, a aceleração por HAXM e as bibliotecas do Google Play.


Por exemplo, para baixar o pacote do Google Android Emulator, clique na marca de seleção ao lado de **Android Emulator** e clique no botão **Instalar Atualizações**:

![Instalar o Android Emulator na guia Ferramentas](android-sdk-images/mac/sdkmanager-08.png)


Uma caixa de diálogo podem ser exibida com a mensagem, _Alguns componentes podem ser atualizados. Deseja atualizá-los agora?_ Clique em **Sim**. Em seguida, é mostrada uma caixa de diálogo de aceitação da Licença:


![Tela de aceitação da licença](android-sdk-images/mac/sdkmanager-09.png)


Clique em **Aceitar** se estiver de acordo com os termos e condições. Na parte inferior da janela, uma barra de progresso indica o andamento do download e da instalação. Após a conclusão da instalação, a guia **Ferramentas** mostrará que as ferramentas e adicionais foram instalados.



### <a name="platforms-tab"></a>Guia Plataformas

A guia **Plataformas** exibe uma lista de versões de SDK de plataforma junto com outros recursos (como imagens do sistema) para cada plataforma.


![Captura de tela do painel Plataformas](android-sdk-images/mac/sdkmanager-11.png)


Essa tela lista a versão do Android (como **Android 7.0**), o nome do código (**Nougat**), o nível da API (como **24**) e o status (**Instalado**, se a plataforma estiver instalada). A guia **Plataformas** é utilizada para instalar componentes do nível da API do Android que você tem como alvo. Para saber mais sobre as versões e níveis de API do Android, consulte [Noções Básicas sobre os Níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

Se todos os componentes de uma plataforma estiverem instalados, uma marca de seleção aparecerá ao lado do nome da plataforma. Se nem todos os componentes de uma plataforma estiverem instalados, a caixa da plataforma será preenchida. 


É possível expandir uma plataforma para ver seus componentes (e quais deles estão instalados) clicando na **seta** à esquerda da plataforma.
Clique na **seta para baixo** para recolher a lista de componentes de uma plataforma.


Para adicionar outra plataforma ao SDK, clique na caixa ao lado da plataforma até que a marca de seleção apareça para instalar todos os componentes e, em seguida, clique em **Aplicar Alterações**:


![Exemplo de como adicionar componentes Android 4.4 ao SDK do Android](android-sdk-images/mac/sdkmanager-12.png)


Para instalar somente o SDK, clique uma vez na caixa ao lado da plataforma. É possível selecionar quaisquer componentes individuais necessários:


![Exemplo de como adicionar alguns componentes do Android 4.4](android-sdk-images/mac/sdkmanager-13.png)




Observe que o número de componentes a serem instalados é exibido ao lado do botão **Aplicar Alterações**. Depois de clicar no botão **Aplicar Alterações**, a tela **Aceitação da Licença** será exibida:



![Caixa de diálogo Aceitação da Licença na guia Plataformas](android-sdk-images/mac/sdkmanager-14.png)


Clique em **Aceitar** se estiver de acordo com os termos e condições. Essa caixa de diálogo poderá ser exibida outras vezes quando houver diversos componentes a serem instalados. Na parte inferior da janela, uma barra de progresso indicará o andamento do download e da instalação. Quando o processo de download e instalação for concluído (isso pode levar vários minutos, de acordo com a quantidade de componentes que precisam ser baixados), os componentes adicionados serão marcados com uma marca de seleção e listados como **Instalados**.

-----

 
## <a name="summary"></a>Resumo

Este guia explicou como instalar e usar a ferramenta Xamarin Android SDK Manager no Visual Studio e no Visual Studio para Mac.


## <a name="related-links"></a>Links relacionados

- [Alterações nas Ferramentas do SDK do Android](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
