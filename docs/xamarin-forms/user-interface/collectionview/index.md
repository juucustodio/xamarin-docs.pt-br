---
title: Xamarin. Forms CollectionView
description: O CollectionView é uma exibição flexível e de alto desempenho para apresentações de listas de dados usando as especificações de layout diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 79af2bb2ecda8d3d8df9ee9ec40276b0c59b6864
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005312"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

O [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é uma exibição flexível e de alto desempenho para apresentações de listas de dados usando as especificações de layout diferente.

## <a name="datapopulate-datamd"></a>[Dados](populate-data.md)

Um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) é preenchida com dados, definindo sua [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implemente `IEnumerable`. A aparência de cada item na lista pode ser definida ao configurar o [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Por padrão, uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) exibirá seus itens em uma lista vertical. No entanto, grades e listas verticais e horizontais podem ser especificadas.

## <a name="selectionselectionmd"></a>[Seleção](selection.md)

Por padrão, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, a seleção única e múltipla pode ser habilitada.

## <a name="empty-viewsemptyviewmd"></a>[Modos de exibição vazios](emptyview.md)

Na [ `CollectionView` ](xref:Xamarin.Forms.CollectionView), um modo de exibição vazio pode ser especificado que fornece comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou vários modos de exibição.

## <a name="scrollingscrollingmd"></a>[Rolagem](scrolling.md)

Quando um dedo do usuário para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens são totalmente exibidas. Além disso, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) define dois [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolagem programaticamente os itens na exibição. Uma das sobrecargas rola o item no índice especificado na exibição, enquanto o outro rola o item especificado na exibição.
