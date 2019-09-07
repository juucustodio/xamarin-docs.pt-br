---
title: Noções básicas do Xamarin. Designer Android
description: Este tópico apresenta os recursos do Xamarin. Designer Android, explica como iniciar o designer, descreve o Design Surface e detalha como usar o painel Propriedades para editar as propriedades do widget.
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: 0089f8681142b1d3d3446c21ba89ab2648e4a638
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761414"
---
# <a name="xamarinandroid-designer-basics"></a>Noções básicas do Xamarin. Designer Android

_Este tópico apresenta os recursos do Xamarin. Designer Android, explica como iniciar o designer, descreve o Design Surface e detalha como usar o painel Propriedades para editar as propriedades do widget._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>Iniciando o designer

O designer é iniciado automaticamente quando um layout é criado ou pode ser iniciado clicando-se duas vezes em um arquivo de layout existente. Por exemplo, clicar duas vezes em **activity_main. axml** na pasta **> layout de recursos** carregará o designer como visto nesta captura de tela:

[![Tela do designer no Visual Studio](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

Da mesma forma, você pode adicionar um novo layout clicando com o botão direito do mouse na pasta de **layout** na **Gerenciador de soluções** e selecionando **Adicionar > novo item... > O layout do Android**:

[![Caixa de diálogo Adicionar novo item](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

Isso cria um novo arquivo de layout **. axml** e o carrega no designer.

> [!TIP]
> As versões mais recentes do Visual Studio dão suporte à abertura de arquivos .xml dentro do Android Designer.
>
> Tanto arquivos .axml quanto .xml são compatíveis com o Android Designer.

## <a name="designer-features"></a>Recursos do designer

O designer é composto por várias seções que dão suporte a seus diversos recursos, conforme mostrado na seguinte captura de tela:

[![Diagrama dos painéis de designer](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

Ao editar um layout no designer, você usa os seguintes recursos para criar e moldar seu design:

- **Design Surface** &ndash; Facilita a construção visual da interface do usuário, fornecendo a você uma representação editável de como o layout aparecerá no dispositivo. O **design Surface** é exibido dentro do **painel de design** (com a **barra de ferramentas do designer** posicionada acima dele).

- **Painel de origem** Fornece uma exibição da fonte XML subjacente que corresponde ao design apresentado no **design Surface.** &ndash;

- **Barra de ferramentas do designer** &ndash; Exibe uma lista de seletores: Configurações de **dispositivo**, **versão**, **tema**, configuração de layout e barra de ações. A **barra de ferramentas do designer** também inclui ícones para iniciar o editor de tema e habilitar a grade de design de material.

- **Caixa de ferramentas** Fornece uma lista de widgets e layouts que você pode arrastar e soltar na **design Surface.** &ndash;

- **Janela Propriedades** &ndash; Lista as propriedades do widget selecionado para exibição e edição.

- **Estrutura de tópicos do documento** &ndash; Exibe a árvore de widgets que compõem o layout. Você pode clicar em um item na árvore para fazer com que ele seja selecionado na **design Surface**. Além disso, clicar em um item na árvore carrega as propriedades do item na janela **Propriedades** .

## <a name="design-surface"></a>A superfície de design

O designer torna possível arrastar e soltar widgets da caixa de ferramentas para a **design Surface**. Quando você interage com widgets no designer (adicionando novos widgets ou reposicionando os existentes), linhas verticais e horizontais são exibidas para marcar os pontos de inserção disponíveis. No exemplo a seguir, um novo `Button` widget está sendo arrastado para a **design Surface**:

[![Linhas de inserção de exemplo em Design Surface](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

Além disso, os widgets podem ser copiados: você pode usar copiar e colar para copiar um widget ou pode arrastar e soltar um widget existente enquanto pressiona a tecla <kbd>Ctrl</kbd> .

### <a name="designer-toolbar"></a>Barra de ferramentas do designer

A **barra de ferramentas do designer** (posicionada acima da **design Surface**) apresenta seletores de configuração e menus de ferramentas:

[![Diagrama da barra de ferramentas do designer](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

A **barra de ferramentas do designer** fornece acesso aos seguintes recursos:

- **Seletor de layout alternativo** &ndash; Permite que você selecione diferentes versões de layout.

- **Seletor de dispositivo** &ndash; Define um conjunto de qualificadores (como tamanho da tela, resolução e disponibilidade do teclado) associados a um dispositivo específico. Você também pode adicionar e excluir novos dispositivos.

- **Seletor de versão do Android** &ndash; A versão do Android para a qual o layout está sendo direcionado. O designer renderizará o layout de acordo com a versão do Android selecionada.

- **Seletor de tema** &ndash; Seleciona o tema da interface do usuário para o layout.

- **Seletor de configuração** Seleciona a configuração do dispositivo, como *retrato* ou *paisagem.* &ndash;

- **Opções do qualificador de recursos**Abre uma caixa de diálogo que apresenta menus suspensos para seleção de idioma, modo de interface do usuário, modo noturno e opções de tela de ida e volta. &ndash;

- **Configurações da barra de ações** &ndash; Define as configurações da barra de ações para o layout.

- **Editor de tema** Abre o *Editor de tema*, o que torna possível personalizar elementos do tema selecionado. &ndash;

- **Grade de design de material** Habilita ou desabilita a grade de *design do material.* &ndash; O item de menu suspenso adjacente à grade de design do material abre uma caixa de diálogo que permite que você personalize a grade.

Cada um desses recursos é explicado em mais detalhes nestes tópicos:

- [Qualificadores de recursos e opções de visualização](~/android/user-interface/android-designer/resource-qualifiers.md) fornecem informações detalhadas sobre o **seletor de dispositivo**, **seletor de versão do Android**, **seletor de tema**, **seletor de configuração**, **qualificações de recursos Opções**e **configurações da barra de ações**.

- As [exibições de layout alternativo](~/android/user-interface/android-designer/alternative-layout-views.md) explicam como usar o **seletor de layout alternativo**.

- Os [recursos de design de material do Xamarin. designer Android](~/android/user-interface/android-designer/material-design-features.md) fornecem uma visão geral abrangente do editor de **tema** e da grade de **design do material**.

### <a name="context-menu-commands"></a>Comandos do menu de contexto

Um menu de contexto está disponível no **design Surface** e na estrutura de **Tópicos do documento**. Esse menu exibe os comandos que estão disponíveis para o widget selecionado e seu contêiner, facilitando a execução de operações em contêineres (que nem sempre são fáceis de selecionar no **design Surface**). Aqui está um exemplo de um menu de contexto:

[![Menu de contexto de exemplo ao clicar com o botão direito do mouse no Design Surface](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

Neste exemplo, clicar com o botão direito `TextView` do mouse em uma abre um menu de contexto que fornece várias opções:

- **LinearLayout** abre um submenu para editar o `LinearLayout` pai do `TextView`. &ndash;

- **Exclua**, **Copie**e **recorte** &ndash; `TextView`as operações que se aplicam ao clique com o botão direito do mouse.

### <a name="zoom-controls"></a>Controles de zoom

O **design Surface** dá suporte ao zoom por meio de vários controles, conforme mostrado:

[![Diagrama dos controles de zoom de Design Surface](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

Esses controles facilitam a visualização de determinadas áreas da interface do usuário no designer:

- **Realçar contêineres** Realça os contêineres na design Surface para que sejam mais fáceis de localizar ao ampliar e reduzir. &ndash;

- **Tamanho normal** &ndash; Renderiza o layout pixel-for-pixel para que você possa ver como o layout irá examinar a resolução do dispositivo selecionado.

- **Ajustar à janela** &ndash; Define o nível de zoom para que todo o layout fique visível no design Surface.

- **Ampliar** &ndash; Amplia incrementalmente com cada clique, ampliando o layout.

- **Reduzir** &ndash; Reduz incrementalmente com cada clique, fazendo com que o layout pareça menor no design Surface.

Observe que a configuração de zoom escolhida não afeta a interface do usuário do aplicativo em tempo de execução.

## <a name="switching-between-design-and-source-panes"></a>Alternando entre os painéis design e fonte

Na faixa central entre os painéis **design** e **fonte** , há vários botões que são usados para modificar como os painéis **design** e **fonte** são exibidos:

[![Locais do botão de exibição do painel](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

Esses botões fazem o seguinte:

- **Design** do Esse botão superior, **design**, seleciona o painel **Design.** &ndash; Quando esse botão é clicado, os painéis **caixa de ferramentas** e **Propriedades** são habilitados e a barra de ferramentas do **Editor de texto** não é exibida. Quando o botão **recolher** é clicado (veja abaixo), o painel **design** é apresentado sozinho sem o painel de **origem** .

- **Alternar painéis** Esse botão (que se assemelha a duas setas opostas) troca os painéis design e fonte para que o painel fonte fique à esquerda e o painel Design esteja à direita. &ndash; Clicar novamente alterna esses painéis de volta para seus locais originais.

- **Origem** do Esse botão (que se assemelha a dois colchetes angulares opostos) seleciona o painel **fonte.** &ndash; Quando esse botão é clicado, os painéis **caixa de ferramentas** e **Propriedades** são desabilitados e a barra de ferramentas do **Editor de texto** se torna visível na parte superior do Visual Studio. Quando o botão **recolher** é clicado (veja abaixo), clicar no botão **fonte** exibe o painel **fonte** em vez do painel **design** .

- **Divisão vertical** Esse botão (que se assemelha a uma barra vertical), exibe os painéis **design** e fonte lado a lado. &ndash; Essa é a organização padrão.

- **Divisão horizontal** Esse botão (que se assemelha a uma barra horizontal), exibe o painel de **design** acima do painel de **origem.** &ndash; Os **painéis de permuta** podem ser clicados para posicionar o painel de **origem** acima do painel de **design** .

- **Recolher painel** Esse botão (que se assemelha a dois colchetes de ângulo à direita) "recolhe" a exibição de painel duplo do **design** e da fonte em uma única exibição de um desses painéis. &ndash;
    Esse botão torna-se o botão **expandir painel** (semelhante a dois colchetes angulares à esquerda), que pode ser clicado para retornar a exibição de volta para o modo de exibição de painel duplo (**design** e **origem**).

Quando o **painel recolher** é clicado, somente o painel **design** é exibido. No entanto, você pode clicar no botão **fonte** para exibir apenas o painel **origem** . Clique no botão **design** novamente para retornar ao painel **design** .

## <a name="source-pane"></a>Painel de origem

O painel **origem** exibe a origem XML subjacente ao design mostrado no **design Surface**. Como ambos os modos de exibição estão disponíveis ao mesmo tempo, é possível criar um design de interface do usuário voltando-se entre uma representação visual do design e a origem XML subjacente para o design:

[![Exemplo de origem XML no painel de origem](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

As alterações feitas na origem XML são processadas imediatamente no **design Surface**; as alterações feitas no **design Surface** fazem com que a origem XML exibida no painel de **origem** seja atualizada de acordo. Quando você faz alterações em XML no painel **fonte** , recursos de preenchimento automático e IntelliSense estão disponíveis para agilizar o desenvolvimento da interface do usuário baseada em XML, conforme explicado a seguir.

Para maior facilidade de navegação ao trabalhar com arquivos XML longos, o painel de **origem** dá suporte ao ScrollBar do Visual Studio (como visto à direita na captura de tela anterior). Para obter mais informações sobre a barra de rolagem, consulte [como controlar seu código Personalizando a barra de rolagem](https://msdn.microsoft.com/library/dn237345.aspx).

### <a name="autocompletion"></a>Preenchimento automático

Ao começar a digitar o nome de um atributo para um widget, você pode pressionar <kbd>Ctrl + espaço</kbd> para ver uma lista de possíveis conclusões. Por exemplo, depois de `android:lay` inserir no exemplo a seguir (seguido digitando <kbd>Ctrl + espaço</kbd>), a seguinte lista é apresentada:

[![Preenchimento automático do atributo de layout](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

Pressione <kbd>Enter</kbd> para aceitar a primeira conclusão listada ou use as teclas de direção para rolar para a conclusão desejada e pressione <kbd>Enter</kbd>. Como alternativa, você pode usar o mouse para rolar e clicar na conclusão desejada.

### <a name="intellisense"></a>IntelliSense

Depois de inserir um novo atributo para um widget e começar a atribuir um valor, o IntelliSense aparece depois que um caractere de gatilho é digitado e fornece uma lista de valores válidos a serem usados para esse atributo. Por exemplo, após a primeira aspa dupla ser inserida para `android:layout_width` no exemplo a seguir, um seletor de preenchimento automático aparecerá para fornecer a lista de opções válidas para essa largura:

[![Exemplo de IntelliSense para largura de layout](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

Na parte inferior desse pop-up há dois botões (conforme descrito em vermelho na captura de tela acima). Clicar no botão **recursos do projeto** à esquerda restringe a lista a recursos que fazem parte do projeto do aplicativo, enquanto clicar no botão recursos do **Framework** à direita restringe a lista para exibir os recursos disponíveis na estrutura.
Estes botões são ativados ou desativados: você pode clicar neles novamente para desabilitar a ação de filtragem fornecida por cada um.

## <a name="properties-pane"></a>Painel Propriedades

O designer oferece suporte à edição de propriedades do widget por meio do painel **Propriedades** : 

![Captura de tela da janela Propriedades](designer-basics-images/vs/08-property-pad.png)

As propriedades listadas no painel **Propriedades** são alteradas dependendo de qual widget está selecionado na **design Surface**.

### <a name="default-values"></a>Valores padrão

As propriedades da maioria dos widgets ficarão em branco na janela **Propriedades** porque seus valores herdam do tema do Android selecionado.
A janela **Propriedades** só mostrará valores explicitamente definidos para o widget selecionado; Ele não mostrará valores que são herdados do tema.

### <a name="referencing-resources"></a>Referenciando recursos

Algumas propriedades podem referenciar recursos que são definidos em arquivos diferentes do arquivo layout **. axml** . Os casos mais comuns desse tipo são `string` e `drawable` recursos. No entanto, as `Boolean` referências também podem ser usadas para outros recursos, como valores e dimensões. Quando uma propriedade dá suporte a referências de recursos, um ícone de procura (um quadrado) é mostrado ao lado da entrada de texto da propriedade. Esse botão abre um seletor de recursos quando clicado.

Por exemplo, a captura de tela a seguir mostra as opções disponíveis ao clicar no quadrado mais escuro à direita do campo de texto `Text` para um widget na janela **Propriedades** :

[![Exemplo de lista de opções de texto](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

Quando **Resource...** é clicado, a caixa de diálogo **selecionar recurso** é apresentada:

[![Exemplo de captura de tela de recursos com vários recursos listados](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

Nessa lista, você pode selecionar um recurso de texto a ser usado para esse widget em vez de embutir o texto no painel **Propriedades** . O exemplo a seguir ilustra o seletor de `Src` recursos para a `ImageView`propriedade de um:

[![Recurso de ícone de listagem do seletor de recursos para um ImageView](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

Clicar no quadrado em branco à direita da `Src` Propriedade abre a caixa de diálogo **selecionar recurso** com uma lista de recursos que variam de cores (como mostrado acima) a drawables.

### <a name="boolean-property-references"></a>Referências de propriedade booliana

As propriedades *boolianas* normalmente são selecionadas como marcas de seleção ao lado de uma propriedade no janela Propriedades. Você pode designar um `true` valor `false` ou marcando ou desmarcando essa caixa de seleção, ou pode selecionar uma referência de propriedade clicando no quadrado de preenchimento escuro à direita da propriedade. No exemplo a seguir, o texto é alterado para todas as maiúsculas clicando na referência de propriedade **Text All Caps** Boolean associada `TextView`à selecionada:

![Exemplo de configuração de propriedades booleanas](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>Editando propriedades embutidas

O Designer Android dá suporte à edição direta de determinadas propriedades na **design Surface** (para que você não precise Pesquisar essas propriedades na lista de propriedades). As propriedades que podem ser editadas diretamente incluem texto, margem e tamanho.

### <a name="text"></a>Texto

As propriedades de texto de alguns widgets ( `Button` como e `TextView`) podem ser editadas diretamente no **design Surface**. Clicar duas vezes em um widget irá colocá-lo no modo de edição, conforme mostrado abaixo:

![Recurso de texto para a cadeia de caracteres de saudação](designer-basics-images/vs/12-text-resource.png "Recurso de texto")

Você pode inserir um novo valor de texto ou pode inserir uma nova cadeia de caracteres de recurso. No exemplo a seguir, o `@string/hello` recurso está sendo substituído pelo `CLICK THIS BUTTON`texto:

![Shift + Enter para vincular automaticamente o texto a um novo recurso](designer-basics-images/vs/13-shift-enter-resource.png)

Essa alteração é armazenada na Propriedade do `text` widget; ela não modifica o valor atribuído `@string/hello` ao recurso.
Quando você chave em uma nova cadeia de texto, pode pressionar <kbd>Shift</kbd> +
<kbd>Enter</kbd> para vincular automaticamente o texto inserido a um novo recurso.

### <a name="margin"></a>Margem

Quando você seleciona um widget, o designer exibe identificadores que permitem alterar o tamanho ou a margem do widget interativamente. Clicar no widget enquanto ele está selecionado alterna entre o modo de edição de margem e o modo de edição de tamanho.

Quando você clica em um widget pela primeira vez, as alças de margem são exibidas. Se você mover o mouse para uma das alças, o designer exibirá a propriedade que o identificador irá alterar (como mostrado abaixo para `layout_marginLeft` a propriedade):

![Captura de tela mostrando alças de margem no designer](designer-basics-images/vs/15-margin-handles.png)

Se uma margem já tiver sido definida, as linhas pontilhadas serão exibidas, indicando o espaço ocupado pela margem:

![Exemplo de linhas pontilhadas marcando espaço em um botão](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>Size

Conforme mencionado anteriormente, você pode alternar para o modo de edição de tamanho clicando em um widget enquanto ele já estiver selecionado. Clique no identificador triangular para definir o tamanho da dimensão indicada para `wrap_content`:

![Encapsular conteúdo e redimensionar alças](designer-basics-images/vs/17-wrap-content.png)

Clicar no identificador de **conteúdo de encapsulamento** reduz o widget nessa dimensão para que não seja maior do que o necessário para encapsular o conteúdo incluído. Neste exemplo, o texto do botão é reduzido horizontalmente, conforme mostrado na próxima captura de tela.

Quando o valor do tamanho é definido como **encapsular conteúdo**, o designer exibe um identificador triangular apontando na direção oposta para alterar o tamanho `match_parent`para:

![Corresponder identificador pai](designer-basics-images/vs/18-match-parent.png)

Clicar no identificador **pai de correspondência** restaura o tamanho dessa dimensão para que seja o mesmo que o widget pai.

Além disso, você pode arrastar a alça de redimensionamento circular (conforme mostrado nas capturas de tela acima) para redimensionar o widget para um valor arbitrário `dp` . Quando você fizer isso, ambos os identificadores **pai** de **conteúdo** e correspondência serão apresentados para essa dimensão:

![Alças de redimensionamento circular](designer-basics-images/vs/19-resize-dp.png)

Nem todos os contêineres permitem `Size` a edição de um widget. Por exemplo, observe que na captura de tela abaixo com `LinearLayout` a selecionada, as alças de redimensionamento não aparecem:

![Sem identificadores de redimensionamento](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>Estrutura de Tópicos do Documento

O **contorno do documento** exibe a hierarquia do widget do layout.
No exemplo a seguir, o widget `LinearLayout` recipiente é selecionado:

![Exemplo de estrutura de tópicos de documento](designer-basics-images/vs/21-document-outline.png)

O contorno do widget selecionado (neste caso, a `LinearLayout`) também é realçado no **design Surface**. O widget selecionado na estrutura de tópicos do documento permanece em sincronia com sua contraparte na **design Surface**. Isso é útil para selecionar grupos de modos de exibição, que nem sempre são fáceis de selecionar na **design Surface**.

A **estrutura de tópicos do documento** dá suporte a copiar e colar, ou você pode usar arrastar e soltar. O recurso arrastar e soltar tem suporte da **estrutura de tópicos do documento** para a **design Surface** , bem como da **design Surface** para a estrutura de **Tópicos do documento**. Além disso, clicar com o botão direito do mouse em um item no **contorno do documento** exibe o menu de contexto desse item (o mesmo menu de contexto que aparece quando você clica com o botão direito do mouse no mesmo widget na **design Surface**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="launching-the-designer"></a>Iniciando o designer

O designer é iniciado automaticamente quando um layout é criado ou pode ser iniciado clicando-se duas vezes em um arquivo. axml existente. Por exemplo, clicar duas vezes em **Main. axml** na pasta **> layout de recursos** carregará o designer, conforme mostrado abaixo:

[![Tela do designer no Visual Studio para Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

Da mesma forma, você pode adicionar um novo layout clicando com o botão direito do mouse na pasta de **layout** na **painel de soluções** e selecionando **Adicionar > novo arquivo > o layout de > do Android**:

[![Caixa de diálogo Adicionar novo arquivo](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

Isso cria um novo arquivo. axml e o carrega no Design Surface.

> [!TIP]
> As versões mais recentes do Visual Studio dão suporte à abertura de arquivos .xml dentro do Android Designer.
>
> Tanto arquivos .axml quanto .xml são compatíveis com o Android Designer.

## <a name="designer-features"></a>Recursos do designer

O designer é composto por várias seções que dão suporte a seus diversos recursos, conforme mostrado na seguinte captura de tela:

[![Diagrama dos painéis de designer](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

Ao editar um layout no designer, você usa os seguintes recursos para criar e moldar seu design:

- **Design Surface** &ndash; Facilita a construção visual da interface do usuário, fornecendo a você uma representação editável de como o layout aparecerá no dispositivo.

- **Barra de ferramentas** &ndash; Exibe uma lista de seletores: Configurações de **dispositivo**, **versão**, **tema**, configuração de layout e barra de ações. A barra de ferramentas também inclui ícones para iniciar o editor de tema e habilitar a grade de design de material.

- **Caixa de ferramentas** &ndash; Fornece uma lista de widgets e layouts que você pode arrastar e soltar na design Surface.

- **Painel de propriedades** &ndash; Lista as propriedades do widget selecionado para exibição e edição.

- **Estrutura de tópicos do documento** &ndash; Exibe a árvore de widgets que compõem o layout. Você pode clicar em um item na árvore para fazer com que ele seja selecionado no designer. Além disso, clicar em um item na árvore carrega as propriedades do item no painel de propriedades.

## <a name="toolbar"></a>Barra de ferramentas

A barra de ferramentas (posicionada acima da Design Surface) apresenta seletores de configuração e menus de ferramentas:

[![Diagrama da barra de ferramentas do designer](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

A barra de ferramentas fornece acesso aos seguintes recursos:

- **Seletor de layout alternativo** &ndash; Permite que você selecione diferentes versões de layout.

- **Seletor de dispositivo** &ndash; Define um conjunto de qualificadores associados a um dispositivo específico, como tamanho da tela, resolução e disponibilidade do teclado. Você também pode adicionar e excluir novos dispositivos.

- **Seletor de versão do Android** &ndash; A versão do Android para a qual o layout está sendo direcionado. O designer renderizará o layout de acordo com a versão do Android selecionada.

- **Seletor de tema** &ndash; Seleciona o tema da interface do usuário para o layout.

- **Seletor de configuração** Seleciona a configuração do dispositivo, como *retrato* ou *paisagem.* &ndash;

- **Opções do qualificador de recursos**Abre uma caixa de diálogo que apresenta menus suspensos para seleção de idioma, modo de interface do usuário, modo noturno e opções de tela de ida e volta. &ndash;

- **Configurações da barra de ações** &ndash; Define as configurações da barra de ações para o layout.

- **Editor de tema** Abre o *Editor de tema*, o que torna possível personalizar elementos do tema selecionado. &ndash;

- **Grade de design de material** Habilita ou desabilita a grade de *design do material.* &ndash; O item de menu suspenso adjacente à grade de design do material abre uma caixa de diálogo que permite que você personalize a grade.

Cada um desses recursos é explicado em mais detalhes nestes tópicos:

[Qualificadores de recursos e opções de visualização](~/android/user-interface/android-designer/resource-qualifiers.md) fornecem informações detalhadas sobre o **seletor de dispositivo**, **seletor de versão do Android**, **seletor de tema**, **seletor de configuração**, **qualificações de recursos Opções**e **configurações da barra de ações**.

As [exibições de layout alternativo](~/android/user-interface/android-designer/alternative-layout-views.md) explicam como usar o **seletor de layout alternativo**.

Os [recursos de design de material](~/android/user-interface/android-designer/material-design-features.md) fornecem uma visão geral abrangente do editor de **tema** e da grade de **design de material**.

## <a name="design-surface"></a>A superfície de design

O designer torna possível arrastar e soltar widgets da caixa de ferramentas para a Design Surface. Quando você interage com widgets no designer (adicionando novos widgets ou reposicionando os existentes), linhas verticais e horizontais são exibidas para marcar os pontos de inserção disponíveis. No exemplo a seguir, um novo `Button` widget está sendo arrastado para a design Surface:

[![Linhas de inserção de exemplo em Design Surface](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

Além disso, os widgets podem ser copiados: você pode usar copiar e colar para copiar um widget ou pode arrastar e soltar um widget existente enquanto pressiona a tecla <kbd>Ctrl</kbd> .

### <a name="context-menu-commands"></a>Comandos do menu de contexto

Um menu de contexto está disponível no Design Surface e na estrutura de tópicos do documento. Esse menu exibe os comandos que estão disponíveis para o widget selecionado e seu contêiner, facilitando a execução de operações em contêineres (que nem sempre são fáceis de selecionar no Design Surface). Aqui está um exemplo de um menu de contexto:

[![Menu de contexto de exemplo ao clicar com o botão direito do mouse no Design Surface](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

Neste exemplo, clicar com o botão direito `Button` do mouse em uma abre um menu de contexto que fornece várias opções:

- **LinearLayout** abre um submenu para editar o `LinearLayout` pai do `Button`. &ndash;

- **Operações de recortar**, &ndash; copiar e excluir que se aplicam ao clique com o botão direito do mouse. `Button`

### <a name="zoom-controls"></a>Controles de zoom

O Design Surface dá suporte ao zoom por meio de vários controles, conforme mostrado:

[![Diagrama dos controles de zoom de Design Surface](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

Esses controles facilitam a visualização de determinadas áreas da interface do usuário no designer:

- **Realçar contêineres** &ndash; Realça os contêineres na design Surface para que sejam mais fáceis de localizar ao ampliar e reduzir.

- **Tamanho normal** &ndash; Renderiza o layout pixel-for-pixel para que você possa ver como o layout irá examinar a resolução do dispositivo selecionado.

- **Ajustar à janela** &ndash; Define o nível de zoom para que todo o layout fique visível no design Surface.

- **Ampliar** &ndash; Amplia incrementalmente com cada clique, ampliando o layout.

- **Reduzir** &ndash; Reduz incrementalmente com cada clique, fazendo com que o layout pareça menor no design Surface.

Observe que a configuração de zoom escolhida não afeta a interface do usuário do aplicativo em tempo de execução.

## <a name="property-pad"></a>Painel de propriedades

O designer oferece suporte à edição de propriedades do widget por meio do **painel de propriedades**. As propriedades listadas no painel de propriedades são alteradas dependendo de qual widget está selecionado na superfície do designer. Quando o `Button` no exemplo anterior é selecionado, as propriedades desse `Button` widget são mostradas:

[![Captura de tela do painel de propriedades](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>Seções do painel de propriedades

O painel de propriedades é dividido em várias seções que agrupam propriedades &ndash; semelhantes, e isso facilita a localização das propriedades de interesse:

- **Widget** Propriedades principais do widget, `id`como, `visibility` `text`, etc. &ndash; As propriedades para gerenciar o conteúdo do widget geralmente são colocadas aqui.

- **Estilo** do Propriedades que alteram a aparência visual do widget, `font`como, `text color` `background`,, etc. &ndash;

- **Layout** do &ndash; Propriedades que definem o local e o tamanho do widget.

- **Rolar** &ndash; Propriedades de rolagem.

- **Comportamento** do &ndash; Sinalizadores que definem a forma com que o widget se comporta.

### <a name="default-values"></a>Valores padrão

As propriedades da maioria dos widgets ficarão em branco no **painel de propriedades** porque seus valores herdam do tema do Android selecionado. O **painel de propriedades** só mostrará valores explicitamente definidos para o widget selecionado; Ele não mostrará valores que são herdados do tema.

### <a name="referencing-resources"></a>Referenciando recursos

Algumas propriedades podem referenciar recursos que são definidos em arquivos diferentes do arquivo layout **. axml** . Os casos mais comuns desse tipo são `string` e `drawable` recursos. No entanto, as `Boolean` referências também podem ser usadas para outros recursos, como valores e dimensões.
Quando uma propriedade dá suporte a referências de recursos, um ícone de procura &hellip;(reticências) é mostrado ao lado da entrada de texto da propriedade.
Quando clicado, esse botão abre um seletor de recursos.

Por exemplo, a captura de tela a seguir mostra os recursos disponíveis ao clicar nas reticências à direita do campo de `Button` texto de um widget no **painel de propriedades**:

[![Exemplo de captura de tela de recursos com dois recursos listados](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

O exemplo a seguir ilustra o seletor de `Src` recursos para a `ImageView`propriedade de um:

[![Recurso de ícone de listagem do seletor de recursos para um ImageView](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>Referências de propriedade booliana

Normalmente, as propriedades *booleanas* são mostradas como uma caixa de seleção no painel de propriedades. Quando uma `Boolean` Propriedade dá suporte a referências de recursos, uma pequena caixa de seleção é exibida ao lado da propriedade. Uma caixa de seleção `true` marcada significa e uma marca `false`vazia significa. Você também pode inserir diretamente um valor como `true` ou. `false` Passar o mouse sobre a entrada abre um pequeno ícone de campo de texto. Você pode clicar nele se desejar inserir o valor manualmente.

[![Exemplo de configuração de propriedades booleanas](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>Propriedades agrupadas

Alguns widgets têm propriedades com vários valores que são agrupadas ( `Padding`como, por exemplo). Esses valores de propriedade são listados no **painel de propriedades** em uma única linha expansível. Algumas dessas propriedades podem ser editadas diretamente na linha agrupada, como a `Padding` Propriedade mostrada abaixo:

[![Exemplo de configurações para a propriedade Padding](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>Editando propriedades embutidas

O Designer Android dá suporte à edição direta de determinadas propriedades na Design Surface (para que você não precise Pesquisar essas propriedades na lista de propriedades). As propriedades que podem ser editadas diretamente incluem texto, margem e tamanho.

### <a name="text"></a>Texto

As propriedades de texto de alguns widgets ( `Button` como e `TextView`) podem ser editadas diretamente no design Surface. Clicar duas vezes em um widget irá colocá-lo no modo de edição, conforme mostrado abaixo:

[![Recurso de texto para a cadeia de caracteres de saudação](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

Você pode inserir um novo valor de texto ou pode inserir uma nova cadeia de caracteres de recurso. No exemplo a seguir, o `@string/hello` recurso está sendo substituído pelo `CLICK THIS BUTTON`texto:

[![Shift + Enter para vincular automaticamente o texto a um novo recurso](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

Essa alteração é armazenada na Propriedade do `text` widget; ela não modifica o valor atribuído `@string/hello` ao recurso.
Quando você chave em uma nova cadeia de texto, pode pressionar <kbd>Shift</kbd> +
<kbd>Enter</kbd> para vincular automaticamente o texto inserido a um novo recurso.

### <a name="margin"></a>Margem

Quando você seleciona um widget, o designer exibe identificadores que permitem alterar o tamanho ou a margem do widget interativamente. Clicar no widget enquanto ele está selecionado alterna entre o modo de edição de margem e o modo de edição de tamanho.

Quando você clica em um widget pela primeira vez, as alças de margem são exibidas. Se você mover o mouse para uma das alças, o designer exibirá a propriedade que o identificador irá alterar (como mostrado abaixo para `layout_marginLeft` a propriedade):

[![Captura de tela mostrando alças de margem no designer](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

Se uma margem já tiver sido definida, as linhas pontilhadas serão exibidas, indicando o espaço ocupado pela margem:

[![Exemplo de linhas pontilhadas marcando espaço em um botão](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>Size

Conforme mencionado anteriormente, você pode alternar para o modo de edição de tamanho clicando em um widget enquanto ele já estiver selecionado. Clique no identificador triangular para definir o tamanho da dimensão indicada para `wrap_content`:

[![Encapsular conteúdo e redimensionar alças](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

Clicar no identificador de **conteúdo de encapsulamento** reduz o widget nessa dimensão para que não seja maior do que o necessário para encapsular o conteúdo incluído. Neste exemplo, o texto do botão é reduzido horizontalmente, conforme mostrado na próxima captura de tela.

Quando o valor do tamanho é definido como **encapsular conteúdo**, o designer exibe um identificador triangular apontando na direção oposta para alterar o tamanho `match_parent`para:

[![Corresponder identificador pai](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

Clicar no identificador **pai de correspondência** restaura o tamanho dessa dimensão para que seja o mesmo que o widget pai.

Além disso, você pode arrastar a alça de redimensionamento circular (conforme mostrado nas capturas de tela acima) para redimensionar o widget para um valor arbitrário `dp` . Quando você fizer isso, ambos os identificadores **pai** de **conteúdo** e correspondência serão apresentados para essa dimensão:

[![Alças de redimensionamento circular](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

Nem todos os contêineres permitem `Size` a edição de um widget. Por exemplo, observe que na captura de tela abaixo com `LinearLayout` a selecionada, as alças de redimensionamento não aparecem:

[![Sem identificadores de redimensionamento](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>Estrutura de Tópicos do Documento

O **contorno do documento** exibe a hierarquia do widget do layout.
No exemplo a seguir, o widget `LinearLayout` recipiente é selecionado:

[![Estrutura de tópicos do documento](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

O contorno do widget selecionado (neste caso, a `LinearLayout`) também é realçado no design Surface. O widget selecionado na estrutura de tópicos do documento permanece em sincronia com sua contraparte na Design Surface. Isso é útil para selecionar grupos de modos de exibição, que nem sempre são fáceis de selecionar na Design Surface.

A estrutura de tópicos do documento dá suporte a copiar e colar, ou você pode usar arrastar e soltar. O recurso arrastar e soltar tem suporte da estrutura de tópicos do documento para a Design Surface, bem como da Design Surface para a estrutura de tópicos do documento. Além disso, clicar com o botão direito do mouse em um item no contorno do documento exibe o menu de contexto desse item (o mesmo menu de contexto que aparece quando você clica com o botão direito do mouse no mesmo widget na Design Surface).

-----
