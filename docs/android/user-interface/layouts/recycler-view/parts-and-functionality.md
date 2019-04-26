---
title: RecyclerView partes e funcionalidade
description: Uma visão geral do Gerenciador de layout de RecyclerView, adaptador e titular de modo de exibição.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 13678d3b1bca102e6f608ad1c11838db1f14cd08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61309141"
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView partes e funcionalidade


`RecyclerView` identificadores de algumas tarefas internamente (por exemplo, a rolagem e reciclagem de modos de exibição), mas ele é essencialmente um gerente que coordena as classes auxiliares para exibir uma coleção. `RecyclerView` delegar tarefas para as classes de auxiliar a seguir:

-   **`Adapter`** &ndash; Infla layouts de item (instancia o conteúdo de um arquivo de layout) e associa os dados para exibições que são exibidas em um `RecyclerView`. O adaptador também relata eventos de clique do item.

-   **`LayoutManager`** &ndash; Mede e posiciona os modos de exibição do item dentro de um `RecyclerView` e gerencia a política para o modo de exibição de reciclagem.

-   **`ViewHolder`** &ndash; Procura e armazena as referências do modo de exibição. O titular da exibição também ajuda a detecção de cliques de exibição do item.

-   **`ItemDecoration`** &ndash; Permite que um aplicativo Adicionar deslocamentos de layout e desenho especiais às exibições específicas para desenhar linhas divisórias entre itens, destaques e limites de agrupamento visual.

-   **`ItemAnimator`** &ndash; Define as animações que ocorram durante as ações de item ou como as alterações são feitas para o adaptador.

A relação entre o `RecyclerView`, `LayoutManager`, e `Adapter` classes é ilustrado no diagrama a seguir:

![Diagrama de RecyclerView contendo LayoutManager, usando o adaptador para acessar o conjunto de dados](parts-and-functionality-images/01-recyclerview-diagram.png)

Como esta figura ilustra, o `LayoutManager` pode ser pensada como intermediário entre o `Adapter` e o `RecyclerView`. O `LayoutManager` faz chamadas no `Adapter` métodos em nome da `RecyclerView`. Por exemplo, o `LayoutManager` chamadas de um `Adapter` método quando é hora de criar uma nova exibição para uma posição de item específico no `RecyclerView`. O `Adapter` descompacta o layout para aquele item e cria um `ViewHolder` instância (não mostrada) para referências de cache para os modos de exibição nessa posição. Quando o `LayoutManager` chamadas a `Adapter` para associar um item específico ao conjunto de dados, o `Adapter` localiza os dados para aquele item, recupera do conjunto de dados e o copia para o modo de exibição do item associado.

Ao usar `RecyclerView` em seu aplicativo, é necessário criar tipos derivados das seguintes classes:

-   **`RecyclerView.Adapter`** &ndash; Fornece uma associação de conjunto de dados do seu aplicativo (que é específico ao seu aplicativo) para modos de exibição de item são exibidos dentro de `RecyclerView`. O adaptador sabe como associar cada posição de exibição do item no `RecyclerView` para um local específico na fonte de dados. Além disso, o adaptador manipula o layout do conteúdo dentro de cada item individual de modo de exibição e cria o detentor de modo de exibição para cada modo de exibição. O adaptador também relata eventos de clique de item que são detectados pelo modo de exibição do item.

-   **`RecyclerView.ViewHolder`** &ndash; Armazena em cache as referências aos modos de exibição em seu arquivo de layout do item para que as pesquisas de recursos não são repetidas desnecessariamente. O titular da exibição também organiza para eventos de clique de item a ser encaminhada para o adaptador quando um usuário toca o modo de exibição de item associado do titular exibição.

-   **`RecyclerView.LayoutManager`** &ndash; Posiciona os itens dentro do `RecyclerView`. Você pode usar um dos vários gerenciadores de layout predefinido ou você pode implementar seu próprio Gerenciador de layout personalizado.
    `RecyclerView` delegados a diretiva de layout para o Gerenciador de layout, é possível conectar em um Gerenciador de layout diferente sem precisar significativas alterações em seu aplicativo.

