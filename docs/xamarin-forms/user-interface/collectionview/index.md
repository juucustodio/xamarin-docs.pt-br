---
title: CollectionView do Xamarin. Forms
description: CollectionView é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8050d952556ce0b55a7ce72bc5f25de903fee6e5
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696995"
---
# <a name="xamarinforms-collectionview"></a>CollectionView do Xamarin. Forms

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

A [`CollectionView`](xref:Xamarin.Forms.CollectionView) é uma exibição flexível e de alto desempenho para apresentar listas de dados usando diferentes especificações de layout.

## <a name="datapopulate-datamd"></a>[Dados](populate-data.md)

Uma [`CollectionView`](xref:Xamarin.Forms.CollectionView) é populada com dados definindo sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) para qualquer coleção que implemente `IEnumerable`. A aparência de cada item na lista pode ser definida definindo a propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Por padrão, um [`CollectionView`](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, listas e grades verticais e horizontais podem ser especificadas.

## <a name="selectionselectionmd"></a>[Seleção](selection.md)

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.

## <a name="empty-viewsemptyviewmd"></a>[Exibições vazias](emptyview.md)

No [`CollectionView`](xref:Xamarin.Forms.CollectionView), um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando nenhum dado está disponível para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrollingscrollingmd"></a>[Rolagem](scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define dois métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que programaticamente rolam os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.

## <a name="groupinggroupingmd"></a>[Agrupamento](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pode exibir dados agrupados corretamente definindo sua propriedade `IsGrouped` como `true`.
