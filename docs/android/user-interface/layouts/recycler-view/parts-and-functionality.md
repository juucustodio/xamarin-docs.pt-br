---
title: Funcionalidade e partes de RecyclerView
ms.topic: article
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 89679f7d825422ab34dd77b31a7a3fde60f36e99
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="recyclerview-parts-and-functionality"></a>Funcionalidade e partes de RecyclerView


`RecyclerView` identificadores de algumas tarefas internamente (como a rolagem e reciclagem de modos de exibição), mas ele é essencialmente um manager que coordena as classes auxiliares para exibir uma coleção. `RecyclerView` delegar tarefas para as classes de auxiliar a seguir:

-   **`Adapter`** &ndash; Inflação layouts de item (instancia o conteúdo de um arquivo de layout) e associa dados para exibições que são exibidas em um `RecyclerView`. O adaptador também relata eventos de clique do item.

-   **`LayoutManager`** &ndash; Medidas e posiciona modos de exibição do item dentro de um `RecyclerView` e gerencia a política para exibir a reciclagem.

-   **`ViewHolder`** &ndash; Procura e armazena as referências do modo de exibição. O proprietário de exibição também ajuda com detectar cliques de exibição do item.

-   **`ItemDecoration`** &ndash; Permite que um aplicativo Adicionar deslocamentos de layout e de desenho especiais para exibições específicas para desenhar divisores entre itens, destaques e limites de agrupamento visual.

-   **`ItemAnimator`** &ndash; Define as animações que ocorrem durante as ações de item ou como as alterações são feitas para o adaptador.

A relação entre o `RecyclerView`, `LayoutManager`, e `Adapter` classes é ilustrado no diagrama a seguir:

![Diagrama de RecyclerView contendo LayoutManager, usando o adaptador para acessar o conjunto de dados](parts-and-functionality-images/01-recyclerview-diagram.png)

Como esta figura ilustra, o `LayoutManager` pode ser pensada como um intermediário entre o `Adapter` e `RecyclerView`. O `LayoutManager` faz chamadas no `Adapter` métodos em nome da `RecyclerView`. Por exemplo, o `LayoutManager` chama um `Adapter` método quando é hora de criar uma nova exibição para uma posição de item específico no `RecyclerView`. O `Adapter` inflação o layout para o item e cria um `ViewHolder` instância (não mostrada) para referências de cache para os modos de exibição da posição. Quando o `LayoutManager` chama o `Adapter` para associar um item específico para o conjunto de dados, o `Adapter` localiza os dados para o item, recupera do conjunto de dados e copiá-lo para o modo de exibição do item associado.

Ao usar `RecyclerView` em seu aplicativo, é necessário criar tipos derivados das seguintes classes:

-   **`RecyclerView.Adapter`** &ndash; Fornece uma associação de conjunto de dados do aplicativo (que é específico ao seu aplicativo) a modos de exibição do item que são exibidos dentro do `RecyclerView`. O adaptador sabe como associar cada posição de exibição de itens na `RecyclerView` em um local específico na fonte de dados. Além disso, o adaptador manipula o layout do conteúdo em exibição de cada item individual e cria o proprietário de exibição para cada modo de exibição. O adaptador também relata eventos de clique de item que são detectados pelo modo de exibição de item.

-   **`RecyclerView.ViewHolder`** &ndash; Armazena em cache as referências aos modos de exibição em seu arquivo de layout do item para que as pesquisas de recursos não sejam repetidas desnecessariamente. Também organiza o proprietário de exibição para eventos de clique de item a serem encaminhados para o adaptador quando um usuário toca em modo de exibição do titular exibição item associado.

-   **`RecyclerView.LayoutManager`** &ndash; Posiciona itens dentro de `RecyclerView`. Você pode usar um dos vários gerenciadores de layout predefinidos ou você pode implementar seu próprio Gerenciador de layout personalizado.
    `RecyclerView` delegados a diretiva de layout para o Gerenciador de layout, para que você pode conectar um Gerenciador de layout diferentes sem ter que fazer significativas alterações ao seu aplicativo.

