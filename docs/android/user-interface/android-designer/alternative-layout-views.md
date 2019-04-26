---
title: Exibições de layout alternativos
description: Este tópico explica como os layouts podem ser atualizados usando os qualificadores de recursos. Por exemplo, pode haver uma versão de um layout que é usado somente quando o dispositivo estiver no modo paisagem e uma versão de layout que é apenas para o modo retrato.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 03b80d3fb1ed7c8db108f86b3b3923c20e1d908f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089627"
---
# <a name="alternative-layout-views"></a>Modos de exibição de layout alternativo

_Este tópico explica como você poderá criar layouts de versão por meio qualificadores de recursos. Por exemplo, criando uma versão de um layout que é usado somente quando o dispositivo estiver no modo paisagem e uma versão de layout que é apenas para o modo retrato._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Criação de layouts alternativos

Quando você clica na **exibição de Layout alternativos** ícone (à esquerda do **dispositivo**), um painel de visualização é aberta para listar os layouts alternativos disponíveis em seu projeto. Se não houver nenhum layouts alternativos, o **padrão** exibição é exibida: 

[![Painel de exibição de layout alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "painel de exibição de layout alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Quando você clicar no sinal de adição verde lado **nova versão**, o **criar variação de Layout** caixa de diálogo é aberta para que você possa selecionar os qualificadores de recursos para esta variação de layout: 

[![Criar Variação de layout](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "criar variação de layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

No exemplo a seguir, o qualificador de recurso para **orientação da tela** é definido como **paisagem**e o **tamanho da tela** é alterado para **grande**. Isso cria uma nova versão de layout denominada **grande land**:

[![Terra grande variação](alternative-layout-views-images/vs/03-large-land-sml.png "variação land grande")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Observe que o painel de visualização à esquerda exibe os efeitos das seleções de qualificador de recurso. Clicando em **adicionar** cria o layout alternativo e alterna o Designer para que o layout. O **exibição de Layout alternativa** painel Visualização indica qual layout é carregado no Designer por meio de um pequeno indicador de à direita, conforme indicado na seguinte captura de tela: 

[![Indicador de layout carregado](alternative-layout-views-images/vs/04-new-layout-sml.png "indicador de layout carregado")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)


## <a name="editing-alternative-layouts"></a>Edição de layouts alternativos

Ao criar layouts alternativos, geralmente é desejável para fazer uma única alteração que se aplica a todas as versões com fork de um layout. Por exemplo, você talvez queira alterar o texto do botão para amarelo em todos os layouts. Se você tiver um grande número de layouts e você precisa para se propagar uma única alteração a todas as versões, manutenção pode rapidamente se tornar complicado e sujeito a erros.

Para simplificar a manutenção de várias versões de layout, o Designer fornece uma **editar vários** modo que suas alterações serão propagadas entre um ou mais layouts. Quando mais de um layout estiver presente, o **editar vários** ícone é exibido: 

[![Ícone de edição com vários](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "ícone Editar vários")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Quando você clica o **editar vários** ícone, linhas aparecem que indicam que os layouts são vinculados (conforme mostrado abaixo); ou seja, quando você faz uma alteração em um layout, essa alteração é propagada para os layouts vinculados. Você pode desvincular todos os layouts, clicando no ícone de dentro de um círculo indicado na seguinte captura de tela: 

[![Desvincular todos os layouts](alternative-layout-views-images/vs/06-multi-linked-sml.png "desvincular todos os layouts")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Se você tiver mais de dois layouts, você pode seletivamente alternar o botão Editar para a esquerda de cada visualização de layout para determinar quais layouts estão conectadas entre si. Por exemplo, se você quiser fazer uma única alteração é propagada para o primeiro e último dos três layouts, você primeiro seria desvincular o layout intermediário conforme mostrado aqui: 

[![Desvincular layout de intermediária](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "desvincular layout de intermediária")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

Neste exemplo, uma alteração feita como o **padrão** ou **longo** layout será propagada para o outro layout, mas não para o **grande land** layout.

### <a name="multi-edit-example"></a>Exemplo de edição de várias 

Em geral, quando você faz uma alteração em um layout, essa mesma alteração é propagada para todos os outros layouts vinculados. Por exemplo, adicionando um novo `TextView` widget para o **padrão** layout e alterar seu texto de cadeia de caracteres para `Portrait` fará com que a mesma alteração a serem feitas nos layouts de todos os links. Aqui está como ele aparece na **padrão** layout: 

[![Adicionar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "adicionar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
O `TextView` também é adicionado ao **grande land** layout exibir porque ele está vinculado a **padrão** layout: 

[![Paisagem TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "paisagem TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
Mas e se você quiser fazer uma alteração que é local para apenas um layout (ou seja, você não deseja que a alteração seja propagada para qualquer um dos outros layouts)? Para fazer isso, você deve desvincular o layout que você deseja alterar antes de modificá-lo, conforme explicado a seguir. 

### <a name="making-local-changes"></a>Alterações locais 

Suponha que desejemos que ambos os layouts de ter adicionado `TextView`, mas também queremos alterar a cadeia de caracteres de texto na **grande land** layout para `Landscape` em vez de `Portrait`. Se fizermos essa alteração **grande land** enquanto ambos os layouts são vinculados, a alteração serão propagadas de volta para o **padrão** layout. Portanto, vamos primeiro deve desvincular dois layouts antes de fazermos a alteração. Quando podemos modificar o texto no **grande land** para `Landscape`, o Designer marca essa alteração com um contorno vermelho para indicar que a alteração é local para o **grande land** layout e é *não* propagadas de volta para o **padrão** layout: 

[![Alterar local](alternative-layout-views-images/vs/10-local-change-sml.png "alteração Local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
Quando você clica o **padrão** layout para exibi-lo, o `TextView` cadeia de caracteres de texto ainda está definida como `Portrait`. 

## <a name="handling-conflicts"></a>Tratamento de conflitos 

Se você optar por alterar a cor do texto na **padrão** layout para verde, você verá um ícone de aviso são exibidos no layout vinculado. Clicar em que o layout é aberto o layout para revelar o conflito. O widget que causou o conflito é realçado com um contorno vermelho e a seguinte mensagem é exibida: *Alterações recentes causaram conflitos neste layout alternativo*. 

[![Alteração conflitante](alternative-layout-views-images/vs/11-conflicting-change-sml.png "alteração conflitante")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
Um *caixa de conflito* é exibido à direita do widget para explicar o conflito: 

[![Aviso de conflito](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

A caixa de conflito mostra a lista de propriedades que foram alterados e lista seus valores. Clicando em **ignorar conflito** só se aplica a alteração da propriedade a este widget. Clicando em **Apply** aplica-se a alteração de propriedade para este widget, bem como para o widget de equivalente no vinculada **padrão** layout. Se todas as alterações de propriedade forem aplicadas, o conflito é automaticamente descartado. 

### <a name="view-group-conflicts"></a>Exibir conflitos de grupo 

As alterações de propriedade não são a única fonte de conflitos. Conflitos podem ser detectados durante a inserção ou remoção de widgets. Por exemplo, quando o **grande land** layout é desvinculado da **padrão** layout e o `TextView` no **grande land** layout é arrastado e solto acima a `Button`, o Designer marca o widget movido para indicar que o conflito:

[![Exibir conflitos de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Exibir conflitos de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
No entanto, não há nenhum marcador no `Button`. Embora a posição do `Button` tiver sido alterado, o `Button` mostra as alterações não aplicadas que são específicas para o **grandes land** configuração. 

Se um `CheckBox` é adicionado ao **padrão** layout, outro conflito é gerado e um ícone de aviso é exibido sobre o **grande land** layout: 

[![Conflito de caixa de seleção](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "conflito de caixa de seleção")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
Clicar a **grande land** layout revela o conflito. A seguinte mensagem é exibida: *Alterações recentes causaram conflitos neste layout alternativo*: 

[![Conflito de layout ALT](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "conflito de layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Além disso, a caixa de conflitos exibe a seguinte mensagem:

[![Mensagem de conflito](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Adicionando o `CheckBox` causa um conflito porque o **grande land** layout possui alterações no `LinearLayout` que o contém. No entanto, nesse caso, a caixa de conflitos exibe o widget que acaba de ser inserido na **padrão** layout (o `CheckBox`).

Se você clicar **ignorar conflito**, o Designer de resolve o conflito, permitindo que o widget exibido na caixa de conflito ser arrastadas e soltas no layout de onde o widget está ausente (nesse caso, o **grande land**  layout): 

[![Grupo Conflito resolvido](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "grupo Conflito resolvido")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Como visto no exemplo anterior com o `Button`, o `CheckBox` não tem um marcador de alteração vermelho porque somente o `LinearLayout` tem alterações que foram aplicadas no **grande land** layout.



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Criação de layouts alternativos

Quando você clica na **exibição de Layout alternativos** ícone (à esquerda do **dispositivo**), um painel de visualização é aberta para listar os layouts alternativos disponíveis em seu projeto. Se não houver nenhum layouts alternativos, o **padrão** exibição é exibida: 

[![Painel de exibição de layout alternativo](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Quando você clicar no sinal de adição verde lado **nova versão**, o **criar variação de Layout** caixa de diálogo é aberta para que você possa selecionar os qualificadores de recursos para esta variação de layout: 

[![Criar Variação de layout](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

No exemplo a seguir, o qualificador de recurso para **orientação da tela** é definido como **paisagem**e o **tamanho da tela** é alterado para **grande**. Isso cria uma nova versão de layout denominada **grande land**:

[![Terra grande variação](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Observe que o painel de visualização à esquerda exibe os efeitos das seleções de qualificador de recurso. Clicando em **adicionar** cria o layout alternativo e alterna o Designer para que o layout. O **exibição de Layout alternativa** painel Visualização indica qual layout é carregado no Designer por meio de um pequeno indicador de à direita, conforme indicado na seguinte captura de tela: 

[![Indicador de layout carregado](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Edição de layouts alternativos

Ao criar layouts alternativos, geralmente é desejável para fazer uma única alteração que se aplica a todas as versões com fork de um layout. Por exemplo, você talvez queira alterar o texto do botão para amarelo em todos os layouts. Se você tiver um grande número de layouts e você precisa para se propagar uma única alteração a todas as versões, manutenção pode rapidamente se tornar complicado e sujeito a erros.

Para simplificar a manutenção de várias versões de layout, o Designer fornece uma **editar vários** modo que suas alterações serão propagadas entre um ou mais layouts. Quando mais de um layout estiver presente, o **editar vários** ícone é exibido: 

[![Ícone de edição de várias](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Quando você clica o **editar vários** ícone, linhas aparecem que indicam que os layouts são vinculados (conforme mostrado abaixo); ou seja, quando você faz uma alteração em um layout, essa alteração é propagada para os layouts vinculados. Você pode desvincular todos os layouts, clicando no ícone de dentro de um círculo indicado na seguinte captura de tela: 

[![Desvincular todos os layouts](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Se você tiver mais de dois layouts, você pode seletivamente alternar o botão Editar para a esquerda de cada visualização de layout para determinar quais layouts estão conectadas entre si. Por exemplo, se você quiser fazer uma única alteração é propagada para o primeiro e último dos três layouts, você primeiro seria desvincular o layout intermediário conforme mostrado aqui: 

[![Desvincular layout intermediária](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

Neste exemplo, uma alteração feita como o **padrão** ou **longo** layout será propagada para outro layout, mas não para o **grande land** layout. 

### <a name="multi-edit-example"></a>Exemplo de edição de várias 

Em geral, quando você faz uma alteração em um layout, essa mesma alteração é propagada para todos os outros layouts vinculados. Por exemplo, adicionando um novo `TextView` widget para o **padrão** layout e alterar seu texto de cadeia de caracteres para `Portrait` fará com que a mesma alteração a serem feitas nos layouts de todos os links. Aqui está como ele aparece na **padrão** layout: 

[![Adicionar TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

O `TextView` também é adicionado ao **grande land** layout exibir porque ele está vinculado a **padrão** layout: 

[![Paisagem TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
Mas e se você quiser fazer uma alteração que é local para apenas um layout (ou seja, você não deseja que a alteração seja propagada para qualquer um dos outros layouts)? Para fazer isso, você deve desvincular o layout que você deseja alterar antes de modificá-lo, conforme explicado a seguir. 

### <a name="making-local-changes"></a>Alterações locais 

Suponha que desejemos que ambos os layouts de ter adicionado `TextView`, mas também queremos alterar a cadeia de caracteres de texto na **grande land** layout para `Landscape` em vez de `Portrait`. Se fizermos essa alteração **grande land** enquanto ambos os layouts são vinculados, a alteração serão propagadas de volta para o **padrão** layout. Portanto, vamos primeiro deve desvincular dois layouts antes de fazermos a alteração. Quando podemos modificar o texto no **grande land** para `Landscape`, o Designer marca essa alteração com um contorno vermelho para indicar que a alteração é local para o **grande land** layout e é *não* propagadas de volta para o **padrão** layout: 

[![Alterar local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Quando você clica o **padrão** layout para exibi-lo, o `TextView` cadeia de caracteres de texto ainda está definida como `Portrait`. 

## <a name="handling-conflicts"></a>Tratamento de conflitos 

Se você optar por alterar a cor do texto na **padrão** layout para verde, você verá um ícone de aviso são exibidos no layout vinculado. Clicar em que o layout é aberto o layout para revelar o conflito. O widget que causou o conflito é realçado com um contorno vermelho e a seguinte mensagem é exibida: *Alterações recentes causaram conflitos neste layout alternativo*. 

[![Alteração conflitante](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Um *caixa de conflito* é exibido à direita do widget para explicar o conflito: 

[![Aviso de conflito](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

A caixa de conflito mostra a lista de propriedades que foram alterados e lista seus valores. Clicando em **ignorar conflito** só se aplica a alteração da propriedade a este widget. Clicando em **Apply** aplica-se a alteração de propriedade para este widget, bem como para o widget de equivalente no vinculada **padrão** layout. Se todas as alterações de propriedade forem aplicadas, o conflito é automaticamente descartado. 

### <a name="view-group-conflicts"></a>Exibir conflitos de grupo 

As alterações de propriedade não são a única fonte de conflitos. Conflitos podem ser detectados durante a inserção ou remoção de widgets. Por exemplo, quando o **grande land** layout é desvinculado da **padrão** layout e o `TextView` no **grande land** layout é arrastado e solto acima a `Button`, o Designer marca o widget movido para indicar que o conflito:

[![Conflito de modo de exibição de grupos](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
No entanto, não há nenhum marcador no `Button`. Embora a posição do `Button` tiver sido alterado, o `Button` mostra as alterações não aplicadas que são específicas para o **grandes land** configuração. 

Se um `CheckBox` é adicionado ao **padrão** layout, outro conflito é gerado e um ícone de aviso é exibido sobre o **grande land** layout: 

[![Conflito de caixa de seleção](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
Clicar a **grande land** layout revela o conflito. A seguinte mensagem é exibida: *Alterações recentes causaram conflitos neste layout alternativo*. 

[![Conflito de layout ALT](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
Além disso, a caixa de conflitos exibe a seguinte mensagem:

[![Mensagem de conflito](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Adicionando o `CheckBox` causa um conflito porque o **grande land** layout possui alterações no `LinearLayout` que o contém. No entanto, nesse caso, a caixa de conflitos exibe o widget que acaba de ser inserido na **padrão** layout (o `CheckBox`).

Se você clicar **ignorar conflito**, o Designer de resolve o conflito, permitindo que o widget exibido na caixa de conflito ser arrastadas e soltas no layout de onde o widget está ausente (nesse caso, o **grande land**  layout): 

[![Grupo Conflito resolvido](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
Como visto no exemplo anterior com o `Button`, o `CheckBox` não tem um marcador de alteração vermelho porque somente o `LinearLayout` tem alterações que foram aplicadas no **grande land** layout.


-----


### <a name="conflict-persistence"></a>Persistência de conflito

Conflitos são persistidos no arquivo de layout como comentários XML, conforme mostrado aqui:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Portanto, quando um projeto é fechado e reaberto, todos os conflitos ainda estar lá &ndash; mesmo aqueles que foram ignorados.



