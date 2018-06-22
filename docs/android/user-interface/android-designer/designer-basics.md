---
title: Noções básicas de designers
description: Este tópico apresenta os recursos do Designer, explica como iniciar o Designer, descreve a superfície de Design e fornece detalhes sobre como usar o painel de propriedades para editar as propriedades do widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a8201301fc0437ecb79a81f40e865f14dc6af020
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798805"
---
# <a name="designer-basics"></a>Noções básicas de designers

_Este tópico apresenta os recursos do Designer, explica como iniciar o Designer, descreve a superfície de Design e fornece detalhes sobre como usar o painel de propriedades para editar as propriedades do widget._


## <a name="launching-the-designer"></a>Iniciar o Designer

O Designer é aberto automaticamente quando um layout é criado, ou pode ser iniciado clicando duas vezes em um arquivo .axml existente. Por exemplo, clicando duas vezes em **Main.axml** no **recursos > Layout** pasta carregará o Designer, conforme mostrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Tela de Designer no Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Tela de Designer no Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Da mesma forma, você pode adicionar um novo layout clicando com o **layout** pasta o **Solution Explorer** e selecionando **Adicionar > Novo Item... > Layout Android**:

[![Adicionar Novo Item de caixa de diálogo](designer-basics-images/vs/02-add-new-layout-sml.w157.png)](designer-basics-images/vs/02-add-new-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Da mesma forma, você pode adicionar um novo layout clicando com o **layout** pasta o **preenchimento de solução** e selecionando **Adicionar > novo arquivo > Android > Layout**:

[![Adicionar caixa de diálogo do novo arquivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

-----

Isso cria um novo arquivo .axml e carrega-os para a superfície de Design.



## <a name="designer-features"></a>Recursos do Designer

O Designer é composto por várias seções que oferecem suporte a seus vários recursos, como mostrado na captura de tela a seguir:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama dos painéis do Designer](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama dos painéis do Designer](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

-----

Quando você edita um layout no Designer, você usa os recursos a seguir para criar e formatar seu design:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   **Superfície de design** &ndash; facilita a criação visual de interface do usuário, fornecendo uma representação editável de como o layout será exibido no dispositivo.

-   **Barra de ferramentas** &ndash; exibe uma lista de seletores: **dispositivo**, **versão**, **tema**, configuração de layout e as configurações da barra de ação. A barra de ferramentas também inclui ícones para iniciar o Editor de tema e para habilitar a grade de Design do Material.

-   **Caixa de ferramentas** &ndash; fornece uma lista de widgets e layouts que você pode arrastar e soltar para a superfície de Design.

-   **Janela propriedades** &ndash; lista as propriedades do widget selecionado para exibição e edição.

-   **Estrutura de tópicos de documento** &ndash; exibe a árvore de widgets que compõem o layout. Você pode clicar em um item da árvore para fazer com que ele seja selecionado no Designer. Além disso, clicando em um item na árvore de carrega as propriedades do item na janela Propriedades.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   **Superfície de design** &ndash; facilita a criação visual de interface do usuário, fornecendo uma representação editável de como o layout será exibido no dispositivo.

-   **Barra de ferramentas** &ndash; exibe uma lista de seletores: **dispositivo**, **versão**, **tema**, configuração de layout e as configurações da barra de ação. A barra de ferramentas também inclui ícones para iniciar o Editor de tema e para habilitar a grade de Design do Material.

-   **Caixa de ferramentas** &ndash; fornece uma lista de widgets e layouts que você pode arrastar e soltar para a superfície de Design.

-   **Propriedade preenchimento** &ndash; lista as propriedades do widget selecionado para exibição e edição.

-   **Estrutura de tópicos de documento** &ndash; exibe a árvore de widgets que compõem o layout. Você pode clicar em um item da árvore para fazer com que ele seja selecionado no Designer. Além disso, clicando em um item na árvore de carrega as propriedades do item no painel de propriedade.

-----



## <a name="toolbar"></a>Barra de ferramentas

A barra de ferramentas (posicionada acima da superfície de Design) apresenta seletores de configuração e menus de ferramenta:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama de barra de ferramentas do Designer](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama de barra de ferramentas do Designer](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

-----


A barra de ferramentas fornece acesso aos seguintes recursos:

-   **Seletor de Layout alternativo** &ndash; permite que você selecione de versões diferentes de layout.

-   **Seletor de dispositivo** &ndash; define um conjunto de qualificadores associados a um determinado dispositivo, como tamanho da tela, resolução e disponibilidade de teclado. Você também pode adicionar e excluir novos dispositivos.

-   **Seletor de versão do Android** &ndash; versão do Android a que se destina o layout. O Designer processará o layout de acordo com a versão selecionada do Android.

-   **Seletor de tema** &ndash; seleciona o tema de interface do usuário para o layout.

-   **Seletor de configuração** &ndash; seleciona a configuração do dispositivo, como *retrato* ou *paisagem*.

-   **Opções de qualificador de recurso** &ndash; abre uma caixa de diálogo que apresenta os menus suspensos para selecionar *idioma*, *modo de interface do usuário*, *modo noite*, e *Tela round* opções.

-   **As configurações de barra de ação** &ndash; define as configurações de barra de ação para o layout.

-   **Editor de temas** &ndash; abre o *Editor de temas*, que possibilita que você personalize os elementos do tema selecionado.

-   **Grade de Design material** &ndash; habilita ou desabilita o *grade de Design de Material*. O item de menu suspenso adjacente à grade de Design de Material abre uma caixa de diálogo que permite que você personalize a grade.

Cada um desses recursos é explicada mais detalhadamente nestes tópicos:

[Qualificadores de recursos e opções de visualização](~/android/user-interface/android-designer/resource-qualifiers.md) fornece informações detalhadas sobre o **dispositivo seletor**, **Android seletor de versão**, **seletor de tema**, **Configuração seletor**, **opções do recurso de qualificações**, e **configurações de barra de ação**.

[Modos de exibição de Layout alternativo](~/android/user-interface/android-designer/alternative-layout-views.md) explica como usar o **alternativa Layout seletor**.

[Recursos de Design de material](~/android/user-interface/android-designer/material-design-features.md) fornece uma visão abrangente do **tema do Editor** e o **grade de Design de Material**.



## <a name="design-surface"></a>A superfície de design

O Designer possibilita arrastar e soltar widgets da caixa de ferramentas para a superfície de Design. Quando você interage com os widgets de Designer (por adicionando novos widgets ou reposicionar os existentes), linhas horizontais e verticais são exibidas para marcar os pontos de inserção disponíveis. No exemplo a seguir, um novo `Button` widget é arrastado para a superfície de Design:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Linhas de inserção de exemplo na superfície de Design](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Linhas de inserção de exemplo na superfície de Design](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

-----

Além disso, os widgets podem ser copiados: você pode usar o copiar e colar para copiar um widget ou você pode arrastar e soltar um widget existente ao pressionar o <kbd>Ctrl</kbd> chave.


### <a name="context-menu-commands"></a>Comandos de Menu de contexto

Um menu de contexto está disponível na superfície de Design e na estrutura de tópicos do documento. Esse menu mostra os comandos que estão disponíveis para o widget selecionado e o contêiner, tornando mais fácil para você realizar operações em contêineres (que não são sempre fácil selecionar na superfície de Design). Aqui está um exemplo de um menu de contexto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Menu de contexto de exemplo quando o botão direito do mouse na superfície de Design](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

Neste exemplo, clicando duas vezes um `TextView` abre um menu de contexto que fornece várias opções:

-   **LinearLayout** &ndash; abre um submenu para edição de `LinearLayout` pai do `TextView`.

-   **Excluir**, **cópia**, e **Recortar** &ndash; operações que se aplicam às com `TextView`.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Menu de contexto de exemplo quando o botão direito do mouse na superfície de Design](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

Neste exemplo, clicando duas vezes um `TextView` abre um menu de contexto que fornece várias opções:

-   **RelativeLayout** &ndash; abre um submenu para edição de `RelativeLayout` pai do `TextView`.

-   **LinearLayout** &ndash; abre um submenu para edição de `LinearLayout` pai do `TextView`.

-   **Excluir**, **cópia**, e **Recortar** &ndash; operações que se aplicam às com `TextView`.

-----

Neste exemplo, clicando duas vezes um `TextView` abre um menu de contexto que fornece várias opções:

-   **LinearLayout** &ndash; abre um submenu para edição de `LinearLayout` pai do `TextView`.

-   **Excluir**, **cópia**, e **Recortar** &ndash; operações que se aplicam às com `TextView`.



### <a name="zoom-controls"></a>Controles de zoom

A superfície de Design oferece suporte a aumentar o zoom por meio de vários controles, conforme mostrado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Diagrama dos controles de zoom de superfície de Design](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Diagrama dos controles de zoom de superfície de Design](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

-----

Esses controles de facilitam a visualização de determinadas áreas da interface do usuário no Designer:

-   **Realçar contêineres** &ndash; realça contêineres na superfície de Design para que eles são mais fáceis de localizar ao zoom.

-   **Tamanho normal** &ndash; renderiza o layout para a pixels para que você possa ver como o layout examinará a resolução do dispositivo selecionado.

-   **Ajustar à janela** &ndash; define o nível de zoom para que todo o layout está visível na superfície de Design.

-   **Ampliar** &ndash; amplia incrementalmente com cada clique, ampliando o layout.

-   **Zoom** &ndash; zoom incrementalmente com cada clique, fazendo com que o layout pareça menor na superfície de Design.

Observe que a configuração de zoom escolhido não afeta a interface do usuário do aplicativo em tempo de execução.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="property-pad"></a>Preenchimento de propriedade

O Designer oferece suporte à edição de propriedades de widget por meio de **propriedade preenchimento**. As propriedades listadas na alteração do preenchimento de propriedade dependendo de qual widget está selecionado na superfície do Designer. Quando o `Button` no exemplo anterior é selecionada, as propriedades que `Button` widget são mostradas:

[![Captura de tela da área de propriedade](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="properties-window"></a>Janela Propriedades

O Designer oferece suporte à edição de propriedades de widget por meio de **janela propriedades**. As propriedades listadas na alteração da janela de propriedades dependendo de qual widget está selecionado na superfície de Design.
Quando o `Button` no exemplo anterior é selecionada, as propriedades que `Button` widget são mostradas:

![Captura de tela da janela Propriedades](designer-basics-images/vs/08-property-pad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="property-pad-sections"></a>Seções do painel de propriedade

O painel de propriedade é dividido em várias seções que agrupam propriedades semelhantes &ndash; isso torna mais fácil localizar as propriedades de interesse:

-   **Widget** &ndash; propriedades principais do widget, como `id`, `visibility`, `text`, etc. Propriedades de gerenciamento de conteúdo do widget geralmente são colocadas aqui.

-   **Estilo** &ndash; propriedades que alteram a aparência visual do widget, como `font`, `text color`, `background`, etc.

-   **Layout** &ndash; propriedades que defina o local e o tamanho do widget.

-   **Rolagem** &ndash; propriedades de rolagem.

-   **Comportamento** &ndash; sinalizadores que definir o comportamento do widget.

-----



### <a name="default-values"></a>Valores padrão

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

As propriedades da maioria dos widgets estará em branco no **propriedades** janela como seus valores são herdados do Android tema selecionado.
O **propriedades** janela mostrará apenas os valores que são definidos explicitamente para o widget selecionado; ele não mostrará os valores que são herdados do tema.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

As propriedades da maioria dos widgets estará em branco no **propriedade preenchimento** como seus valores são herdados do Android tema selecionado. O **propriedade preenchimento** mostrará apenas os valores que são definidos explicitamente para o widget selecionado; ele não mostrará os valores que são herdados do tema.

-----


### <a name="referencing-resources"></a>Referência a recursos

Algumas propriedades podem fazer referência a recursos que são definidos em arquivos que não seja o layout **.axml** arquivo. Os casos mais comuns desse tipo são `string` e `drawable` recursos. No entanto, as referências também podem ser usadas para outros recursos, tais como `Boolean` valores e dimensões.
Quando uma propriedade dá suporte a referências de recurso, um ícone de navegação (uma elipse, &hellip;) é mostrado ao lado da entrada de texto para a propriedade.
Esse botão abre um seletor de recursos quando clicado.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por exemplo, a captura de tela a seguir mostra os recursos disponíveis ao clicar no botão de reticências à direita do campo de texto para um `Button` widget no **propriedades** janela:

[![Captura de tela de recursos de exemplo com dois recursos listados](designer-basics-images/vs/09-resources-sml.png)](designer-basics-images/vs/09-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por exemplo, a captura de tela a seguir mostra os recursos disponíveis ao clicar no botão de reticências à direita do campo de texto para um `Button` widget no **teclado de propriedade**:

[![Captura de tela de recursos de exemplo com dois recursos listados](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

-----

O exemplo a seguir ilustra o seletor de recurso para o `Src` propriedade de um `ImageView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Listagem de recurso de ícone para um ImageView de seletor de recursos](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Listagem de recurso de ícone para um ImageView de seletor de recursos](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

-----



### <a name="boolean-property-references"></a>Referências de propriedade booleana

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

*Boolean* propriedades são normalmente escolhidas em um menu suspenso na janela Propriedades. Você pode selecionar um `true` ou `false` valor, ou você pode selecionar uma referência de propriedade clicando **selecionar recurso...** . Você também pode inserir diretamente um valor como `true` ou `false`.

![Exemplo de definição de propriedades Boolianas](designer-basics-images/vs/11-boolean.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

*Boolean* propriedades normalmente são mostradas como uma caixa de seleção no painel de propriedade. Quando um `Boolean` propriedade dá suporte a referências de recurso, uma pequena caixa de seleção aparece ao lado da propriedade. Uma caixa de seleção marcada significa `true` e uma caixa vazia significa `false`. Você também pode inserir diretamente um valor como `true` ou `false`. Posicionar o mouse sobre a entrada exibe um ícone de campo de texto pequenos. Se você deseja inserir o valor manualmente, você pode clicar nele.

[![Exemplo de definição de propriedades Boolianas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>Propriedades agrupadas

Alguns widgets têm propriedades de vários valores que são agrupadas juntos (como `Padding`, por exemplo). Esses valores de propriedade são listadas no **propriedade preenchimento** em uma linha única, expansível. Algumas dessas propriedades podem ser editadas diretamente na linha agrupada, como o `Padding` propriedade mostrada abaixo:

[![Configurações de exemplo para a propriedade de preenchimento](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

-----


## <a name="editing-properties-inline"></a>Editando propriedades embutidas

O Designer Android oferece suporte a edição direta de determinadas propriedades na superfície de Design (de modo que não é necessário para essas propriedades na lista de propriedades de pesquisa). Propriedades que podem ser editadas diretamente incluem texto, margem e tamanho.

### <a name="text"></a>Texto

As propriedades de texto de alguns widgets (como `Button` e `TextView`), podem ser editados diretamente na superfície de Design. Clicando duas vezes em um widget irá colocá-lo em modo de edição, conforme mostrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recurso de texto para a cadeia de caracteres hello](designer-basics-images/vs/12-text-resource.png "recursos de texto")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Recurso de texto para a cadeia de caracteres de saudação](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

-----

Você pode inserir um novo valor de texto, ou você pode inserir uma nova cadeia de caracteres de recurso. No exemplo a seguir, o `@string/hello` recurso está sendo substituído com o texto, `CLICK THIS BUTTON`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Shift + Enter para vincular automaticamente o texto para um novo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Shift + Enter para vincular automaticamente o texto para um novo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

-----

Essa alteração é armazenada no widget de `text` propriedade; ele não modifica o valor atribuído para o `@string/hello` recursos.
Quando a chave em uma nova cadeia de caracteres de texto, você pode pressionar <kbd>Shift</kbd> +
<kbd>Enter</kbd> para vincular automaticamente o texto digitado para um novo recurso.


### <a name="margin"></a>Margem

Quando você seleciona um widget, o Designer exibe identificadores que permitem que você altere o tamanho ou a margem do widget interativamente. Clique o widget enquanto ele está selecionado para alternar entre o modo de edição de margem e o modo de edição de tamanho.

Quando você clica em um widget pela primeira vez, são exibidas as alças de margem. Se você mover o mouse para uma das alças, o Designer exibe a propriedade que irá alterar o identificador (conforme mostrado abaixo para o `layout_marginLeft` propriedade):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de tela mostrando a margem manipula no Designer](designer-basics-images/vs/15-margin-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Captura de tela mostrando a margem manipula no Designer](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

-----

Se já tiver sido definida uma margem, linhas pontilhadas são exibidas, indicando que o espaço ocupado a margem:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Exemplo de linhas pontilhadas marcação de espaço em torno de um botão](designer-basics-images/vs/16-margins-set.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Exemplo de linhas pontilhadas marcação de espaço em torno de um botão](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

-----



### <a name="size"></a>Tamanho

Como mencionado anteriormente, você pode alternar para modo de edição de tamanho, clicando em um widget enquanto ainda estiver selecionado. Clique na alça triangular para definir o tamanho para a dimensão indicada para `wrap_content`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Alças de redimensionamento wrap conteúdo](designer-basics-images/vs/17-wrap-content.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Alças de redimensionamento wrap conteúdo](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

-----

Clicando o **encapsular conteúdo** identificador reduz o widget dessa dimensão de forma que ele não é maior que o necessário para incluir o conteúdo incluído. Neste exemplo, o texto do botão reduz horizontalmente, conforme mostrado na seguinte captura de tela.

Quando o valor de tamanho é definido como **encapsular conteúdo**, o Designer exibe um identificador triangular apontando na direção oposta para alterar o tamanho da `match_parent`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Identificador do pai de correspondência](designer-basics-images/vs/18-match-parent.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Identificador do pai de correspondência](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

-----

Clicando o **correspondência pai** identificador restaura o tamanho dessa dimensão para que seja o mesmo que o widget de pai.

Além disso, você pode arrastar a alça de redimensionamento circular (conforme mostrado nas capturas de tela acima) para redimensionar o widget para uma arbitrário `dp` valor. Quando você faz isso, ambos **encapsular conteúdo** e **correspondência pai** identificadores são apresentados para a dimensão:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Alças de redimensionamento circular](designer-basics-images/vs/19-resize-dp.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Alças de redimensionamento circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

-----

Nem todos os contêineres permitem a edição de `Size` de um widget. Por exemplo, observe que a captura de tela abaixo com o `LinearLayout` selecionada, as alças de redimensionamento são exibidas:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Não há alças de redimensionamento](designer-basics-images/vs/20-no-resize-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Não há alças de redimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

-----



## <a name="document-outline"></a>Estrutura de Tópicos do Documento

O **esboço de documento** exibe a hierarquia de widget do layout.
No exemplo a seguir, o que contém `LinearLayout` widget é selecionado:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Estrutura de tópicos do documento](designer-basics-images/vs/21-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Estrutura de tópicos do documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

-----

A estrutura de tópicos do widget selecionado (nesse caso, um `LinearLayout`) também está realçado na superfície de Design. O widget selecionado na estrutura de tópicos do documento permanece em sincronia com seu correspondente na superfície de Design. Isso é útil para selecionar os grupos de modo de exibição, o que nem sempre são fáceis de selecionar na superfície de Design.

A estrutura de tópicos do documento oferece suporte a copiar e colar, ou você pode arrastar e soltar. Arrastar e soltar é suportada da estrutura de tópicos de documento para a superfície de Design, bem como a superfície de Design para a estrutura de tópicos do documento. Além disso, o mouse em um item na estrutura de tópicos do documento exibe o menu de contexto para aquele item (o mesmo menu de contexto que aparece quando você clica esse mesmo widget na superfície de Design).
