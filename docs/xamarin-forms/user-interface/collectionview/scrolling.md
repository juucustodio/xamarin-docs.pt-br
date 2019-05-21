---
title: Rolagem de CollectionView de xamarin. Forms
description: Quando um dedo do usuário para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens são totalmente exibidas. Além disso, o CollectionView define dois métodos de ScrollTo, que rolagem programaticamente os itens na exibição.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: b2f32f6695fffa27068fce9d8c12f4ecd9157bc2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970534"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Rolagem de CollectionView de xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define dois [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolagem os itens na exibição. Uma das sobrecargas rola o item no índice especificado na exibição, enquanto o outro rola o item especificado na exibição. Ambas as sobrecargas têm argumentos adicionais que podem ser especificados para indicar a posição exata do item depois que a rolagem for concluída e se deseja animar a rolagem.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define uma [ `ScrollToRequested` ](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que é disparado quando um dos [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos é invocado. O [ `ScrollToRequestedEventArgs` ](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompanha o `ScrollToRequested` evento tem muitas propriedades, incluindo `IsAnimated`, `Index`, `Item`, e `ScrollToPosition`. Essas propriedades são definidas a partir dos argumentos especificados no `ScrollTo` chamadas de método.

Quando um dedo do usuário para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens são totalmente exibidas. Esse recurso é conhecido como encaixe, como itens de ajuste para posicionar durante a rolagem é interrompido. Para obter mais informações, consulte [ajustar pontos](#snap-points).

## <a name="scroll-an-item-at-an-index-into-view"></a>Rolar um item em um índice na exibição

A primeira [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga do método rola o item no índice especificado na exibição. Considerando um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView`, o exemplo a seguir mostra como rolar o item no índice 12 no modo de exibição:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>Rolar um item na exibição

A segunda [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga do método rola o item especificado na exibição. Considerando um [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView`, o exemplo a seguir mostra como rolar o item especificado no modo de exibição:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>Posição de rolagem de controle

Ao rolar um item no modo de exibição, a posição exata do item após a rolagem pode ser especificada com o `position` argumento do [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. Esse argumento aceita uma [ `ScrollToPosition` ](xref:Xamarin.Forms.ScrollToPosition) membro de enumeração.

### <a name="makevisible"></a>MakeVisible

O [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até que ele esteja visível no modo de exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Esse código de exemplo resulta na rolagem mínima necessária para rolar o item na exibição:

[![Captura de tela de uma lista vertical de CollectionView com um item é colocado na exibição, no iOS e Android](scrolling-images/scrolltoposition-makevisible.png "CollectionView de lista vertical com item rolado")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView de lista vertical com rolado item")

> [!NOTE]
> O [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) membro é usado por padrão, se o `position` argumento não for especificado ao chamar o `ScrollTo` método.

### <a name="start"></a>Início

O [ `ScrollToPosition.Start` ](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o início do modo de exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Esse código de exemplo resulta no item que está sendo rolado para o início da exibição:

[![Captura de tela de uma lista vertical de CollectionView com um item é colocado na exibição, no iOS e Android](scrolling-images/scrolltoposition-start.png "CollectionView de lista vertical com item rolado")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView de lista vertical com rolado item")

### <a name="center"></a>Centralizado 

O [ `ScrollToPosition.Center` ](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o centro do modo de exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Esse código de exemplo resulta no item que está sendo rolado para o centro da exibição:

[![Captura de tela de uma lista vertical de CollectionView com um item é colocado na exibição, no iOS e Android](scrolling-images/scrolltoposition-center.png "CollectionView de lista vertical com item rolado")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView de lista vertical com rolado item")

### <a name="end"></a>End

O [ `ScrollToPosition.End` ](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até o final do modo de exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Esse código de exemplo resulta no item que está sendo rolado até o final do modo de exibição:

[![Captura de tela de uma lista vertical de CollectionView com um item é colocado na exibição, no iOS e Android](scrolling-images/scrolltoposition-end.png "CollectionView de lista vertical com item rolado")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView de lista vertical com rolado item")

## <a name="disable-scroll-animation"></a>Desativar a animação de rolagem

Uma animação de rolagem é exibida quando um item de rolagem no modo de exibição. No entanto, essa animação pode ser desabilitada definindo a `animate` argumento do `ScrollTo` método `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>Pontas de encaixe

Quando um dedo do usuário para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens são totalmente exibidas. Esse recurso é conhecido como encaixe, como itens de ajustar-se à posição quando rolagem for interrompido e é controlada pelas seguintes propriedades do [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento de pontos de alinhamento durante a rolagem.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de alinhamento são alinhados com os itens.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

> [!NOTE]
> Quando o ajuste ocorre, ele ocorrerá na direção que produz a menor quantidade de movimento.

### <a name="snap-points-type"></a>Tipo de pontas de encaixe

O [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType) enumeração define os seguintes membros:

- `None` indica que rolagem não se ajusta aos itens.
- `Mandatory` indica que o conteúdo sempre se ajusta para o mais próximo snap aponta para onde rolagem naturalmente pararia, ao longo da direção da inércia.
- `MandatorySingle` indica o mesmo comportamento que `Mandatory`, mas rola apenas um item por vez.

Por padrão, o [ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) estiver definida como `SnapPointsType.None`, que garante que a rolagem não se ajusta itens, conforme mostrado nas capturas de tela seguir:

[![Captura de tela de uma lista vertical de CollectionView sem pontos de alinhamento, no iOS e Android](scrolling-images/snappoints-none.png "lista vertical de CollectionView sem pontos de alinhamento")](scrolling-images/snappoints-none-large.png#lightbox "lista vertical de CollectionView sem snap pontos")

### <a name="snap-points-alignment"></a>Ajustar-se pontos de alinhamento

O [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment) enumeração define `Start`, `Center`, e `End` membros.

> [!IMPORTANT]
> O valor da [ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriedade só é respeitada quando o [ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriedade é definida como `Mandatory`, ou `MandatorySingle`.

#### <a name="start"></a>Início

O `SnapPointsAlignment.Start` membro indica que pontos de alinhamento são alinhados com a borda à esquerda de itens.

Por padrão, o [ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) estiver definida como `SnapPointsAlignment.Start`. No entanto, para fins de integridade, o exemplo XAML a seguir mostra como definir este membro de enumeração:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando um dedo do usuário para iniciar uma rolagem, o item superior será alinhado à parte superior do modo de exibição:

[![Captura de tela de uma lista vertical de CollectionView com pontos de alinhamento de início, no iOS e Android](scrolling-images/snappoints-start.png "CollectionView de lista vertical com pontos de alinhamento de início")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView de lista vertical com início pontos de ajuste")

#### <a name="center"></a>Centralizado 

O `SnapPointsAlignment.Center` membro indica que pontos de alinhamento são alinhados com a Central de itens. O exemplo XAML a seguir mostra como definir este membro de enumeração:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando um dedo do usuário para iniciar uma rolagem, o item superior será centralizado na parte superior do modo de exibição:

[![Captura de tela de uma lista vertical de CollectionView com pontos de alinhamento central, no iOS e Android](scrolling-images/snappoints-center.png "CollectionView de lista vertical com pontos de alinhamento central")](scrolling-images/snappoints-center-large.png#lightbox "CollectionView de lista vertical com pontos de alinhamento central")

#### <a name="end"></a>End

O `SnapPointsAlignment.End` membro indica que os pontos de alinhamento são alinhados com a borda direita de itens. O exemplo XAML a seguir mostra como definir este membro de enumeração:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando um dedo do usuário para iniciar uma rolagem, o item na parte inferior será alinhado à parte inferior do modo de exibição:

[![Captura de tela de uma lista vertical de CollectionView com pontos de alinhamento final, no iOS e Android](scrolling-images/snappoints-end.png "CollectionView de lista vertical com pontos de extremidade de snap")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView de lista vertical com snap final pontos")

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
