---
title: Xamarin.FormsSwipeView
description: O Xamarin.Forms SwipeView é um controle de contêiner que envolve um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c9d0621cdd5bb85690771d8bdfd0060b6a352cb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136247"
---
# <a name="xamarinforms-swipeview"></a>Xamarin.FormsSwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

O `SwipeView` é um controle de contêiner que envolve um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo:

[![Captura de tela do SwipeView passando itens em um CollectionView, no iOS e no Android](swipeview-images/swipeview-collectionview.png "SwipeView passar itens")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView passar itens")

`SwipeView`está disponível em Xamarin.Forms 4,4. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à sua `AppDelegate` classe no Ios, à sua `MainActivity` classe no Android ou à sua `App` classe no UWP, antes de chamar `Forms.Init` :

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` define as propriedades a seguir:

- `LeftItems`, do tipo `SwipeItems` , que representa os itens de passar o dedo que podem ser invocados quando o controle é transformado do lado esquerdo.
- `RightItems`, do tipo `SwipeItems` , que representa os itens do dedo que podem ser invocados quando o controle é transdedodo do lado direito.
- `TopItems`, do tipo `SwipeItems` , que representa os itens do dedo que podem ser invocados quando o controle é transdedodo de cima para baixo.
- `BottomItems`, do tipo `SwipeItems` , que representa os itens de passar o dedo que podem ser invocados quando o controle é transformado da parte inferior para cima.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Além disso, o `SwipeView` herda a [`Content`](xref:Xamarin.Forms.ContentView.Content) propriedade da [`ContentView`](xref:Xamarin.Forms.ContentView) classe. A `Content` propriedade é a propriedade content da `SwipeView` classe e, portanto, não precisa ser definida explicitamente.

A `SwipeView` classe também define quatro eventos:

- `SwipeStarted`é acionado quando um dedo é iniciado. O `SwipeStartedEventArgs` objeto que acompanha esse evento tem uma `SwipeDirection` propriedade, do tipo `SwipeDirection` .
- `SwipeChanging`é acionado à medida que o dedo se move. O `SwipeChangingEventArgs` objeto que acompanha esse evento tem uma `SwipeDirection` propriedade, do tipo `SwipeDirection` e uma `Offset` Propriedade do tipo `double` .
- `SwipeEnded`é acionado quando um dedo termina. O `SwipeEndedEventArgs` objeto que acompanha esse evento tem uma `SwipeDirection` propriedade, do tipo `SwipeDirection` .
- `CloseRequested`é acionado quando os itens do dedo são fechados.

Além disso, `SwipeView` `Open` o inclui `Close` os métodos e, que programaticamente abrem e fecham os itens do dedo, respectivamente.

> [!NOTE]
> `SwipeView`o tem uma plataforma específica no iOS e no Android, que controla a transição usada ao abrir um `SwipeView` . Para obter mais informações, consulte [modo de transição de toque SwipeView no](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) [modo de transição de toque do SwipeView e do Ios no Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Criar um SwipeView

Um `SwipeView` deve definir o conteúdo que `SwipeView` envolve o encapsulamento e os itens do dedo que são revelados pelo gesto de passar o dedo. Os itens do dedo são um ou mais `SwipeItem` objetos que são colocados em uma das quatro `SwipeView` coleções direcionais- `LeftItems` , `RightItems` , `TopItems` ou `BottomItems` .

O exemplo a seguir mostra como criar uma instância de `SwipeView` em XAML:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

Este é o código C# equivalente:

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

Neste exemplo, o `SwipeView` conteúdo é um [`Grid`](xref:Xamarin.Forms.Grid) que contém [`Label`](xref:Xamarin.Forms.Label) :

[![Captura de tela do conteúdo do SwipeView, no iOS e no Android](swipeview-images/swipeview-content.png "Conteúdo do SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Conteúdo do SwipeView")

Os itens do dedo são usados para executar ações no `SwipeView` conteúdo e são revelados quando o controle é transdedodo do lado esquerdo:

[![Captura de tela de itens de deslize do SwipeView, no iOS e no Android](swipeview-images/swipeview-swipeitems.png "SwipeView passar itens")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView passar itens")

Por padrão, um item de dedo é executado quando ele é tocado pelo usuário. No entanto, esse comportamento pode ser alterado. Para obter mais informações, consulte [modo de toque](#swipe-mode).

Depois que um item do dedo é executado, os itens do dedo ficam ocultos e o `SwipeView` conteúdo é exibido novamente. No entanto, esse comportamento pode ser alterado. Para obter mais informações, consulte [comportamento do dedo](#swipe-behavior).

> [!NOTE]
> Passe o dedo sobre o conteúdo e passe o dedo para que os itens possam ser colocados embutidos ou definidos como recursos.

## <a name="swipe-items"></a>Passar os itens

As `LeftItems` `RightItems` coleções,, e `TopItems` `BottomItems` são do tipo `SwipeItems` . A `SwipeItems` classe define as seguintes propriedades:

- `Mode`, do tipo `SwipeMode` , que indica o efeito de uma interação de passar o dedo. Para obter mais informações sobre o modo de toque, consulte [modo de toque](#swipe-mode).
- `SwipeBehaviorOnInvoked`, do tipo `SwipeBehaviorOnInvoked` , que indica como um `SwipeView` se comporta depois que um item de dedo é invocado. Para obter mais informações sobre o comportamento do dedo, consulte [comportamento do dedo](#swipe-behavior).

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Cada item do dedo é definido como um `SwipeItem` objeto que é colocado em uma das quatro `SwipeItems` coleções direcionais. A `SwipeItem` classe deriva da [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe e adiciona os seguintes membros:

- Uma `BackgroundColor` propriedade, do tipo `Color` , que define a cor do plano de fundo do item do dedo. Essa propriedade é apoiada por uma propriedade vinculável.
- Um `Invoked` evento, que é acionado quando o item do dedo é executado.

> [!IMPORTANT]
> A [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe define várias propriedades, incluindo `Command` , `CommandParameter` , `IconImageSource` e `Text` . Essas propriedades podem ser definidas em um `SwipeItem` objeto para definir sua aparência e para definir um `ICommand` que é executado quando o item do dedo é invocado. Para obter mais informações, consulte [ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

O exemplo a seguir mostra dois `SwipeItem` objetos na `LeftItems` coleção de um `SwipeView` :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

A aparência de cada `SwipeItem` um é definida por uma combinação das `Text` `IconImageSource` Propriedades, e `BackgroundColor` :

[![Captura de tela de itens de deslize do SwipeView, no iOS e no Android](swipeview-images/swipeview-swipeitems.png "SwipeView passar itens")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView passar itens")

Quando um `SwipeItem` é tocado, seu `Invoked` evento é acionado e manipulado por seu manipulador de eventos registrado. Como alternativa, a `Command` propriedade pode ser definida como uma `ICommand` implementação que será executada quando o `SwipeItem` for invocado.

> [!NOTE]
> Quando a aparência de um `SwipeItem` é definida somente usando as `Text` `IconImageSource` Propriedades ou, o conteúdo é sempre centralizado.

Além de definir o dedo de itens como `SwipeItem` objetos, também é possível definir exibições de item de toque personalizado. Para obter mais informações, consulte [Personalizar itens do dedo](#custom-swipe-items).

## <a name="swipe-direction"></a>Direção do dedo

`SwipeView`dá suporte a quatro direções de toque diferentes, com a direção do dedo sendo definida pela `SwipeItems` coleção direcional à `SwipeItem` qual os objetos são adicionados. Cada direção do dedo pode conter seus próprios itens de dedo. Por exemplo, o exemplo a seguir mostra um `SwipeView` cujos itens de passe do dedo dependem da direção do dedo:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

Neste exemplo, o `SwipeView` conteúdo pode ser transdedodo para a direita ou para a esquerda. Passar o dedo para a direita mostrará o item de dedo de **exclusão** , enquanto o dedo para a esquerda mostrará os itens de dedo de **compartilhamento** e **favoritos** .

> [!WARNING]
> Somente uma instância de uma `SwipeItems` coleção direcional pode ser definida por vez em um `SwipeView` . Portanto, você não pode ter duas `LeftItems` definições em um `SwipeView` .

Os `SwipeStarted` `SwipeChanging` eventos, e `SwipeEnded` relatam a direção do dedo por meio da `SwipeDirection` Propriedade nos argumentos do evento. Essa propriedade é do tipo `SwipeDirection` , que é uma enumeração que consiste em quatro membros:

- `Right`indica que um toque à direita ocorreu.
- `Left`indica que ocorreu um dedo à esquerda.
- `Up`indica que ocorreu um toque para cima.
- `Down`indica que um dedo para baixo ocorreu.

## <a name="swipe-mode"></a>Modo de toque

A `SwipeItems` classe tem uma `Mode` propriedade, que indica o efeito de uma interação de passar o dedo. Essa propriedade deve ser definida como um dos `SwipeMode` membros da enumeração:

- `Reveal`indica que um dedo revela os itens do dedo. Este é o valor padrão da propriedade `SwipeItems.Mode`.
- `Execute`indica que um toque executa os itens do dedo.

No modo de revelação, o usuário passa um dedo `SwipeView` para abrir um menu que consiste em um ou mais itens de toque e deve tocar explicitamente em um item de dedo para executá-lo. Depois que o item do dedo for executado, os itens do dedo serão fechados e o `SwipeView` conteúdo será exibido novamente. No modo de execução, o usuário passa um dedo `SwipeView` para abrir um menu que consiste em um ou mais itens de passar o dedo, que são executados automaticamente. Após a execução, os itens do dedo são fechados e o `SwipeView` conteúdo é exibido novamente.

O exemplo a seguir mostra um `SwipeView` configurado para usar o modo de execução:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

Neste exemplo, o `SwipeView` conteúdo pode ser transdedodo direito para revelar o item do dedo, que é executado imediatamente. Após a execução, o `SwipeView` conteúdo será exibido novamente.

## <a name="swipe-behavior"></a>Comportamento do dedo

A `SwipeItems` classe tem uma `SwipeBehaviorOnInvoked` propriedade, que indica como um `SwipeView` se comporta depois que um item de dedo é invocado. Essa propriedade deve ser definida como um dos `SwipeBehaviorOnInvoked` membros da enumeração:

- `Auto`indica que no modo de revelação o `SwipeView` é fechado depois que um item de dedo é invocado e, no modo de execução, o `SwipeView` permanece aberto depois que um item de dedo é invocado. Este é o valor padrão da propriedade `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close`indica que o `SwipeView` fecha depois que um item de dedo é invocado.
- `RemainOpen`indica que o `SwipeView` permanece aberto depois que um item de dedo é invocado.

O exemplo a seguir mostra um `SwipeView` configurado para permanecer aberto depois que um item de dedo é invocado:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>Itens do dedo personalizado

Itens de passe do dedo personalizados podem ser definidos com o `SwipeItemView` tipo. A `SwipeItemView` classe deriva da [`ContentView`](xref:Xamarin.Forms.ContentView) classe e adiciona as seguintes propriedades:

- `Command`, do tipo `ICommand` , que é executado quando um item de dedo é tocado.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `SwipeItemView` classe também define um `Invoked` evento que é disparado quando o item é tocado, depois que o `Command` é executado.

O exemplo a seguir mostra um `SwipeItemView` objeto na `LeftItems` coleção de um `SwipeView` :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

Neste exemplo, o `SwipeItemView` inclui um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contém um [`Entry`](xref:Xamarin.Forms.Entry) e um [`Label`](xref:Xamarin.Forms.Label) . Depois que o usuário insere a entrada no `Entry` , o restante do `SwipeViewItem` pode ser tocado, o que executa o `ICommand` definido pela `SwipeItemView.Command` propriedade.

## <a name="open-and-close-a-swipeview-programmatically"></a>Abrir e fechar um SwipeView programaticamente

`SwipeView`inclui `Open` `Close` os métodos e, que programaticamente abrem e fecham os itens do dedo, respectivamente.

O `Open` método requer um `OpenSwipeItem` argumento para especificar a direção da qual o `SwipeView` será aberto. A `OpenSwipeItem` enumeração tem quatro membros:

- `LeftItems`, que indica que o `SwipeView` será aberto da esquerda, para revelar os itens do dedo na `LeftItems` coleção.
- `TopItems`, que indica que o `SwipeView` será aberto na parte superior, para revelar os itens do dedo na `TopItems` coleção.
- `RightItems`, que indica que o `SwipeView` será aberto da direita, para revelar os itens do dedo na `RightItems` coleção.
- `BottomItems`, que indica que o `SwipeView` será aberto na parte inferior, para revelar os itens do dedo na `BottomItems` coleção.

Dado um `SwipeView` nomeado `swipeView` , o exemplo a seguir mostra como abrir um `SwipeView` para revelar os itens do dedo na `LeftItems` coleção:

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

O `swipeView` pode então ser fechado com o `Close` método:

```csharp
swipeView.Close();
```

> [!NOTE]
> Quando o `Close` método é invocado, o `CloseRequested` evento é acionado.

## <a name="disable-a-swipeview"></a>Desabilitar um SwipeView

Um aplicativo pode inserir um estado no qual o toque de um item de conteúdo não é uma operação válida. Nesses casos, o `SwipeView` pode ser desabilitado definindo sua `IsEnabled` propriedade como `false` . Isso impedirá que os usuários possam passar o dedo no conteúdo para revelar os itens do dedo.

Além disso, ao definir a `Command` propriedade de um `SwipeItem` ou `SwipeItemView` , o `CanExecute` delegado de `ICommand` pode ser especificado para habilitar ou desabilitar o item de deslize.

## <a name="related-links"></a>Links relacionados

- [SwipeView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.FormsMenuItem](~/xamarin-forms/user-interface/menuitem.md)
