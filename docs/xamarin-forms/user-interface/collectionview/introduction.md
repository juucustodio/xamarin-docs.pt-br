---
title: Xamarin.Forms Introdução de CollectionView
description: CollectionView é uma alternativa flexível e de alto desempenho para ListView.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ad9ef36471b14ad44b108189b94bb22d1b3db41
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590240"
---
# <a name="no-locxamarinforms-collectionview-introduction"></a>Xamarin.Forms Introdução de CollectionView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para o [`ListView`](xref:Xamarin.Forms.ListView) . Por exemplo, as capturas de tela a seguir mostram um `CollectionView` que usa uma grade vertical de duas colunas e que permite seleção múltipla:

[![Captura de tela de um layout de grade vertical CollectionView, no iOS e no Android](introduction-images/verticalgrid-multipleselection.png "Layout de grade vertical CollectionView com seleção múltipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout de grade vertical CollectionView com seleção múltipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) deve ser usado para apresentar listas de dados que exigem rolagem ou seleção. Um layout vinculável pode ser usado quando os dados a serem exibidos não exigem rolagem ou seleção. Para obter mais informações, consulte [layouts vinculáveis no Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponível em Xamarin.Forms 4,3.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponível no iOS e no Android, mas está [disponível apenas parcialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) no plataforma universal do Windows.

## <a name="collectionview-and-listview-differences"></a>Diferenças de CollectionView e ListView

Embora as [`CollectionView`](xref:Xamarin.Forms.CollectionView) [`ListView`](xref:Xamarin.Forms.ListView) APIs e sejam semelhantes, há algumas diferenças notáveis:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um modelo de layout flexível, que permite que os dados sejam apresentados vertical ou horizontalmente, em uma lista ou em uma grade.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte à seleção única e múltipla.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) Não tem conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utiliza automaticamente a virtualização fornecida pelos controles nativos subjacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) reduz a superfície de API do [`ListView`](xref:Xamarin.Forms.ListView) . Muitas propriedades e eventos do [`ListView`](xref:Xamarin.Forms.ListView) não estão presentes no `CollectionView` .
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) Não inclui separadores internos.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) gerará uma exceção se sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) for atualizada fora do thread da interface do usuário.

## <a name="move-from-listview-to-collectionview"></a>Mover de ListView para CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) as implementações em Xamarin.Forms aplicativos existentes podem ser migradas para [`CollectionView`](xref:Xamarin.Forms.CollectionView) implementações com a ajuda da seguinte tabela:

| Conceito | API de ListView | CollectionView |
|---|---|---|
| Dados | `ItemsSource` | Um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é preenchido com dados definindo sua `ItemsSource` propriedade. Para obter mais informações, consulte [popular um CollectionView com dados](populate-data.md#populate-a-collectionview-with-data). |
| Aparência do item | `ItemTemplate` | A aparência de cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser definida definindo a `ItemTemplate` propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Para obter mais informações, consulte [definir a aparência do item](populate-data.md#define-item-appearance). |
| Células | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) Não tem conceito de células e, portanto, não há conceito de indicadores de divulgação. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista. |
| Separadores de linha | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) Não inclui separadores internos. Eles podem ser fornecidos, se desejado, no modelo de item. |
| Seleção | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte à seleção única e múltipla. Para obter mais informações, consulte [ Xamarin.Forms seleção CollectionView](selection.md). |
| Altura da linha | `HasUnevenRows`, `RowHeight` | Em um `CollectionView` , a altura da linha de cada item é determinada pela `ItemSizingStrategy` propriedade. Para obter mais informações, consulte [dimensionamento de item](layout.md#item-sizing).|
| Cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa automaticamente a virtualização fornecida pelos controles nativos subjacentes. |
| Cabeçalhos e rodapés | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode apresentar um cabeçalho e um rodapé que rolam com os itens da lista, por meio das `Header` `Footer` Propriedades,, e `HeaderTemplate` `FooterTemplate` . Para obter mais informações, consulte [cabeçalhos e rodapés](layout.md#headers-and-footers). |
| Agrupamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibe dados agrupados corretamente definindo sua `IsGrouped` propriedade como `true` . Cabeçalhos de grupo e rodapés de grupo podem ser personalizados definindo `GroupHeaderTemplate` as `GroupFooterTemplate` Propriedades e como  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos. Para obter mais informações, consulte [ Xamarin.Forms agrupamento CollectionView](grouping.md). |
| Puxar para atualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Há suporte para a funcionalidade de pull para atualizar ao definir um [`CollectionView`](xref:Xamarin.Forms.CollectionView) como o filho de um `RefreshView` . Para obter mais informações, consulte [pull to Refresh](populate-data.md#pull-to-refresh). |
| Itens de menu de contexto | `ContextActions` | Os itens de menu de contexto têm suporte ao definir um `SwipeView` como a exibição raiz no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada item de dados no [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Para obter mais informações, consulte [menus de contexto](populate-data.md#context-menus). |
| Rolagem | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) define `ScrollTo` métodos, que rolam itens para exibição. Para obter mais informações, consulte [rolagem](scrolling.md). |

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Layouts vinculáveis em Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
