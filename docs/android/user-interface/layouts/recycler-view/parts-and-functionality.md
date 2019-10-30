---
title: Partes e funcionalidades do RecyclerView
description: Uma visão geral do Gerenciador de layout RecyclerView, do adaptador e do detentor da exibição.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 823215b7cc1bac8a8f6bffc6e480400135e88ce8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028828"
---
# <a name="recyclerview-parts-and-functionality"></a>Partes e funcionalidades do RecyclerView

`RecyclerView` manipula algumas tarefas internamente (como a rolagem e a reciclagem de exibições), mas é essencialmente um gerente que coordena as classes auxiliares para exibir uma coleção. `RecyclerView` delega tarefas para as seguintes classes auxiliares:

- **`Adapter`** &ndash; os layouts de item (instancia o conteúdo de um arquivo de layout) e associa os dados às exibições exibidas em uma `RecyclerView`. O adaptador também relata eventos de clique de item.

- **`LayoutManager`** &ndash; medidas e posiciona modos de exibição de item dentro de um `RecyclerView` e gerencia a política para a reciclagem da exibição.

- **`ViewHolder`** &ndash; pesquisa e armazena referências de exibição. O detentor da exibição também ajuda na detecção de cliques de exibição de item.

- **`ItemDecoration`** &ndash; permite que um aplicativo adicione deslocamentos especiais de desenho e layout a modos de exibição específicos para desenhar divisores entre itens, destaques e limites de agrupamento Visual.

- **`ItemAnimator`** &ndash; define as animações que ocorrem durante as ações do item ou conforme são feitas alterações no adaptador.

A relação entre as classes `RecyclerView`, `LayoutManager`e `Adapter` é descrita no diagrama a seguir:

![Diagrama de RecyclerView que contém o LayoutManager, usando o adaptador para acessar o conjunto de dados](parts-and-functionality-images/01-recyclerview-diagram.png)

Como esta figura ilustra, a `LayoutManager` pode ser considerada como o intermediário entre o `Adapter` e o `RecyclerView`. O `LayoutManager` faz chamadas em métodos `Adapter` em nome do `RecyclerView`. Por exemplo, a `LayoutManager` chama um método `Adapter` quando é hora de criar uma nova exibição para uma posição de item específica na `RecyclerView`. O `Adapter` o o layout do item e cria uma instância de `ViewHolder` (não mostrada) para armazenar em cache referências às exibições nessa posição. Quando o `LayoutManager` chama o `Adapter` para associar um item específico ao conjunto de dados, o `Adapter` localiza os dados para esse item, recupera-os do conjunto de dados e os copia para o modo de exibição de item associado.

Ao usar `RecyclerView` em seu aplicativo, é necessário criar tipos derivados das seguintes classes:

- **`RecyclerView.Adapter`** &ndash; fornece uma associação do conjunto de dados do aplicativo (que é específico ao seu aplicativo) para exibições de item que são exibidas no `RecyclerView`. O adaptador sabe como associar cada posição de exibição de item no `RecyclerView` a um local específico na fonte de dados. Além disso, o adaptador manipula o layout do conteúdo em cada exibição de item individual e cria o detentor da exibição para cada exibição. O adaptador também relata eventos de clique de item detectados pela exibição de item.

- **`RecyclerView.ViewHolder`** &ndash; faz referência a caches para as exibições em seu arquivo de layout de item para que as pesquisas de recursos não sejam repetidas desnecessariamente. O detentor da exibição também organiza os eventos de clique de item a serem encaminhados para o adaptador quando um usuário toca na exibição de item associada do detentor da exibição.

- **`RecyclerView.LayoutManager`** &ndash; posiciona os itens dentro do `RecyclerView`. Você pode usar um dos vários gerenciadores de layout predefinidos ou pode implementar seu próprio Gerenciador de layout personalizado.
    `RecyclerView` delega a política de layout para o Gerenciador de layout, para que você possa conectar um Gerenciador de layout diferente sem precisar fazer alterações significativas em seu aplicativo.

