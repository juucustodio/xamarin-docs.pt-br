---
title: Qualificadores de recursos e opções de visualização
description: Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador são correspondidas. Um exemplo simples é um recurso de cadeia de caracteres do idioma qualificado. Um recurso de cadeia de caracteres pode ser definido como padrão, com outros recursos alternativos definidos para ser usado para idiomas adicionais. Todos os tipos de recurso podem ser qualificados, incluindo o layout em si.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bc9eb145b6d9ed7bd441d625f533c5cbbd87fccd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificadores de recursos e opções de visualização

_Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador são correspondidas. Um exemplo simples é um recurso de cadeia de caracteres do idioma qualificado. Um recurso de cadeia de caracteres pode ser definido como padrão, com outros recursos alternativos definidos para ser usado para idiomas adicionais. Todos os tipos de recurso podem ser qualificados, incluindo o layout em si._


## <a name="custom-device-configurations"></a>Configurações de dispositivo personalizado

Android está disponível em uma grande quantidade de dispositivos e resoluções de tela.
Para ajudar a interfaces de usuário de design que vários dispositivos de destino, o Designer vem com uma variedade de configurações do dispositivo interno. Ele também dá suporte a adição de configurações de dispositivo adicionais; Essas configurações são baseadas em *qualificadores* que você especificar para distinguir uma configuração de dispositivo de outra. Há muitos tipos diferentes de qualificadores. Para obter mais informações sobre esses tipos de recursos, consulte [recursos Android](~/android/app-fundamentals/resources-in-android/index.md).

