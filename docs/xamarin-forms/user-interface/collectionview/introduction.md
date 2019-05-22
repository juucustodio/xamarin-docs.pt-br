---
title: Introdução do xamarin. Forms CollectionView
description: O CollectionView é uma exibição flexível e de alto desempenho para apresentações de listas de dados usando as especificações de layout diferente.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 2ee7b2c203251e519af088a550e7e26f30aa62c8
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971095"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introdução do xamarin. Forms CollectionView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) é um modo de exibição para apresentar as listas de dados usando as especificações de layout diferente. Tem como objetivo fornecer uma mais flexível e alternativa de alto desempenho para o [ `ListView` ](xref:Xamarin.Forms.ListView). Por exemplo, capturas de tela as seguir mostram um `CollectionView` que usa uma grade vertical de duas colunas, e que permite que a seleção múltipla:

[![Captura de tela de um layout de grade vertical CollectionView, no iOS e Android](introduction-images/verticalgrid-multipleselection.png "CollectionView layout de grade vertical com seleção múltipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "CollectionView layout de grade vertical com seleção múltipla")

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) só está disponível no iOS e Android.

## <a name="collectionview-and-listview-differences"></a>Diferenças de CollectionView e ListView

Enquanto o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) e [ `ListView` ](xref:Xamarin.Forms.ListView) APIs são semelhantes, existem algumas diferenças importantes:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um modelo de layout flexível que permite que os dados a ser apresentado verticalmente ou horizontalmente, em uma lista ou uma grade.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte a única e seleção múltipla.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) não tem nenhum conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utiliza automaticamente a virtualização fornecida pelos controles nativos subjacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) reduz a superfície de API do [ `ListView` ](xref:Xamarin.Forms.ListView). Muitas propriedades e eventos do [ `ListView` ](xref:Xamarin.Forms.ListView) não estão presentes no `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) não inclui separadores internos.

## <a name="move-from-listview-to-collectionview"></a>Mover de ListView para CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) implementações em implementações existentes do xamarin. Forms podem ser migradas para [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) implementações com a Ajuda da tabela a seguir:

| Conceito | API de ListView | CollectionView |
|---|---|---|
| Dados | `ItemsSource` | Um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é preenchida com dados, definindo seu `ItemsSource` propriedade. Para obter mais informações, consulte [popular um CollectionView de dados](populate-data.md#populate-a-collectionview-with-data). |
| Aparência do item | `ItemTemplate` | A aparência de cada item em uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser definida ao configurar o `ItemTemplate` propriedade a um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Para obter mais informações, consulte [definem a aparência do item](populate-data.md#define-item-appearance). |
| Células | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) não tem nenhum conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista. |
| Separadores de linha | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) não inclui separadores internos. Eles podem ser fornecidos, se desejado, no modelo de item. |
| Seleção | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) dá suporte a única e seleção múltipla. Para obter mais informações, consulte [xamarin. Forms CollectionView seleção](selection.md). |
| Altura da linha | `HasUnevenRows`, `RowHeight` | Em um `CollectionView`, a altura da linha de cada item é determinada pelo `ItemSizingStrategy` propriedade. Para obter mais informações, consulte [dimensionamento Item](layout.md#item-sizing).|
| Cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa automaticamente a virtualização fornecida pelos controles nativos subjacentes. |
| Cabeçalhos e rodapés | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | Cabeçalhos e rodapés não são atualmente suportados no `CollectionView`, mas será adicionado em uma versão futura.|
| Agrupamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | Agrupamento não é suportado atualmente no `CollectionView`, mas será adicionado em uma versão futura. |
| Deslizar para atualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Deslizar para atualizar não é suportado atualmente no `CollectionView`, mas será adicionado em uma versão futura. |
| Ações de contexto | `ContextActions` | Ações de contexto não têm suporte no momento no `CollectionView`, mas será adicionado em uma versão futura. |
| Rolagem | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) define `ScrollTo` métodos, que rolagem os itens na exibição. Para obter mais informações, consulte [rolagem](scrolling.md). |

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
