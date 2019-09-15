---
title: Xamarin. Forms ListView
description: Este guia apresenta a ListView do Xamarin. Forms, que pode ser usada para apresentar dados em listas interativas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
ms.openlocfilehash: 9d7dd98b98c7722d6fcdcb49c10e326732fa9f0b
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70997990"
---
# <a name="xamarinforms-listview"></a>Xamarin. Forms ListView

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)é uma exibição para apresentar listas de dados, especialmente listas longas que exigem rolagem.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView)o. Para obter mais informações, confira [CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

Um `ListView` controle pode ser usado em qualquer situação em que você esteja Exibindo listas de dados roláveis. A `ListView` classe dá suporte a ações de contexto e Associação de dados.

O `ListView` controle não deve ser confundido [`TableView`](~/xamarin-forms/user-interface/tableview.md) com o controle. O `TableView` controle é uma opção melhor sempre que você tem uma lista não vinculada de opções ou dados, pois permite que opções predefinidas sejam especificadas em XAML. Por exemplo, o aplicativo de configurações do IOS, que tem um conjunto de opções da maioria predefinida, é mais `TableView` adequado para `ListView`usar um do que um.

A `ListView` classe não dá suporte à definição de itens de lista em XAML, `ItemsSource` você deve usar a propriedade ou `ItemTemplate` a associação de dados com um para definir itens na lista.

Um `ListView` é mais adequado para coleções que consistem em um único tipo de dados. Esse requisito é porque apenas um tipo de célula pode ser usado para cada linha na lista. O `TableView` controle pode dar suporte a vários tipos de células, portanto, é uma opção melhor quando você precisa exibir vários tipos de dados.

Para obter mais informações sobre como associar dados `ListView` a uma instância do, consulte [fontes de dados do ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componentes

O `ListView` controle tem vários componentes disponíveis para exercitar a funcionalidade nativa de cada plataforma. Esses componentes são definidos nas seções a seguir.

### <a name="headers-and-footerscustomizing-list-appearancemdheaders-and-footers"></a>[Cabeçalhos e rodapés](customizing-list-appearance.md#headers-and-footers)

Os componentes de cabeçalho e rodapé são exibidos no início e no final de uma lista, separados dos dados da lista. Cabeçalhos e rodapés podem ser associados a uma fonte de dados separada da fonte de dados do ListView.

### <a name="groupscustomizing-list-appearancemdgrouping"></a>[Agrupa](customizing-list-appearance.md#grouping)

Os dados em `ListView` um podem ser agrupados para facilitar a navegação. Normalmente, os grupos são associados a dados. A captura de tela a `ListView` seguir mostra um com dados agrupados:

["Dados agrupados em um ListView" agrupados em um ListView ![](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "")

### <a name="cellscustomizing-cell-appearancemd"></a>[Células](customizing-cell-appearance.md)

Os itens de dados `ListView` em uma são chamados de células. Cada célula corresponde a uma linha de dados. Houver células internas à sua escolha, ou você pode definir sua própria célula personalizado. Células de internas e personalizadas podem ser usadas/definido em XAML ou código.

- [As células internas](customizing-cell-appearance.md#built-in-cells), `TextCell` como e `ImageCell`, correspondem a controles nativos e são especialmente com bom desempenho.
  - Um [`TextCell`](customizing-cell-appearance.md#textcell) exibe uma cadeia de texto, opcionalmente com texto detalhado. Texto de detalhe é renderizado como uma segunda linha em uma fonte menor com uma cor de ênfase.
  - Um [`ImageCell`](customizing-cell-appearance.md#imagecell) exibe uma imagem com texto. Aparece como um `TextCell` com uma imagem à esquerda.
- [As células personalizadas](customizing-cell-appearance.md#custom-cells) são usadas para apresentar dados complexos. Por exemplo, uma célula personalizada pode ser usada para apresentar uma lista de músicas que incluem o álbum e o artista.

A captura de tela a `ListView` seguir mostra um com itens ImageCell:

["Itens ImageCell em um ListView" itens de ImageCell em um ListView ![](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "")

Para saber mais sobre como personalizar células em um `ListView`, consulte [Personalizando a aparência da célula ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidade

A `ListView` classe oferece suporte a vários estilos de interação.

- O [pull para atualização](interactivity.md#pull-to-refresh) permite que o usuário puxe `ListView` para baixo para atualizar o conteúdo.
- As [ações de contexto](interactivity.md#context-actions) permitem que o desenvolvedor especifique ações personalizadas em itens de lista individuais. Por exemplo, você pode implementar a ação para passar o dedo no iOS, ou toque ações no Android.
- A [seleção](interactivity.md#selection-and-taps) permite que o desenvolvedor anexe a funcionalidade aos eventos de seleção e desmarcação em itens de lista.

A captura de tela a `ListView` seguir mostra um com ações de contexto:

Ações de contexto ["ações de contexto em uma ListView" em um ListView ![](images/context-default-cropped.png)](images/context-default.png#lightbox "")

Para saber mais sobre os recursos de interatividade `ListView`do, consulte [ações & interatividade com ListView](interactivity.md).

## <a name="related-links"></a>Links relacionados

- [Trabalhando com ListView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Associação bidirecional (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Criado em células (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Células personalizadas (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Agrupamento (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Exibição de renderizador personalizado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interatividade do ListView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
