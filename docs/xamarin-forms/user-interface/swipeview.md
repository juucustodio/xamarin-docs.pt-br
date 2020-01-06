---
title: SwipeView Xamarin. Forms
description: O SwipeView Xamarin. Forms é um controle de contêiner que encapsula um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 4119a650c431013bb0c8e680de600ed4e73d0c93
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490384"
---
# <a name="xamarinforms-swipeview"></a>SwipeView Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

O `SwipeView` é um controle de contêiner que envolve um item de conteúdo e fornece itens de menu de contexto que são revelados por um gesto de passar o dedo:

[![Captura de tela do SwipeView passando itens em um CollectionView, no iOS e no Android](swipeview-images/swipeview-collectionview.png "SwipeView passar itens")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView passar itens")

`SwipeView` está disponível no Xamarin. Forms 4,4. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à sua classe `AppDelegate` no iOS, à sua classe `MainActivity` no Android ou à sua classe `App` em UWP, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` Define as propriedades a seguir:

- `LeftItems`, do tipo `SwipeItems`, que representa os itens do dedo que podem ser invocados quando o controle é transdedodo do lado esquerdo.
- `RightItems`, do tipo `SwipeItems`, que representa os itens do dedo que podem ser invocados quando o controle é transdedodo do lado direito.
- `TopItems`, do tipo `SwipeItems`, que representa os itens do dedo que podem ser invocados quando o controle é transdedodo de cima para baixo.
- `BottomItems`, do tipo `SwipeItems`, que representa os itens do dedo que podem ser invocados quando o controle é transformado de baixo para cima.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser destinos de associações de dados e estilizadas.

Além disso, o `SwipeView` herda a propriedade [`Content`](xref:Xamarin.Forms.ContentView.Content) da classe [`ContentView`](xref:Xamarin.Forms.ContentView) . A propriedade `Content` é a propriedade content da classe `SwipeView` e, portanto, não precisa ser definida explicitamente.

A classe `SwipeView` também define quatro eventos:

- `SwipeStarted` é acionado quando um dedo é iniciado. O objeto `SwipeStartedEventArgs` que acompanha esse evento tem uma propriedade `SwipeDirection`, do tipo `SwipeDirection`.
- `SwipeChanging` é acionado à medida que o dedo se move. O objeto `SwipeChangingEventArgs` que acompanha esse evento tem uma propriedade `SwipeDirection`, do tipo `SwipeDirection`e uma propriedade `Offset` do tipo `double`.
- `SwipeEnded` é acionado quando um dedo termina. O objeto `SwipeEndedEventArgs` que acompanha esse evento tem uma propriedade `SwipeDirection`, do tipo `SwipeDirection`.
- `CloseRequested` é acionado quando os itens do dedo são fechados.

Além disso, `SwipeView` define um método `Close`, que fecha os itens do dedo.

> [!NOTE]
> o `SwipeView` tem uma plataforma específica no iOS e no Android, que controla a transição usada ao abrir um `SwipeView`. Para obter mais informações, consulte [modo de transição de toque SwipeView no](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) [modo de transição de toque do SwipeView e do Ios no Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Criar um SwipeView

Um `SwipeView` deve definir o conteúdo que o `SwipeView` encapsulado e os itens do dedo que são revelados pelo gesto de passar o dedo. Os itens do dedo são um ou mais objetos `SwipeItem` que são colocados em uma das quatro coleções direcionais `SwipeView`-`LeftItems`, `RightItems`, `TopItems`ou `BottomItems`.

O exemplo a seguir mostra como instanciar um `SwipeView` no XAML:

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

Neste exemplo, o conteúdo de `SwipeView` é uma [`Grid`](xref:Xamarin.Forms.Grid) que contém uma [`Label`](xref:Xamarin.Forms.Label):

[![Captura de tela do conteúdo do SwipeView, no iOS e no Android](swipeview-images/swipeview-content.png "Conteúdo do SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Conteúdo do SwipeView")

Os itens do dedo são usados para executar ações no conteúdo de `SwipeView` e são revelados quando o controle é transformado do lado esquerdo:

[![Captura de tela de itens de deslize do SwipeView, no iOS e no Android](swipeview-images/swipeview-swipeitems.png "SwipeView passar itens")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView passar itens")

Por padrão, um item de dedo é executado quando ele é tocado pelo usuário. No entanto, esse comportamento pode ser alterado. Para obter mais informações, consulte [modo de toque](#swipe-mode).

Depois que um item do dedo é executado, os itens do dedo ficam ocultos e o conteúdo do `SwipeView` é exibido novamente. No entanto, esse comportamento pode ser alterado. Para obter mais informações, consulte [comportamento do dedo](#swipe-behavior).

> [!NOTE]
> Passe o dedo sobre o conteúdo e passe o dedo para que os itens possam ser colocados embutidos ou definidos como recursos.

## <a name="swipe-items"></a>Passar os itens

As coleções `LeftItems`, `RightItems`, `TopItems`e `BottomItems` são do tipo `SwipeItems`. A classe `SwipeItems` define as seguintes propriedades:

- `Mode`, do tipo `SwipeMode`, que indica o efeito de uma interação de passar o dedo. Para obter mais informações sobre o modo de toque, consulte [modo de toque](#swipe-mode).
- `SwipeBehaviorOnInvoked`, do tipo `SwipeBehaviorOnInvoked`, que indica como um `SwipeView` se comporta depois que um item de dedo é invocado. Para obter mais informações sobre o comportamento do dedo, consulte [comportamento do dedo](#swipe-behavior).

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser destinos de associações de dados e estilizadas.

Cada item de deslize é definido como um objeto `SwipeItem` que é colocado em uma das quatro coleções direcionais `SwipeItems`. A classe `SwipeItem` deriva da classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) e adiciona os seguintes membros:

- Uma propriedade `BackgroundColor`, do tipo `Color`, que define a cor do plano de fundo do item do dedo. Essa propriedade é apoiada por uma propriedade vinculável.
- Um evento `Invoked`, que é acionado quando o item do dedo é executado.

> [!IMPORTANT]
> A classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) define várias propriedades, incluindo `Command`, `CommandParameter`, `IconImageSource`e `Text`. Essas propriedades podem ser definidas em um objeto `SwipeItem` para definir sua aparência e para definir um `ICommand` que é executado quando o item do dedo é invocado. Para obter mais informações, consulte [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

O exemplo a seguir mostra dois objetos `SwipeItem` na coleção `LeftItems` de um `SwipeView`:

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

A aparência de cada `SwipeItem` é definida pelas propriedades `Text`, `IconImageSource`e `BackgroundColor`:

[![Captura de tela de itens de deslize do SwipeView, no iOS e no Android](swipeview-images/swipeview-swipeitems.png "SwipeView passar itens")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView passar itens")

Quando um `SwipeItem` é tocado, seu evento de `Invoked` é acionado e manipulado por seu manipulador de eventos registrado. Como alternativa, a propriedade `Command` pode ser definida como uma implementação de `ICommand` que será executada quando o `SwipeItem` for invocado.

> [!NOTE]
> Além de definir os itens do dedo como `SwipeItem` objetos, também é possível definir exibições de item de toque personalizado. Para obter mais informações, consulte [Personalizar itens do dedo](#custom-swipe-items).

## <a name="swipe-direction"></a>Direção do dedo

o `SwipeView` dá suporte a quatro direções de toque diferentes, com a direção do dedo sendo definida pela coleção de `SwipeItems` direcional à qual os objetos `SwipeItem` são adicionados. Cada direção do dedo pode conter seus próprios itens de dedo. Por exemplo, o exemplo a seguir mostra um `SwipeView` cujos itens do dedo dependem da direção do dedo:

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

Neste exemplo, o conteúdo do `SwipeView` pode ser transdedodo para a direita ou para a esquerda. Passar o dedo para a direita mostrará o item de dedo de **exclusão** , enquanto o dedo para a esquerda mostrará os itens de dedo de **compartilhamento** e **favoritos** .

> [!WARNING]
> Somente uma instância de uma coleção de `SwipeItems` direcional pode ser definida por vez em um `SwipeView`. Portanto, você não pode ter duas definições de `LeftItems` em um `SwipeView`.

Os eventos `SwipeStarted`, `SwipeChanging`e `SwipeEnded` relatam a direção do dedo por meio da propriedade `SwipeDirection` nos argumentos do evento. Essa propriedade é do tipo `SwipeDirection`, que é uma enumeração que consiste em quatro membros:

- `Right` indica que ocorreu um toque à direita.
- `Left` indica que ocorreu um dedo à esquerda.
- `Up` indica que ocorreu um toque para cima.
- `Down` indica que ocorreu um dedo para baixo.

## <a name="swipe-mode"></a>Modo de toque

A classe `SwipeItems` tem uma propriedade `Mode`, que indica o efeito de uma interação de passar o dedo. Essa propriedade deve ser definida como um dos membros de enumeração de `SwipeMode`:

- `Reveal` indica que um dedo revela os itens do dedo. Este é o valor padrão da propriedade `SwipeItems.Mode`.
- `Execute` indica que um toque executa os itens do dedo.

No modo revelar, o usuário passa um `SwipeView` para abrir um menu que consiste em um ou mais itens de toque e deve tocar explicitamente em um item de dedo para executá-lo. Depois que o item do dedo for executado, os itens do dedo serão fechados e o conteúdo do `SwipeView` será exibido novamente. No modo de execução, o usuário passa um `SwipeView` para abrir um menu que consiste em um ou mais itens de passar o dedo, que são executados automaticamente. Após a execução, os itens do dedo são fechados e o conteúdo do `SwipeView` é exibido novamente.

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

Neste exemplo, o conteúdo do `SwipeView` pode ser transdedodo direito para revelar o item do dedo, que é executado imediatamente. Após a execução, o conteúdo do `SwipeView` é exibido novamente.

## <a name="swipe-behavior"></a>Comportamento do dedo

A classe `SwipeItems` tem uma propriedade `SwipeBehaviorOnInvoked`, que indica como um `SwipeView` se comporta depois que um item de dedo é invocado. Essa propriedade deve ser definida como um dos membros de enumeração de `SwipeBehaviorOnInvoked`:

- `Auto` indica que no modo de revelação o `SwipeView` é fechado depois que um item de dedo é invocado e, no modo de execução, o `SwipeView` permanece aberto depois que um item de dedo é invocado. Este é o valor padrão da propriedade `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indica que o `SwipeView` é fechado depois que um item de dedo é invocado.
- `RemainOpen` indica que a `SwipeView` permanece aberta depois que um item de dedo é invocado.

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

Itens de passe do dedo personalizados podem ser definidos com o tipo de `SwipeItemView`. A classe `SwipeItemView` deriva da classe [`ContentView`](xref:Xamarin.Forms.ContentView) e adiciona as seguintes propriedades:

- `Command`, do tipo `ICommand`, que é executado quando um item do dedo é tocado.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser destinos de associações de dados e estilizadas.

A classe `SwipeItemView` também define um evento `Invoked` que é disparado quando o item é tocado, depois que o `Command` é executado.

O exemplo a seguir mostra um objeto `SwipeItemView` na coleção `LeftItems` de um `SwipeView`:

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

Neste exemplo, o `SwipeItemView` consiste em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contém um [`Entry`](xref:Xamarin.Forms.Entry) e um [`Label`](xref:Xamarin.Forms.Label). Depois que o usuário insere a entrada na `Entry`, o restante do `SwipeViewItem` pode ser tocado, o que executa o `ICommand` definido pela propriedade `SwipeItemView.Command`.

## <a name="disable-a-swipeview"></a>Desabilitar um SwipeView

Um aplicativo pode inserir um estado no qual o toque de um item de conteúdo não é uma operação válida. Nesses casos, o `SwipeView` pode ser desabilitado definindo sua propriedade `IsEnabled` como `false`. Isso impedirá que os usuários possam passar o dedo no conteúdo para revelar os itens do dedo.

Além disso, ao definir a propriedade `Command` de um `SwipeItem` ou `SwipeItemView`, o delegado `CanExecute` do `ICommand` pode ser especificado para habilitar ou desabilitar o item de dedo.

## <a name="related-links"></a>Links relacionados

- [SwipeView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
