---
title: Partes e funcionalidades do RecyclerView
description: Uma visão geral do Gerenciador de layout RecyclerView, do adaptador e do detentor da exibição.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: bea058a1f275d6f02fe4cbdf70f8e47a11d1cf8e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764119"
---
# <a name="recyclerview-parts-and-functionality"></a>Partes e funcionalidades do RecyclerView

`RecyclerView`manipula algumas tarefas internamente (como a rolagem e reciclagem de exibições), mas é essencialmente um gerente que coordena classes auxiliares para exibir uma coleção. `RecyclerView`delega tarefas para as seguintes classes auxiliares:

- **`Adapter`** O cria layouts de item (instancia o conteúdo de um arquivo de layout) e associa dados a exibições que são exibidas em um `RecyclerView`. &ndash; O adaptador também relata eventos de clique de item.

- **`LayoutManager`** Mede e posiciona modos de exibição de `RecyclerView` item dentro de um e gerencia a política para a reciclagem da exibição. &ndash;

- **`ViewHolder`** &ndash; Pesquisa e armazena referências de exibição. O detentor da exibição também ajuda na detecção de cliques de exibição de item.

- **`ItemDecoration`** &ndash; Permite que um aplicativo adicione deslocamentos especiais de desenho e layout a modos de exibição específicos para desenhar divisores entre itens, destaques e limites de agrupamento Visual.

- **`ItemAnimator`** &ndash; Define as animações que ocorrem durante as ações do item ou conforme são feitas alterações no adaptador.

A relação entre as `RecyclerView`classes `LayoutManager`, e `Adapter` é representada no diagrama a seguir:

![Diagrama de RecyclerView que contém o LayoutManager, usando o adaptador para acessar o conjunto de dados](parts-and-functionality-images/01-recyclerview-diagram.png)

Como a figura ilustra, o `LayoutManager` pode ser considerado o intermediário entre o `Adapter` e o `RecyclerView`. O `LayoutManager` faz chamadas em `Adapter` `RecyclerView`métodos em nome do. Por exemplo, o `LayoutManager` chama um `Adapter` método quando é hora de criar uma nova exibição para uma posição de item `RecyclerView`específica no. O `Adapter` analisa o layout desse item e cria uma `ViewHolder` instância (não mostrada) para armazenar em cache as referências às exibições nessa posição. Quando o `LayoutManager` chama o `Adapter` para associar um item específico ao conjunto de dados, o `Adapter` localiza os dados desse item, recupera-os do conjunto de dados e os copia para a exibição de item associada.

Ao usar `RecyclerView` em seu aplicativo, é necessário criar tipos derivados das seguintes classes:

- **`RecyclerView.Adapter`** Fornece uma associação do conjunto de dados do aplicativo (que é específico ao seu aplicativo) para exibições de item que são exibidas `RecyclerView`no. &ndash; O adaptador sabe como associar cada posição de exibição de item no `RecyclerView` a um local específico na fonte de dados. Além disso, o adaptador manipula o layout do conteúdo em cada exibição de item individual e cria o detentor da exibição para cada exibição. O adaptador também relata eventos de clique de item detectados pela exibição de item.

- **`RecyclerView.ViewHolder`** &ndash; Armazena em cache as referências às exibições em seu arquivo de layout de item para que as pesquisas de recurso não sejam repetidas desnecessariamente. O detentor da exibição também organiza os eventos de clique de item a serem encaminhados para o adaptador quando um usuário toca na exibição de item associada do detentor da exibição.

- **`RecyclerView.LayoutManager`** Posiciona os itens dentro `RecyclerView`do. &ndash; Você pode usar um dos vários gerenciadores de layout predefinidos ou pode implementar seu próprio Gerenciador de layout personalizado.
    `RecyclerView`Delega a política de layout ao Gerenciador de layout para que você possa conectar um Gerenciador de layout diferente sem precisar fazer alterações significativas em seu aplicativo.

