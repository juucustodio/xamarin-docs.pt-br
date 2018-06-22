---
title: Recursos de material de Design
description: Este tópico descreve os recursos do Designer que tornam mais fácil para os desenvolvedores criarem layouts de Material compatível com Design. Esta seção apresenta e explica como usar a grade de Material, a paleta de cores de Material, a escala tipográficas e o Editor de tema.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: a764efe7f2cadd8c777f8427c0220e45eec662c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773625"
---
# <a name="material-design-features"></a>Recursos de material de Design

_Este tópico descreve os recursos do Designer que tornam mais fácil para os desenvolvedores criarem layouts de Material compatível com Design. Esta seção apresenta e explica como usar a grade de Material, a paleta de cores de Material, a escala tipográficas e o Editor de tema._


> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evoluir 2016: Todos podem criar aplicativos lindos com Material Design**

## <a name="overview"></a>Visão geral

O Designer de xamarin inclui recursos que facilitam a criação de layouts de Material Design compatíveis. Se você não estiver familiarizado com Material de Design, consulte o [Material de Design Introdução](https://www.google.com/design/spec/material-design/introduction.html).

Neste guia, teremos que examinar os seguintes recursos de Designer:

-   *Grade de material* &ndash; uma sobreposição na superfície de Design que mostra uma grade, espaçamento e linhas para ajudá-lo a colocar os widgets de layout de acordo com as diretrizes de Design do Material.

-   *Paleta de cores material* &ndash; caixa de diálogo de preenchimento de propriedade de um que ajuda você a escolher uma cor da paleta de Design de Material oficial.

-   *Escala tipográfica* &ndash; caixa de diálogo de um bloco de propriedade que fornece uma variedade de configurações compatíveis com o Material Design para o `textAppearance` propriedade dos campos de texto.

-   *Editor de temas* &ndash; um editor de recurso coloridas que permite que você defina as informações de cores para um subconjunto de um tema. Por exemplo, você pode visualizar e modificar as cores de Material, como `colorPrimary`, `colorPrimaryDark`, e `colorAccent`.

Vamos examinar cada um desses recursos de ter e fornecem exemplos de como usá-los.



## <a name="material-design-grid"></a>Grade de material de Design

O menu de grade de Design de Material está disponível na barra de ferramentas na parte superior do Designer:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Grade de Design de material](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Grade de Design de material](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

-----

Quando você clicar no ícone de grade de Design de Material, o Designer exibe uma sobreposição na superfície de Design que inclui os seguintes elementos:

-   Linhas (linhas em laranja)

-   Espaçamento (áreas verdes)

-   Uma grade (linhas azuis)

Esses elementos podem ser vistos na captura de tela a seguir:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Contorno e espaçamento da grade](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Contorno e espaçamento da grade](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Cada um desses itens de sobreposição é configurável. Quando você clica no botão de reticências ao lado do menu de grade de Design de Material, popover uma caixa de diálogo é aberta que permite que você ativar/desativar a grade, configure o posicionamento de linhas e definir os espaçamentos. Observe que todos os valores são expressos em `dp` (independente de densidade de pixels):

![Grade de configurações, contorno e espaçamento](material-design-features-images/vs/03-grid-configuration.png)

Para adicionar um novo contorno, digite um novo valor de deslocamento no **deslocamento** , selecione um local (**esquerdo**, **superior**, **direita**, ou  **inferior**) e clique no ícone para adicionar o novo contorno +.

Da mesma forma, para adicionar um novo espaçamento, digite o tamanho e o deslocamento (em dp) para o **tamanho** e **deslocamento** caixas, respectivamente. Selecione um local (**esquerdo**, **superior**, **direita**, ou **inferior**) e clique no ícone para adicionar o novo espaçamento +.

Quando você alterar esses valores de configuração, eles são salvos no arquivo XML de layout e reutilizados quando você abrir o layout novamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Cada um desses itens de sobreposição é configurável. Quando você clica no triângulo para baixo ao lado do menu de grade de Design de Material, popover uma caixa de diálogo é aberta que permite que você ativar/desativar a grade, configure o posicionamento de linhas e definir os espaçamentos. Observe que todos os valores são expressos em `dp` (independente de densidade de pixels):

[![Grade de configurações, contorno e espaçamento](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Para adicionar um novo contorno, digite um novo valor de deslocamento no **deslocamento** , selecione um local (**esquerdo**, **superior**, **direita**, ou  **inferior**) e clique no ícone para adicionar o novo contorno +.

Da mesma forma, para adicionar um novo espaçamento, digite o tamanho e o deslocamento (em dp) para o **tamanho** e **deslocamento** caixas, respectivamente. Selecione um local (**esquerdo**, **superior**, **direita**, ou **inferior**) e clique no ícone para adicionar o novo espaçamento +.

Quando você alterar esses valores de configuração, eles são salvos no arquivo XML de layout e reutilizados quando você abrir o layout novamente.


## <a name="material-design-color-palette"></a>Paleta de cores de material de Design

Cada item do painel de propriedade que aceita uma cor agora tem um ícone adicional que você pode usar para abrir a paleta de cores do Material de Design, conforme mostrado nesta captura de tela:

[![Ícone de cor](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando você clicar nesse ícone, popover uma caixa de diálogo é aberta que permite que você configurar a cor da propriedade da paleta de cores de Design de Material de:

[![Paleta de cores do material Design](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

A parte superior da paleta de cores exibe cores de Design de Material principais enquanto a parte inferior da Paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matizes é exibida na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *Indigo 500*:

[![Escolha Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` é definido para o código de cor para *Indigo 500* (`#ff3f51b5`), e o Designer atualiza a cor de plano de fundo do botão para refletir essa alteração:

[![Alterações de tonalidade de plano de fundo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Para obter mais informações sobre a paleta de cores de Material de Design, consulte o Material de Design [guia da paleta de cores](http://www.google.com/design/spec/style/color.html#color-color-palette).

## <a name="typographic-scale"></a>Escala tipográfica

O **a aparência do texto** seção o **propriedade** teclado **estilo** guia tem um ícone que permite que você selecione um `TextAppearance` estilo que está de acordo com o Material de Design especificação de:

[![Guia de estilo](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando você clicar nesse ícone, ele abre o **escala tipográficas** popover caixa de diálogo, que apresenta uma lista de estilos de texto pré-configurado que você pode escolher:

[![Seletor de estilo de texto](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

No exemplo a seguir, clicando em **exibir 1** altera o texto do botão para uma fonte maior de **exibir 1**:

[![Estilo de exibição 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

O estilo de texto no **escala tipográficas** caixa de diálogo segue o **tema** configuração. Por exemplo, se o **Light** tema é escolhido no Designer, a lista dos espelhos de estilos de texto disponíveis a **Light** tema:

[![Tema claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

-----



## <a name="theme-editor"></a>Editor de temas

O **Editor de temas** permite que você personalize as informações de cores para um subconjunto de atributos de tema. Para abrir o **Editor de temas**, clique no ícone de pincel na barra de ferramentas:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Ícone do Editor de temas](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ícone do Editor de temas](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

-----

Embora o **Editor de temas** está acessível na barra de ferramentas de destino todas as versões do Android e níveis de API, somente um subconjunto dos recursos descritos abaixo estão disponíveis se o nível de API de destino for anterior a 21 de API (Android 5.0 Pirulito).

O painel esquerdo do **Editor de temas** exibe a lista de cores que compõem o tema selecionado no momento (neste exemplo, estamos usando o `Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Editor de temas](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Editor de temas](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

-----

Quando você seleciona uma cor à esquerda, o painel à direita fornece as seguintes guias para ajudá-lo a editar essa cor:

-   **Herdar** &ndash; exibe um diagrama de herança de estilo para a cor selecionada e lista o cor resolvido e o código de cor atribuída a essa cor de tema.

-   **Seletor de cores** &ndash; permite que você altere o selecionado para cor como qualquer valor arbitrário.

-   **Paleta material** &ndash; permite a você alterar o selecionado para cor para um valor que está em conformidade com Material de Design.

-   **Recursos** &ndash; recursos no tema de cores permite que você alterar a cor selecionada para um dos outros existente.

Vamos examinar cada uma dessas guias em detalhes.



### <a name="inherit-tab"></a>Herdar de guia

Como mostrado no exemplo a seguir, o **herdar** guia lista a herança de estilo para o **em segundo plano** cor do **tema padrão**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Herdar de guia](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Herdar de guia](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

-----

Neste exemplo, o **tema padrão** herda de um estilo que usa `@color/background_material_dark` , mas substitui-lo com `color/material_grey_850`, que tem um valor de código de cor de `#ff303030`.
Para obter mais informações sobre herança de estilo, consulte [estilos e temas](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).



### <a name="color-picker"></a>Seletor de Cor

Captura de tela a seguir ilustra o **seletor de cores**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Seletor de cores](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Seletor de cores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

-----

Neste exemplo, o **em segundo plano** cor pode ser alterada para qualquer valor através de várias maneiras:

-   Clicar diretamente em uma cor.
-   Inserindo valores de matiz, saturação e brilho.
-   Inserindo valores RGB (vermelhos, verdes e azuis) em decimal.
-   Definindo a alfa (opacidade) para a cor selecionada.
-   Inserir o código hexadecimal de cor diretamente.

A cor escolhida no seletor de cores é *não* restrito às diretrizes de Design de Material ou para o conjunto de recursos de cores disponíveis.


### <a name="resources"></a>Recursos

O **recursos** guia oferece uma lista de recursos de cor que já estão presentes no tema:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Recursos](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

-----

Usando o **recursos** guia restringe as opções para essa lista de cores. Tenha em mente que se você escolher um recurso de cor que já está atribuído a outra parte do tema, dois elementos adjacentes de interface do usuário podem "executados juntos" (porque eles têm a mesma cor) e se tornar difícil para o usuário para fazer a distinção.



### <a name="material-palette"></a>Paleta de material

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O **Material paleta** guia abre o **paleta de cores do Material de Design**. Escolha um valor de cor essa paleta restringe sua opção de cor para que seja consistente com as diretrizes de Design do Material.

[![Paleta de material](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png#lightbox)

A parte superior da paleta de cores exibe cores de Design de Material principais enquanto a parte inferior da Paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matizes é exibida na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *Indigo 500*:

![Selecione Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` é definido para o código de cor para *Indigo 500* (`#ff3f51b5`), e o Designer atualiza a cor de plano de fundo para refletir essa alteração:

[![Tonalidade de plano de fundo alterada](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png#lightbox)

Para obter mais informações sobre a paleta de cores de Material de Design, consulte o Material de Design [guia da paleta de cores](http://www.google.com/design/spec/style/color.html#color-color-palette).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O **Material paleta** guia abre o **paleta de cores do Material de Design** descrito [anterior](#material_palette). Escolha um valor de cor essa paleta restringe sua opção de cor para que seja consistente com as diretrizes de Design do Material.

[![Paleta de material](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

-----



### <a name="creating-a-new-theme"></a>Criar um novo tema

O exemplo a seguir, usaremos a paleta de Material para criar um novo tema personalizado. Primeiro, vamos alterar o **em segundo plano** cor *azul 900*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Alterar o plano de fundo para 900 azul](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Alterar o plano de fundo para 900 azul](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

-----


Quando um recurso de cor é alterado, aparece uma mensagem com a mensagem, *o tema atual tem alterações não salvas*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Aviso de alterações não salvas](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Aviso de alterações não salvas](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

-----


O **em segundo plano** cor no Designer foi alterado para a nova seleção de cor, mas essa alteração ainda não foram salvas. São oferecidas duas opções para como lidar com a alteração:

-   **Descartar alterações** &ndash; descarta a nova opção de cor (ou opções) e reverte o tema ao seu estado original.

-   **Criar novo tema** &ndash; cria um novo tema que é derivado do tema selecionado no momento e usa as substituições de cor feitas a **Editor de temas**.

Quando você clica em **criar novo tema**, o seguinte ocorrerá, dependendo do tema selecionado:

-   Se o tema selecionado atualmente no Designer não é um tema de projeto, são apresentados com a opção de criar um novo arquivo de recurso com o tema personalizado (usando o tema selecionado como o pai do tema personalizado). O Designer alterna para o tema personalizado após sua criação.

-   Se o tema selecionado for um tema de projeto que é definido em um local, você verá o **tema atualização** opção; clicar nessa opção atualiza o tema selecionado no momento, em vez de criar um novo.

-   Se o tema selecionado é um tema de projeto que está definido em vários locais (por exemplo, de forma diferente-sufixo **valores** pastas como **v21 valores**), você verá uma caixa de diálogo que pergunta para um novo local para salvar o tema personalizado.

Continuando o exemplo anterior, clicando em **criar novo tema** resulta na criação de um novo tema chamado **personalizado**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Tema personalizado adicionado](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Tema personalizado adicionado](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png#lightbox)

-----


Como o tema selecionado no momento não é um tema de projeto, não há nenhuma caixa de diálogo para atualizar o tema selecionado ou para especificar um novo local.


## <a name="summary"></a>Resumo

Este tópico descreveu os recursos de Design de Material disponíveis no Designer de xamarin. Ele explicado como habilitar e configurar a grade de Design de Material, como usar a paleta de cores do Material de Design para editar propriedades de cor e como usar o seletor de escala tipográficas para configurar as propriedades de texto. Ele também demonstrou como usar o Editor de tema para criar novos temas personalizados que estão em conformidade com as diretrizes de Design do Material. Para obter mais informações sobre o suporte de xamarin para Material de Design, consulte [Material tema](~/android/user-interface/material-theme.md).



## <a name="related-links"></a>Links relacionados

- [Tema de material](~/android/user-interface/material-theme.md)
- [Introdução de material de Design](https://www.google.com/design/spec/material-design/introduction.html)
