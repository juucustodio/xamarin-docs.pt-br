---
title: Xamarin. Forms ListView
description: Este guia apresenta o ListView de xamarin. Forms, que pode ser usado para apresentar dados em listas bonitos e interativas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 4aae2a206f07ea6caa7fc0d7530fb9fec05ce5f0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648423"
---
# <a name="xamarinforms-listview"></a>Xamarin. Forms ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)é uma exibição para apresentar listas de dados, especialmente listas longas que exigem rolagem.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView)o. Para obter mais informações, confira [CollectionView do Xamarin. Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

Certifique-se de que ListView é o controle certo para suas necessidades. ListView pode ser usado em qualquer situação em que você está exibindo a lista rolável de dados. ListViews dão suporte a ações de contexto e associação de dados.

Não deve ser confundido com o ListView [modo de tabela](~/xamarin-forms/user-interface/tableview.md). O controle de modo de tabela é uma opção melhor, sempre que você tem uma lista não associadas de opções ou de dados. Por exemplo, o aplicativo de configurações do iOS, que tem um conjunto predefinido de principalmente de opções, é mais adequado usar o modo de tabela que ListView.

Também Observe que um ListView é melhor adequada para dados homogêneos &ndash; ou seja, todos os dados devem ser do mesmo tipo. Isso ocorre porque apenas um tipo de célula pode ser usado para cada linha na lista. TableViews pode dar suporte a vários tipos de célula, portanto, eles são uma opção melhor quando você precisar combinar modos de exibição.

## <a name="components"></a>Componentes
ListView tem um número de componentes disponíveis para praticar a funcionalidade nativa de cada plataforma. Cada um desses componentes é descrita abaixo:

- **[Cabeçalhos e rodapés](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; separar de texto ou modo de exibição para exibir no início e no final de uma lista de dados da lista. Cabeçalhos e rodapés de páginas podem ser associados a uma fonte de dados independentemente da fonte de dados do ListView.
- **[Grupos](customizing-list-appearance.md#Grouping)**  &ndash; dados em um ListView que podem ser agrupados para facilitar a navegação. Normalmente, os grupos são associados a dados:

![](images/grouping-depth.png "ListView com dados agrupados")

- **[Células](customizing-cell-appearance.md)**  &ndash; dados em um ListView são apresentados nas células. Cada célula corresponde a uma linha de dados. Houver células internas à sua escolha, ou você pode definir sua própria célula personalizado. Células de internas e personalizadas podem ser usadas/definido em XAML ou código.
  - **[Interna](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; criados nas células, especialmente TextCell e ImageCell, pode ser ótimo para o desempenho, já que eles correspondem aos controles nativos em cada plataforma.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; exibe uma cadeia de caracteres de texto, opcionalmente, com o texto detalhado. Texto de detalhe é renderizado como uma segunda linha em uma fonte menor com uma cor de ênfase.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; exibe uma imagem com texto. Aparece como um TextCell com uma imagem à esquerda.
  - **[Células personalizadas](customizing-cell-appearance.md#customcells)**  &ndash; células personalizadas são ótimos quando você precisa apresentar dados complexos. Por exemplo, uma exibição personalizada poderia ser usada para apresentar uma lista de músicas, incluindo o álbum e o artista:

![](images/image-cell-default.png "ListView com ImageCells")

Para saber mais sobre como personalizar células em um ListView, consulte [personalizando aparência de célula do ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidade
ListView oferece suporte a um número de estilos de interação, incluindo:

- **[Atualização de pull](interactivity.md#Pull_to_Refresh)**  &ndash; ListView oferece suporte a pull para atualizar em cada plataforma.
- **[Ações de contexto](interactivity.md#Context_Actions)**  &ndash; ListView oferece suporte a executar ações em itens individuais em uma lista. Por exemplo, você pode implementar a ação para passar o dedo no iOS, ou toque ações no Android.
- **[Seleção](interactivity.md#selectiontaps)**  &ndash; você pode escutar seleções e contíguas para agir quando uma linha é tocada.

![](images/context-default.png "ListView com ações de contexto")

Para saber mais sobre os recursos de interatividade do ListView, consulte [ações & interatividade com ListView](interactivity.md).

## <a name="related-links"></a>Links relacionados

- [Trabalhando com ListView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Associação bidirecional (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Criado em células (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Células personalizadas (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Agrupamento (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Exibição de renderizador personalizado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interatividade do ListView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
