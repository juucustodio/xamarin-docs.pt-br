---
title: CarouselView Xamarin. Forms
description: O CarouselView é uma exibição para apresentar listas de dados em um layout como um carrossel.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908264"
---
# <a name="xamarinforms-carouselview"></a>CarouselView Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

> [!IMPORTANT]
> A documentação do CarouselView está em desenvolvimento e alguns links podem se referir à documentação CollectionView. Na maioria dos casos, as informações devem ser aplicáveis devido à natureza de CarouselView que se baseiam em CollectionView.

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

O [`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout do tipo carrossel. Sua implementação se baseia no [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="datacollectionviewpopulate-datamd"></a>[Dados](../collectionview/populate-data.md)

Um [`CarouselView`](xref:Xamarin.Forms.CarouselView) é populado com dados definindo [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sua propriedade para qualquer coleção que `IEnumerable`implementa. A aparência de cada item na lista pode ser definida definindo a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)como a.

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Por padrão, um [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens em uma lista horizontal. No entanto, ele também tem acesso aos mesmos layouts que CollectionView, incluindo uma orientação vertical.

## <a name="empty-viewscollectionviewemptyviewmd"></a>[Exibições vazias](../collectionview/emptyview.md)

No [`CarouselView`](xref:Xamarin.Forms.CarouselView), um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrollingcollectionviewscrollingmd"></a>[Rolagem](../collectionview/scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, [`CarouselView`](xref:Xamarin.Forms.CarouselView) o define [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dois métodos, que rolam programaticamente os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.
