---
title: Introdução de CollectionView do Xamarin. Forms
description: CollectionView é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 14abf2e7eff64d2e3e9656bf1ca76f4cee615408
ms.sourcegitcommit: 5ef92b44f0d10c58013d3c3dd6283509f1499587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69986079"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introdução de CollectionView do Xamarin. Forms

![Esta API está atualmente em pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição para apresentar listas de dados usando especificações de layout diferentes. Ele visa fornecer uma alternativa mais flexível e de alto desempenho para [`ListView`](xref:Xamarin.Forms.ListView)o. Por exemplo, as capturas de tela a `CollectionView` seguir mostram um que usa uma grade vertical de duas colunas e que permite seleção múltipla:

[ ![Captura de tela de um layout de grade vertical CollectionView, em layout de](introduction-images/verticalgrid-multipleselection.png "grade vertical CollectionView") para IOS e Android com seleção múltipla] (introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout de grade vertical CollectionView com seleção múltipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)está disponível no Xamarin. Forms 4,0. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à `AppDelegate` sua classe no Ios ou à sua `MainActivity` classe no Android, antes de `Forms.Init`chamar:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)está disponível no iOS e no Android, mas está [disponível apenas parcialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) no plataforma universal do Windows.

## <a name="collectionview-and-listview-differences"></a>Diferenças de CollectionView e ListView

Embora as [`CollectionView`](xref:Xamarin.Forms.CollectionView) APIs [`ListView`](xref:Xamarin.Forms.ListView) e sejam semelhantes, há algumas diferenças notáveis:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)tem um modelo de layout flexível, que permite que os dados sejam apresentados vertical ou horizontalmente, em uma lista ou em uma grade.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte à seleção única e múltipla.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)Não tem conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)utiliza automaticamente a virtualização fornecida pelos controles nativos subjacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)reduz a superfície de API [`ListView`](xref:Xamarin.Forms.ListView)do. Muitas propriedades e eventos do [`ListView`](xref:Xamarin.Forms.ListView) não estão presentes no `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)Não inclui separadores internos.

## <a name="move-from-listview-to-collectionview"></a>Mover de ListView para CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)implementações em implementações existentes do Xamarin. Forms podem ser [`CollectionView`](xref:Xamarin.Forms.CollectionView) migradas para implementações com a ajuda da seguinte tabela:

| Conceito | API de ListView | CollectionView |
|---|---|---|
| Dados | `ItemsSource` | Um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é preenchido com dados definindo sua `ItemsSource` propriedade. Para obter mais informações, consulte [popular um CollectionView com dados](populate-data.md#populate-a-collectionview-with-data). |
| Aparência do item | `ItemTemplate` | A aparência de cada item em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser definida definindo a `ItemTemplate` Propriedade como [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)a. Para obter mais informações, consulte [definir a aparência do item](populate-data.md#define-item-appearance). |
| Células | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Não tem conceito de células. Em vez disso, um modelo de dados é usado para definir a aparência de cada item de dados na lista. |
| Separadores de linha | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Não inclui separadores internos. Eles podem ser fornecidos, se desejado, no modelo de item. |
| Seleção | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)dá suporte à seleção única e múltipla. Para obter mais informações, consulte [seleção de CollectionView do Xamarin. Forms](selection.md). |
| Altura da linha | `HasUnevenRows`, `RowHeight` | Em um `CollectionView`, a altura da linha de cada item é determinada `ItemSizingStrategy` pela propriedade. Para obter mais informações, consulte dimensionamento de [Item](layout.md#item-sizing).|
| Cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)usa automaticamente a virtualização fornecida pelos controles nativos subjacentes. |
| Cabeçalhos e rodapés | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)pode apresentar um cabeçalho e um rodapé que rolam com os itens da lista, por `Header`meio `Footer`das `HeaderTemplate` `FooterTemplate` Propriedades,, e. Para obter mais informações, consulte [cabeçalhos e rodapés](layout.md#headers-and-footers). |
| Agrupamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)exibe dados agrupados corretamente definindo sua `IsGrouped` Propriedade como. `true` Cabeçalhos de grupo e rodapés de grupo podem ser personalizados definindo `GroupHeaderTemplate` as `GroupFooterTemplate` Propriedades e [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) como objetos. Para obter mais informações, consulte [agrupamento CollectionView do Xamarin. Forms](grouping.md). |
| Efetuar pull para atualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | O pull para a atualização não tem suporte no `CollectionView`momento no, mas será adicionado em uma versão futura. |
| Ações de contexto | `ContextActions` | No momento, não há suporte para ações `CollectionView`de contexto no, mas elas serão adicionadas em uma versão futura. |
| Rolagem | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)define `ScrollTo` métodos, que rolam itens para exibição. Para obter mais informações, consulte [rolagem](scrolling.md). |

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
