---
title: Xamarin.FormsCollectionView
description: CollectionView é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a2c9fd9e6e48192bc2237d6b451b533fcee6e6ed
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136442"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.FormsCollectionView

## <a name="introduction"></a>[Introdução](introduction.md)

O [`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.

## <a name="data"></a>[Dados](populate-data.md)

Um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é populado com dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implementa `IEnumerable` . A aparência de cada item na lista pode ser definida definindo a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Layout](layout.md)

Por padrão, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, listas e grades verticais e horizontais podem ser especificadas.

## <a name="selection"></a>[Seleção](selection.md)

Por padrão, a [`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.

## <a name="empty-views"></a>[Exibições vazias](emptyview.md)

No [`CollectionView`](xref:Xamarin.Forms.CollectionView) , um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrolling"></a>[Rolagem](scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, o [`CollectionView`](xref:Xamarin.Forms.CollectionView) define dois [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolam programaticamente os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.

## <a name="grouping"></a>[Agrupamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir dados agrupados corretamente definindo sua `IsGrouped` propriedade como `true` .
