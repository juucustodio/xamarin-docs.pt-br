---
title: Xamarin.FormsRolagem de CollectionView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04d190971fa5ef16e08091600558f7f016bc8605
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134505"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin.FormsRolagem de CollectionView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define dois [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolam itens para o modo de exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição. Ambas as sobrecargas têm argumentos adicionais que podem ser especificados para indicar o grupo ao qual o item pertence, a posição exata do item após a conclusão da rolagem e se a rolagem deve ser animada.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define um [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que é acionado quando um dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos é invocado. O [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompanha o `ScrollToRequested` evento tem muitas propriedades, incluindo `IsAnimated` ,, `Index` `Item` e `ScrollToPosition` . Essas propriedades são definidas a partir dos argumentos especificados nas `ScrollTo` chamadas de método.

Além disso, [`CollectionView`](xref:Xamarin.Forms.CollectionView) o define um `Scrolled` evento que é acionado para indicar que a rolagem ocorreu. O `ItemsViewScrolledEventArgs` objeto que acompanha o `Scrolled` evento tem muitas propriedades. Para obter mais informações, consulte [detectar rolagem](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)também define uma `ItemsUpdatingScrollMode` propriedade que representa o comportamento de rolagem do `CollectionView` quando novos itens são adicionados a ele. Para obter mais informações sobre essa propriedade, consulte [controlar posição de rolagem quando novos itens forem adicionados](#control-scroll-position-when-new-items-are-added).

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como encaixe, pois os itens se ajustam à posição quando a rolagem é interrompida. Para obter mais informações, consulte [snap Points](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)também pode carregar dados de forma incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar rolagem

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define um `Scrolled` evento que é acionado para indicar que a rolagem ocorreu. O exemplo de XAML a seguir mostra um `CollectionView` que define um manipulador de eventos para o `Scrolled` evento:

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

Neste exemplo de código, o `OnCollectionViewScrolled` manipulador de eventos é executado quando o evento é disparado `Scrolled` :

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

Neste exemplo, o `OnCollectionViewScrolled` manipulador de eventos gera os valores do `ItemsViewScrolledEventArgs` objeto que acompanha o evento.

> [!IMPORTANT]
> O `Scrolled` evento é acionado para rolagens iniciadas pelo usuário e para rolagens programáticas.

## <a name="scroll-an-item-at-an-index-into-view"></a>Rolar um item em um índice para a exibição

A primeira [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga do método rola o item no índice especificado para a exibição. Dado um [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView` , o exemplo a seguir mostra como rolar o item no índice 12 para a exibição:

```csharp
collectionView.ScrollTo(12);
```

Como alternativa, um item em dados agrupados pode ser rolado para a exibição, especificando os índices de item e de grupo. O exemplo a seguir mostra como rolar o terceiro item no segundo grupo para a exibição:

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> O [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento é acionado quando o [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) método é invocado.

## <a name="scroll-an-item-into-view"></a>Rolar um item para a exibição

A sobrecarga do segundo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) método rola o item especificado para a exibição. Dado um [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView` , o exemplo a seguir mostra como rolar o item de macaco Proboscis para a exibição:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

Como alternativa, um item em dados agrupados pode ser rolado para a exibição, especificando o item e o grupo. O exemplo a seguir mostra como rolar o item de macaco Proboscis no grupo Monkeys para a exibição:

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> O [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento é acionado quando o [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) método é invocado.

## <a name="disable-scroll-animation"></a>Desabilitar animação de rolagem

Uma animação de rolagem é exibida ao rolar um item para a exibição. No entanto, essa animação pode ser desabilitada definindo o `animate` argumento do `ScrollTo` método como `false` :

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posição de rolagem de controle

Ao rolar um item para a exibição, a posição exata do item após a rolagem concluída pode ser especificada com o `position` argumento dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. Esse argumento aceita um [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro de enumeração.

### <a name="makevisible"></a>MakeVisible

O [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até que fique visível na exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de exemplo resulta na rolagem mínima necessária para rolar o item para a exibição:

[![Captura de tela de uma lista vertical CollectionView com um item rolado na exibição, no iOS e no Android](scrolling-images/scrolltoposition-makevisible.png "Lista vertical CollectionView com item rolado")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Lista vertical CollectionView com item rolado")

> [!NOTE]
> O [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro será usado por padrão, se o `position` argumento não for especificado ao chamar o `ScrollTo` método.

### <a name="start"></a>Iniciar

O [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até o início da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de exemplo resulta no item que está sendo rolado para o início da exibição:

[![Captura de tela de uma lista vertical CollectionView com um item rolado na exibição, no iOS e no Android](scrolling-images/scrolltoposition-start.png "Lista vertical CollectionView com item rolado")](scrolling-images/scrolltoposition-start-large.png#lightbox "Lista vertical CollectionView com item rolado")

### <a name="center"></a>Centro

O [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o centro da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de exemplo resulta no item que está sendo rolado para o centro da exibição:

[![Captura de tela de uma lista vertical CollectionView com um item rolado na exibição, no iOS e no Android](scrolling-images/scrolltoposition-center.png "Lista vertical CollectionView com item rolado")](scrolling-images/scrolltoposition-center-large.png#lightbox "Lista vertical CollectionView com item rolado")

### <a name="end"></a>End

O [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o final da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de exemplo resulta no item que está sendo rolado para o final da exibição:

[![Captura de tela de uma lista vertical CollectionView com um item rolado na exibição, no iOS e no Android](scrolling-images/scrolltoposition-end.png "Lista vertical CollectionView com item rolado")](scrolling-images/scrolltoposition-end-large.png#lightbox "Lista vertical CollectionView com item rolado")

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar a posição de rolagem quando novos itens forem adicionados

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define uma `ItemsUpdatingScrollMode` propriedade, que é apoiada por uma propriedade vinculável. Essa propriedade Obtém ou define um `ItemsUpdatingScrollMode` valor de enumeração que representa o comportamento de rolagem do `CollectionView` quando novos itens são adicionados a ele. A enumeração `ItemsUpdatingScrollMode` define os seguintes membros:

- `KeepItemsInView`ajusta o deslocamento de rolagem para manter o primeiro item visível exibido quando novos itens são adicionados.
- `KeepScrollOffset`mantém o deslocamento de rolagem relativo ao início da lista quando novos itens são adicionados.
- `KeepLastItemInView`ajusta o deslocamento de rolagem para manter o último item visível quando novos itens são adicionados.

O valor padrão da `ItemsUpdatingScrollMode` propriedade é `KeepItemsInView` . Portanto, quando novos itens forem adicionados a um [`CollectionView`](xref:Xamarin.Forms.CollectionView) primeiro item visível na lista, permanecerão exibidos. Para garantir que itens recém-adicionados sempre fiquem visíveis na parte inferior da lista, a `ItemsUpdatingScrollMode` propriedade deve ser definida como `KeepLastItemInView` :

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilidade da barra de rolagem

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility` Propriedades, que são apoiadas por propriedades vinculáveis. Essas propriedades obtêm ou definem um [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor de enumeração que representa quando a barra de rolagem horizontal ou vertical é visível. A enumeração `ScrollBarVisibility` define os seguintes membros:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indica o comportamento da barra de rolagem padrão para a plataforma e é o valor padrão para as `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` Propriedades e.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indica que as barras de rolagem estarão visíveis, mesmo quando o conteúdo couber na exibição.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indica que as barras de rolagem não estarão visíveis, mesmo se o conteúdo não couber na exibição.

## <a name="snap-points"></a>Pontos de ajuste

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como encaixe, pois os itens se ajustam à posição quando a rolagem é interrompida e é controlado pelas seguintes propriedades da [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , especifica o comportamento dos pontos de ajuste ao rolar.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , especifica como os pontos de ajuste são alinhados com os itens.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> Quando o ajuste ocorre, ele ocorrerá na direção que produz a menor quantidade de movimentos.

### <a name="snap-points-type"></a>Tipo de pontos de ajuste

A [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumeração define os seguintes membros:

- `None`indica que a rolagem não se ajusta aos itens.
- `Mandatory`indica que o conteúdo sempre se ajusta ao ponto de ajuste mais próximo para onde a rolagem seria interrompida naturalmente, ao longo da direção de inércia.
- `MandatorySingle`indica o mesmo comportamento que `Mandatory` , mas apenas rola um item de cada vez.

Por padrão, a [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriedade é definida como `SnapPointsType.None` , o que garante que a rolagem não enencaixe itens, conforme mostrado nas capturas de tela a seguir:

[![Captura de tela de uma lista vertical CollectionView sem pontos de ajuste, no iOS e no Android](scrolling-images/snappoints-none.png "Lista vertical CollectionView sem pontos de ajuste")](scrolling-images/snappoints-none-large.png#lightbox "Lista vertical CollectionView sem pontos de ajuste")

### <a name="snap-points-alignment"></a>Alinhamento dos pontos de ajuste

A [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumeração define `Start` `Center` `End` os membros, e.

> [!IMPORTANT]
> O valor da [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriedade é respeitado somente quando a [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriedade é definida como `Mandatory` , ou `MandatorySingle` .

#### <a name="start"></a>Iniciar

O `SnapPointsAlignment.Start` membro indica que os pontos de ajuste estão alinhados com a borda à esquerda dos itens.

Por padrão, a [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriedade é definida como `SnapPointsAlignment.Start` . No entanto, para fins de integridade, o exemplo XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem, o item superior será alinhado com a parte superior da exibição:

[![Captura de tela de uma lista vertical de CollectionView com pontos de partida de início, no iOS e no Android](scrolling-images/snappoints-start.png "Lista vertical CollectionView com pontos de ajuste de início")](scrolling-images/snappoints-start-large.png#lightbox "Lista vertical CollectionView com pontos de ajuste de início")

#### <a name="center"></a>Centro

O `SnapPointsAlignment.Center` membro indica que os pontos de ajuste estão alinhados ao centro dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem, o item superior será alinhado ao centro na parte superior da exibição:

[![Captura de tela de uma lista vertical CollectionView com pontos de alinhamento do centro, no iOS e no Android](scrolling-images/snappoints-center.png "Lista vertical CollectionView com pontos de alinhamento do centro")](scrolling-images/snappoints-center-large.png#lightbox "Lista vertical CollectionView com pontos de alinhamento do centro")

#### <a name="end"></a>End

O `SnapPointsAlignment.End` membro indica que os pontos de ajuste estão alinhados com a borda à direita dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem, o item inferior será alinhado com a parte inferior da exibição:

[![Captura de tela de uma lista vertical CollectionView com pontos de ajuste finais, no iOS e no Android](scrolling-images/snappoints-end.png "Lista vertical CollectionView com pontos de ajuste finais")](scrolling-images/snappoints-end-large.png#lightbox "Lista vertical CollectionView com pontos de ajuste finais")

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