Além disso, você pode opcionalmente estender as classes a seguir para alterar a aparência de `RecyclerView` em seu aplicativo:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Se você não estender `ItemDecoration` e `ItemAnimator`, `RecyclerView` usa padrão implementações. Este guia explica como criar personalizado `ItemDecoration` e `ItemAnimator` classes; para obter mais informações sobre essas classes, consulte [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Como exibir a reciclagem funciona

`RecyclerView` uma exibição de item não aloca para cada item na fonte de dados. Em vez disso, ele aloca somente o número de modos de exibição do item que caibam na tela e reutiliza os layouts de item, como o usuário rolar. Quando o modo de exibição primeiro rola ficará oculta, ele passa pelo processo de reciclagem ilustrado na figura a seguir:

[![Diagrama que ilustra as seis etapas de reciclagem de exibição](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  Quando uma exibição rola ficará oculta e não será mais exibida, ele se torna um *exibição de descarte*.

2.  O modo de exibição de recorte é colocado em um pool e se torna um *reciclar exibição*.
    Esse pool é um cache de visualizações que exibem o mesmo tipo de dados.

3.  Quando um novo item é exibido, um modo de exibição é obtido do pool de reciclagem para reutilização. Como esse modo de exibição deve ser associado novamente pelo adaptador antes de ser exibido, ela é chamada uma *sujas exibição*.

4.  O modo de exibição incorreto é reciclado: o adaptador localiza os dados para o próximo item a ser exibido e copia esses dados para os modos de exibição para este item. Referências para esses modos de exibição são recuperadas do proprietário da exibição associado à exibição reciclada.

5.  A exibição reciclada é adicionada à lista de itens no `RecyclerView` que está prestes a ir na tela.

6.  O modo de exibição reciclado fica na tela como o usuário rolar a `RecyclerView` para o próximo item na lista. Enquanto isso, outra exibição rola ficará oculta e reciclagem de acordo com as etapas acima.

Além de reutilização de exibição de item, `RecyclerView` também usa outra otimização de eficiência: exibir proprietários. Um *proprietário de exibição* é uma classe simple caches exibir referências. Cada vez que o adaptador inflação um arquivo de layout do item, ele também cria um proprietário de exibição correspondente. O proprietário da exibição usa `FindViewById` obter referências para os modos de exibição dentro do arquivo inflated do layout do item. Essas referências são usadas para carregar novos dados para os modos de exibição, toda vez que o layout é reciclado para mostrar novos dados.
 


### <a name="the-layout-manager"></a>O Gerenciador de Layout

O Gerenciador de layout é responsável por itens de posicionamento de `RecyclerView` exibir; determina quais itens de direção devem ser exibidos, a orientação (se itens são exibidos verticalmente ou horizontalmente) e o tipo de apresentação (uma lista ou uma grade) (na ordem normal ou na ordem inversa). O Gerenciador de layout também é responsável por calcular o tamanho e posição de cada item de **RecycleView** exibir.

O Gerenciador de layout tem uma finalidade adicional: ela determina a política de quando reciclar os modos de exibição do item que não são visíveis para o usuário.
Como o Gerenciador de layout está ciente de quais modos de exibição são visíveis (e que não sejam), é em melhor posição para decidir quando uma exibição pode ser reciclada. Para reciclar um modo de exibição, o Gerenciador de layout normalmente faz chamadas para o adaptador para substituir o conteúdo de uma exibição reciclado com dados diferentes, conforme descrito anteriormente na [como o modo de exibição de reciclagem funciona](#recycling).

Você pode estender `RecyclerView.LayoutManager` para criar seu próprio layout manager, ou você pode usar um Gerenciador de layout predefinidos. `RecyclerView` fornece os seguintes gerenciadores de layout predefinidos:

-   **`LinearLayoutManager`** &ndash; Organiza itens em uma coluna que pode ser rolada verticalmente, ou em uma linha que pode ser rolada horizontalmente.

-   **`GridLayoutManager`** &ndash; Exibe itens em uma grade.

-   **`StaggeredGridLayoutManager`** &ndash; Exibe itens em uma grade irregular, em que alguns itens têm larguras e alturas diferentes.

Para especificar o Gerenciador de layout, instanciar o Gerenciador de layout escolhido e passá-lo para o `SetLayoutManager` método. Observe que você *deve* especifique o Gerenciador de layout &ndash; `RecyclerView` não selecione um Gerenciador de layout predefinidos por padrão.

Para obter mais informações sobre o Gerenciador de layout, consulte o [referência de classe RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>O proprietário de exibição

O proprietário de exibição é uma classe que você define para caching de referências de exibição. O adaptador usa essas referências de exibição para associar cada modo de exibição para seu conteúdo. Todos os itens a `RecyclerView` tem uma instância de proprietário de exibição associada que armazena em cache as referências de exibição para aquele item. Para criar um proprietário de exibição, use as etapas a seguir para definir uma classe para conter o conjunto exato de modos de exibição por item:

1.  Subclasse `RecyclerView.ViewHolder`.
2.  Implemente um construtor que procura e armazena as referências de exibição.
3.  Implementar propriedades que o adaptador pode usar para acessar essas referências.

Um exemplo detalhado de um `ViewHolder` implementação é apresentada em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte o [referência de classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>O adaptador

A maioria do "trabalho pesado anterior" do `RecyclerView` código integração ocorre no adaptador. `RecyclerView` requer que você forneça um adaptador derivado `RecyclerView.Adapter` para acessar a fonte de dados e popular cada item com o conteúdo da fonte de dados.
Como a fonte de dados é específico do aplicativo, você deve implementar a funcionalidade do adaptador que sabe como acessar os dados. O adaptador extrai informações de fonte de dados e carrega-os em cada item a `RecyclerView` coleção.

O desenho a seguir ilustra como o adaptador mapeia o conteúdo em uma fonte de dados através de proprietários de exibição para exibições individuais dentro de cada item de linha no `RecyclerView`:

[![Diagrama que ilustra o adaptador de conexão de fonte de dados com ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

O adaptador carrega cada `RecyclerView` linha com dados de um item de linha específica. Posição de linha *P*, por exemplo, o adaptador localiza os dados associados na posição *P* dentro da fonte de dados e copia esses dados para a linha do item na posição *P* do `RecyclerView` coleção.
No desenho acima, por exemplo, o adaptador usa o proprietário de exibição para pesquisar as referências para o `ImageView` e `TextView` naquela posição, portanto, não precisa chamar repetidamente `FindViewById` para os modos de exibição como o usuário rola através da coleção e reutiliza os modos de exibição.

Quando você implementa um adaptador, você deve substituir o seguinte `RecyclerView.Adapter` métodos:

-   **`OnCreateViewHolder`** &ndash; Instancia o proprietário de arquivo e o modo de exibição de layout do item.

-   **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada para os modos de exibição cujas referências são armazenadas no proprietário de exibição fornecido.

-   **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto o posicionamento de itens dentro de `RecyclerView`. 



### <a name="notifying-recyclerview-of-data-changes"></a>Notificando RecyclerView das alterações de dados

`RecyclerView` não atualizar automaticamente sua exibição quando o conteúdo de seus dados de origem alterações; o adaptador deve notificar `RecyclerView` quando há uma alteração no conjunto de dados. O conjunto de dados pode alterar de muitas maneiras; Por exemplo, o conteúdo dentro de um item pode ser alterado ou a estrutura geral de dados pode ser alterada.
`RecyclerView.Adapter` fornece vários métodos que você pode chamar para que `RecyclerView` responde às alterações de dados da maneira mais eficiente:

-  **`NotifyItemChanged`** &ndash; Indica que o item na posição especificada foi alterada.

-  **`NotifyItemRangeChanged`** &ndash; Indica que os itens no intervalo especificado de posições que foram alterados.

-  **`NotifyItemInserted`** &ndash; Indica que o item na posição especificada foi inserido recentemente.

-  **`NotifyItemRangeInserted`** &ndash; Indica que os itens no intervalo especificado de posições foi inseridos recentemente.

-  **`NotifyItemRemoved`** &ndash; Indica que o item na posição especificada foi removido.

-  **`NotifyItemRangeRemoved`** &ndash; Indica que os itens no intervalo especificado de posições foram removidos.

-  **`NotifyDataSetChanged`** &ndash; Indica que o conjunto de dados foi alterado (força uma atualização completa).

Se você souber exatamente como seu conjunto de dados foi alterado, você pode chamar os métodos apropriados acima para atualizar `RecyclerView` da maneira mais eficiente. Se você não souber exatamente como seu conjunto de dados foi alterado, você pode chamar `NotifyDataSetChanged`, que é muito menos eficiente porque `RecyclerView` deve atualizar todas as exibições que são visíveis para o usuário. Para obter mais informações sobre esses métodos, consulte [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

No próximo tópico, [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), um aplicativo de exemplo é implementado para demonstrar os exemplos de código real das peças e funcionalidade descritas acima.


## <a name="related-links"></a>Links relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estendendo o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
