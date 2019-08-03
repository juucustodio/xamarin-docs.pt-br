---
title: Rolagem de CollectionView do Xamarin. Forms
description: Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, CollectionView define dois métodos roleto, que rolam programaticamente os itens para a exibição.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 89bbe402f056b875a7dadd96527364847ad470e8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738935"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Rolagem de CollectionView do Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define dois [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que rolam itens para o modo de exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição. Ambas as sobrecargas têm argumentos adicionais que podem ser especificados para indicar a posição exata do item após a conclusão da rolagem e se a rolagem deve ser animada.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define um [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que é acionado quando um [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dos métodos é invocado. O [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompanha o `ScrollToRequested` evento tem muitas `ScrollToPosition` `IsAnimated` `Index`Propriedades, incluindo, ,e.`Item` Essas propriedades são definidas a partir dos argumentos especificados nas `ScrollTo` chamadas de método.

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como encaixe, pois os itens se ajustam à posição quando a rolagem é interrompida. Para obter mais informações, consulte [snap Points](#snap-points).

## <a name="scroll-an-item-at-an-index-into-view"></a>Rolar um item em um índice para a exibição

A primeira [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga do método rola o item no índice especificado para a exibição. Dado um [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView`, o exemplo a seguir mostra como rolar o item no índice 12 para a exibição:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>Rolar um item para a exibição

A sobrecarga [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) do segundo método rola o item especificado para a exibição. Dado um [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto chamado `collectionView`, o exemplo a seguir mostra como rolar o item especificado para a exibição:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>Posição de rolagem de controle

Ao rolar um item para a exibição, a posição exata do item após a rolagem concluída pode ser especificada com o `position` argumento [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dos métodos. Esse argumento aceita um [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro de enumeração.

### <a name="makevisible"></a>MakeVisible

O [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até que fique visível na exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de exemplo resulta na rolagem mínima necessária para rolar o item para a exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com um item rolado na exibição, em lista vertical de CollectionView do Android e Ios](scrolling-images/scrolltoposition-makevisible.png "com item rolado") ] (scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Lista vertical CollectionView com item rolado")

> [!NOTE]
> O [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro será usado por padrão, se o `position` argumento não for especificado ao chamar o `ScrollTo` método.

### <a name="start"></a>Início

O [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado até o início da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de exemplo resulta no item que está sendo rolado para o início da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com um item rolado na exibição, em lista vertical de CollectionView do Android e Ios](scrolling-images/scrolltoposition-start.png "com item rolado") ] (scrolling-images/scrolltoposition-start-large.png#lightbox "Lista vertical CollectionView com item rolado")

### <a name="center"></a>Centralizado

O [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o centro da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de exemplo resulta no item que está sendo rolado para o centro da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com um item rolado na exibição, em lista vertical de CollectionView do Android e Ios](scrolling-images/scrolltoposition-center.png "com item rolado") ] (scrolling-images/scrolltoposition-center-large.png#lightbox "Lista vertical CollectionView com item rolado")

### <a name="end"></a>End

O [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membro indica que o item deve ser rolado para o final da exibição:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de exemplo resulta no item que está sendo rolado para o final da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com um item rolado na exibição, em lista vertical de CollectionView do Android e Ios](scrolling-images/scrolltoposition-end.png "com item rolado") ] (scrolling-images/scrolltoposition-end-large.png#lightbox "Lista vertical CollectionView com item rolado")

## <a name="disable-scroll-animation"></a>Desabilitar animação de rolagem

Uma animação de rolagem é exibida ao rolar um item para a exibição. No entanto, essa animação pode ser desabilitada `animate` definindo o argumento `ScrollTo` do método `false`como:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>Pontos de ajuste

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como encaixe, pois os itens se ajustam à posição quando a rolagem é interrompida e é controlado pelas seguintes [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) Propriedades da classe:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento dos pontos de ajuste ao rolar.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de ajuste são alinhados com os itens.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> Quando o ajuste ocorre, ele ocorrerá na direção que produz a menor quantidade de movimentos.

### <a name="snap-points-type"></a>Tipo de pontos de ajuste

A [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumeração define os seguintes membros:

- `None`indica que a rolagem não se ajusta aos itens.
- `Mandatory`indica que o conteúdo sempre se ajusta ao ponto de ajuste mais próximo para onde a rolagem seria interrompida naturalmente, ao longo da direção de inércia.
- `MandatorySingle`indica o mesmo comportamento que `Mandatory`, mas apenas rola um item de cada vez.

Por padrão, a [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriedade é definida como `SnapPointsType.None`, o que garante que a rolagem não enencaixe itens, conforme mostrado nas capturas de tela a seguir:

[ ![Captura de tela de uma lista vertical de CollectionView sem pontos de encaixe, na lista vertical de CollectionView do Android e Ios](scrolling-images/snappoints-none.png "sem os pontos de ajuste") ] (scrolling-images/snappoints-none-large.png#lightbox "Lista vertical CollectionView sem pontos de ajuste")

### <a name="snap-points-alignment"></a>Alinhamento dos pontos de ajuste

A [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumeração define `Start`os `Center`Membros, `End` e.

> [!IMPORTANT]
> O valor da [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriedade é respeitado somente quando a [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriedade é definida como `Mandatory`, ou `MandatorySingle`.

#### <a name="start"></a>Início

O `SnapPointsAlignment.Start` membro indica que os pontos de ajuste estão alinhados com a borda à esquerda dos itens.

Por padrão, a [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriedade é definida como `SnapPointsAlignment.Start`. No entanto, para fins de integridade, o exemplo XAML a seguir mostra como definir esse membro de enumeração:

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

Quando um usuário passa o dedo para iniciar uma rolagem, o item superior será alinhado com a parte superior da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com pontos de partida de início, na lista vertical de CollectionView do Android e Ios](scrolling-images/snappoints-start.png "com pontos de ajuste de início") ] (scrolling-images/snappoints-start-large.png#lightbox "Lista vertical CollectionView com pontos de ajuste de início")

#### <a name="center"></a>Centralizado

O `SnapPointsAlignment.Center` membro indica que os pontos de ajuste estão alinhados ao centro dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

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

Quando um usuário passa o dedo para iniciar uma rolagem, o item superior será alinhado ao centro na parte superior da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com pontos de alinhamento do centro, na lista vertical de CollectionView do Android e Ios](scrolling-images/snappoints-center.png "com pontos de alinhamento do centro") ] (scrolling-images/snappoints-center-large.png#lightbox "Lista vertical CollectionView com pontos de alinhamento do centro")

#### <a name="end"></a>End

O `SnapPointsAlignment.End` membro indica que os pontos de ajuste estão alinhados com a borda à direita dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

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

Quando um usuário passa o dedo para iniciar uma rolagem, o item inferior será alinhado com a parte inferior da exibição:

[ ![Captura de tela de uma lista vertical de CollectionView com pontos de ajuste finais, na lista vertical de CollectionView do Android e Ios](scrolling-images/snappoints-end.png "com pontos de ajuste finais") ] (scrolling-images/snappoints-end-large.png#lightbox "Lista vertical CollectionView com pontos de ajuste finais")

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
