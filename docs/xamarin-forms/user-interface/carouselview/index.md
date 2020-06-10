---
Título: " Xamarin.Forms CarouselView" Descrição: "o CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens".
MS. Prod: xamarin MS. AssetID: 5b673347-cdba-4532-820f-fb5f070c86bc MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/08/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

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