Além disso, você pode, opcionalmente, estender as seguintes classes para alterar a aparência de `RecyclerView` em seu aplicativo:

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Se você não estender `ItemDecoration` e `ItemAnimator`, `RecyclerView` usará as implementações padrão. Este guia não explica como criar classes de `ItemDecoration` e de `ItemAnimator` personalizadas; para obter mais informações sobre essas classes, consulte [RecyclerView. dodecoração](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

<a name="recycling" />

## <a name="how-view-recycling-works"></a>Como funciona a reciclagem de exibição

`RecyclerView` não aloca uma exibição de item para cada item na fonte de dados. Em vez disso, ele aloca apenas o número de exibições de item que se ajustam à tela e reutiliza os layouts de item à medida que o usuário rola. Quando a exibição é rolada pela primeira vez, ela passa pelo processo de reciclagem ilustrado na figura a seguir:

[![diagrama ilustrando as seis etapas da reciclagem da exibição](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Quando uma exibição rola para fora da visão e não é mais exibida, ela se torna uma *exibição de sucata*.

2. A exibição de sucata é colocada em um pool e torna-se uma *exibição de reciclagem*.
    Esse pool é um cache de exibições que exibem o mesmo tipo de dados.

3. Quando um novo item é exibido, uma exibição é retirada do pool de reciclagem para reutilização. Como essa exibição deve ser vinculada novamente pelo adaptador antes de ser exibida, ela é chamada de *exibição suja*.

4. A exibição suja é reciclada: o adaptador localiza os dados para o próximo item a ser exibido e copia esses dados para os modos de exibição deste item. As referências para essas exibições são recuperadas do detentor da exibição associado ao modo de exibição reciclado.

5. A exibição reciclada é adicionada à lista de itens no `RecyclerView` que estão prestes a entrar na tela.

6. A exibição reciclada é exibida na tela enquanto o usuário rola a `RecyclerView` para o próximo item da lista. Enquanto isso, outra exibição rola de visão e é reciclada de acordo com as etapas acima.

Além da reutilização de modo de exibição de item, `RecyclerView` também usa outra otimização de eficiência: exibir os contentores. Um *detentor de exibição* é uma classe simples que armazena em cache referências de exibição. Cada vez que o adaptador inplana um arquivo de layout de item, ele também cria um detentor de exibição correspondente. O detentor da exibição usa `FindViewById` para obter referências às exibições dentro do arquivo de layout de item inplano. Essas referências são usadas para carregar novos dados nas exibições toda vez que o layout é reciclado para mostrar novos dados.

## <a name="the-layout-manager"></a>O Gerenciador de layout

O Gerenciador de layout é responsável por posicionar itens na exibição de `RecyclerView`; Ele determina o tipo de apresentação (uma lista ou uma grade), a orientação (se os itens são exibidos vertical ou horizontalmente) e quais itens de direção devem ser exibidos (em ordem normal ou em ordem inversa). O Gerenciador de layout também é responsável por calcular o tamanho e a posição de cada item na exibição **RecycleView** .

O Gerenciador de layout tem uma finalidade adicional: determina a política para quando reciclar exibições de item que não são mais visíveis para o usuário.
Como o Gerenciador de layout está ciente das exibições que estão visíveis (e quais não são), ela está na melhor posição para decidir quando uma exibição pode ser reciclada. Para reciclar um modo de exibição, o Gerenciador de layout normalmente faz chamadas para o adaptador para substituir o conteúdo de uma exibição reciclada com dados diferentes, conforme descrito anteriormente em [como a reciclagem de exibição funciona](#recycling).

Você pode estender `RecyclerView.LayoutManager` para criar seu próprio Gerenciador de layout ou pode usar um Gerenciador de layout predefinido. `RecyclerView` fornece os seguintes gerenciadores de layout predefinidos:

- **`LinearLayoutManager`** &ndash; organiza os itens em uma coluna que pode ser rolada verticalmente ou em uma linha que pode ser rolada horizontalmente.

- **`GridLayoutManager`** &ndash; exibe itens em uma grade.

- **`StaggeredGridLayoutManager`** &ndash; exibe itens em uma grade escalonada, em que alguns itens têm alturas e larguras diferentes.

Para especificar o Gerenciador de layout, crie uma instância do Gerenciador de layout escolhido e passe-o para o método `SetLayoutManager`. Observe que você *deve* especificar o Gerenciador de layout &ndash; `RecyclerView` não seleciona um Gerenciador de layout predefinido por padrão.

Para obter mais informações sobre o Gerenciador de layout, consulte a [referência de classe RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

## <a name="the-view-holder"></a>O detentor da exibição

O detentor da exibição é uma classe que você define para as referências de exibição de cache. O adaptador usa essas referências de exibição para associar cada exibição ao seu conteúdo. Cada item no `RecyclerView` tem uma instância do detentor de exibição associada que armazena em cache as referências de exibição para esse item. Para criar um detentor da exibição, use as etapas a seguir para definir uma classe para manter o conjunto exato de exibições por item:

1. `RecyclerView.ViewHolder`de subclasse.
2. Implemente um construtor que pesquise e armazene as referências de exibição.
3. Implemente propriedades que o adaptador pode usar para acessar essas referências.

Um exemplo detalhado de uma implementação de `ViewHolder` é apresentado em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte a [referência de classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

## <a name="the-adapter"></a>O adaptador

A maior parte do "trabalho pesado" do código de integração `RecyclerView` ocorre no adaptador. `RecyclerView` requer que você forneça um adaptador derivado de `RecyclerView.Adapter` para acessar a fonte de dados e preencher cada item com o conteúdo da fonte de dados.
Como a fonte de dados é específica do aplicativo, você deve implementar a funcionalidade do adaptador que entenda como acessar seus dados. O adaptador extrai informações da fonte de dados e carrega-as em cada item na coleção de `RecyclerView`.

O desenho a seguir ilustra como o adaptador mapeia o conteúdo em uma fonte de dados por meio de contentores para modos de exibição individuais dentro de cada item de linha no `RecyclerView`:

[Diagrama de![que ilustra a fonte de dados de conexão do adaptador para ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

O adaptador carrega cada linha de `RecyclerView` com dados para um item de linha específico. Para a posição de linha *P*, por exemplo, o adaptador localiza os dados associados na posição *p* na fonte de dados e copia esses dados para o item de linha na posição *p* na coleção de `RecyclerView`.
No desenho acima, por exemplo, o adaptador usa o detentor da exibição para pesquisar as referências para o `ImageView` e `TextView` nessa posição para que ele não precise chamar repetidamente `FindViewById` para essas exibições à medida que o usuário rola pela coleção e reutiliza exibições.

Ao implementar um adaptador, você deve substituir os seguintes métodos de `RecyclerView.Adapter`:

- **`OnCreateViewHolder`** &ndash; instancia o arquivo de layout de item e o detentor da exibição.

- **`OnBindViewHolder`** &ndash; carrega os dados na posição especificada nas exibições cujas referências são armazenadas no espaço de exibição fornecido.

- **`ItemCount`** &ndash; retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto está posicionando itens dentro do `RecyclerView`.

## <a name="notifying-recyclerview-of-data-changes"></a>Notificando RecyclerView de alterações de dados

`RecyclerView` não atualiza automaticamente sua exibição quando o conteúdo de sua fonte de dados é alterado; o adaptador deve notificar `RecyclerView` quando há uma alteração no conjunto de dados. O conjunto de dados pode ser alterado de várias maneiras; por exemplo, o conteúdo dentro de um item pode ser alterado ou a estrutura geral dos dados pode ser alterada.
`RecyclerView.Adapter` fornece vários métodos que você pode chamar para que `RecyclerView` responda às alterações de dados da maneira mais eficiente:

- **`NotifyItemChanged`** &ndash; sinaliza que o item na posição especificada foi alterado.

- **`NotifyItemRangeChanged`** &ndash; sinaliza que os itens no intervalo especificado de posições foram alterados.

- **`NotifyItemInserted`** &ndash; sinaliza que o item na posição especificada foi recentemente inserido.

- **`NotifyItemRangeInserted`** &ndash; sinaliza que os itens no intervalo especificado de posições foram inseridos recentemente.

- **`NotifyItemRemoved`** &ndash; sinaliza que o item na posição especificada foi removido.

- **`NotifyItemRangeRemoved`** &ndash; sinaliza que os itens no intervalo especificado de posições foram removidos.

- **`NotifyDataSetChanged`** &ndash; sinaliza que o conjunto de dados foi alterado (força uma atualização completa).

Se você souber exatamente como o conjunto de dados foi alterado, poderá chamar os métodos apropriados acima para atualizar `RecyclerView` da maneira mais eficiente. Se você não souber exatamente como o conjunto de dados foi alterado, poderá chamar `NotifyDataSetChanged`, o que é muito menos eficiente porque `RecyclerView` deve atualizar todas as exibições visíveis para o usuário. Para obter mais informações sobre esses métodos, consulte [RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

No próximo tópico, [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), um aplicativo de exemplo é implementado para demonstrar exemplos de código reais das partes e funcionalidades descritas acima.

## <a name="related-links"></a>Links relacionados

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
