---
title: Exibições de layout alternativos
description: Este tópico explica como os layouts podem ser com controle de versão usando qualificadores de recursos. Por exemplo, pode haver uma versão de um layout que é usada somente quando o dispositivo está no modo paisagem e uma versão de layout somente para o modo retrato.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 017d2d05c04dfaf2378ad1b0129eb4a75be2e777
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019484"
---
# <a name="alternative-layout-views"></a>Modos de exibição de layout alternativos

_Este tópico explica como você pode obter layouts de versão usando qualificadores de recursos. Por exemplo, a criação de uma versão de um layout que é usada somente quando o dispositivo está no modo paisagem e uma versão de layout somente para o modo retrato._

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>Criando layouts alternativos

Quando você clica no ícone de **exibição de layout alternativo** (à esquerda do **dispositivo**), um painel de visualização é aberto para listar os layouts alternativos disponíveis no seu projeto. Se não houver layouts alternativos, a exibição **padrão** será apresentada: 

[![Painel de exibição de layout alternativo](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "Painel de exibição de layout alternativo")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

Quando você clica no sinal de adição verde ao lado de **nova versão**, a caixa de diálogo **criar variação de layout** é aberta para que você possa selecionar os qualificadores de recursos para esta variação de layout: 

[![Criar variação de layout](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "Criar variação de layout")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

No exemplo a seguir, o qualificador de recurso para a **orientação de tela** é definido como **paisagem**e o **tamanho da tela** é alterado para **grande**. Isso cria uma nova versão de layout chamada **Large-Land**:

[![Variação em grande escala](alternative-layout-views-images/vs/03-large-land-sml.png "Variação em grande escala")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

Observe que o painel de visualização à esquerda exibe os efeitos das seleções de qualificador de recurso. Clicar em **Adicionar** cria o layout alternativo e alterna o designer para esse layout. O painel de visualização de **exibição de layout alternativo** indica qual layout é carregado no designer por meio de um pequeno ponteiro à direita, conforme indicado na seguinte captura de tela: 

[![Indicador de layout carregado](alternative-layout-views-images/vs/04-new-layout-sml.png "Indicador de layout carregado")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Editando layouts alternativos

Quando você cria layouts alternativos, muitas vezes é desejável fazer uma única alteração que se aplica a todas as versões bifurcadas de um layout. Por exemplo, talvez você queira alterar o texto do botão para amarelo em todos os layouts. Se você tiver um grande número de layouts e precisar propagar uma única alteração para todas as versões, a manutenção poderá rapidamente se tornar complicada e propenso a erros.

Para simplificar a manutenção de várias versões de layout, o designer fornece um modo de **várias edições** que propaga as alterações em um ou mais layouts. Quando mais de um layout estiver presente, o ícone de **várias edições** será exibido: 

[![Ícone de várias edições](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "Ícone de várias edições")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

Quando você clica no ícone de **várias edições** , aparecem linhas que indicam que os layouts estão vinculados (como mostrado abaixo); ou seja, quando você faz uma alteração em um layout, essa alteração é propagada para todos os layouts vinculados. Você pode desvincular todos os layouts clicando no ícone de círculo indicado na seguinte captura de tela: 

[![Desvincular todos os layouts](alternative-layout-views-images/vs/06-multi-linked-sml.png "Desvincular todos os layouts")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

Se você tiver mais de dois layouts, poderá alternar seletivamente o botão Editar à esquerda de cada visualização de layout para determinar quais layouts estão vinculados. Por exemplo, se você quiser fazer uma única alteração que se propaga para o primeiro e o último dos três layouts, primeiro desvinculo o layout do meio, conforme mostrado aqui: 

[![Desvincular layout do meio](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "Desvincular layout do meio")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

Neste exemplo, uma alteração feita no layout **padrão** ou **longo** será propagada para o outro layout, mas não para o layout de **grande terra** .

### <a name="multi-edit-example"></a>Exemplo de várias edições 

Em geral, quando você faz uma alteração em um layout, essa mesma alteração é propagada para todos os outros layouts vinculados. Por exemplo, adicionar um novo widget de `TextView` ao layout **padrão** e alterar sua cadeia de texto para `Portrait` fará com que a mesma alteração seja feita em todos os layouts vinculados. Veja como ele se parece no layout **padrão** : 

[![Adicionar TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "Adicionar TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

A `TextView` também é adicionada à exibição de layout de **grande terra** porque está vinculada ao layout **padrão** : 

[![TextView paisagem](alternative-layout-views-images/vs/09-landscape-textview-sml.png "TextView paisagem")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

Mas e se você quiser fazer uma alteração que seja local para apenas um layout (ou seja, você não quer que a alteração seja propagada para qualquer um dos outros layouts)? Para fazer isso, você deve desvincular o layout que deseja alterar antes de modificá-lo, conforme explicado a seguir. 

### <a name="making-local-changes"></a>Fazendo alterações locais 

Suponha que queremos que os dois layouts tenham o `TextView` adicionado, mas também queremos alterar a cadeia de caracteres de texto no layout de **grande terra** para `Landscape` em vez de `Portrait`. Se fizermos essa alteração em **grande escala** enquanto os dois layouts estiverem vinculados, a alteração será propagada de volta para o layout **padrão** . Portanto, devemos primeiro desvincular os dois layouts antes de fazermos a alteração. Quando modificamos o texto em **grande escala** para `Landscape`, o designer marca essa alteração com um quadro vermelho para indicar que a alteração é local para o layout de **grande terra** e *não* é propagada de volta para o layout **padrão** : 

[![Alteração local](alternative-layout-views-images/vs/10-local-change-sml.png "Alteração local")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

Quando você clica no layout **padrão** para exibi-lo, o `TextView` cadeia de caracteres de texto ainda é definido como `Portrait`. 

## <a name="handling-conflicts"></a>Manipulando conflitos 

Se você decidir alterar a cor do texto no layout **padrão** para verde, verá um ícone de aviso aparecendo no layout vinculado. Clicar nesse layout abre o layout para revelar o conflito. O widget que causou o conflito é realçado com um quadro vermelho e a seguinte mensagem é exibida: *alterações recentes causaram conflitos nesse layout alternativo*. 

[![Alteração conflitante](alternative-layout-views-images/vs/11-conflicting-change-sml.png "Alteração conflitante")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

Uma *caixa de conflito* é exibida à direita do widget para explicar o conflito: 

[![aviso de conflito](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

A caixa conflito mostra a lista de propriedades que foram alteradas e lista seus valores. Clicar em **ignorar conflito** aplica a propriedade alterar somente para este widget. Clicar em **aplicar** aplica a alteração de propriedade a esse widget, bem como ao widget correspondente no layout **padrão** vinculado. Se todas as alterações de propriedade forem aplicadas, o conflito será descartado automaticamente. 

### <a name="view-group-conflicts"></a>Exibir conflitos de grupo 

As alterações de propriedade não são a única fonte de conflitos. Os conflitos podem ser detectados ao inserir ou remover widgets. Por exemplo, quando o layout de **grande terra** é desvinculado do layout **padrão** , e a `TextView` no layout de **grande terra** é arrastada e descartada acima da `Button`, o designer marca o widget movido para indicar o conflito:

[![Exibir conflito de grupo](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "Exibir conflito de grupo")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

No entanto, não há nenhum marcador no `Button`. Embora a posição da `Button` tenha sido alterada, a `Button` não mostra nenhuma alteração aplicada específica à configuração de **grande terra** . 

Se um `CheckBox` for adicionado ao layout **padrão** , outro conflito será gerado e um ícone de aviso será exibido sobre o layout de **grande terra** : 

[![Conflito de caixa de seleção](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "Conflito de caixa de seleção")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

Clicar no layout de **grande terra** revela o conflito. A seguinte mensagem é exibida: *alterações recentes causaram conflitos neste layout alternativo*: 

[![Conflito de layout Alt](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Conflito de layout Alt")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

Além disso, a caixa conflito exibe a seguinte mensagem:

[![mensagem de conflito](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Adicionar o `CheckBox` causa um conflito porque o layout de **grande terra** tem alterações no `LinearLayout` que o contém. No entanto, nesse caso, a caixa conflito exibe o widget que acabou de ser inserido no layout **padrão** (o `CheckBox`).

Se você clicar em **ignorar conflito**, o designer resolverá o conflito, permitindo que o widget exibido na caixa de conflitos seja arrastado e solto no layout onde o widget está ausente (nesse caso, o layout de **grande terra** ): 

[![Conflito de grupo resolvido](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "Conflito de grupo resolvido")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

Como visto no exemplo anterior com a `Button`, o `CheckBox` não tem um marcador de alteração vermelho porque apenas os `LinearLayout` têm alterações que foram aplicadas no layout de **grande terra** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>Criando layouts alternativos

Quando você clica no ícone de **exibição de layout alternativo** (à esquerda do **dispositivo**), um painel de visualização é aberto para listar os layouts alternativos disponíveis no seu projeto. Se não houver layouts alternativos, a exibição **padrão** será apresentada: 

[painel de exibição de layout alternativo![](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

Quando você clica no sinal de adição verde ao lado de **nova versão**, a caixa de diálogo **criar variação de layout** é aberta para que você possa selecionar os qualificadores de recursos para esta variação de layout: 

[![criar variação de layout](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

No exemplo a seguir, o qualificador de recurso para a **orientação de tela** é definido como **paisagem**e o **tamanho da tela** é alterado para **grande**. Isso cria uma nova versão de layout chamada **Large-Land**:

[![variação em grande escala](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

Observe que o painel de visualização à esquerda exibe os efeitos das seleções de qualificador de recurso. Clicar em **Adicionar** cria o layout alternativo e alterna o designer para esse layout. O painel de visualização de **exibição de layout alternativo** indica qual layout é carregado no designer por meio de um pequeno ponteiro à direita, conforme indicado na seguinte captura de tela: 

[![indicador de layout carregado](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>Editando layouts alternativos

Quando você cria layouts alternativos, muitas vezes é desejável fazer uma única alteração que se aplica a todas as versões bifurcadas de um layout. Por exemplo, talvez você queira alterar o texto do botão para amarelo em todos os layouts. Se você tiver um grande número de layouts e precisar propagar uma única alteração para todas as versões, a manutenção poderá rapidamente se tornar complicada e propenso a erros.

Para simplificar a manutenção de várias versões de layout, o designer fornece um modo de **várias edições** que propaga as alterações em um ou mais layouts. Quando mais de um layout estiver presente, o ícone de **várias edições** será exibido: 

[![ícone de várias edições](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

Quando você clica no ícone de **várias edições** , aparecem linhas que indicam que os layouts estão vinculados (como mostrado abaixo); ou seja, quando você faz uma alteração em um layout, essa alteração é propagada para todos os layouts vinculados. Você pode desvincular todos os layouts clicando no ícone de círculo indicado na seguinte captura de tela: 

[![desvincular todos os layouts](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

Se você tiver mais de dois layouts, poderá alternar seletivamente o botão Editar à esquerda de cada visualização de layout para determinar quais layouts estão vinculados. Por exemplo, se você quiser fazer uma única alteração que se propaga para o primeiro e o último dos três layouts, primeiro desvinculo o layout do meio, conforme mostrado aqui: 

[![desvincular o layout do meio](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

Neste exemplo, uma alteração feita no layout **padrão** ou **longo** será propagada para outro layout, mas não para o layout de **grande terra** . 

### <a name="multi-edit-example"></a>Exemplo de várias edições 

Em geral, quando você faz uma alteração em um layout, essa mesma alteração é propagada para todos os outros layouts vinculados. Por exemplo, adicionar um novo widget de `TextView` ao layout **padrão** e alterar sua cadeia de texto para `Portrait` fará com que a mesma alteração seja feita em todos os layouts vinculados. Veja como ele se parece no layout **padrão** : 

[![adicionar TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

A `TextView` também é adicionada à exibição de layout de **grande terra** porque está vinculada ao layout **padrão** : 

[TextView paisagem![](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

Mas e se você quiser fazer uma alteração que seja local para apenas um layout (ou seja, você não quer que a alteração seja propagada para qualquer um dos outros layouts)? Para fazer isso, você deve desvincular o layout que deseja alterar antes de modificá-lo, conforme explicado a seguir. 

### <a name="making-local-changes"></a>Fazendo alterações locais 

Suponha que queremos que os dois layouts tenham o `TextView` adicionado, mas também queremos alterar a cadeia de caracteres de texto no layout de **grande terra** para `Landscape` em vez de `Portrait`. Se fizermos essa alteração em **grande escala** enquanto os dois layouts estiverem vinculados, a alteração será propagada de volta para o layout **padrão** . Portanto, devemos primeiro desvincular os dois layouts antes de fazermos a alteração. Quando modificamos o texto em **grande escala** para `Landscape`, o designer marca essa alteração com um quadro vermelho para indicar que a alteração é local para o layout de **grande terra** e *não* é propagada de volta para o layout **padrão** : 

[![alteração local](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

Quando você clica no layout **padrão** para exibi-lo, o `TextView` cadeia de caracteres de texto ainda é definido como `Portrait`. 

## <a name="handling-conflicts"></a>Manipulando conflitos 

Se você decidir alterar a cor do texto no layout **padrão** para verde, verá um ícone de aviso aparecendo no layout vinculado. Clicar nesse layout abre o layout para revelar o conflito. O widget que causou o conflito é realçado com um quadro vermelho e a seguinte mensagem é exibida: *alterações recentes causaram conflitos nesse layout alternativo*. 

[![alteração conflitante](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

Uma *caixa de conflito* é exibida à direita do widget para explicar o conflito: 

[![aviso de conflito](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

A caixa conflito mostra a lista de propriedades que foram alteradas e lista seus valores. Clicar em **ignorar conflito** aplica a propriedade alterar somente para este widget. Clicar em **aplicar** aplica a alteração de propriedade a esse widget, bem como ao widget correspondente no layout **padrão** vinculado. Se todas as alterações de propriedade forem aplicadas, o conflito será descartado automaticamente. 

### <a name="view-group-conflicts"></a>Exibir conflitos de grupo 

As alterações de propriedade não são a única fonte de conflitos. Os conflitos podem ser detectados ao inserir ou remover widgets. Por exemplo, quando o layout de **grande terra** é desvinculado do layout **padrão** , e a `TextView` no layout de **grande terra** é arrastada e descartada acima da `Button`, o designer marca o widget movido para indicar o conflito:

[conflito de![exibir grupo](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

No entanto, não há nenhum marcador no `Button`. Embora a posição da `Button` tenha sido alterada, a `Button` não mostra nenhuma alteração aplicada específica à configuração de **grande terra** . 

Se um `CheckBox` for adicionado ao layout **padrão** , outro conflito será gerado e um ícone de aviso será exibido sobre o layout de **grande terra** : 

[conflito de caixa de seleção de![](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

Clicar no layout de **grande terra** revela o conflito. A seguinte mensagem é exibida: *alterações recentes causaram conflitos neste layout alternativo*. 

[conflito de layout de![Alt](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

Além disso, a caixa conflito exibe a seguinte mensagem:

[![mensagem de conflito](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Adicionar o `CheckBox` causa um conflito porque o layout de **grande terra** tem alterações no `LinearLayout` que o contém. No entanto, nesse caso, a caixa conflito exibe o widget que acabou de ser inserido no layout **padrão** (o `CheckBox`).

Se você clicar em **ignorar conflito**, o designer resolverá o conflito, permitindo que o widget exibido na caixa de conflitos seja arrastado e solto no layout onde o widget está ausente (nesse caso, o layout de **grande terra** ): 

[![conflito de grupo resolvido](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

Como visto no exemplo anterior com a `Button`, o `CheckBox` não tem um marcador de alteração vermelho porque apenas os `LinearLayout` têm alterações que foram aplicadas no layout de **grande terra** .

-----

### <a name="conflict-persistence"></a>Persistência de conflito

Os conflitos são persistidos no arquivo de layout como comentários XML, como mostrado aqui:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Portanto, quando um projeto é fechado e reaberto, todos os conflitos ainda estarão lá &ndash; mesmo aqueles que foram ignorados.
