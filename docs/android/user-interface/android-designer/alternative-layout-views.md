---
title: Modos de exibição de Layout alternativo
description: Este tópico explica como os layouts podem ser com controle de versão usando os qualificadores de recursos. Por exemplo, pode haver uma versão de um layout que é usado somente quando o dispositivo estiver no modo paisagem e uma versão de layout é apenas para o modo retrato.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: d2228169ed5d8575c9e332c85d963fca0400dea8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="alternative-layout-views"></a>Modos de exibição de Layout alternativo

_Este tópico explica como os layouts podem ser com controle de versão usando os qualificadores de recursos. Por exemplo, pode haver uma versão de um layout que é usado somente quando o dispositivo estiver no modo paisagem e uma versão de layout é apenas para o modo retrato._


## <a name="creating-alternative-layouts"></a>Criar Layouts alternativos

Quando você clica o **alternativo de exibição de Layout** ícone (à esquerda do **dispositivo**), um painel de visualização é aberta para listar os layouts alternativos disponíveis em seu projeto. Se não houver nenhum layouts alternativos, o **padrão** exibição é apresentada: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Painel de exibição de layout alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "painel de exibição de layout alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Painel de exibição de layout alternativo](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

Quando você clique no sinal de mais verde ao lado de **nova versão**, o **criar Layout variação** caixa de diálogo é aberta para que você possa selecionar os qualificadores de recursos para esta variação de layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Criar layout variação](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "criar variação de layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Criar Variação de layout](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


No exemplo a seguir, o qualificador de recurso para **orientação da tela** é definido como **paisagem**e o **tamanho da tela** é alterado para **grande**. Isso cria uma nova versão de layout denominada **grande Terra**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Terra grande variação](alternative-layout-views-images/vs/03-large-land-sml.png "variação de terra grande")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Terra grande variação](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


Observe que o painel de visualização à esquerda exibe os efeitos das seleções de qualificador de recurso. Clicando em **adicionar** cria o layout alternativo e alterna o Designer para que o layout. O **exibição de Layout de alternativa** painel Visualização indica qual layout é carregado no Designer por meio de um pequeno indicador direita conforme indicado na captura de tela a seguir: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Indicador de layout carregado](alternative-layout-views-images/vs/04-new-layout-sml.png "indicador de layout carregado")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Indicador de layout carregado](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>Edição de Layouts alternativos

Ao criar layouts alternativos, muitas vezes é desejável para fazer uma única alteração se aplica a todas as versões bifurcadas de um layout. Por exemplo, convém alterar o texto do botão para amarelo em todos os layouts. Se você tiver um grande número de layouts, manutenção pode rapidamente se tornar complicada e propensas a erros se você precisar propagar uma alteração única para todas as versões. 

Para simplificar a manutenção de várias versões de layout, o Designer fornece uma **editar vários** modo que suas alterações serão propagadas em um ou mais layouts. Quando mais de um layout estiver presente, o **editar vários** ícone é exibido: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Editar vários](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "editar vários")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Editar vários](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


Quando você clica o **editar vários** ícone linhas aparecem que indicam que os layouts são vinculados (conforme mostrado abaixo); ou seja, quando você fizer uma alteração em um layout, essa alteração será propagada para os layouts vinculados. Desvincule todos os layouts, clicando no ícone de dentro de um círculo indicado na captura de tela a seguir: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Desvincular todos os layouts](alternative-layout-views-images/vs/06-multi-linked-sml.png "desvincular todos os layouts")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Desvincular todos os layouts](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


Se você tiver mais de dois layouts, você pode alternar seletivamente o botão Editar para a esquerda de cada visualização de layout para determinar quais layouts são vinculados. Por exemplo, se você quiser fazer uma alteração simples que se propaga para o primeiro e último três layouts, deve primeiro desvincular o layout intermediário conforme mostrado aqui: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout intermediária desvincular](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "layout intermediária desvincular")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Desvincular layout intermediária](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

Neste exemplo, uma alteração feita para o **padrão** ou **longo** layout será propagado para outros layout, mas não para o **grande Terra** layout. 



### <a name="multi-edit-example"></a>Exemplo de multi-editar 

Em geral, quando você fizer uma alteração em um layout, essa mesma alteração é propagada para todos os outros layouts vinculados. Por exemplo, adicionando um novo `TextView` widget para o **padrão** layout e alterar seu texto de cadeia de caracteres para `Portrait` fará com que a mesma alteração a serem feitas nos layouts de todos os links. Aqui está como ele aparece no **padrão** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Adicionar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "adicionar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Adicionar TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

O `TextView` também é adicionado ao **grande Terra** layout exibir porque ele está vinculado a **padrão** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Paisagem TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "paisagem TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![TextView paisagem](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

Mas e se você quiser fazer uma alteração que é local para apenas um layout (ou seja, você não quiser a alteração ser propagadas para qualquer outro layout de)? Para fazer isso, você precisa desvincular o layout que você deseja alterar antes de modificá-lo, conforme explicado a seguir. 



### <a name="making-local-changes"></a>Alterações locais 

Vamos supor que queremos que ambos os layouts de ter adicionado `TextView`, mas queremos alterar a cadeia de caracteres de texto no **grande Terra** layout `Landscape` em vez de `Portrait`. Caso essa alteração **grande Terra** enquanto ambos os layouts são vinculados, as alterações serão propagadas para o **padrão** layout. Portanto, vamos primeiro deve desvincular dois layouts antes de fazermos a alteração. Quando é modificar o texto na **grande Terra** para `Landscape`, o Designer marca variam de acordo com um quadro vermelho para indicar que a alteração é local para o **grande Terra** layout e *não* propagadas de volta para o **padrão** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Alterar local](alternative-layout-views-images/vs/10-local-change-sml.png "alteração Local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Alterar local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

Quando você clica o **padrão** layout para exibi-lo, o `TextView` cadeia de caracteres de texto ainda está definida `Portrait`. 



## <a name="handling-conflicts"></a>Controlando conflitos 

Se você decidir alterar a cor do texto no **padrão** layout para verde, você verá um ícone de aviso aparecer no layout vinculado. Clicar nesse layout abre o layout para revelar o conflito. O widget que causou o conflito é realçado com um quadro vermelho e a seguinte mensagem é exibida: *alterações recentes causaram conflitos neste layout alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Alteração conflitante](alternative-layout-views-images/vs/11-conflicting-change-sml.png "alteração conflitante")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Alteração conflitante](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

Um *caixa conflito* é exibido à direita do widget para explicar o conflito: 

[![Aviso de conflito](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

A caixa de conflito mostra a lista de propriedades que foram alterados e lista seus valores. Clicando em **conflito ignorar** só se aplica a alteração da propriedade a este widget. Clicando em **aplicar** aplica-se a alteração da propriedade para este widget bem como o widget correspondente no vinculado **padrão** layout. Se todas as alterações de propriedade são aplicadas, o conflito é automaticamente descartado. 


### <a name="view-group-conflicts"></a>Conflitos de grupo do modo de exibição 

Alterações de propriedade não são a única fonte de conflitos. Conflitos podem ser detectados durante a inserção ou remoção de widgets. Por exemplo, quando o **grande Terra** layout é desvinculado do **padrão** layout e o `TextView` no **grande Terra** layout é arrastado e solto acima a `Button`, o Designer marca o widget movido para indicar o conflito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Exibir conflitos de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Exibir conflitos de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Conflito de grupo do modo de exibição](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

No entanto, não há nenhum marcador no `Button`. Embora a posição do `Button` foi alterado, o `Button` não mostra nenhuma alteração aplicada que são específicas para o **grande Terra** configuração. 

Se um `CheckBox` é adicionado ao **padrão** layout, outro conflito será gerado e um ícone de aviso será exibido no **grande Terra** layout: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Conflito de caixa de seleção](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflito de caixa de seleção")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Conflito de caixa de seleção](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

Clique o **grande Terra** layout revela o conflito. A seguinte mensagem é exibida: *alterações recentes causaram conflitos neste layout alternativo*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Conflito de layout ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflito de layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Conflito de layout ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

Além disso, a caixa de conflitos exibe a seguinte mensagem:

[![Mensagem de conflito](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Adicionando o `CheckBox` causa um conflito porque o **grande Terra** layout possui alterações no `LinearLayout` que o contém. No entanto, nesse caso a caixa de conflitos exibe o widget que acaba de ser inserido no **padrão** layout (o `CheckBox`).

Se você clicar em **conflito ignorar**, o Designer resolverá o conflito, permitindo que o widget exibido na caixa de conflito ser arrastadas e soltas no layout de onde o widget está ausente (nesse caso, o **Terra grande**  layout): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Resolver conflitos de grupo](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "resolver conflitos de grupo")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Resolver conflitos de grupo](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

Conforme visto no exemplo anterior com o `Button`, o `CheckBox` não tem um marcador vermelho alteração porque somente o `LinearLayout` tem alterações que foram aplicadas no **grande Terra** layout.



### <a name="conflict-persistence"></a>Persistência de conflito

Conflitos são mantidos no arquivo de layout como comentários XML, conforme mostrado aqui:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Portanto, quando um projeto for fechado e reaberto, todos os conflitos ainda estejam lá &ndash; até mesmo aqueles que foram ignorados.