Além disso, você pode, opcionalmente, estender as classes a seguir para alterar a aparência de `RecyclerView` em seu aplicativo:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Se você não estender `ItemDecoration` e `ItemAnimator`, `RecyclerView` implementações padrão de usos. Este guia não explica como criar um personalizado `ItemDecoration` e `ItemAnimator` classes; para obter mais informações sobre essas classes, consulte [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Como exibir a reciclagem funciona

`RecyclerView` não alocar uma exibição de item para cada item na fonte de dados. Em vez disso, ele aloca somente o número de modos de exibição do item que se encaixam na tela e reutiliza os layouts do item como o usuário rolar. Quando o modo de exibição rola primeiro fora de Vista, ele percorre o processo de reciclagem ilustrado na figura a seguir:

[![Diagrama que ilustra as seis etapas da reciclagem do modo de exibição](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  Quando um modo de exibição rola fora de Vista e não será mais exibido, ela se torna uma *sucatear o modo de exibição*.

2.  O modo de exibição de recorte é colocado em um pool e se torna uma *reciclar o modo de exibição*.
    Esse pool é um cache de visualizações que exibem o mesmo tipo de dados.

3.  Quando um novo item é exibido, um modo de exibição é obtido da reciclagem do pool para reutilização. Como este modo de exibição deve ser associado novamente pelo adaptador antes de serem exibidos, ele é chamado uma *sujos exibição*.

4.  O modo de exibição sujo é reciclado: o adaptador localiza os dados para o próximo item a ser exibido e copia esses dados para os modos de exibição para este item. Referências para esses modos de exibição são recuperadas do titular da exibição associada com o modo de exibição reciclado.

5.  A exibição reciclada é adicionada à lista de itens no `RecyclerView` que estão prestes a entrar na tela.

6.  O modo de exibição reciclado entra na tela conforme o usuário rolar o `RecyclerView` para o próximo item na lista. Enquanto isso, outra exibição rola fora de Vista e é reciclada de acordo com as etapas acima.

Além de reutilização de exibição de item, `RecyclerView` também usa outra otimização de eficiência: exibir proprietários. Um *titular de exibição* é uma classe simples que caches exibir referências. Cada vez que o adaptador infla um arquivo de layout do item, ele também cria um proprietário de exibição correspondente. O titular da exibição usa `FindViewById` obter referências para os modos de exibição dentro do arquivo inflado do layout do item. Essas referências são usadas para carregar novos dados em modos de exibição de toda vez que o layout será reciclado para mostrar os novos dados.
 


### <a name="the-layout-manager"></a>O Gerenciador de Layout

O Gerenciador de layout é responsável por posicionando itens no `RecyclerView` exibir; ele determina quais itens da direção devem ser exibidos, a orientação (se itens são exibidos horizontal ou verticalmente) e o tipo de apresentação (uma lista ou uma grade) (na ordem normal ou na ordem inversa). O Gerenciador de layout também é responsável por calcular o tamanho e a posição de cada item na **RecycleView** exibir.

O Gerenciador de layout tem um objetivo de adicional: ela determina a política para quando a reciclagem de modos de exibição do item que não são mais visíveis ao usuário.
Como o Gerenciador de layout está ciente de quais modos de exibição são visíveis (e quais não são), é na melhor posição para decidir quando uma exibição pode ser reciclada. Para reciclar um modo de exibição, o Gerenciador de layout normalmente faz chamadas para o adaptador para substituir o conteúdo de uma exibição reciclado com dados diferentes, conforme descrito anteriormente [como o modo de exibição de reciclagem funciona](#recycling).

Você pode estender `RecyclerView.LayoutManager` para criar seu próprio layout manager, ou você pode usar um Gerenciador de layout predefinido. `RecyclerView` fornece os seguintes gerenciadores de layout predefinido:

-   **`LinearLayoutManager`** &ndash; Organiza os itens em uma coluna que pode ser rolada verticalmente, ou em uma linha que pode ser rolada horizontalmente.

-   **`GridLayoutManager`** &ndash; Exibe itens em uma grade.

-   **`StaggeredGridLayoutManager`** &ndash; Exibe itens em uma grade irregular, em que alguns itens têm larguras e alturas diferentes.

Para especificar o Gerenciador de layout, criar uma instância de seu gerente de layout escolhido e passá-lo para o `SetLayoutManager` método. Observe que você *devem* especifique o Gerenciador de layout &ndash; `RecyclerView` não selecione um Gerenciador de layout predefinido por padrão.

Para obter mais informações sobre o Gerenciador de layout, consulte a [referência de classe RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>O titular da exibição

O titular da exibição é uma classe que você define para armazenar em cache as referências do modo de exibição. O adaptador usa essas referências de modo de exibição para associar cada modo de exibição para seu conteúdo. Todos os itens a `RecyclerView` tem uma instância de titular de exibição associado que armazena em cache as referências de modo de exibição para aquele item. Para criar um portador de modo de exibição, use as etapas a seguir para definir uma classe para conter o conjunto exato de modos de exibição por item:

1.  Subclasse `RecyclerView.ViewHolder`.
2.  Implemente um construtor que procura e armazena as referências de modo de exibição.
3.  Implemente as propriedades que o adaptador pode usar para acessar essas referências.

Um exemplo detalhado de um `ViewHolder` implementação é apresentada na [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte o [referência de classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>O adaptador

A maioria do "trabalho pesado" do `RecyclerView` ocorre o código de integração no adaptador. `RecyclerView` exige que você forneça um adaptador derivado `RecyclerView.Adapter` para acessar a fonte de dados e popular cada item com o conteúdo da fonte de dados.
Como a fonte de dados é específico do aplicativo, você deve implementar a funcionalidade do adaptador que compreende como acessar seus dados. O adaptador extrai informações de fonte de dados e carrega-os em cada item no `RecyclerView` coleção.

O desenho a seguir ilustra como o adaptador mapeia o conteúdo em uma fonte de dados por meio de proprietários de modo de exibição para exibições individuais dentro de cada item de linha no `RecyclerView`:

[![Diagrama que ilustra o adaptador de conexão de fonte de dados com ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

O adaptador carrega cada `RecyclerView` linha com dados de um item de linha específica. Para a posição da linha *P*, por exemplo, o adaptador localiza os dados associados na posição *P* dentro da fonte de dados e copia esses dados para a linha de item na posição *P* no `RecyclerView` coleção.
No desenho acima, por exemplo, o adaptador usa o detentor de modo de exibição para pesquisar as referências para o `ImageView` e `TextView` nessa posição, portanto, não precisa chamar repetidamente `FindViewById` para esses modos de exibição como o usuário rola por meio da coleção e reutiliza os modos de exibição.

Ao implementar um adaptador, você deve substituir o seguinte `RecyclerView.Adapter` métodos:

-   **`OnCreateViewHolder`** &ndash; Instancia o detentor de arquivo e o modo de exibição de layout do item.

-   **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada para os modos de exibição cujas referências são armazenadas no proprietário de determinado modo de exibição.

-   **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto ele está posicionando itens dentro de `RecyclerView`. 



### <a name="notifying-recyclerview-of-data-changes"></a>Notificando RecyclerView das alterações de dados

`RecyclerView` não atualiza automaticamente a exibição quando o conteúdo dos seus dados de origem alterações; o adaptador deve notificar `RecyclerView` quando há uma alteração no conjunto de dados. O conjunto de dados pode alterar de muitas maneiras; Por exemplo, o conteúdo dentro de um item pode ser alterado ou a estrutura geral de dados pode ser alterada.
`RecyclerView.Adapter` Fornece uma série de métodos que você pode chamar, de modo que `RecyclerView` responde às alterações de dados da maneira mais eficiente:

-  **`NotifyItemChanged`** &ndash; Indica que o item na posição especificada foi alterado.

-  **`NotifyItemRangeChanged`** &ndash; Indica que os itens no intervalo especificado de posições foram alterados.

-  **`NotifyItemInserted`** &ndash; Indica que o item na posição especificada foi inserido recentemente.

-  **`NotifyItemRangeInserted`** &ndash; Sinais de que os itens no intervalo especificado de posições foram inseridos recentemente.

-  **`NotifyItemRemoved`** &ndash; Indica que o item na posição especificada foi removido.

-  **`NotifyItemRangeRemoved`** &ndash; Sinais de que os itens no intervalo especificado de posições foram removidos.

-  **`NotifyDataSetChanged`** &ndash; Sinais de que o conjunto de dados foi alterado (força uma atualização completa).

Se você souber exatamente como seu conjunto de dados foi alterado, você pode chamar os métodos apropriados acima para atualizar `RecyclerView` da maneira mais eficiente. Se você não souber exatamente como seu conjunto de dados foi alterado, você pode chamar `NotifyDataSetChanged`, que é muito menos eficiente porque `RecyclerView` deve atualizar todas as exibições que são visíveis para o usuário. Para obter mais informações sobre esses métodos, consulte [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

No próximo tópico [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), um aplicativo de exemplo é implementado para demonstrar os exemplos de código real das partes e funcionalidade descritas acima.


## <a name="related-links"></a>Links relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