Na parte inferior do seletor de dispositivo de menu é um **personalizar** opção conforme mostrado abaixo:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu de seletor de dispositivo](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menu de seletor de dispositivo](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Selecionando **personalizar** exibe uma caixa de diálogo que você pode usar para navegar por meio de configurações de dispositivo disponível. Quando você clica o **definições de dispositivo** guia, uma lista de todas as definições de dispositivo conhecido é apresentada:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Gerenciador de AVD](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Gerenciador de AVD](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


Dispositivos pré-configurados no Designer não podem ser modificados. No entanto, você pode clicar em **criar dispositivo...**  para definir uma definição de dispositivo personalizada. Como alternativa, você pode selecionar uma definição existente e clique em **Clone...**  usá-la como ponto de partida para uma nova definição.
Por exemplo, ao selecionar a definição **Nexus 5** e clicar em **Clone...** , a seguinte caixa de diálogo será exibida:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo de clone](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo de clone](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


Na próxima captura de tela, o nome é alterado para **Nexus 5 personalizado** e os parâmetros do dispositivo são modificados para criar uma nova definição de dispositivo personalizada. Neste exemplo, **retrato** está desabilitada para que a definição do dispositivo é somente de cenário:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


Ao clicar em **Clonar dispositivo**, uma nova definição de dispositivo é criada, que agora aparece na lista **Definições de dispositivo**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Definições de dispositivos atualizados](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Definições de dispositivos atualizados](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Observe que cada definição de dispositivo de usuário criado é exibida com um ícone verde como mostrado acima. Ao retornar para o **dispositivo** menu de seletor, a nova definição de dispositivo personalizada é apresentada na seção superior da lista (se você não vir a configuração de dispositivo personalizado nesta lista, tente reiniciar o IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado aparece na lista de dispositivos](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado aparece na lista de dispositivos](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Selecionar essa configuração de dispositivo modifica o layout de acordo com as personalizações que criou anteriormente (nesse caso, somente paisagem modo):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Dispositivo personalizado em uso](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Dispositivo personalizado em uso](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Opções de qualificador de recurso

**Opções de qualificador de recurso** pode ser acessado clicando no ícone de triângulo para baixo à direita do **configuração do dispositivo** opções:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Opções de qualificador de recurso](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Opções de qualificador de recurso](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Essa caixa de diálogo apresenta menus suspensos para os seguintes qualificadores de recursos:

-   **Idioma** &ndash; exibe os recursos de idioma disponíveis e oferece uma opção para adicionar novos recursos de idioma/região.

-   **Modo de interface do usuário** &ndash; modos de exibição de lista (como **encaixe de carro** e **encaixe de suporte técnico**), bem como instruções de layout.

Cada um desses menus suspensos abre novas caixas de diálogo onde você pode selecionar e configurar os qualificadores de recursos (conforme explicado a seguir).



### <a name="language"></a>Idioma

O **idioma** menu suspenso lista apenas os idiomas que têm recursos definidos (ou **todos os idiomas**, que é o padrão). No entanto, há também uma **Adicionar idioma/região...**  opção que permite que você adicione um novo idioma na lista:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Adicionar idioma/região](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Adicionar idioma/região](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


Quando você clica em **Adicionar idioma/região...** , o **Selecionar idioma** caixa de diálogo é aberta para exibir listas suspensas de regiões e idiomas disponíveis:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "lista de idiomas")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


Neste exemplo, escolhemos **fr (francês)** para o idioma e **BE** (Bélgica) para o dialeto regional de francês. Observe que o **região** campo é opcional porque muitos idiomas podem ser especificados sem levar em consideração para regiões específicas. Quando o **idioma** menu suspenso for aberto novamente, ele exibe o recurso de idioma/região adicionados recentemente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Idioma e região escolhida](resource-qualifiers-images/vs/11-language-region-added.png "escolhida de idioma e região")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Idioma e região escolhida](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Observe que, se você adiciona um novo idioma, mas você não criar novos recursos para ele, não o fará de idioma adicionadas mostrado na próxima vez que abrir o projeto.



### <a name="ui-mode"></a>Modo de interface do usuário

Quando você clica o **modo de interface do usuário** menu suspenso, uma lista de modos é exibida, como **Normal**, **encaixe de carro**, **encaixe de suporte técnico**, **Televisão**, **dispositivo**, e **inspecionar**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu do modo de interface do usuário](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Essa lista abaixo são os modos noite **não noite** e **noite**, seguido as instruções de layout **esquerda para a direita** e **direita para a esquerda** (para informações sobre **esquerda para a direita** e **direita para a esquerda** opções, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
Os últimos itens no **opções de qualificador de recurso** caixa de diálogo são o **telas de ida e volta** (para uso com desgaste Android) ou **arredondará telas** (para obter informações sobre round e telas de não-round, consulte [Layouts](https://developer.android.com/training/wearables/ui/layouts.html)).
Para obter mais informações sobre modos de interface do usuário Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menu do modo de interface do usuário](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

Essa lista abaixo são os modos noite **não noite** e **noite**, seguido as instruções de layout **esquerda para a direita** e **direita para a esquerda**. Para obter mais informações sobre modos de interface do usuário Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Para obter informações sobre **esquerda para a direita** e **direita para a esquerda** opções, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Tela round

O último item no **opções de qualificador de recurso** caixa de diálogo é o **Round tela** menu. Esse menu permite que você selecione **telas de ida e volta** (para uso com desgaste Android) ou **retangulares telas**:

[![Menu de tela round](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Configurações de barra de ação

O **as configurações de barra de ação** ícone está disponível para a esquerda do ícone de pincel (Editor de tema):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Configurações de barra de ação](resource-qualifiers-images/vs/14-action-bar.png "configurações de barra de ação")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Configurações de barra de ação](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Esse ícone abre popover uma caixa de diálogo que fornece uma maneira de selecionar um dos três modos de barra de ação:

-   **Padrão** &ndash; consiste em um texto logotipo ou ícone e o título com uma legenda opcional.

-   **Lista** &ndash; modo de navegação da lista. Em vez de texto do título estático, esse modo apresenta um menu de lista para navegação dentro da atividade (ou seja, ele pode ser apresentado ao usuário como uma lista suspensa).

-   **Guias** &ndash; modo de navegação do guia. Em vez de texto do título estático, esse modo apresenta uma série de guias de navegação dentro da atividade.



## <a name="themes"></a>Temas

O **tema** menu suspenso exibe todos os temas definidos no projeto. Selecionando **mais temas** abre uma caixa de diálogo com uma lista de todos os temas disponíveis do SDK do Android instalado, conforme mostrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Lista de temas mais](resource-qualifiers-images/vs/15-theme-menu-sml.png "lista mais temas")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de temas mais](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


Quando um tema é selecionado, a superfície de Design é atualizada para mostrar o efeito do novo tema. Observe que essa alteração se tornam permanente somente se o **Okey** botão é clicado no **tema** caixa de diálogo. Quando um tema tiver sido selecionado, ela será incluída no **tema** lista suspensa conforme mostrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Tema claro agora está disponível](resource-qualifiers-images/vs/16-light-theme.png "tema claro agora está disponível")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Tema claro agora está disponível](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Versão do Android

O Android **versão** seletor define a versão do Android que é usada para renderizar o layout no Designer. O Seletor exibe todas as versões compatíveis com a versão do framework de destino do projeto:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Lista de versões Android](resource-qualifiers-images/vs/17-android-version.png "versões de lista do Android")

A versão do framework de destino pode ser definida nas configurações do projeto em **Propriedades > aplicativos > compilar usando a versão do Android**. Para obter mais informações sobre a versão do framework de destino, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão do framework de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis no **janela propriedades**. Lista de widgets é *não* determinado pelo valor selecionado no **versão** seletor da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto para o Android 4.4, você ainda pode selecionar Android 6.0 no seletor de versão de barra de ferramentas para ver a aparência de projeto no Android 6.0, mas você não poderá adicionar widgets que são específicos para Android 6.0 &ndash;  Você ainda será limitado para os widgets que estão disponíveis no Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Lista de versões do Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

A versão do framework de destino pode ser definida nas configurações do projeto sob o **opções de projeto > compilar > geral** seção. Para obter mais informações sobre a versão do framework de destino, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão do framework de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis no **propriedade preenchimento**. Lista de widgets é *não* determinado pelo valor selecionado no **versão** seletor da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto para o Android 4.4, você ainda pode selecionar Android 6.0 no seletor de versão de barra de ferramentas para ver a aparência de projeto no Android 6.0, mas você não poderá adicionar widgets que são específicos para Android 6.0 &ndash;  Você ainda será limitado para os widgets que estão disponíveis no Android 4.4.

-----
