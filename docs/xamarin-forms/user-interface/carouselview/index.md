---
title: CarouselView Xamarin. Forms
description: O CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar a passar por uma coleção de itens.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696980"
---
# <a name="xamarinforms-carouselview"></a>CarouselView Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

O [`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar por uma coleção de itens.

## <a name="datapopulate-datamd"></a>[Dados](populate-data.md)

Uma [`CarouselView`](xref:Xamarin.Forms.CarouselView) é populada com dados definindo sua propriedade [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) para qualquer coleção que implemente `IEnumerable`. A aparência de cada item pode ser definida definindo a propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Layout](layout.md)

Por padrão, um [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens em uma lista horizontal. No entanto, ele também tem acesso aos mesmos layouts que CollectionView, incluindo uma orientação vertical.

## <a name="interactioninteractionmd"></a>[Inter](interaction.md)

O item exibido no momento em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser acessado por meio das propriedades `CurrentItem` e `Position`.

## <a name="empty-viewsemptyviewmd"></a>[Exibições vazias](emptyview.md)

No [`CarouselView`](xref:Xamarin.Forms.CarouselView), um modo de exibição vazio pode ser especificado para fornecer comentários ao usuário quando nenhum dado está disponível para exibição. O modo de exibição vazio pode ser uma cadeia de caracteres, uma exibição ou várias exibições.

## <a name="scrollingscrollingmd"></a>[Rolagem](scrolling.md)

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dois métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que programaticamente rolam os itens para a exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição.