Além disso, você pode, opcionalmente, estender as seguintes classes para alterar a aparência `RecyclerView` de em seu aplicativo:

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Se você não estender `ItemDecoration` e `ItemAnimator`, `RecyclerView` o usará as implementações padrão. Este guia não explica como criar `ItemDecoration` classes e `ItemAnimator` personalizadas; para obter mais informações sobre essas classes, consulte [RecyclerView. dodecoração](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

<a name="recycling" />

### <a name="how-view-recycling-works"></a>Como funciona a reciclagem de exibição

`RecyclerView`não aloca uma exibição de item para cada item na fonte de dados. Em vez disso, ele aloca apenas o número de exibições de item que se ajustam à tela e reutiliza os layouts de item à medida que o usuário rola. Quando a exibição é rolada pela primeira vez, ela passa pelo processo de reciclagem ilustrado na figura a seguir:

[![Diagrama ilustrando as seis etapas da reciclagem da exibição](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Quando uma exibição rola para fora da visão e não é mais exibida, ela se torna uma *exibição de sucata*.

2. A exibição de sucata é colocada em um pool e torna-se uma *exibição de reciclagem*.
    Esse pool é um cache de exibições que exibem o mesmo tipo de dados.

3. Quando um novo item é exibido, uma exibição é retirada do pool de reciclagem para reutilização. Como essa exibição deve ser vinculada novamente pelo adaptador antes de ser exibida, ela é chamada de *exibição suja*.

4. A exibição suja é reciclada: o adaptador localiza os dados para o próximo item a ser exibido e copia esses dados para os modos de exibição deste item. As referências para essas exibições são recuperadas do detentor da exibição associado ao modo de exibição reciclado.

5. A exibição reciclada é adicionada à lista de itens no `RecyclerView` que está prestes a ir na tela.

6. A exibição reciclada é exibida na tela enquanto o usuário rola `RecyclerView` para o próximo item da lista. Enquanto isso, outra exibição rola de visão e é reciclada de acordo com as etapas acima.

Além da reutilização de modo de exibição `RecyclerView` de item, o também usa outra otimização de eficiência: exibir os contentores. Um *detentor de exibição* é uma classe simples que armazena em cache referências de exibição. Cada vez que o adaptador inplana um arquivo de layout de item, ele também cria um detentor de exibição correspondente. O detentor da exibição `FindViewById` usa para obter referências às exibições dentro do arquivo de layout de item inplano. Essas referências são usadas para carregar novos dados nas exibições toda vez que o layout é reciclado para mostrar novos dados.

### <a name="the-layout-manager"></a>O Gerenciador de layout

O Gerenciador de layout é responsável por posicionar itens `RecyclerView` na exibição; ele determina o tipo de apresentação (uma lista ou uma grade), a orientação (se os itens são exibidos vertical ou horizontalmente) e quais itens de direção devem ser exibidos (em ordem normal ou em ordem inversa). O Gerenciador de layout também é responsável por calcular o tamanho e a posição de cada item na exibição **RecycleView** .

O Gerenciador de layout tem uma finalidade adicional: determina a política para quando reciclar exibições de item que não são mais visíveis para o usuário.
Como o Gerenciador de layout está ciente das exibições que estão visíveis (e quais não são), ela está na melhor posição para decidir quando uma exibição pode ser reciclada. Para reciclar um modo de exibição, o Gerenciador de layout normalmente faz chamadas para o adaptador para substituir o conteúdo de uma exibição reciclada com dados diferentes, conforme descrito anteriormente em [como a reciclagem de exibição funciona](#recycling).

Você pode estender `RecyclerView.LayoutManager` para criar seu próprio Gerenciador de layout ou pode usar um Gerenciador de layout predefinido. `RecyclerView`fornece os seguintes gerenciadores de layout predefinidos:

- **`LinearLayoutManager`** &ndash; Organiza os itens em uma coluna que pode ser rolada verticalmente ou em uma linha que pode ser rolada horizontalmente.

- **`GridLayoutManager`** &ndash; Exibe itens em uma grade.

- **`StaggeredGridLayoutManager`** &ndash; Exibe os itens em uma grade escalonada, em que alguns itens têm alturas e larguras diferentes.

Para especificar o Gerenciador de layout, crie uma instância do Gerenciador de layout escolhido e `SetLayoutManager` passe-o para o método. Observe que você *deve* especificar que o Gerenciador &ndash; `RecyclerView` de layout não selecione um Gerenciador de layout predefinido por padrão.

Para obter mais informações sobre o Gerenciador de layout, consulte a [referência de classe RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

### <a name="the-view-holder"></a>O detentor da exibição

O detentor da exibição é uma classe que você define para as referências de exibição de cache. O adaptador usa essas referências de exibição para associar cada exibição ao seu conteúdo. Cada item no `RecyclerView` tem uma instância do detentor de exibição associada que armazena em cache as referências de exibição para esse item. Para criar um detentor da exibição, use as etapas a seguir para definir uma classe para manter o conjunto exato de exibições por item:

1. Subclasse `RecyclerView.ViewHolder`.
2. Implemente um construtor que pesquise e armazene as referências de exibição.
3. Implemente propriedades que o adaptador pode usar para acessar essas referências.

Um exemplo detalhado de uma `ViewHolder` implementação é apresentado em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obter mais informações `RecyclerView.ViewHolder`sobre, consulte a [referência de classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="the-adapter"></a>O adaptador

A maior parte do "trabalho pesado" do código `RecyclerView` de integração ocorre no adaptador. `RecyclerView`exige que você forneça um adaptador derivado do `RecyclerView.Adapter` para acessar sua fonte de dados e popular cada item com o conteúdo da fonte de dados.
Como a fonte de dados é específica do aplicativo, você deve implementar a funcionalidade do adaptador que entenda como acessar seus dados. O adaptador extrai informações da fonte de dados e carrega-as em cada item da `RecyclerView` coleção.

O desenho a seguir ilustra como o adaptador mapeia o conteúdo em uma fonte de dados por meio de contentores para modos de exibição `RecyclerView`individuais dentro de cada item de linha no:

[![Diagrama que ilustra a fonte de dados de conexão do adaptador para ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

O adaptador carrega cada `RecyclerView` linha com dados para um item de linha específico. Para a posição de linha *P*, por exemplo, o adaptador localiza os dados associados na posição *p* na fonte de dados e copia esses dados para o item de linha na posição *p* na `RecyclerView` coleção.
No desenho acima, por exemplo, o adaptador usa o detentor da exibição para pesquisar as referências para o `ImageView` e `TextView` nessa posição para que ele não precise chamar `FindViewById` repetidamente para essas exibições à medida que o usuário rola pela coleção e reutiliza exibições.

Ao implementar um adaptador, você deve substituir os seguintes `RecyclerView.Adapter` métodos:

- **`OnCreateViewHolder`** &ndash; Instancia o arquivo de layout de item e o detentor da exibição.

- **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada nas exibições cujas referências são armazenadas no espaço de exibição fornecido.

- **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto está posicionando itens dentro `RecyclerView`do. 

### <a name="notifying-recyclerview-of-data-changes"></a>Notificando RecyclerView de alterações de dados

`RecyclerView`Não atualiza automaticamente sua exibição quando o conteúdo de sua fonte de dados é alterado; o adaptador deve notificar `RecyclerView` quando há uma alteração no conjunto de dados. O conjunto de dados pode ser alterado de várias maneiras; por exemplo, o conteúdo dentro de um item pode ser alterado ou a estrutura geral dos dados pode ser alterada.
`RecyclerView.Adapter`fornece vários métodos que você pode chamar para que `RecyclerView` respondam às alterações de dados da maneira mais eficiente:

- **`NotifyItemChanged`** &ndash; Sinaliza que o item na posição especificada foi alterado.

- **`NotifyItemRangeChanged`** &ndash; Sinaliza que os itens no intervalo especificado de posições foram alterados.

- **`NotifyItemInserted`** &ndash; Sinaliza que o item na posição especificada foi recentemente inserido.

- **`NotifyItemRangeInserted`** &ndash; Sinaliza que os itens no intervalo especificado de posições foram inseridos recentemente.

- **`NotifyItemRemoved`** &ndash; Sinaliza que o item na posição especificada foi removido.

- **`NotifyItemRangeRemoved`** &ndash; Sinaliza que os itens no intervalo especificado de posições foram removidos.

- **`NotifyDataSetChanged`** &ndash; Sinaliza que o conjunto de dados foi alterado (força uma atualização completa).

Se você souber exatamente como o conjunto de dados foi alterado, poderá chamar os métodos apropriados acima para atualizar `RecyclerView` da maneira mais eficiente. Se você não souber exatamente como o conjunto de dados foi alterado, poderá chamar `NotifyDataSetChanged`, o que é muito menos eficiente, pois `RecyclerView` o deve atualizar todas as exibições visíveis para o usuário. Para obter mais informações sobre esses métodos, consulte [RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

No próximo tópico, [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), um aplicativo de exemplo é implementado para demonstrar exemplos de código reais das partes e funcionalidades descritas acima.

## <a name="related-links"></a>Links relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
