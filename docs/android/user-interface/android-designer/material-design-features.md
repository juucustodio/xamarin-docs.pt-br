---
title: Recursos de Design de Material de xamarin. Android Designer
description: Este tópico descreve os recursos de Designer que tornam mais fácil para os desenvolvedores criem Material Design compatível com layouts. Esta seção apresenta e explica como usar a grade de Material, a paleta de cores de Material, a escala tipográficos e o Editor de tema.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: eb636c3b7a41adbab9162e192ead65def377a1a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118975"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Recursos de Design de Material de Designer do xamarin. Android

_Este tópico descreve os recursos de Designer que tornam mais fácil para os desenvolvedores criem Material Design compatível com layouts. Esta seção apresenta e explica como usar a grade de Material, a paleta de cores de Material, a escala tipográficos e o Editor de tema._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evoluir 2016: Todos podem criar belos aplicativos com o Design de Material**

## <a name="overview"></a>Visão geral

O Designer do xamarin. Android inclui recursos que facilitam a criação de layouts de Material Design compatíveis. Se você não estiver familiarizado com o Design de Material, consulte o [introdução de Design de Material](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Neste guia, teremos que examinar os seguintes recursos de Designer:

-   *Grade de material* &ndash; uma sobreposição na superfície de Design que mostra uma grade, espaçamento e linhas para ajudá-lo a colocar os widgets de layout de acordo com as diretrizes de Design de Material.

-   *Editor de tema* &ndash; um editor de recursos de cor pequeno que lhe permite define informações de cores para um subconjunto de um tema. Por exemplo, você pode visualizar e modificar as cores de Material, como `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

Vamos examinar cada um desses recursos e fornecem exemplos de como usá-los.

## <a name="material-design-grid"></a>Grade de Design de material

O menu de grade de Design de Material está disponível na barra de ferramentas na parte superior do Designer:

[![Grade de Design de material](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Quando você clica no ícone de grade de Design de Material, o Designer exibe uma sobreposição na superfície de Design que inclui os seguintes elementos:

-   Linhas (linhas em laranja)

-   Espaçamento (verdes áreas)

-   Uma grade (linhas azuis)

Esses elementos podem ser vistos na captura de tela anterior. Cada um desses itens de sobreposição é configurável. Quando você clica no botão de reticências ao lado do menu de grade de Design de Material, um pop-over de caixa de diálogo é aberta que permite que você ativar/desativar grade, configure o posicionamento de linhas e definir espaços. Observe que todos os valores são expressos em `dp` (pixels independentes de densidade):

[![Grade, contorno e a configuração de espaçamento](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Para adicionar novos contorno, insira um novo valor de deslocamento na **deslocamento** , selecione um local (**esquerdo**, **superior**, **direita**, ou  **inferior**) e clique no ícone para adicionar o novo contorno +. Da mesma forma, para adicionar um novo espaçamento, insira o tamanho e deslocamento (no ponto de distribuição) para o **tamanho** e **deslocamento** caixas, respectivamente. Selecione um local (**esquerdo**, **superior**, **direita**, ou **inferior**) e clique no ícone para adicionar o novo espaçamento +.

Quando você alterar esses valores de configuração, elas são salvas no arquivo XML de layout e reutilizadas quando você abre o layout novamente.


## <a name="theme-editor"></a>Editor de tema

O **Editor de tema** permite que você personalize as informações de cores para um subconjunto de atributos de tema. Para abrir o **Editor de tema**, clique no ícone de pincel na barra de ferramentas:

[![Ícone do Editor de tema](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Embora o **Editor de tema** é acessível na barra de ferramentas para todos os níveis de API e versões do Android de destino somente um subconjunto dos recursos descritos abaixo estão disponíveis se o nível de API de destino for anterior a API 21 (Android 5.0 Pirulito).

O painel esquerdo do **Editor de tema** exibe a lista de cores que formam o tema selecionado no momento (neste exemplo, estamos usando o `Default Theme`):

[![Editor de tema](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Quando você seleciona uma cor à esquerda, o painel direito fornece as seguintes guias para ajudá-lo a editar essa cor:

-   **Herdar** &ndash; exibe um diagrama de herança de estilo para a cor selecionada e lista a cor resolvido e o código de cor atribuída a essa cor de tema.

-   **Seletor de cores** &ndash; permite que você altere a cor selecionada como qualquer valor arbitrário.

-   **Paleta de materiais** &ndash; permite a você alterar a cor selecionada para um valor que está de acordo com o Design de Material.

-   **Recursos** &ndash; permite que você altere a cor selecionada para um dos outros recursos de cor existente no tema.

Vamos examinar cada uma dessas guias em detalhes.

### <a name="inherit-tab"></a>Herdar de guia

Como visto no exemplo a seguir, o **herdar** guia lista a herança de estilo para o **plano de fundo** cor do **tema padrão**:

[![Herdar de guia](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

Neste exemplo, o **tema padrão** herda de um estilo que usa `@color/background_material_light` , mas substitui-lo com `color/material_grey_50`, que tem um valor de código de cor de `#fffafafa`.
Para obter mais informações sobre herança de estilo, consulte [estilos e temas](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Seletor de Cor

Captura de tela a seguir ilustra a **seletor de cor**:

[![Seletor de cores](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

Neste exemplo, o **plano de fundo** cor pode ser alterada para qualquer valor através de várias maneiras:

-   Clicando em uma cor diretamente.
-   Inserindo valores de matiz, saturação e brilho.
-   Inserindo valores RGB (vermelhos, verdes, azuis) em decimal.
-   Definindo o alfa (opacity) para a cor selecionada.
-   Inserir o código de cor hexadecimal diretamente.

É a cor escolhida no seletor de cores *não* restrito às diretrizes de Design de Material ou para o conjunto de recursos de cores disponíveis.

### <a name="resources"></a>Recursos

O **recursos** guia oferece uma lista de recursos de cor que já estão presentes no tema:

[![Recursos](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

Usando o **recursos** guia restringe as opções para essa lista de cores. Lembre-se de que se você escolher um recurso de cor que já está atribuído a outra parte do tema, dois elementos adjacentes da interface do usuário podem "executados juntos" (porque eles têm a mesma cor) e se tornar difícil distinguir o usuário.

### <a name="material-palette"></a>Paleta de materiais

O **Material paleta** guia é aberta a **paleta de cores de Design de Material**. Escolher um valor de cor da paleta desse restringe sua opção de cor para que ele seja consistente com as diretrizes de Design de Material:

[![Paleta de materiais](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

A parte superior da paleta de cores exibe cores primárias de Design de Material, enquanto a parte inferior da Paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matizes é exibido na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *500 Indigo*:

![Selecione Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` é definido como o código de cor para *500 Indigo* (`#ff3f51b5`), e o Designer atualiza a cor do plano de fundo para refletir essa alteração:

[![Alterado de tonalidade de plano de fundo](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Para obter mais informações sobre a paleta de cores do Design de Material, consulte o Design de Material [guia da paleta de cores](https://material.io/design/color/).

### <a name="creating-a-new-theme"></a>Criando um novo tema

No exemplo a seguir, usaremos a paleta de Material para criar um novo tema personalizado. Primeiro, vamos alterar o **plano de fundo** cor a ser *azul 900*:

![Alterar o plano de fundo como azul 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Quando um recurso de cor é alterado, uma mensagem é exibida com a mensagem *tema atual tem alterações não salvas*:

[![Aviso de alterações não salvas](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

O **plano de fundo** cor no Designer foi alterado para a nova seleção de cor, mas essa alteração ainda não foi salvo. Neste ponto, você pode fazer o seguinte:

-   Clique em **descartar alterações** para descartar a nova opção de cor (ou opções) e reverter o tema ao seu estado original. 

-   Pressione <kbd>CTRL + S</kbd> para salvar suas alterações para o tema no momento. 

No exemplo a seguir <kbd>CTRL + S</kbd> foi pressionada para que as alterações foram salvas **AppTheme**:

[![Alterações salvas AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Resumo

Este tópico descreveu os recursos de Design de Material disponíveis no Designer de xamarin. Android. Ele explicou como habilitar e configurar a grade de Design de Material, e ele explicou como usar o Editor de tema para criar novos temas personalizados que estão em conformidade com as diretrizes de Design de Material.
Para obter mais informações sobre o suporte de xamarin. Android para o Design de Material, consulte [tema de Material](~/android/user-interface/material-theme.md).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Neste guia, vamos um examine os seguintes recursos de Designer:

-   *Grade de Design de material* &ndash; uma sobreposição na superfície de Design que mostra uma grade, espaçamento e linhas para ajudá-lo a colocar os widgets de layout de acordo com as diretrizes de Design de Material.

-   *Paleta de cores de Design de material* &ndash; caixa de diálogo de um painel de propriedade que ajuda você a escolher uma cor da paleta de Design de Material oficial.

-   *Escala tipográfica* &ndash; caixa de diálogo de um painel de propriedade que fornece uma opção de configurações compatíveis com o Material Design para o `textAppearance` propriedade dos campos de texto.

-   *Editor de tema* &ndash; um editor de recursos de cor pequeno que lhe permite define informações de cores para um subconjunto de um tema. Por exemplo, você pode visualizar e modificar as cores de Material, como `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

Vamos examinar cada um desses recursos e fornecem exemplos de como usá-los.

## <a name="material-design-grid"></a>Grade de Design de material

O menu de grade de Design de Material está disponível na barra de ferramentas na parte superior do Designer:

[![Grade de Design de material](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Quando você clica no ícone de grade de Design de Material, o Designer exibe uma sobreposição na superfície de Design que inclui os seguintes elementos:

-   Linhas (linhas em laranja)

-   Espaçamento (verdes áreas)

-   Uma grade (linhas azuis)

Esses elementos podem ser vistos na captura de tela a seguir:

[![Contorno, espaçamento e grade](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Cada um desses itens de sobreposição é configurável. Quando você clica no botão de reticências (&hellip;) ao lado do menu de grade de Design de Material, abre um pop-over de caixa de diálogo que permite que você ativar/desativar grade, configure o posicionamento de linhas e definir os espaçamentos. Observe que todos os valores são expressos em `dp` (pixels independentes de densidade):

[![Grade, contorno e a configuração de espaçamento](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Para adicionar novos contorno, insira um novo valor de deslocamento na **deslocamento** , selecione um local (**esquerdo**, **superior**, **direita**, ou  **inferior**) e clique no ícone (que aparece para a direita quando um valor for inserido) + para adicionar o contorno de novo. Da mesma forma, para adicionar um novo espaçamento, insira o tamanho e deslocamento (no ponto de distribuição) para o **tamanho** e **deslocamento** caixas, respectivamente. Selecione um local (**esquerdo**, **superior**, **direita**, ou **inferior**) e clique no ícone para adicionar o novo espaçamento +.

Quando você alterar esses valores de configuração, elas são salvas no arquivo XML de layout e reutilizadas quando você abre o layout novamente.

## <a name="material-design-color-palette"></a>Paleta de cores de Design de material

Cada item do painel de propriedade que aceita uma cor agora tem um ícone de paleta adicional que você pode usar para abrir a paleta de cores de Design de Material, conforme mostrado nesta captura de tela:

[![Ícone de cor](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando você clicar nesse ícone, um pop-over de caixa de diálogo é aberta que permite que você defina a cor da propriedade da paleta de cores de Design de Material:

[![Paleta de cores do material Design](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

A parte superior da paleta de cores exibe cores primárias de Design de Material, enquanto a parte inferior da Paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matizes é exibido na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *500 Indigo*:

[![Escolha Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` é definido como o código de cor para *500 Indigo* (`#ff3f51b5`), e o Designer atualiza a cor do plano de fundo do botão para refletir essa alteração:

[![Alterações de tonalidade do plano de fundo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Para obter mais informações sobre a paleta de cores do Design de Material, consulte o Design de Material [guia da paleta de cores](https://material.io/design/color/).

## <a name="typographic-scale"></a>Escala tipográfica

O **aparência do texto** seção o **propriedade** pad **estilo** guia tem um ícone que permite que você selecione uma de um `TextAppearance` estilo que está de acordo com o Design de Material especificação de:

[![Guia de estilo](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando você clicar nesse ícone, ele abre o **escala tipográficas** pop-de over caixa de diálogo, que apresenta uma lista de estilos de texto pré-configurados que podem ser escolhidos:

[![Seletor de estilo do texto](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

No exemplo a seguir, clicando em **exibir 1** altera o texto do botão com a maior fonte do **exibir 1**:

[![Estilo de exibição 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

O estilo de texto na **escala tipográficas** caixa de diálogo segue o **tema** configuração. Por exemplo, se o **Light** tema é escolhido no Designer, a lista dos espelhos de estilos de texto disponível a **luz** tema:

[![Tema claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor de tema

O **Editor de tema** permite que você personalize as informações de cores para um subconjunto de atributos de tema. Para abrir o **Editor de tema**, clique no ícone de pincel na barra de ferramentas:

[![Ícone do Editor de tema](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Embora o **Editor de tema** é acessível na barra de ferramentas para todos os níveis de API e versões do Android de destino somente um subconjunto dos recursos descritos abaixo estão disponíveis se o nível de API de destino for anterior a API 21 (Android 5.0 Pirulito).

O painel esquerdo do **Editor de tema** exibe a lista de cores que formam o tema selecionado no momento (neste exemplo, estamos usando o `Default Theme`):

[![Editor de tema](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Quando você seleciona uma cor à esquerda, o painel direito fornece as seguintes guias para ajudá-lo a editar essa cor:

-   **Herdar** &ndash; exibe um diagrama de herança de estilo para a cor selecionada e lista a cor resolvido e o código de cor atribuída a essa cor de tema.

-   **Seletor de cores** &ndash; permite que você altere a cor selecionada como qualquer valor arbitrário.

-   **Paleta de materiais** &ndash; permite a você alterar a cor selecionada para um valor que está de acordo com o Design de Material.

-   **Recursos** &ndash; permite que você altere a cor selecionada para um dos outros recursos de cor existente no tema.

Vamos examinar cada uma dessas guias em detalhes.

### <a name="inherit-tab"></a>Herdar de guia

Como visto no exemplo a seguir, o **herdar** guia lista a herança de estilo para o **plano de fundo** cor do **tema padrão**:

[![Herdar de guia](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

Neste exemplo, o **tema padrão** herda de um estilo que usa `@color/background_material_dark` , mas substitui-lo com `color/material_grey_850`, que tem um valor de código de cor de `#ff303030`.
Para obter mais informações sobre herança de estilo, consulte [estilos e temas](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Seletor de Cor

Captura de tela a seguir ilustra a **seletor de cor**:

[![Seletor de cores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)


Neste exemplo, o **plano de fundo** cor pode ser alterada para qualquer valor através de várias maneiras:

-   Clicando em uma cor diretamente.
-   Inserindo valores de matiz, saturação e brilho.
-   Inserindo valores RGB (vermelhos, verdes, azuis) em decimal.
-   Definindo o alfa (opacity) para a cor selecionada.
-   Inserir o código de cor hexadecimal diretamente.

É a cor escolhida no seletor de cores *não* restrito às diretrizes de Design de Material ou para o conjunto de recursos de cores disponíveis.

### <a name="resources"></a>Recursos

O **recursos** guia oferece uma lista de recursos de cor que já estão presentes no tema:

[![Recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

Usando o **recursos** guia restringe as opções para essa lista de cores. Lembre-se de que se você escolher um recurso de cor que já está atribuído a outra parte do tema, dois elementos adjacentes da interface do usuário podem "executados juntos" (porque eles têm a mesma cor) e se tornar difícil distinguir o usuário.

### <a name="material-palette"></a>Paleta de materiais

O **Material paleta** guia é aberta a **paleta de cores de Design de Material** descrito [anteriores](#material_palette). Escolher um valor de cor da paleta desse restringe sua opção de cor para que ele seja consistente com as diretrizes de Design de Material.

[![Paleta de materiais](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Criando um novo tema

No exemplo a seguir, usaremos a paleta de Material para criar um novo tema personalizado. Primeiro, vamos alterar o **plano de fundo** cor a ser *azul 900*:

[![Alterar o plano de fundo como azul 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Quando um recurso de cor é alterado, uma mensagem é exibida com a mensagem *tema atual tem alterações não salvas*:

[![Aviso de alterações não salvas](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

Foi feita a alteração de cor no Designer, mas essa alteração ainda não foi salvo. Neste ponto, você pode fazer o seguinte:

-   Clique em **descartar alterações** para descartar a nova opção de cor (ou opções) e reverter o tema ao seu estado original. 

-   Pressione  **&#8984; + S** salvar suas alterações em um novo tema chamado **personalizado**. 


## <a name="summary"></a>Resumo

Este tópico descreveu os recursos de Design de Material disponíveis no Designer de xamarin. Android. Ele explicou como habilitar e configurar a grade de Design de Material, como usar a paleta de cores de Design de Material para editar propriedades de cor e como usar o seletor de escala tipográficas para configurar propriedades de texto. Ele também demonstrou como usar o Editor de tema para criar novos temas personalizados que estão em conformidade com as diretrizes de Design de Material. Para obter mais informações sobre o suporte de xamarin. Android para o Design de Material, consulte [tema de Material](~/android/user-interface/material-theme.md).

-----


## <a name="related-links"></a>Links relacionados

- [Tema de material](~/android/user-interface/material-theme.md)
- [Introdução de Design de material](https://material.io/design/introduction)
