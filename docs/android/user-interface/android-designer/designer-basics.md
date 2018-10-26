---
title: Noções básicas do xamarin. Android Designer
description: Este tópico apresenta os recursos de Designer do xamarin. Android, explica como iniciar o Designer, descreve a superfície de Design e fornece detalhes sobre como usar o painel de propriedades para editar as propriedades do widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: fe909d72f3c6d6733318b5dcbd1858a1a9e28b37
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108191"
---
# <a name="xamarinandroid-designer-basics"></a>Noções básicas do xamarin. Android Designer

_Este tópico apresenta os recursos de Designer do xamarin. Android, explica como iniciar o Designer, descreve a superfície de Design e fornece detalhes sobre como usar o painel de propriedades para editar as propriedades do widget._


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Iniciar o Designer

O Designer é iniciado automaticamente na criação de um layout, ou pode ser iniciado clicando duas vezes em um arquivo de layout existente. Por exemplo, duas vezes **activity_main.axml** na **recursos > Layout** pasta carregará o Designer, como visto nesta captura de tela:

[![Tela de Designer no Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Da mesma forma, você pode adicionar um novo layout clicando com o **layout** pasta o **Gerenciador de soluções** e selecionando **Adicionar > Novo Item... > Layout do Android**:

[![Adicionar Novo Item de caixa de diálogo](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Isso cria uma nova **. axml** arquivo de layout e o carrega no Designer.

## <a name="designer-features"></a>Recursos de Designer

O Designer é composto de várias seções que oferecem suporte a seus vários recursos, como mostrado na seguinte captura de tela:

[![Diagrama dos painéis do Designer](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Quando você edita um layout no Designer, você use os recursos a seguir para criar e formatar seu design:

-   **Superfície de design** &ndash; facilita a construção visual de interface do usuário, fornecendo uma representação editável de como o layout será exibido no dispositivo. O **superfície de Design** é exibida dentro a **painel de Design** (com o **barra de ferramentas do Designer** posicionado acima dele).

-   **Painel de fonte** &ndash; fornece uma exibição da fonte XML subjacente que corresponde ao design apresentado na **superfície de Design**.

-   **Barra de ferramentas Designer** &ndash; exibe uma lista de seletores: **dispositivo**, **versão**, **tema**, configuração de layout e as configurações da barra de ação. O **barra de ferramentas do Designer** também inclui ícones para iniciar o Editor de tema e para ativar a grade de Design de Material.

-   **Caixa de ferramentas** &ndash; fornece uma lista de widgets e layouts que você pode arrastar e soltar os **superfície de Design**.

-   **Janela propriedades** &ndash; lista as propriedades do widget selecionado para exibição e edição.

-   **Estrutura de tópicos de documento** &ndash; exibe a árvore de widgets que compõem o layout. Você pode clicar em um item na árvore para fazer com que ele seja selecionado na **superfície de Design**. Além disso, clicar em um item na árvore de carrega as propriedades do item para o **propriedades** janela.

## <a name="design-surface"></a>A superfície de design

O Designer torna possível para que você possa arrastar e soltar widgets da caixa de ferramentas para o **superfície de Design**. Quando você interage com os widgets no Designer (adicionando novos widgets ou reposicionar os existentes), linhas verticais e horizontais são exibidas para marcar os pontos de inserção disponíveis. No exemplo a seguir, uma nova `Button` widget está sendo arrastado para o **superfície de Design**:

[![Exemplo de linhas de inserção na superfície de Design](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Além disso, os widgets podem ser copiados: você pode usar o copiar e colar para copiar um widget, ou você pode arrastar e soltar um widget existente ao pressionar o <kbd>CTRL</kbd> chave.

### <a name="designer-toolbar"></a>Barra de ferramentas do Designer

O **barra de ferramentas do Designer** (posicionada acima a **superfície de Design**) apresenta menus de ferramentas e os seletores de configuração:

[![Diagrama da barra de ferramentas de Designer](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

O **barra de ferramentas do Designer** fornece acesso aos seguintes recursos:

-   **Seletor de Layout alternativo** &ndash; lhe permite selecionar entre versões diferentes do layout.

-   **Seletor de dispositivo** &ndash; define um conjunto de qualificadores (como tamanho da tela, resolução e disponibilidade de teclado) associado a um determinado dispositivo. Você também pode adicionar e excluir novos dispositivos.

-   **Seletor de versão do Android** &ndash; o Android versão que o layout está direcionando. O Designer será renderizado o layout de acordo com a versão do Android selecionado.

-   **Seletor de tema** &ndash; seleciona o tema da interface do usuário para o layout.

-   **Seletor de configuração** &ndash; seleciona a configuração do dispositivo, como *retrato* ou *paisagem*.

-   **Opções do qualificador de recurso** &ndash; abre uma caixa de diálogo que apresenta os menus suspensos para selecionar *idioma*, *modo de interface do usuário*, *modo noturno*, e *Tela arredondada* opções.

-   **Configurações da barra de ação** &ndash; define as configurações de barra de ação para o layout.

-   **Editor de tema** &ndash; abre os *Editor de tema*, que possibilita personalizar elementos de tema selecionado.

-   **Grade de Design de material** &ndash; habilita ou desabilita a *grade de Design de Material*. O item de menu suspenso lado a grade de Design de Material abre uma caixa de diálogo que lhe permite personalizar a grade.

Cada um desses recursos é explicada mais detalhadamente nestes tópicos:

-   [Qualificadores de recursos e opções de visualização](~/android/user-interface/android-designer/resource-qualifiers.md) fornece informações detalhadas sobre a **seletor de dispositivo**, **seletor de versão Android**, **seletor de tema**, **Seletor de configuração**, **opções de qualificações de recursos**, e **configurações da barra de ação**.

-   [Modos de exibição de Layout alternativo](~/android/user-interface/android-designer/alternative-layout-views.md) explica como usar o **seletor de Layout alternativa**.

-   [Recursos de Design de Material do xamarin. Android Designer](~/android/user-interface/android-designer/material-design-features.md) fornece uma visão abrangente do **Editor de tema** e o **grade de Design de Material**.

### <a name="context-menu-commands"></a>Comandos de menu de contexto

Um menu de contexto está disponível na **superfície de Design** e, nas **Document Outline**. Esse menu exibe os comandos que estão disponíveis para o widget selecionado e seu contêiner, tornando mais fácil para executar operações em contêineres (que sempre são fáceis de select em não a **superfície de Design**). Aqui está um exemplo de um menu de contexto:

[![Menu de contexto de exemplo quando o botão direito do mouse na superfície de Design](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

Neste exemplo, clicando duas vezes um `TextView` abre um menu de contexto que fornece várias opções:

-   **LinearLayout** &ndash; abre um submenu para edição de `LinearLayout` pai do `TextView`.

-   **Exclua**, **cópia**, e **Recortar** &ndash; operações que se aplicam ao clicado `TextView`.


### <a name="zoom-controls"></a>Controles de zoom

O **superfície de Design** dá suporte à aplicação de zoom por meio de vários controles conforme mostrado:

[![Diagrama dos controles de zoom da superfície de Design](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Esses controles torná-lo mais fácil de ver determinadas áreas da interface do usuário no Designer:

-   **Realçar contêineres** &ndash; realça os contêineres na **superfície de Design** para que eles são mais fáceis de localizar ao aplicar zoom de entrada e saída.

-   **Tamanho normal** &ndash; renderiza o layout para a pixels, para que você possa ver qual será a aparência de layout na resolução do dispositivo selecionado.

-   **Ajustar à janela** &ndash; define o nível de zoom para que todo o layout está visível na superfície de Design.

-   **Ampliar** &ndash; amplia incrementalmente com cada clique, ampliando o layout.

-   **Zoom** &ndash; diminui o zoom incrementalmente com cada clique, tornando o layout parecer menores na superfície de Design.

Observe que a configuração de zoom escolhido não afeta a interface do usuário do aplicativo em tempo de execução.

## <a name="switching-between-design-and-source-panes"></a>Alternar entre painéis Design e código-fonte

Na faixa de center entre o **Design** e **fonte** painéis, há vários botões que são usadas para modificar como o **Design** e **fonte**painéis são exibidos:

[![Locais do botão de exibição de painel](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Esses botões faça o seguinte:

-   **Design** &ndash; esse botão de nível mais alto, **Design**, seleciona o **Design** painel. Quando esse botão é clicado, o **caixa de ferramentas** e **propriedades** painéis estão habilitadas e o **barra de ferramentas do Editor de texto** não é exibido. Quando o **recolher** botão é clicado (veja abaixo), o **Design** painel é apresentado sozinho sem o **origem** painel.

-   **Alternar painéis** &ndash; esse botão (que se parece com duas setas opostas) alterna o **Design** e **origem** painéis, de modo que o **origem** painel está em à esquerda e o **Design** painel está à direita. Esses painéis clicando nele novamente trocas de volta para seus locais originais.

-   **Código-fonte** &ndash; seleciona esse botão (que é semelhante a dois colchetes angulares opostos) a **origem** painel. Quando esse botão é clicado, o **caixa de ferramentas** e **propriedades** painéis estão desabilitadas e o **barra de ferramentas do Editor de texto** fica visível na parte superior do Visual Studio. Quando o **recolher** botão é clicado (veja abaixo), clicando no **fonte** botão exibe o **origem** painel, em vez do **Design** painel.

-   **Divisão vertical** &ndash; esse botão (que se parece com uma barra vertical), exibe o **Design** e **origem** painéis lado a lado. Isso é a organização padrão.

-   **Divisão horizontal** &ndash; esse botão (que se parece com uma barra horizontal), exibe o **Design** acima do painel de **origem** painel. **Alternar painéis** pode ser clicado para colocar o **código-fonte** acima do painel de **Design** painel.

-   **Recolher painel** &ndash; esse botão (que é semelhante a dois colchetes apontando para a direita) "recolhe" a exibição de painel dual de **Design** e **origem** em uma única exibição de um dos Esses painéis.
    Este botão se transforma o **expanda o painel** botão (que se assemelha a dois colchetes que aponta para a esquerda), que pode ser clicado para retornar a exibição de volta ao painel duplo (**Design** e **fonte**) modo de exibição.

Quando **Painel Recolher** é clicado, somente os **Design** painel é exibido. No entanto, você pode clicar na **fonte** botão em vez disso exibir somente os **fonte** painel. Clique o **Design** botão novamente para retornar para o **Design** painel.

## <a name="source-pane"></a>Painel de fonte

O **fonte** painel exibe o código-fonte XML subjacente de design mostrado na **superfície de Design**. Como ambos os modos de exibição estão disponíveis ao mesmo tempo, é possível criar um design de interface do usuário, indo e vindo entre uma representação visual do design e a código-fonte XML subjacente para o design:

[![Origem do XML de exemplo no painel de código-fonte](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

As alterações feitas na origem XML imediatamente são renderizadas na **superfície de Design**; as alterações feitas na **superfície de Design** fazem com que o código-fonte XML exibido no **origem** painel para atualizado adequadamente. Quando você faz alterações em XML na **origem** painel, preenchimento automático e recursos do IntelliSense estão disponíveis para acelerar o desenvolvimento de interface do usuário baseada em XML, conforme explicado a seguir.

Para maior facilidade de navegação ao trabalhar com arquivos XML longos, a **origem** painel dá suporte a barra de rolagem do Visual Studio (como visto à direita na captura de tela anterior). Para obter mais informações sobre a barra de rolagem, consulte [como acompanhar o código Personalizando a barra de rolagem](https://msdn.microsoft.com/library/dn237345.aspx).


### <a name="autocompletion"></a>Preenchimento automático

Quando você começa a digitar o nome de um atributo para um widget, você poderá pressionar <kbd>CTRL + espaço</kbd> para ver uma lista de possíveis conclusões. Por exemplo, depois de inserir `android:lay` no exemplo a seguir (seguido digitando <kbd>CTRL + espaço</kbd>), a lista a seguir é apresentada:

[![Preenchimento automático do atributo de layout](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Pressione <kbd>ENTER</kbd> para aceitar o preenchimento com listados, ou use as teclas de seta para rolar para o preenchimento desejado e pressione <kbd>ENTER</kbd>. Como alternativa, você pode usar o mouse para role e clique em de preenchimento desejado.

### <a name="intellisense"></a>IntelliSense

Depois de inserir um novo atributo para um widget e começar a atribuir a ele um valor, o IntelliSense é exibida depois que um caractere disparador for digitado e fornece uma lista de valores válidos para usar para o atributo. Por exemplo, após a primeira aspas duplas é inserida para `android:layout_width` no exemplo a seguir, um seletor de preenchimento automático pop-up para fornecer a lista de opções válidas para essa largura:

[![Exemplo do IntelliSense para a largura do layout](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

Na parte inferior dessa janela pop-up, há dois botões (conforme descrito em vermelho na captura de tela acima). Clicando na **recursos do projeto** botão à esquerda restringe a lista recursos que fazem parte do projeto do aplicativo, enquanto clica o **recursos de estrutura** restringe o botão à direita da lista para Exiba recursos disponíveis do framework.
Esses botões Ativar ou desativar: você pode clicar em-los novamente para desativar a ação de filtragem que cada uma fornece.



## <a name="properties-pane"></a>Painel Propriedades

O Designer oferece suporte à edição de propriedades de widget por meio de **propriedades** painel: 

![Captura de tela da janela Propriedades](designer-basics-images/vs/08-property-pad.png)

As propriedades listadas na **propriedades** alterar de painel, dependendo de qual widget está selecionada na **superfície de Design**.

### <a name="default-values"></a>Valores padrão

As propriedades da maioria dos widgets ficarão em branco na **propriedades** janela como seus valores são herdados do Android tema selecionado.
O **propriedades** janela mostrará apenas os valores que são definidos explicitamente para o widget selecionado; ele não mostrará os valores que são herdados do tema.

### <a name="referencing-resources"></a>Referenciando recursos

Algumas propriedades podem fazer referência a recursos que são definidos nos arquivos que não seja o layout **. axml** arquivo. Os casos mais comuns desse tipo são `string` e `drawable` recursos. No entanto, as referências também podem ser usadas para outros recursos, tais como `Boolean` valores e dimensões. Quando uma propriedade dá suporte a referências de recurso, um ícone de busca (um quadrado) é mostrado ao lado da entrada de texto para a propriedade. Esse botão abre um seletor de recurso quando clicado.

Por exemplo, a captura de tela a seguir mostra as opções disponíveis ao clicar no quadrado escurecido à direita do campo de texto para um `Text` widget em de **propriedades** janela:

[![Lista de exemplos de opções de texto](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Quando **recursos...**  é clicado, o **selecionar recurso** caixa de diálogo é apresentada:

[![Captura de tela de recursos de exemplo com vários recursos listados](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

Nesta lista, você pode selecionar um recurso de texto a ser usado para o widget em vez de embutir o texto a **propriedades** painel. O exemplo a seguir ilustra o seletor de recurso para o `Src` propriedade de um `ImageView`:

[![Listagem de recurso de ícone para um ImageView de seletor de recurso](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Clicando no quadrado em branco à direita do `Src` propriedade abre o **selecionar recurso** caixa de diálogo com uma lista de recursos que variam de cores (conforme mostrado acima) a desenháveis.


### <a name="boolean-property-references"></a>Referências de propriedade booliano

*Booliano* propriedades normalmente são selecionadas como marcas de seleção ao lado de uma propriedade na janela Propriedades. Você pode designar uma `true` ou `false` valor, marcando ou desmarcando a caixa de seleção, ou você pode selecionar uma referência de propriedade clicando no quadrado escuro-preenchido à direita da propriedade. No exemplo a seguir, o texto é alterado para todas em maiusculas clicando o **texto todas maiusculas** referência da propriedade booliana associada com selecionado `TextView`:

![Exemplo de como definir as propriedades Boolianas](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Editando as propriedades embutidas

O Designer do Android dá suporte à edição direta de determinadas propriedades na **superfície de Design** (de modo que você não precisa pesquisar para essas propriedades na lista de propriedades). Propriedades que podem ser editadas diretamente incluem texto, margem e tamanho.

### <a name="text"></a>Texto

As propriedades de texto de alguns widgets (como `Button` e `TextView`), podem ser editados diretamente na **superfície de Design**. Clicar duas vezes em um widget irá colocá-lo no modo de edição, conforme mostrado abaixo:

![Recurso de texto para a cadeia de caracteres hello](designer-basics-images/vs/12-text-resource.png "recurso de texto")

Você pode inserir um novo valor de texto, ou você pode inserir uma nova cadeia de caracteres de recurso. No exemplo a seguir, o `@string/hello` recurso está sendo substituído com o texto, `CLICK THIS BUTTON`:

![Shift + Enter para vincular automaticamente o texto para um novo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

Essa alteração é armazenada no widget de `text` propriedade; ele não modifica o valor atribuído ao `@string/hello` recursos.
Quando a chave em uma nova cadeia de caracteres de texto, você poderá pressionar <kbd>Shift</kbd> +
<kbd>Enter</kbd> vincular automaticamente o texto digitado em um novo recurso.

### <a name="margin"></a>Margem

Quando você seleciona um widget, o Designer exibe os identificadores que permitem que você alterar o tamanho ou a margem do widget interativamente. Clique o widget enquanto ele estiver selecionado para alternar entre o modo de edição de margem e o modo de edição de tamanho.

Quando você clica em um widget pela primeira vez, são exibidas alças de margem. Se você mover o mouse para uma das alças, o Designer exibe a propriedade que o identificador será alterado (conforme mostrado abaixo para o `layout_marginLeft` propriedade):

![Captura de tela mostrando a margem lida com o Designer](designer-basics-images/vs/15-margin-handles.png)

Se já tiver sido definida uma margem, linhas pontilhadas são exibidas, que indica o espaço que ocupa a margem:

![Exemplo de marcação de espaço em torno de um botão de linha pontilhada](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Tamanho

Como mencionado anteriormente, você pode alternar para modo de edição de tamanho, clicando em um widget enquanto ainda estiver selecionado. Clique na alça triangular para definir o tamanho para a dimensão indicada para `wrap_content`:

![Alças de redimensionamento e conteúdo de quebra automática de linha](designer-basics-images/vs/17-wrap-content.png)

Clicar a **encapsular conteúdo** identificador reduz o widget nessa dimensão para que ele seja maior do que o necessário para encapsular o conteúdo contido. Neste exemplo, o texto do botão é reduzido horizontalmente, conforme mostrado na seguinte captura de tela.

Quando o valor do tamanho é definido como **encapsular conteúdo**, o Designer exibe um identificador triangular apontando na direção oposta para alterar o tamanho a ser `match_parent`:

![Identificador do pai de correspondência](designer-basics-images/vs/18-match-parent.png)

Clicar a **correspondência pai** identificador restaura o tamanho nessa dimensão para que ele seja o mesmo que o widget pai.

Além disso, você pode arrastar a alça de redimensionamento circular (conforme mostrado nas capturas de tela acima) para redimensionar o widget para um arbitrário `dp` valor. Quando você fizer isso, ambas **encapsular conteúdo** e **correspondência pai** identificadores são apresentados para a dimensão:

![Alças de redimensionamento circular](designer-basics-images/vs/19-resize-dp.png)

Nem todos os contêineres permitem editar o `Size` de um widget. Por exemplo, observe que a captura de tela abaixo, com o `LinearLayout` selecionada, as alças de redimensionamento são exibidas:

![Não há alças de redimensionamento](designer-basics-images/vs/20-no-resize-handles.png)


## <a name="document-outline"></a>Estrutura de Tópicos do Documento

O **Document Outline** exibe a hierarquia de widget do layout.
No exemplo a seguir, o que contém `LinearLayout` widget está selecionada:

![Exemplo de estrutura de tópicos do documento](designer-basics-images/vs/21-document-outline.png)

A estrutura de tópicos do widget selecionado (nesse caso, uma `LinearLayout`) também é realçado na **superfície de Design**. O widget selecionado na estrutura de tópicos de documento permanece em sincronia com seu equivalente na **superfície de Design**. Isso é útil para selecionar os grupos de modo de exibição, que nem sempre são fáceis de select na **superfície de Design**.

O **Document Outline** suporta copiar e colar ou você pode arrastar e soltar. Arrastar e soltar é suportado do **Document Outline** para o **superfície de Design** , bem como dos **superfície de Design** para o **Document Outline**. Além disso, clicando duas vezes em um item a **Document Outline** exibe o menu de contexto para aquele item (o mesmo menu de contexto que aparece quando o botão direito do mouse mesmo widget na **superfície de Design**).




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="launching-the-designer"></a>Iniciar o Designer

O Designer é iniciado automaticamente na criação de um layout, ou pode ser iniciado clicando duas vezes em um arquivo. axml existente. Por exemplo, duas vezes **Main. axml** na **recursos > Layout** pasta carregará o Designer como mostrado abaixo:

[![Tela de Designer no Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Da mesma forma, você pode adicionar um novo layout clicando com o **layout** pasta na **painel de soluções** e selecionando **Adicionar > novo arquivo > Android > Layout**:

[![Adicionar caixa de diálogo Novo arquivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Isso cria um novo arquivo. axml e carrega-os para a superfície de Design.

## <a name="designer-features"></a>Recursos de Designer

O Designer é composto de várias seções que oferecem suporte a seus vários recursos, como mostrado na seguinte captura de tela:

[![Diagrama dos painéis do Designer](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Quando você edita um layout no Designer, você use os recursos a seguir para criar e formatar seu design:

-   **Superfície de design** &ndash; facilita a construção visual de interface do usuário, fornecendo uma representação editável de como o layout será exibido no dispositivo.

-   **Barra de ferramentas** &ndash; exibe uma lista de seletores: **dispositivo**, **versão**, **tema**, configuração de layout e as configurações da barra de ação. A barra de ferramentas também inclui ícones para iniciar o Editor de tema e para ativar a grade de Design de Material.

-   **Caixa de ferramentas** &ndash; fornece uma lista de widgets e layouts que você pode arrastar e soltar na superfície de Design.

-   **Propriedade Pad** &ndash; lista as propriedades do widget selecionado para exibição e edição.

-   **Estrutura de tópicos de documento** &ndash; exibe a árvore de widgets que compõem o layout. Você pode clicar em um item na árvore para fazer com que ele seja selecionado no Designer. Além disso, clicar em um item na árvore de carrega as propriedades do item no painel de propriedade.

## <a name="toolbar"></a>Barra de ferramentas

A barra de ferramentas (posicionada acima da superfície de Design) apresenta menus de ferramentas e os seletores de configuração:

[![Diagrama da barra de ferramentas de Designer](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

A barra de ferramentas fornece acesso aos seguintes recursos:

-   **Seletor de Layout alternativo** &ndash; lhe permite selecionar entre versões diferentes do layout.

-   **Seletor de dispositivo** &ndash; define um conjunto de qualificadores associado com um determinado dispositivo, como tamanho da tela, resolução e disponibilidade de teclado. Você também pode adicionar e excluir novos dispositivos.

-   **Seletor de versão do Android** &ndash; o Android versão que o layout está direcionando. O Designer será renderizado o layout de acordo com a versão do Android selecionado.

-   **Seletor de tema** &ndash; seleciona o tema da interface do usuário para o layout.

-   **Seletor de configuração** &ndash; seleciona a configuração do dispositivo, como *retrato* ou *paisagem*.

-   **Opções do qualificador de recurso** &ndash; abre uma caixa de diálogo que apresenta os menus suspensos para selecionar *idioma*, *modo de interface do usuário*, *modo noturno*, e *Tela arredondada* opções.

-   **Configurações da barra de ação** &ndash; define as configurações de barra de ação para o layout.

-   **Editor de tema** &ndash; abre os *Editor de tema*, que possibilita personalizar elementos de tema selecionado.

-   **Grade de Design de material** &ndash; habilita ou desabilita a *grade de Design de Material*. O item de menu suspenso lado a grade de Design de Material abre uma caixa de diálogo que lhe permite personalizar a grade.

Cada um desses recursos é explicada mais detalhadamente nestes tópicos:

[Qualificadores de recursos e opções de visualização](~/android/user-interface/android-designer/resource-qualifiers.md) fornece informações detalhadas sobre a **seletor de dispositivo**, **seletor de versão Android**, **seletor de tema**, **Seletor de configuração**, **opções de qualificações de recursos**, e **configurações da barra de ação**.

[Modos de exibição de Layout alternativo](~/android/user-interface/android-designer/alternative-layout-views.md) explica como usar o **seletor de Layout alternativa**.

[Recursos de Design de material](~/android/user-interface/android-designer/material-design-features.md) fornece uma visão abrangente do **Editor de tema** e o **grade de Design de Material**.

## <a name="design-surface"></a>A superfície de design

O Designer possibilita arrastar e soltar widgets da caixa de ferramentas na superfície de Design. Quando você interage com os widgets no Designer (adicionando novos widgets ou reposicionar os existentes), linhas verticais e horizontais são exibidas para marcar os pontos de inserção disponíveis. No exemplo a seguir, um novo `Button` widget está sendo arrastado para a superfície de Design:

[![Exemplo de linhas de inserção na superfície de Design](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Além disso, os widgets podem ser copiados: você pode usar o copiar e colar para copiar um widget, ou você pode arrastar e soltar um widget existente ao pressionar o <kbd>Ctrl</kbd> chave.

### <a name="context-menu-commands"></a>Comandos de menu de contexto

Um menu de contexto está disponível na superfície de Design e na estrutura de tópicos do documento. Esse menu exibe os comandos que estão disponíveis para o widget selecionado e seu contêiner, tornando mais fácil para executar operações em contêineres (que nem sempre são fáceis selecionar na superfície de Design). Aqui está um exemplo de um menu de contexto:

[![Menu de contexto de exemplo quando o botão direito do mouse na superfície de Design](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

Neste exemplo, clicando duas vezes um `Button` abre um menu de contexto que fornece várias opções:

-   **LinearLayout** &ndash; abre um submenu para edição de `LinearLayout` pai do `Button`.

-   **Recortar**, **cópia**, e **excluir** &ndash; operações que se aplicam ao clicado `Button`.

### <a name="zoom-controls"></a>Controles de zoom

A superfície de Design oferece suporte a aumentar o zoom por meio de vários controles, conforme mostrado:

[![Diagrama dos controles de zoom da superfície de Design](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Esses controles torná-lo mais fácil de ver determinadas áreas da interface do usuário no Designer:

-   **Realçar contêineres** &ndash; realça os contêineres na superfície de Design para que eles são mais fáceis de localizar ao aplicar zoom de entrada e saída.

-   **Tamanho normal** &ndash; renderiza o layout para a pixels, para que você possa ver qual será a aparência de layout na resolução do dispositivo selecionado.

-   **Ajustar à janela** &ndash; define o nível de zoom para que todo o layout está visível na superfície de Design.

-   **Ampliar** &ndash; amplia incrementalmente com cada clique, ampliando o layout.

-   **Zoom** &ndash; diminui o zoom incrementalmente com cada clique, tornando o layout parecer menores na superfície de Design.

Observe que a configuração de zoom escolhido não afeta a interface do usuário do aplicativo em tempo de execução.

## <a name="property-pad"></a>Painel de propriedade

O Designer oferece suporte à edição de propriedades de widget por meio de **painel de propriedade**. As propriedades listadas na alteração do painel de propriedade, dependendo de qual widget está selecionado na superfície de Designer. Quando o `Button` está selecionado no exemplo anterior, as propriedades para que `Button` widget são mostrados:

[![Captura de tela do painel de propriedade](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Seções do painel de propriedade

O painel de propriedade é dividido em várias seções que agrupam propriedades semelhantes &ndash; isso facilita a localização de propriedades de interesse:

-   **Widget** &ndash; propriedades principais do widget, tais como `id`, `visibility`, `text`, etc. Propriedades para gerenciar o conteúdo do widget são colocadas em geral aqui.

-   **Estilo** &ndash; propriedades que alteram a aparência visual do widget, como `font`, `text color`, `background`, etc.

-   **Layout** &ndash; propriedades que definir o local e o tamanho do widget.

-   **Rolagem** &ndash; as propriedades de rolagem.

-   **Comportamento** &ndash; sinalizadores de definido como o widget se comporta.

### <a name="default-values"></a>Valores padrão

As propriedades da maioria dos widgets ficarão em branco na **painel de propriedade** porque seus valores herdam o tema Android selecionado. O **painel de propriedade** mostrará apenas os valores que são definidos explicitamente para o widget selecionado; ele não mostrará os valores que são herdados do tema.

### <a name="referencing-resources"></a>Referenciando recursos

Algumas propriedades podem fazer referência a recursos que são definidos nos arquivos que não seja o layout **. axml** arquivo. Os casos mais comuns desse tipo são `string` e `drawable` recursos. No entanto, as referências também podem ser usadas para outros recursos, tais como `Boolean` valores e dimensões.
Quando uma propriedade dá suporte a referências de recursos, um ícone de busca (um botão de reticências, &hellip;) é mostrado ao lado da entrada de texto para a propriedade.
Quando clicado, esse botão abre um seletor de recurso.

Por exemplo, a captura de tela a seguir mostra os recursos disponíveis ao clicar no botão de reticências à direita do campo de texto para um `Button` widget em de **painel de propriedade**:

[![Captura de tela de recursos de exemplo com dois recursos listados](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

O exemplo a seguir ilustra o seletor de recurso para o `Src` propriedade de um `ImageView`:

[![Listagem de recurso de ícone para um ImageView de seletor de recurso](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Referências de propriedade booliano

*Booliano* propriedades normalmente são mostradas como uma caixa de seleção no painel de propriedade. Quando um `Boolean` propriedade dá suporte a referências de recurso, uma pequena caixa de seleção aparece ao lado da propriedade. Significa que uma caixa de seleção marcada `true` e uma caixa vazia significa `false`. Você pode inserir diretamente um valor como `true` ou `false`. Posicionar o mouse sobre a entrada exibe um ícone de campo de texto pequeno. Você pode clicar nele para inserir o valor manualmente.

[![Exemplo de como definir as propriedades Boolianas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>Propriedades agrupadas

Alguns widgets têm propriedades de vários valores que são agrupadas juntos (como `Padding`, por exemplo). Esses valores de propriedade estão listados na **painel de propriedade** em uma linha única e expansível. Algumas dessas propriedades podem ser editadas diretamente na linha agrupada, tais como o `Padding` propriedade mostrada abaixo:

[![Exemplo de configurações para a propriedade de preenchimento](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Editando as propriedades embutidas

O Designer do Android dá suporte à edição direta de determinadas propriedades na superfície de Design (de modo que você não precisa pesquisar para essas propriedades na lista de propriedades). Propriedades que podem ser editadas diretamente incluem texto, margem e tamanho.

### <a name="text"></a>Texto

As propriedades de texto de alguns widgets (como `Button` e `TextView`), pode ser editado diretamente na superfície de Design. Clicar duas vezes em um widget irá colocá-lo no modo de edição, conforme mostrado abaixo:

[![Recurso de texto para a cadeia de caracteres hello](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

Você pode inserir um novo valor de texto, ou você pode inserir uma nova cadeia de caracteres de recurso. No exemplo a seguir, o `@string/hello` recurso está sendo substituído com o texto, `CLICK THIS BUTTON`:

[![Shift + Enter para vincular automaticamente o texto para um novo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Essa alteração é armazenada no widget de `text` propriedade; ele não modifica o valor atribuído ao `@string/hello` recursos.
Quando a chave em uma nova cadeia de caracteres de texto, você poderá pressionar <kbd>Shift</kbd> +
<kbd>Enter</kbd> vincular automaticamente o texto digitado em um novo recurso.

### <a name="margin"></a>Margem

Quando você seleciona um widget, o Designer exibe os identificadores que permitem que você alterar o tamanho ou a margem do widget interativamente. Clique o widget enquanto ele estiver selecionado para alternar entre o modo de edição de margem e o modo de edição de tamanho.

Quando você clica em um widget pela primeira vez, são exibidas alças de margem. Se você mover o mouse para uma das alças, o Designer exibe a propriedade que o identificador será alterado (conforme mostrado abaixo para o `layout_marginLeft` propriedade):

[![Captura de tela mostrando a margem lida com o Designer](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Se já tiver sido definida uma margem, linhas pontilhadas são exibidas, que indica o espaço que ocupa a margem:

[![Exemplo de marcação de espaço em torno de um botão de linha pontilhada](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Tamanho

Como mencionado anteriormente, você pode alternar para modo de edição de tamanho, clicando em um widget enquanto ainda estiver selecionado. Clique na alça triangular para definir o tamanho para a dimensão indicada para `wrap_content`:

[![Alças de redimensionamento e conteúdo de quebra automática de linha](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Clicar a **encapsular conteúdo** identificador reduz o widget nessa dimensão de forma que ele não maior que o necessário para encapsular o conteúdo contido. Neste exemplo, o texto do botão é reduzido horizontalmente, conforme mostrado na seguinte captura de tela.

Quando o valor do tamanho é definido como **encapsular conteúdo**, o Designer exibe um identificador triangular apontando na direção oposta para alterar o tamanho a ser `match_parent`:

[![Identificador do pai de correspondência](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Clicar a **correspondência pai** identificador restaura o tamanho nessa dimensão para que ele seja o mesmo que o widget pai.

Além disso, você pode arrastar a alça de redimensionamento circular (conforme mostrado nas capturas de tela acima) para redimensionar o widget para um arbitrário `dp` valor. Quando você fizer isso, ambas **encapsular conteúdo** e **correspondência pai** identificadores são apresentados para a dimensão:

[![Alças de redimensionamento circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

Nem todos os contêineres permitem editar o `Size` de um widget. Por exemplo, observe que a captura de tela abaixo, com o `LinearLayout` selecionada, as alças de redimensionamento são exibidas:

[![Não há alças de redimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Estrutura de Tópicos do Documento

O **Document Outline** exibe a hierarquia de widget do layout.
No exemplo a seguir, o que contém `LinearLayout` widget está selecionada:

[![Estrutura de tópicos do documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

A estrutura de tópicos do widget selecionado (nesse caso, um `LinearLayout`) também é realçado na superfície de Design. O widget selecionado na estrutura de tópicos de documento permanece em sincronia com seu equivalente na superfície de Design. Isso é útil para selecionar os grupos de modo de exibição, que nem sempre são fáceis selecionar na superfície de Design.

A estrutura de tópicos do documento dá suporte a copiar e colar, ou você pode arrastar e soltar. Arrastar e soltar é tem suporte da estrutura de tópicos de documento para a superfície de Design, bem como a superfície de Design para a estrutura de tópicos do documento. Além disso, o botão direito do mouse um item na estrutura de tópicos do documento exibe o menu de contexto para aquele item (o mesmo menu de contexto que aparece quando o botão direito do mouse na superfície de Design de widget mesmo).

-----
