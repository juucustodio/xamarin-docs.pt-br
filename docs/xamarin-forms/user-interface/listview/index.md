---
title: Xamarin.Forms Descrição de "ListView": "este guia apresenta o Xamarin.Forms ListView, que pode ser usado para apresentar dados em listas interativas".
MS. Prod: xamarin MS. AssetID: FEFDF7E0-720F-4BD1-863F-4477226AA695 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 09/04/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-listview"></a>Xamarin.FormsFotos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)é uma exibição para apresentar listas de dados, especialmente listas longas que exigem rolagem.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para o [`ListView`](xref:Xamarin.Forms.ListView) . Para obter mais informações, consulte [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

Um `ListView` controle pode ser usado em qualquer situação em que você esteja Exibindo listas de dados roláveis. A `ListView` classe dá suporte a ações de contexto e Associação de dados.

O `ListView` controle não deve ser confundido com o [`TableView`](~/xamarin-forms/user-interface/tableview.md) controle. O `TableView` controle é uma opção melhor sempre que você tem uma lista não vinculada de opções ou dados, pois permite que opções predefinidas sejam especificadas em XAML. Por exemplo, o aplicativo de configurações do iOS, que tem um conjunto de opções da maioria predefinida, é mais adequado para usar um `TableView` do que um `ListView` .

A `ListView` classe não dá suporte à definição de itens de lista em XAML, você deve usar a `ItemsSource` propriedade ou a associação de dados com um `ItemTemplate` para definir itens na lista.

Um `ListView` é mais adequado para coleções que consistem em um único tipo de dados. Esse requisito é porque apenas um tipo de célula pode ser usado para cada linha na lista. O `TableView` controle pode dar suporte a vários tipos de células, portanto, é uma opção melhor quando você precisa exibir vários tipos de dados.

Para obter mais informações sobre como associar dados a uma `ListView` instância do, consulte [fontes de dados do ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componentes

O `ListView` controle tem vários componentes disponíveis para exercitar a funcionalidade nativa de cada plataforma. Esses componentes são definidos nas seções a seguir.

### <a name="headers-and-footers"></a>[Cabeçalhos e rodapés](customizing-list-appearance.md#headers-and-footers)

Os componentes de cabeçalho e rodapé são exibidos no início e no final de uma lista, separados dos dados da lista. Cabeçalhos e rodapés podem ser associados a uma fonte de dados separada da fonte de dados do ListView.

### <a name="groups"></a>[Grupos](customizing-list-appearance.md#grouping)

Os dados em um `ListView` podem ser agrupados para facilitar a navegação. Normalmente, os grupos são associados a dados. A captura de tela a seguir mostra um `ListView` com dados agrupados:

[!["Dados agrupados em um ListView"](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "Dados agrupados em um ListView")

### <a name="cells"></a>[Células](customizing-cell-appearance.md)

Os itens de dados em uma `ListView` são chamados de células. Cada célula corresponde a uma linha de dados. Há células internas para escolher ou você pode definir sua própria célula Personalizada. Tanto as células internas quanto as personalizadas podem ser usadas/definidas em XAML ou código.

- [As células internas](customizing-cell-appearance.md#built-in-cells), como `TextCell` e `ImageCell` , correspondem a controles nativos e são especialmente com bom desempenho.
  - Um [`TextCell`](customizing-cell-appearance.md#textcell) exibe uma cadeia de texto, opcionalmente com texto detalhado. O texto detalhado é renderizado como uma segunda linha em uma fonte menor com uma cor de destaque.
  - Um [`ImageCell`](customizing-cell-appearance.md#imagecell) exibe uma imagem com texto. Aparece como um `TextCell` com uma imagem à esquerda.
- [As células personalizadas](customizing-cell-appearance.md#custom-cells) são usadas para apresentar dados complexos. Por exemplo, uma célula personalizada pode ser usada para apresentar uma lista de músicas que incluem o álbum e o artista.

A captura de tela a seguir mostra um `ListView` com itens ImageCell:

[!["Itens ImageCell em um ListView"](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "ImageCell itens em um ListView")

Para saber mais sobre como personalizar células em um `ListView` , consulte [Personalizando a aparência da célula ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidade

A `ListView` classe oferece suporte a vários estilos de interação.

- O [pull para atualização](interactivity.md#pull-to-refresh) permite que o usuário puxe `ListView` para baixo para atualizar o conteúdo.
- As [ações de contexto](interactivity.md#context-actions) permitem que o desenvolvedor especifique ações personalizadas em itens de lista individuais. Por exemplo, você pode implementar o passar o dedo para a ação no iOS ou ações de toque longo no Android.
- A [seleção](interactivity.md#selection-and-taps) permite que o desenvolvedor anexe a funcionalidade aos eventos de seleção e desmarcação em itens de lista.

A captura de tela a seguir mostra um `ListView` com ações de contexto:

[!["Ações de contexto em um ListView"](images/context-default-cropped.png)](images/context-default.png#lightbox "Ações de contexto em um ListView")

Para saber mais sobre os recursos de interatividade do `ListView` , consulte [ações & interatividade com ListView](interactivity.md).

## <a name="related-links"></a>Links relacionados

- [Trabalhando com ListView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Associação de duas vias (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Células internas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Células personalizadas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Agrupamento (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Exibição do renderizador personalizado (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interatividade de ListView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
