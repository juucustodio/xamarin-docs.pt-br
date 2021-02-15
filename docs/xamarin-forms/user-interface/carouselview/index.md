---
title: Xamarin.Forms CarouselView
description: O CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar a passar por uma coleção de itens.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 958a7c292ba636368a016894e98fe8aaff0d0f60
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940558"
---
# <a name="no-locxamarinforms-carouselview"></a>Xamarin.Forms CarouselView

## <a name="introduction"></a>[Introdução](introduction.md)

O [`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens.

## <a name="data"></a>[Dados](populate-data.md)

Um [`CarouselView`](xref:Xamarin.Forms.CarouselView) é populado com dados definindo sua [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriedade para qualquer coleção que implementa `IEnumerable` . A aparência de cada item pode ser definida definindo a [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Layout](layout.md)

Por padrão, um [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens em uma lista horizontal. No entanto, ele também tem acesso aos mesmos layouts que CollectionView, incluindo uma orientação vertical.

## <a name="interaction"></a>[Interação](interaction.md)

O item exibido no momento em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser acessado por meio das `CurrentItem` `Position` Propriedades e.

## <a name="empty-views"></a>[Exibições vazias](emptyview.md)

No [`CarouselView`](xref:Xamarin.Forms.CarouselView) , um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando não há dados disponíveis para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrolling"></a>[Rolagem](scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dois [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolam programaticamente os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.
