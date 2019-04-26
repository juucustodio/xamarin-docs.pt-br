---
title: Qualificadores de recursos e opções de visualização
description: Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador são correspondidos. Um exemplo simples é um recurso de cadeia de caracteres de idioma qualificado. Um recurso de cadeia de caracteres pode ser definido como padrão, com outros recursos alternativos definidos para ser usado para idiomas adicionais. Todos os tipos de recursos podem ser qualificados, incluindo o layout em si.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9d99e6a59b57b59d585b32befdadc0890d41448c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950523"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Qualificadores de recursos e opções de visualização

_Este tópico explica como definir recursos que serão usados somente quando alguns valores de qualificador são correspondidos. Um exemplo simples é um recurso de cadeia de caracteres de idioma qualificado. Um recurso de cadeia de caracteres pode ser definido como padrão, com outros recursos alternativos definidos para ser usado para idiomas adicionais. Todos os tipos de recursos podem ser qualificados, incluindo o layout em si._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>Opções do qualificador de recurso

**Opções do qualificador de recurso** pode ser acessado clicando no ícone de reticências à direita do **paisagem** botão modo:

[![Opções do qualificador de recurso](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

Essa caixa de diálogo apresenta menus suspensos para os qualificadores de recursos a seguir:

-   **Linguagem** &ndash; exibe os recursos de idioma disponível e oferece uma opção para adicionar novos recursos de idioma/região.

-   **Modo de interface do usuário** &ndash; modos de exibição de lista (como **encaixe para carro** e **encaixe de mesa**), bem como instruções de layout.

Cada um desses menus suspensos abre novas caixas de diálogo onde você pode selecionar e configurar os qualificadores de recursos (conforme explicado a seguir).

### <a name="language"></a>Idioma

O **linguagem** menu suspenso lista apenas os idiomas que têm recursos definidos (ou **todos os idiomas**, que é o padrão). No entanto, há também um **Adicionar idioma/região...**  opção que permite que você adicione um novo idioma à lista:

[![Adicionar idioma/região](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

Quando você clica em **Adicionar idioma/região...** , o **Selecionar idioma** caixa de diálogo é aberta para exibir listas suspensas de regiões e idiomas disponíveis:

![Lista de idiomas](resource-qualifiers-images/vs/10-languages.png "lista de idiomas")

Neste exemplo, escolhemos **fr (francês)** para o idioma e **BE** (Bélgica) para o dialeto regional do francês. Observe que o **região** campo é opcional, porque muitos idiomas podem ser especificados sem levar em consideração para regiões específicas. Quando o **linguagem** menu suspenso é aberto novamente, ele exibe o recurso de idioma/região adicionado recentemente:

![Idioma e região escolhida](resource-qualifiers-images/vs/11-language-region-added.png "escolhida de idioma e região")

Observe que, se você adiciona um novo idioma, mas você não criar novos recursos para ele, o idioma adicionado não serão mais ser mostradas na próxima vez que você abrir o projeto.

### <a name="ui-mode"></a>Modo de interface do usuário

Quando você clica o **modo de interface do usuário** menu suspenso, uma lista dos modos é exibida, como **Normal**, **de encaixe para carro**, **encaixe de mesa**, **Televisão**, **Appliance**, e **inspeção**:


[![Menu do modo de interface do usuário](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Essa lista abaixo são os modos de noite **não noite** e **noite**, seguido as instruções de layout **esquerda para a direita** e **direita para a esquerda** (para informações sobre **esquerda para a direita** e **direita para a esquerda** opções, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)).
Os últimos itens na **opções do qualificador de recurso** caixa de diálogo são as **telas de ida e volta** (para uso com o Android Wear) ou **telas não arredondada**.
Para obter informações sobre telas round e não round, consulte [Layouts](https://developer.android.com/training/wearables/ui/layouts.html).
Para obter mais informações sobre os modos de interface do usuário Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

## <a name="action-bar-settings"></a>Configurações da barra de ação

O **configurações da barra de ação** ícone está disponível à esquerda do ícone de pincel (Editor de tema):

![Configurações da barra de ação](resource-qualifiers-images/vs/14-action-bar.png "configurações da barra de ação")

Esse ícone abre um pop-over caixa de diálogo que fornece uma maneira de selecionar um dos três modos de barra de ação:

-   **Standard** &ndash; consiste em um logotipo ou um texto de título e o ícone com uma legenda opcional.

-   **Lista** &ndash; modo de navegação da lista. Em vez de texto do título estático, esse modo apresenta um menu de lista para a navegação dentro da atividade (ou seja, ele pode ser apresentado ao usuário como uma lista suspensa).

-   **Guias** &ndash; modo de navegação da guia. Em vez de texto do título estático, esse modo apresenta uma série de guias para navegação dentro da atividade.

## <a name="themes"></a>Temas

O **tema** menu suspenso exibe todos os temas definidos no projeto. Selecionando **mais temas** abre uma caixa de diálogo com uma lista de todos os temas disponíveis no SDK do Android instalado, conforme mostrado abaixo:

[![Lista de temas mais](resource-qualifiers-images/vs/15-theme-menu-sml.png "lista mais temas")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

Quando um tema é selecionado, a superfície de Design é atualizada para mostrar o efeito do novo tema. Observe que essa alteração se tornam permanente somente se o **Okey** botão é clicado na **tema** caixa de diálogo. Depois que um tema tiver sido selecionado, ele será incluído na **tema** menu de lista suspensa conforme mostrado abaixo:

![Tema claro agora está disponível](resource-qualifiers-images/vs/16-light-theme.png "tema claro agora está disponível")

## <a name="android-version"></a>Versão do Android

O Android **versão** seletor define a versão do Android que é usada para renderizar o layout no Designer. O seletor de exibe todas as versões que são compatíveis com a versão do framework de destino do projeto:

![Lista de versões do Android](resource-qualifiers-images/vs/17-android-version.png "versões lista do Android")

A versão do framework de destino pode ser definida nas configurações do projeto em **Propriedades > aplicativo > compilar usando a versão Android**. Para obter mais informações sobre a versão do framework de destino, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão de framework de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis na **janela de propriedades**. É a lista disponível de widgets *não* determinado pelo valor selecionado na **versão** seletor da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto para o Android 4.4, você ainda pode selecionar o Android 6.0 no seletor de versão de barra de ferramentas para ver a aparência de projeto no Android 6.0, mas você não poderá adicionar widgets que são específicas para o Android 6.0 &ndash;  Você ainda será limitado para os widgets que estão disponíveis no Android 4.4.

Para obter mais informações sobre tipos de recursos, consulte [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>Opções do qualificador de recurso

**Opções do qualificador de recurso** pode ser acessado clicando no ícone de reticências à direita do **paisagem** botão modo:

[![Opções do qualificador de recurso](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

Essa caixa de diálogo apresenta menus suspensos para os qualificadores de recursos a seguir:

-   **Linguagem** &ndash; exibe os recursos de idioma disponível e oferece uma opção para adicionar novos recursos de idioma/região.

-   **Modo de interface do usuário** &ndash; modos de exibição de lista (como **encaixe para carro** e **encaixe de mesa**), bem como instruções de layout.

Cada um desses menus suspensos abre novas caixas de diálogo onde você pode selecionar e configurar os qualificadores de recursos (conforme explicado a seguir).

### <a name="language"></a>Idioma

O **linguagem** menu suspenso lista apenas os idiomas que têm recursos definidos (ou **todos os idiomas**, que é o padrão). No entanto, há também um **Adicionar idioma/região...**  opção que permite que você adicione um novo idioma à lista:

[![Adicionar idioma/região](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

Quando você clica em **Adicionar idioma/região...** , o **Selecionar idioma** caixa de diálogo é aberta para exibir listas suspensas de regiões e idiomas disponíveis:

[![Lista de idiomas](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

Neste exemplo, escolhemos **fr (francês)** para o idioma e **BE** (Bélgica) para o dialeto regional do francês. Observe que o **região** campo é opcional, porque muitos idiomas podem ser especificados sem levar em consideração para regiões específicas. Quando o **linguagem** menu suspenso é aberto novamente, ele exibe o recurso de idioma/região adicionado recentemente:

[![Idioma e região escolhida](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

Observe que, se você adiciona um novo idioma, mas você não criar novos recursos para ele, o idioma adicionado não serão mais ser mostradas na próxima vez que você abrir o projeto.

### <a name="ui-mode"></a>Modo de interface do usuário

Quando você clica o **modo de interface do usuário** menu suspenso, uma lista dos modos é exibida, como **Normal**, **de encaixe para carro**, **encaixe de mesa**, **Televisão**, **Appliance**, e **inspeção**:

[![Menu do modo de interface do usuário](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

Essa lista abaixo são os modos de noite **não noite** e **noite**, seguido as instruções de layout **esquerda para a direita** e **direita para a esquerda**. O último par de opções permite que você selecione **telas de ida e volta** ou **telas retangulares** (útil para dispositivos Android Wear).

Para obter mais informações sobre os modos de interface do usuário Android, consulte [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Para obter informações sobre **esquerda para a direita** e **direita para a esquerda** opções, consulte [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).


## <a name="action-bar-settings"></a>Configurações da barra de ação

O **configurações da barra de ação** ícone está disponível à esquerda do ícone de pincel (Editor de tema):

[![Configurações da barra de ação](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

Esse ícone abre um pop-over caixa de diálogo que fornece uma maneira de selecionar um dos três modos de barra de ação:

-   **Standard** &ndash; consiste em um texto logotipo ou ícone e o título com uma legenda opcional.

-   **Lista** &ndash; modo de navegação da lista. Em vez de texto do título estático, esse modo apresenta um menu de lista para a navegação dentro da atividade (ou seja, ele pode ser apresentado ao usuário como uma lista suspensa).

-   **Guias** &ndash; modo de navegação da guia. Em vez de texto do título estático, esse modo apresenta uma série de guias para navegação dentro da atividade.

## <a name="themes"></a>Temas

O **tema** menu suspenso exibe todos os temas definidos no projeto. Selecionando **mais temas** abre uma caixa de diálogo com uma lista de todos os temas disponíveis no SDK do Android instalado, conforme mostrado abaixo:

[![Lista de temas mais](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

Quando um tema é selecionado, a superfície de Design é atualizada para mostrar o efeito do novo tema. Observe que essa alteração se tornam permanente somente se o **Okey** botão é clicado na **tema** caixa de diálogo. Depois que um tema tiver sido selecionado, ele será incluído na **tema** menu de lista suspensa conforme mostrado abaixo:

[![Tema claro agora está disponível](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Versão do Android

O Android **versão** seletor define a versão do Android que é usada para renderizar o layout no Designer. O seletor de exibe todas as versões que são compatíveis com a versão do framework de destino do projeto:

[![Lista de versões do Android](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

A versão do framework de destino pode ser definida nas configurações do projeto sob o **opções de projeto > Build > geral** seção. Para obter mais informações sobre a versão do framework de destino, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

O conjunto de widgets disponíveis na caixa de ferramentas é determinado pela versão de framework de destino do projeto. Isso também é verdadeiro para as propriedades disponíveis na **painel de propriedade**. É a lista disponível de widgets *não* determinado pelo valor selecionado na **versão** seletor da barra de ferramentas. Por exemplo, se você definir a versão de destino do projeto para o Android 4.4, você ainda pode selecionar o Android 6.0 no seletor de versão de barra de ferramentas para ver a aparência de projeto no Android 6.0, mas você não poderá adicionar widgets que são específicas para o Android 6.0 &ndash;  Você ainda será limitado para os widgets que estão disponíveis no Android 4.4.

Para obter mais informações sobre tipos de recursos, consulte [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).

-----
