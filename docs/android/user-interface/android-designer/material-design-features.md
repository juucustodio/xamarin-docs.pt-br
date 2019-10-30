---
title: Recursos de design de material do Xamarin. Designer Android
description: Este tópico descreve os recursos do designer que facilitam para os desenvolvedores a criação de layouts em conformidade com o design do material. Esta seção apresenta e explica como usar a grade de material, a paleta de cores do material, a escala tipográfica e o editor de tema.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 43397fb855bdf872cf17b315044f34a468c22d00
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029446"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Recursos de design de material do Xamarin. Designer Android

_Este tópico descreve os recursos do designer que facilitam para os desenvolvedores a criação de layouts em conformidade com o design do material. Esta seção apresenta e explica como usar a grade de material, a paleta de cores do material, a escala tipográfica e o editor de tema._

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Desenvolver 2016: todos podem criar aplicativos bonitos com design de material**

## <a name="overview"></a>Visão Geral

O Xamarin. Designer Android inclui recursos que facilitam a criação de layouts em conformidade com o design de material. Se você não estiver familiarizado com o design de material, consulte a [introdução ao design de material](https://material.io/design/introduction).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Neste guia, veremos os seguintes recursos do designer:

- A *grade de Material* &ndash; uma sobreposição na design Surface que mostra uma grade, um espaçamento e uma linha de KEYLINE para ajudá-lo a posicionar widgets de layout de acordo com as diretrizes de design de material.

- O *Editor de tema* &ndash; um editor de recursos de cor pequena que permite definir informações de cor para um subconjunto de um tema. Por exemplo, você pode visualizar e modificar as cores de material, como `colorPrimary`, `colorPrimaryDark`e `colorAccent`.

Vamos examinar cada um desses recursos e fornecer exemplos de como usá-los.

## <a name="material-design-grid"></a>Grade de design de material

O menu da grade de design do material está disponível na barra de ferramentas na parte superior do designer:

[grade de design de![material](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

Quando você clica no ícone de grade de design do material, o designer exibe uma sobreposição no Design Surface que inclui os seguintes elementos:

- Linhas de KEYLINE (linha laranja)

- Espaçamento (áreas verdes)

- Uma grade (linhas azuis)

Esses elementos podem ser vistos na captura de tela anterior. Cada um desses itens de sobreposição é configurável. Quando você clica nas reticências ao lado do menu de grade de design do material, uma caixa de diálogo popover é aberta e permite que você Desabilite/habilite a grade, configure o posicionamento de keylines e defina espaçamentos. Observe que todos os valores são expressos em `dp` (pixels independentes de densidade):

[![grade, KEYLINE e configuração de espaçamento](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

Para adicionar um novo KEYLINE, insira um novo valor de deslocamento na caixa **deslocamento** , selecione um local (**esquerda**, **superior**, **direita**ou **inferior**) e clique no ícone + para adicionar o novo KEYLINE. Da mesma forma, para adicionar um novo espaçamento, insira o tamanho e o deslocamento (em DP) nas caixas **tamanho** e **deslocamento** , respectivamente. Selecione um local (**esquerda**, **superior**, **direita**ou **inferior**) e clique no ícone + para adicionar o novo espaçamento.

Quando você altera esses valores de configuração, eles são salvos no arquivo XML de layout e reutilizados quando você abre o layout novamente.

## <a name="theme-editor"></a>Editor de tema

O **Editor de tema** permite que você personalize informações de cores para um subconjunto de atributos de tema. Para abrir o **Editor de tema**, clique no ícone de pincel na barra de ferramentas:

[ícone do editor de tema![](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

Embora o **Editor de tema** possa ser acessado na barra de ferramentas para todas as versões de destino e níveis de API do Android, somente um subconjunto dos recursos descritos abaixo estará disponível se o nível da API de destino for anterior à API 21 (Android 5,0 pirulito).

O painel esquerdo do **Editor de tema** exibe a lista de cores que compõem o tema atualmente selecionado (neste exemplo, estamos usando o `Default Theme`):

[Editor de tema![](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

Quando você seleciona uma cor à esquerda, o painel à direita fornece as seguintes guias para ajudá-lo a editar essa cor:

- **Herdar** &ndash; exibe um diagrama de herança de estilo para a cor selecionada e lista o código de cor e cor resolvido atribuído a essa cor de tema.

- O **seletor de cor** &ndash; permite que você altere a cor selecionada para qualquer valor arbitrário.

- &ndash; **paleta de materiais** permite alterar a cor selecionada para um valor que esteja de acordo com o design de material.

- **Recursos** &ndash; permite que você altere a cor selecionada para um dos outros recursos de cor existentes no tema.

Vamos examinar cada uma dessas guias detalhadamente.

### <a name="inherit-tab"></a>Guia herdar

Como visto no exemplo a seguir, a guia **Inherit** lista a herança de estilo para a cor do **plano de fundo** do **tema padrão**:

[![a guia herdar](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

Neste exemplo, o **tema padrão** herda de um estilo que usa `@color/background_material_light` mas o substitui por `color/material_grey_50`, que tem um valor de código de cor de `#fffafafa`.
Para obter mais informações sobre herança de estilo, consulte [estilos e temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Seletor de Cor

A captura de tela a seguir ilustra o **seletor de cores**:

[![seletor de cores](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

Neste exemplo, a cor do **plano de fundo** pode ser alterada para qualquer valor por meio de vários meios:

- Clicando em uma cor diretamente.
- Inserindo valores de matiz, saturação e brilho.
- Inserindo valores RGB (vermelho, verde e azul) em decimal.
- Definindo alfa (opacidade) para a cor selecionada.
- Inserindo o código de cor hexadecimal diretamente.

A cor escolhida no seletor de cores *não* é restrita às diretrizes de design de material ou ao conjunto de recursos de cores disponíveis.

### <a name="resources"></a>Recursos

A guia **recursos** oferece uma lista de recursos de cores que já estão presentes no tema:

[![recursos](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

O uso da guia **recursos** restringe suas escolhas a essa lista de cores. Tenha em mente que, se você escolher um recurso de cor que já esteja atribuído a outra parte do tema, dois elementos adjacentes da interface do usuário poderão ser "executados juntos" (porque têm a mesma cor) e tornam-se difícil para o usuário distinguir.

### <a name="material-palette"></a>Paleta de materiais

A guia **paleta de materiais** abre a paleta de cores de design do **material**. Escolher um valor de cor dessa paleta restringe sua escolha de cor para que ela seja consistente com as diretrizes de design de material:

[Paleta de materiais![](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

A parte superior da paleta de cores exibe as cores do design do material primário, enquanto a parte inferior da paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matiz é exibida na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *Indigo 500*:

![Selecione Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` é definido como o código de cor para *Indigo 500* (`#ff3f51b5`) e o designer atualiza a cor do plano de fundo para refletir essa alteração:

[tonalidade de plano de fundo![alterada](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Para obter mais informações sobre a paleta de cores de design do material, consulte o [guia da paleta de cores](https://material.io/design/color/)do design do material.

### <a name="creating-a-new-theme"></a>Criando um novo tema

No exemplo a seguir, usaremos a paleta material para criar um novo tema personalizado. Primeiro, alteraremos a cor do **plano de fundo** para *azul 900*:

![Alterar o plano de fundo para azul 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

Quando um recurso de cor é alterado, uma mensagem é exibida com a mensagem; *o tema atual tem alterações não salvas*:

[aviso de![de alterações não salvas](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

A cor do **plano de fundo** no designer foi alterada para a nova seleção de cor, mas essa alteração ainda não foi salva. Neste ponto, você pode executar um dos seguintes procedimentos:

- Clique em **descartar alterações** para descartar a nova opção de cor (ou opções) e reverter o tema para seu estado original.

- Pressione <kbd>Ctrl + S</kbd> para salvar as alterações no tema atual.

No exemplo a seguir, <kbd>Ctrl + S</kbd> foi pressionado para que as alterações tenham sido salvas em **AppTheme**:

[![alterações salvas em AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>Resumo

Este tópico descreveu os recursos de design de material disponíveis no Xamarin. Designer Android. Ele explicou como habilitar e configurar a grade de design de material e explicou como usar o editor de tema para criar novos temas personalizados que estão em conformidade com as diretrizes de design de material.
Para obter mais informações sobre o suporte do Xamarin. Android para o design de material, consulte [material Theme](~/android/user-interface/material-theme.md).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Neste guia, vamos examinar os seguintes recursos do designer:

- *Grade de design de Material* &ndash; uma sobreposição na design Surface que mostra uma grade, um espaçamento e um KEYLINE para ajudá-lo a posicionar widgets de layout de acordo com as diretrizes de design de material.

- *Paleta de cores de design de Material* &ndash; uma caixa de diálogo de painel de propriedades que ajuda você a escolher uma cor na paleta de design de material oficial.

- *Escala tipográfica* &ndash; uma caixa de diálogo do painel de propriedades que fornece uma opção de configurações em conformidade com o design do material para a propriedade `textAppearance` de campos de texto.

- O *Editor de tema* &ndash; um editor de recursos de cor pequena que permite definir informações de cor para um subconjunto de um tema. Por exemplo, você pode visualizar e modificar as cores de material, como `colorPrimary`, `colorPrimaryDark`e `colorAccent`.

Vamos examinar cada um desses recursos e fornecer exemplos de como usá-los.

## <a name="material-design-grid"></a>Grade de design de material

O menu da grade de design do material está disponível na barra de ferramentas na parte superior do designer:

[grade de design de![material](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

Quando você clica no ícone de grade de design do material, o designer exibe uma sobreposição no Design Surface que inclui os seguintes elementos:

- Linhas de KEYLINE (linha laranja)

- Espaçamento (áreas verdes)

- Uma grade (linhas azuis)

Esses elementos podem ser vistos na seguinte captura de tela:

[![KEYLINE, espaçamento e grade](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

Cada um desses itens de sobreposição é configurável. Quando você clica nas reticências (&hellip;) ao lado do menu de grade de design do material, uma caixa de diálogo popover é aberta e permite que você Desabilite/habilite a grade, configure o posicionamento de keylines e defina os espaçamentos. Observe que todos os valores são expressos em `dp` (pixels independentes de densidade):

[![grade, KEYLINE e configuração de espaçamento](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Para adicionar um novo KEYLINE, insira um novo valor de deslocamento na caixa **deslocamento** , selecione um local (**esquerda**, **superior**, **direita**ou **inferior**) e clique no ícone + (que aparece à direita quando um valor é inserido) para adicionar a nova KEYLINE. Da mesma forma, para adicionar um novo espaçamento, insira o tamanho e o deslocamento (em DP) nas caixas **tamanho** e **deslocamento** , respectivamente. Selecione um local (**esquerda**, **superior**, **direita**ou **inferior**) e clique no ícone + para adicionar o novo espaçamento.

Quando você altera esses valores de configuração, eles são salvos no arquivo XML de layout e reutilizados quando você abre o layout novamente.

## <a name="material-design-color-palette"></a>Paleta de cores de design de material

Cada item do painel de propriedades que aceita uma cor agora tem um ícone de paleta adicional que você pode usar para abrir a paleta de cores de design do material, conforme mostrado nesta captura de tela:

[ícone de cor do![](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Quando você clica nesse ícone, uma caixa de diálogo popover é aberta e torna possível configurar a cor dessa propriedade na paleta de cores do design do material:

[paleta de cores de design de![material](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

A parte superior da paleta de cores exibe as cores do design do material primário, enquanto a parte inferior da paleta exibe um intervalo de matizes para a cor primária selecionada. Por exemplo, quando você seleciona **Indigo**, uma coleção de **Indigo** matiz é exibida na parte inferior da caixa de diálogo.
Quando você seleciona um matiz, a cor da propriedade é alterada para o matiz selecionado. No exemplo a seguir, o `Background Tint` do botão é alterado para *Indigo 500*:

[![escolher Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` é definido como o código de cor para *Indigo 500* (`#ff3f51b5`) e o designer atualiza a cor do plano de fundo do botão para refletir essa alteração:

[![alterações de tonalidade de plano de fundo](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Para obter mais informações sobre a paleta de cores de design do material, consulte o [guia da paleta de cores](https://material.io/design/color/)do design do material.

## <a name="typographic-scale"></a>Escala tipográfica

A seção **aparência de texto** da guia **estilo** do painel de **Propriedades** tem um ícone que permite que você selecione um estilo de `TextAppearance` que esteja de acordo com a especificação de design de material:

[![guia estilo](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Quando você clica nesse ícone, ele abre a caixa de diálogo **escala tipográfica** popover, que apresenta uma lista de estilos de texto pré-configurados que você pode escolher:

[seletor de estilo de texto![](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

No exemplo a seguir, clicar em **Exibir 1** altera o texto do botão para a fonte maior da **exibição 1**:

[![exibir 1 estilo](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

O estilo de texto na caixa de diálogo **escala tipográfica** segue a configuração de **tema** . Por exemplo, se o tema **claro** for escolhido no designer, a lista de estilos de texto disponíveis espelha o tema **claro** :

[Tema![claro](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>Editor de tema

O **Editor de tema** permite que você personalize informações de cores para um subconjunto de atributos de tema. Para abrir o **Editor de tema**, clique no ícone de pincel na barra de ferramentas:

[ícone do editor de tema![](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

Embora o **Editor de tema** possa ser acessado na barra de ferramentas para todas as versões de destino e níveis de API do Android, somente um subconjunto dos recursos descritos abaixo estará disponível se o nível da API de destino for anterior à API 21 (Android 5,0 pirulito).

O painel esquerdo do **Editor de tema** exibe a lista de cores que compõem o tema atualmente selecionado (neste exemplo, estamos usando o `Default Theme`):

[Editor de tema![](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

Quando você seleciona uma cor à esquerda, o painel à direita fornece as seguintes guias para ajudá-lo a editar essa cor:

- **Herdar** &ndash; exibe um diagrama de herança de estilo para a cor selecionada e lista o código de cor e cor resolvido atribuído a essa cor de tema.

- O **seletor de cor** &ndash; permite que você altere a cor selecionada para qualquer valor arbitrário.

- &ndash; **paleta de materiais** permite alterar a cor selecionada para um valor que esteja de acordo com o design de material.

- **Recursos** &ndash; permite que você altere a cor selecionada para um dos outros recursos de cor existentes no tema.

Vamos examinar cada uma dessas guias detalhadamente.

### <a name="inherit-tab"></a>Guia herdar

Como visto no exemplo a seguir, a guia **Inherit** lista a herança de estilo para a cor do **plano de fundo** do **tema padrão**:

[![a guia herdar](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

Neste exemplo, o **tema padrão** herda de um estilo que usa `@color/background_material_dark` mas o substitui por `color/material_grey_850`, que tem um valor de código de cor de `#ff303030`.
Para obter mais informações sobre herança de estilo, consulte [estilos e temas](https://developer.android.com/guide/topics/ui/themes.html#Inheritance).

### <a name="color-picker"></a>Seletor de Cor

A captura de tela a seguir ilustra o **seletor de cores**:

[![seletor de cores](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

Neste exemplo, a cor do **plano de fundo** pode ser alterada para qualquer valor por meio de vários meios:

- Clicando em uma cor diretamente.
- Inserindo valores de matiz, saturação e brilho.
- Inserindo valores RGB (vermelho, verde e azul) em decimal.
- Definindo alfa (opacidade) para a cor selecionada.
- Inserindo o código de cor hexadecimal diretamente.

A cor escolhida no seletor de cores *não* é restrita às diretrizes de design de material ou ao conjunto de recursos de cores disponíveis.

### <a name="resources"></a>Recursos

A guia **recursos** oferece uma lista de recursos de cores que já estão presentes no tema:

[![recursos](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

O uso da guia **recursos** restringe suas escolhas a essa lista de cores. Tenha em mente que, se você escolher um recurso de cor que já esteja atribuído a outra parte do tema, dois elementos adjacentes da interface do usuário poderão ser "executados juntos" (porque têm a mesma cor) e tornam-se difícil para o usuário distinguir.

### <a name="material-palette"></a>Paleta de materiais

A guia **paleta de materiais** abre a paleta de cores de design de **material** descrita [anteriormente](#material-design-color-palette). Escolher um valor de cor dessa paleta restringe sua escolha de cor para que ela seja consistente com as diretrizes de design de material.

[Paleta de materiais![](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>Criando um novo tema

No exemplo a seguir, usaremos a paleta material para criar um novo tema personalizado. Primeiro, alteraremos a cor do **plano de fundo** para *azul 900*:

[![alterar o plano de fundo para azul 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

Quando um recurso de cor é alterado, uma mensagem é exibida com a mensagem; *o tema atual tem alterações não salvas*:

[aviso de![de alterações não salvas](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

A alteração de cor no designer foi feita, mas essa alteração ainda não foi salva. Neste ponto, você pode executar um dos seguintes procedimentos:

- Clique em **descartar alterações** para descartar a nova opção de cor (ou opções) e reverter o tema para seu estado original.

- Pressione  **&#8984; + S** para salvar as alterações em um novo tema chamado **personalizado**.

## <a name="summary"></a>Resumo

Este tópico descreveu os recursos de design de material disponíveis no Xamarin. Designer Android. Ele explicou como habilitar e configurar a grade de design de material, como usar a paleta de cores de design de material para editar propriedades de cores e como usar o seletor de escala tipográfica para configurar propriedades de texto. Ele também demonstrou como usar o editor de tema para criar novos temas personalizados que estão em conformidade com as diretrizes de design de material. Para obter mais informações sobre o suporte do Xamarin. Android para o design de material, consulte [material Theme](~/android/user-interface/material-theme.md).

-----

## <a name="related-links"></a>Links relacionados

- [Tema de material](~/android/user-interface/material-theme.md)
- [Introdução ao design de material](https://material.io/design/introduction)
