---
title: ListView
description: Apresente os dados interativos visualmente atrativos, listas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 7beb9e66a6e6bc29de36fcd605c5daa0098e75a2
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848532"
---
# <a name="listview"></a>ListView

ListView é um modo de exibição de apresentação de listas de dados, especialmente listas longas que requerem a rolagem. Este guia mostrará como usar ListView:

1. **[Fontes de dados](data-and-databinding.md)**  &ndash; popular uma ListView com dados, com ou sem associação de dados.
2. **[Aparência da célula](customizing-cell-appearance.md)**  &ndash; personalizar a aparência das células internas ou criar sua própria célula personalizado.
3. **[Lista aparência](customizing-list-appearance.md)**  &ndash; personalizar a aparência de ListView. Definir cabeçalhos e rodapés, habilite os grupos e alterar a altura das linhas.
4. **[Interatividade](interactivity.md)**  &ndash; tratar toques e as seleções, implementar puxe para atualizar e adicionar ações contextuais.
5. **[Desempenho](performance.md)**  &ndash; evitar problemas de desempenho.

## <a name="use-cases"></a>Casos de uso
Certifique-se de que ListView é o controle à direita para suas necessidades. ListView pode ser usado em qualquer situação em que você está exibindo roláveis listas de dados. ListViews dão suporte a ações de contexto e associação de dados.

ListView não deve ser confundido com [modo de tabela](~/xamarin-forms/user-interface/tableview.md). O controle de modo de tabela é uma opção melhor, sempre que você tiver uma lista de não associadas de opções ou dados. Por exemplo, o aplicativo de configurações do iOS, que tem um conjunto de opções principalmente predefinidos, é mais adequado para usar o modo de tabela de ListView.

Também Observe que uma ListView é melhor adequada para dados homogêneos &ndash; ou seja, todos os dados devem ser do mesmo tipo. Isso ocorre porque apenas um tipo de célula pode ser usado para cada linha na lista. TableViews pode dar suporte a vários tipos de célula, portanto eles são uma opção melhor quando você precisar combinar os modos de exibição.


## <a name="components"></a>Componentes
ListView tem um número de componentes disponíveis para ativar a funcionalidade nativa de cada plataforma. Cada um desses componentes é descrita abaixo:

- **[Cabeçalhos e rodapés](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; texto ou modo de exibição para exibir no início e fim de uma lista, separados dos dados da lista. Cabeçalhos e rodapés podem ser associados a uma fonte de dados independentemente de fonte de dados de ListView.
- **[Grupos de](customizing-list-appearance.md#Grouping)**  &ndash; dados em uma ListView podem ser agrupados para facilitar a navegação. Grupos são geralmente associados a dados:

![](images/grouping-depth.png "ListView com dados agrupados")

- **[Células](customizing-cell-appearance.md)**  &ndash; dados em uma ListView são apresentados em células. Cada célula corresponde a uma linha de dados. Há células internas à sua escolha ou você pode definir sua própria célula personalizado. Células internas e personalizadas podem ser usado/definido em XAML ou em código.
  - **[Internos](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; criados nas células, especialmente TextCell e ImageCell, pode ser ótimo desempenho, pois eles correspondem aos controles nativos em cada plataforma.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; exibe uma cadeia de caracteres de texto, opcionalmente com o texto detalhado. Texto detalhado é renderizado como uma segunda linha em uma fonte menor com uma cor de ênfase.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; exibe uma imagem com texto. Aparece como um TextCell com uma imagem à esquerda.
  - **[Células personalizadas](customizing-cell-appearance.md#customcells)**  &ndash; células personalizado são excelentes quando você precisa apresentar dados complexos. Por exemplo, uma exibição personalizada pode ser usada para apresentar uma lista de músicas, incluindo álbum e artista:

![](images/image-cell-default.png "ListView com ImageCells")

Para saber mais sobre como personalizar células em uma ListView, consulte [personalizando aparência da célula de ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidade
ListView oferece suporte a vários estilos de interação, incluindo:

- **[Puxe para atualizar](interactivity.md#Pull_to_Refresh)**  &ndash; ListView suporta puxe para atualizar em cada plataforma.
- **[Ações de contexto](interactivity.md#Context_Actions)**  &ndash; ListView dá suporte a planos de ação em itens individuais em uma lista. Por exemplo, você pode implementar o dedo para a ação no iOS, ou toque longa ações no Android.
- **[Seleção de](interactivity.md#selectiontaps)**  &ndash; você pode escutar seleções e contíguas providências quando uma linha é tocada.

![](images/context-default.png "ListView com ações de contexto")

Para saber mais sobre os recursos de interatividade do ListView, consulte [ações & interatividade com ListView](interactivity.md).


## <a name="related-links"></a>Links relacionados

- [Trabalhando com ListView (exemplo)](https://developer.xamarin.com/samples/WorkingWithListview)
- [Associação bidirecional (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [Criado em células (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Células personalizadas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Agrupamento (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Exibição personalizada do processador (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView interatividade (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [pasta de trabalho do iOS](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-ios.workbook)
- [Pasta de trabalho do Android](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-android.workbook)
