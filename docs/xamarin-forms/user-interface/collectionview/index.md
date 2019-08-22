---
title: CollectionView do Xamarin. Forms
description: CollectionView é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888601"
---
# <a name="xamarinforms-collectionview"></a>CollectionView do Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

O [`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.

## <a name="datapopulate-datamd"></a>[Dados](populate-data.md)

Um [`CollectionView`](xref:Xamarin.Forms.CollectionView) é populado com dados definindo [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sua propriedade para qualquer coleção que `IEnumerable`implementa. A aparência de cada item na lista pode ser definida definindo a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)como a.

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Por padrão, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, listas e grades verticais e horizontais podem ser especificadas.

## <a name="selectionselectionmd"></a>[Seleção](selection.md)

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) a seleção está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.

## <a name="empty-viewsemptyviewmd"></a>[Exibições vazias](emptyview.md)

No [`CollectionView`](xref:Xamarin.Forms.CollectionView), um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrollingscrollingmd"></a>[Rolagem](scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, [`CollectionView`](xref:Xamarin.Forms.CollectionView) o define [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dois métodos, que rolam programaticamente os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.

## <a name="groupinggroupingmd"></a>[Agrupamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)pode exibir dados agrupados corretamente definindo sua `IsGrouped` Propriedade como. `true`
