---
title: Qualificadores de recursos e opções de visualização
description: Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador forem correspondidos. Um exemplo simples é um recurso de cadeia de caracteres qualificado por idioma. Um recurso de cadeia de caracteres pode ser definido como o padrão, com outros recursos alternativos definidos para serem usados para idiomas adicionais. Todos os tipos de recursos podem ser qualificados, incluindo o próprio layout.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: d9f624084c83b318487f1162a9a2350f9e2cc409
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510497"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificadores de recursos e opções de visualização

_Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador forem correspondidos. Um exemplo simples é um recurso de cadeia de caracteres qualificado por idioma. Um recurso de cadeia de caracteres pode ser definido como o padrão, com outros recursos alternativos definidos para serem usados para idiomas adicionais. Todos os tipos de recursos podem ser qualificados, incluindo o próprio layout._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opções do qualificador de recursos

**As opções** do qualificador de recursos podem ser acessadas clicando no ícone de reticências à direita do botão modo **paisagem** :

[![Opções do qualificador de recursos](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Essa caixa de diálogo apresenta menus suspensos para os seguintes qualificadores de recursos:

-   **Idioma** do &ndash; Exibe recursos de idioma disponíveis e oferece uma opção para adicionar novos recursos de idioma/região.

-   **Modo de interface do usuário** Lista modos de exibição (como **encaixe de carro** e encaixe de mesa), bem como direções de layout.  &ndash;

Cada um desses menus suspensos abre novas caixas de diálogo em que você pode selecionar e configurar qualificadores de recursos (conforme explicado a seguir).

### <a name="language"></a>Idioma

O menu suspenso **idioma** lista somente os idiomas que têm recursos definidos (ou **todos os idiomas**, que é o padrão). No entanto, também há uma opção **Adicionar idioma/região...** que permite adicionar um novo idioma à lista:

[![Adicionar idioma/região](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Quando você clica em **Adicionar idioma/região...** , a caixa de diálogo **Selecionar idioma** é aberta para exibir listas suspensas de regiões e idiomas disponíveis:

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "Lista de idiomas")

Neste exemplo, escolhemos **fr (francês)** para a linguagem e **ser** (Bélgica) para o dialeto regional do francês. Observe que o campo **região** é opcional porque muitas linguagens podem ser especificadas sem considerar regiões específicas. Quando o menu suspenso **idioma** é aberto novamente, ele exibe o recurso recém-adicionado de idioma/região:

![Idioma e região escolhidos](resource-qualifiers-images/vs/11-language-region-added.png "Idioma e região escolhidos")

Observe que, se você adicionar um novo idioma, mas não criar novos recursos para ele, o idioma adicionado não será mais exibido na próxima vez que você abrir o projeto.

### <a name="ui-mode"></a>Modo de interface do usuário

Quando você clica no menu suspenso **modo de interface do usuário** , uma lista de modos é exibida, como **normal**, **encaixe de carro**, **encaixe de mesa**, **televisão**, **dispositivo**e **inspeção**:


[![Menu do modo de interface do usuário](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Abaixo dessa lista, os modos noturnos **não são noturno** e à **noite**, seguidos pelas direções de layout da **esquerda para a direita** e da **direita para a esquerda** (para obter informações sobre as opções da **esquerda para a direita** e da **direita para a esquerda** , consulte [ LayoutDirection](xref:Android.Util.LayoutDirection)).
Os últimos itens na caixa de diálogo opções do qualificador de **recursos** são as **telas** arredondadas (para uso com o Android desgaste) ou **não telas**arredondadas.
Para obter informações sobre telas arredondadas e não arredondadas, consulte [layouts](https://developer.android.com/training/wearables/ui/layouts.html).
Para obter mais informações sobre os modos de interface do usuário do Android, consulte [UiModeManager](xref:Android.App.UiModeManager).

## <a name="action-bar-settings"></a>Configurações da barra de ações

O ícone **configurações da barra de ações** está disponível à esquerda do ícone de pincel (editor de tema):

![Configurações da barra de ações](resource-qualifiers-images/vs/14-action-bar.png "Configurações da barra de ações")

Esse ícone abre uma caixa de diálogo popover que fornece uma maneira de selecionar um dos três modos de barra de ação:

-   **Padrão** &ndash; O consiste em um logotipo ou um texto de ícone e título com um subtítulo opcional.

-   **Lista** de &ndash; Modo de navegação de lista. Em vez de texto de título estático, esse modo apresenta um menu de lista para navegação dentro da atividade (ou seja, pode ser apresentado ao usuário como uma lista suspensa).

-   **Guias** &ndash; Modo de navegação de guia. Em vez de texto de título estático, esse modo apresenta uma série de guias para navegação dentro da atividade.

## <a name="themes"></a>Temas

O menu suspenso **tema** exibe todos os temas definidos no projeto. A seleção de **mais temas** abre uma caixa de diálogo com uma lista de todos os temas disponíveis no SDK do Android instalado, conforme mostrado abaixo:

[![Lista de mais temas](resource-qualifiers-images/vs/15-theme-menu-sml.png "Lista de mais temas")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Quando um tema é selecionado, o Design Surface é atualizado para mostrar o efeito do novo tema. Observe que essa alteração se tornará permanente somente se o botão **OK** for clicado na caixa de diálogo **tema** . Depois que um tema tiver sido selecionado, ele será incluído no menu suspenso **tema** , conforme mostrado abaixo:

O ![tema claro agora está disponível] O (resource-qualifiers-images/vs/16-light-theme.png "tema claro agora está disponível")

## <a name="android-version"></a>Versão do Android

O seletor de **versão** do Android define a versão do Android que é usada para renderizar o layout no designer. O seletor exibe todas as versões que são compatíveis com a versão da estrutura de destino do projeto:

![Lista de versões do Android](resource-qualifiers-images/vs/17-android-version.png "Lista de versões do Android")

A versão da estrutura de destino pode ser definida nas configurações do projeto em **propriedades > aplicativo > compilar usando a versão do Android**. Para obter mais informações sobre a versão do Framework de destino, consulte [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão da estrutura de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis na **janela Propriedades**. A lista de widgets disponíveis *não* é determinada pelo valor selecionado no seletor de **versão** da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto como Android 4,4, ainda poderá selecionar Android 6,0 no seletor de versão da barra de ferramentas para ver a aparência do projeto no Android 6,0, mas não será possível adicionar widgets específicos ao Android 6,0 &ndash;  Você ainda estará limitado aos widgets que estão disponíveis no Android 4,4.

Para obter mais informações sobre tipos de recursos, consulte [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opções do qualificador de recursos

**As opções** do qualificador de recursos podem ser acessadas clicando no ícone de reticências à direita do botão modo **paisagem** :

[![Opções do qualificador de recursos](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Essa caixa de diálogo apresenta menus suspensos para os seguintes qualificadores de recursos:

-   **Idioma** do &ndash; Exibe recursos de idioma disponíveis e oferece uma opção para adicionar novos recursos de idioma/região.

-   **Modo de interface do usuário** Lista modos de exibição (como **encaixe de carro** e encaixe de mesa), bem como direções de layout.  &ndash;

Cada um desses menus suspensos abre novas caixas de diálogo em que você pode selecionar e configurar qualificadores de recursos (conforme explicado a seguir).

### <a name="language"></a>Idioma

O menu suspenso **idioma** lista somente os idiomas que têm recursos definidos (ou **todos os idiomas**, que é o padrão). No entanto, também há uma opção **Adicionar idioma/região...** que permite adicionar um novo idioma à lista:

[![Adicionar idioma/região](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Quando você clica em **Adicionar idioma/região...** , a caixa de diálogo **Selecionar idioma** é aberta para exibir listas suspensas de regiões e idiomas disponíveis:

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

Neste exemplo, escolhemos **fr (francês)** para a linguagem e **ser** (Bélgica) para o dialeto regional do francês. Observe que o campo **região** é opcional porque muitas linguagens podem ser especificadas sem considerar regiões específicas. Quando o menu suspenso **idioma** é aberto novamente, ele exibe o recurso recém-adicionado de idioma/região:

[![Idioma e região escolhidos](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Observe que, se você adicionar um novo idioma, mas não criar novos recursos para ele, o idioma adicionado não será mais exibido na próxima vez que você abrir o projeto.

### <a name="ui-mode"></a>Modo de interface do usuário

Quando você clica no menu suspenso **modo de interface do usuário** , uma lista de modos é exibida, como **normal**, **encaixe de carro**, **encaixe de mesa**, **televisão**, **dispositivo**e **inspeção**:

[![Menu do modo de interface do usuário](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Abaixo dessa lista, os modos noturnos **não** são da noite e da **noite**, seguidos pelas direções de layout da **esquerda para a direita** e da **direita para a esquerda**. O último par de opções permite que você selecione **telas** arredondadas ou **telas retangulares** (úteis para dispositivos Android de desgaste).

Para obter mais informações sobre os modos de interface do usuário do Android, consulte [UiModeManager](xref:Android.App.UiModeManager).
Para obter informações sobre as opções da **esquerda para a direita** e da **direita para a esquerda** , consulte [LayoutDirection](xref:Android.Util.LayoutDirection).


## <a name="action-bar-settings"></a>Configurações da barra de ações

O ícone **configurações da barra de ações** está disponível à esquerda do ícone de pincel (editor de tema):

[![Configurações da barra de ações](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Esse ícone abre uma caixa de diálogo popover que fornece uma maneira de selecionar um dos três modos de barra de ação:

-   **Padrão** &ndash; O consiste em um logotipo ou ícone e texto de título com um subtítulo opcional.

-   **Lista** de &ndash; Modo de navegação de lista. Em vez de texto de título estático, esse modo apresenta um menu de lista para navegação dentro da atividade (ou seja, pode ser apresentado ao usuário como uma lista suspensa).

-   **Guias** &ndash; Modo de navegação de guia. Em vez de texto de título estático, esse modo apresenta uma série de guias para navegação dentro da atividade.

## <a name="themes"></a>Temas

O menu suspenso **tema** exibe todos os temas definidos no projeto. A seleção de **mais temas** abre uma caixa de diálogo com uma lista de todos os temas disponíveis no SDK do Android instalado, conforme mostrado abaixo:

[![Lista de mais temas](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Quando um tema é selecionado, o Design Surface é atualizado para mostrar o efeito do novo tema. Observe que essa alteração se tornará permanente somente se o botão **OK** for clicado na caixa de diálogo **tema** . Depois que um tema tiver sido selecionado, ele será incluído no menu suspenso **tema** , conforme mostrado abaixo:

[![O tema claro agora está disponível](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versão do Android

O seletor de **versão** do Android define a versão do Android que é usada para renderizar o layout no designer. O seletor exibe todas as versões que são compatíveis com a versão da estrutura de destino do projeto:

[![Lista de versões do Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

A versão da estrutura de destino pode ser definida nas configurações do projeto na seção **Opções do projeto > Build > geral** . Para obter mais informações sobre a versão do Framework de destino, consulte [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão da estrutura de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis no **painel de propriedades**. A lista de widgets disponíveis *não* é determinada pelo valor selecionado no seletor de **versão** da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto como Android 4,4, ainda poderá selecionar Android 6,0 no seletor de versão da barra de ferramentas para ver a aparência do projeto no Android 6,0, mas não será possível adicionar widgets específicos ao Android 6,0 &ndash;  Você ainda estará limitado aos widgets que estão disponíveis no Android 4,4.

Para obter mais informações sobre tipos de recursos, consulte [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
