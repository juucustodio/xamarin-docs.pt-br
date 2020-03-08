---
title: CarouselView de Xamarin. Forms
description: Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Além disso, o CarouselView define dois métodos roleto, que rolam programaticamente os itens para a exibição.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 735a572f4aadfc224e545e371525b96f29c9552e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913116"
---
# <a name="xamarinforms-carouselview-scrolling"></a>CarouselView de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as seguintes propriedades relacionadas à rolagem:

- `HorizontalScrollBarVisibility`, do tipo `ScrollBarVisibility`, que especifica quando a barra de rolagem horizontal está visível.
- `IsDragging`, do tipo `bool`, que indica se o `CarouselView` está rolando. Esta é uma propriedade somente leitura, cujo valor padrão é `false`.
- `IsScrollAnimated`, do tipo `bool`, que especifica se uma animação ocorrerá ao rolar a `CarouselView`. O valor padrão é `true`.
- `ItemsUpdatingScrollMode`, do tipo `ItemsUpdatingScrollMode`, que representa o comportamento de rolagem do `CarouselView` quando novos itens são adicionados a ele.
- `VerticalScrollBarVisibility`, do tipo `ScrollBarVisibility`, que especifica quando a barra de rolagem vertical está visível.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser destinos de associações de dados.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) também define dois métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que rolam itens para o modo de exibição. Uma das sobrecargas rola o item no índice especificado para a exibição, enquanto o outro rola o item especificado para a exibição. Ambas as sobrecargas têm argumentos adicionais que podem ser especificados para indicar a posição exata do item após a conclusão da rolagem e se a rolagem deve ser animada.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define um evento de [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) que é acionado quando um dos métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) é invocado. O objeto [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) que acompanha o evento `ScrollToRequested` tem muitas propriedades, incluindo `IsAnimated`, `Index`, `Item`e `ScrollToPosition`. Essas propriedades são definidas a partir dos argumentos especificados nas chamadas de método `ScrollTo`.

Além disso, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define um evento de `Scrolled` que é acionado para indicar que a rolagem ocorreu. O objeto `ItemsViewScrolledEventArgs` que acompanha o evento `Scrolled` tem muitas propriedades. Para obter mais informações, consulte [detectar rolagem](#detect-scrolling).

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como encaixe, pois os itens se ajustam à posição quando a rolagem é interrompida. Para obter mais informações, consulte [snap Points](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) também pode carregar dados de forma incremental à medida que o usuário rola. Para obter mais informações, consulte [carregar dados incrementalmente](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar rolagem

A propriedade `IsDragging` pode ser examinada para determinar se o [`CarouselView`](xref:Xamarin.Forms.CarouselView) está rolando os itens no momento.

Além disso, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define um evento de `Scrolled` que é acionado para indicar que a rolagem ocorreu. Esse evento deve ser consumido quando os dados sobre a rolagem são necessários.

O exemplo de XAML a seguir mostra um `CarouselView` que define um manipulador de eventos para o evento `Scrolled`:

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

Neste exemplo de código, o manipulador de eventos `OnCarouselViewScrolled` é executado quando o evento `Scrolled` é acionado:

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
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

Neste exemplo, o manipulador de eventos `OnCarouselViewScrolled` gera os valores do objeto `ItemsViewScrolledEventArgs` que acompanha o evento.

> [!IMPORTANT]
> O evento `Scrolled` é acionado para rolagens iniciadas pelo usuário e para rolagens programáticas.

## <a name="scroll-an-item-at-an-index-into-view"></a>Rolar um item em um índice para a exibição

A primeira sobrecarga do método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) rola o item no índice especificado para a exibição. Dado um objeto de [`CarouselView`](xref:Xamarin.Forms.CarouselView) chamado `carouselView`, o exemplo a seguir mostra como rolar o item no índice 6 para a exibição:

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> O evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) é acionado quando o método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) é invocado.

## <a name="scroll-an-item-into-view"></a>Rolar um item para a exibição

A segunda [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga do método rola o item especificado para a exibição. Dado um objeto de [`CarouselView`](xref:Xamarin.Forms.CarouselView) chamado `carouselView`, o exemplo a seguir mostra como rolar o item de macaco Proboscis para a exibição:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> O evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) é acionado quando o método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) é invocado.

## <a name="disable-scroll-animation"></a>Desabilitar animação de rolagem

Uma animação de rolagem é exibida ao se mover entre itens em um [`CarouselView`](xref:Xamarin.Forms.CarouselView). Essa animação ocorre tanto para rolagens iniciadas pelo usuário quanto para rolagens programáticas. Definir a propriedade `IsScrollAnimated` como `false` desabilitará a animação para ambas as categorias de rolagem.

Como alternativa, o argumento `animate` do método `ScrollTo` pode ser definido como `false` para desabilitar a animação de rolagem em rolagens programáticas:

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posição de rolagem de controle

Ao rolar um item para a exibição, a posição exata do item após a rolagem concluída pode ser especificada com o argumento `position` dos métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Esse argumento aceita um membro de enumeração [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) .

### <a name="makevisible"></a>MakeVisible

O membro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) indica que o item deve ser rolado até que fique visível na exibição:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de exemplo resulta na rolagem mínima necessária para rolar o item para a exibição.

> [!NOTE]
> O membro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) é usado por padrão, se o argumento `position` não for especificado ao chamar o método `ScrollTo`.

### <a name="start"></a>Iniciar

O membro [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) indica que o item deve ser rolado até o início da exibição:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de exemplo resulta no item que está sendo rolado para o início da exibição.

### <a name="center"></a>Center

O membro [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) indica que o item deve ser rolado para o centro da exibição:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de exemplo resulta no item que está sendo rolado para o centro da exibição.

### <a name="end"></a>Encerramento

O membro [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) indica que o item deve ser rolado para o final da exibição:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de exemplo resulta no item que está sendo rolado para o final da exibição.

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar a posição de rolagem quando novos itens forem adicionados

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define uma propriedade `ItemsUpdatingScrollMode`, que é apoiada por uma propriedade vinculável. Essa propriedade Obtém ou define um valor de enumeração de `ItemsUpdatingScrollMode` que representa o comportamento de rolagem do `CarouselView` quando novos itens são adicionados a ele. A enumeração `ItemsUpdatingScrollMode` define os seguintes membros:

- `KeepItemsInView` ajusta o deslocamento de rolagem para manter o primeiro item visível exibido quando novos itens são adicionados.
- `KeepScrollOffset` mantém o deslocamento de rolagem em relação ao início da lista quando novos itens são adicionados.
- `KeepLastItemInView` ajusta o deslocamento de rolagem para manter o último item visível quando novos itens são adicionados.

O valor padrão da propriedade `ItemsUpdatingScrollMode` é `KeepItemsInView`. Portanto, quando novos itens são adicionados a um [`CarouselView`](xref:Xamarin.Forms.CarouselView) o primeiro item visível na lista permanecerá exibido. Para garantir que os itens recém-adicionados sempre fiquem visíveis na parte inferior da lista, a propriedade `ItemsUpdatingScrollMode` deve ser definida como `KeepLastItemInView`:

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilidade da barra de rolagem

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as propriedades `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility`, que são apoiadas pelas propriedades vinculáveis. Essas propriedades obtêm ou definem um [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor de enumeração que representa quando a barra de rolagem horizontal ou vertical é visível. A enumeração `ScrollBarVisibility` define os seguintes membros:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica o comportamento padrão da barra de rolagem para a plataforma e é o valor padrão para as propriedades `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica que as barras de rolagem estarão visíveis, mesmo quando o conteúdo couber na exibição.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica que as barras de rolagem não estarão visíveis, mesmo que o conteúdo não caiba na exibição.

## <a name="snap-points"></a>Pontos de ajuste

Quando um usuário passa o dedo para iniciar uma rolagem, a posição final da rolagem pode ser controlada para que os itens sejam totalmente exibidos. Esse recurso é conhecido como ajuste, porque os itens se ajustam à posição quando a rolagem é interrompida e é controlado pelas seguintes propriedades da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) :

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento dos pontos de ajuste ao rolar.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de ajuste são alinhados com os itens.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> Quando o ajuste ocorre, ele ocorrerá na direção que produz a menor quantidade de movimentos.

### <a name="snap-points-type"></a>Tipo de pontos de ajuste

A enumeração [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) define os seguintes membros:

- `None` indica que a rolagem não se ajusta aos itens.
- `Mandatory` indica que o conteúdo sempre se ajusta ao ponto de ajuste mais próximo para onde a rolagem seria interrompida naturalmente, ao longo da direção de inércia.
- `MandatorySingle` indica o mesmo comportamento que `Mandatory`, mas apenas rola um item de cada vez.

Por padrão, em um [`CarouselView`](xref:Xamarin.Forms.CarouselView), a propriedade [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) é definida como `SnapPointsType.MandatorySingle`, o que garante que a rolagem role apenas um item de cada vez.

As capturas de tela a seguir mostram uma [`CarouselView`](xref:Xamarin.Forms.CarouselView) com ajuste desativado:

[![Captura de tela de um CarouselView sem pontos de encaixe, no iOS e no Android](scrolling-images/snappoints-none.png "CarouselView sem pontos de encaixe")](scrolling-images/snappoints-none-large.png#lightbox "CarouselView sem pontos de encaixe")

### <a name="snap-points-alignment"></a>Alinhamento dos pontos de ajuste

A enumeração de [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) define os membros `Start`, `Center`e `End`.

> [!IMPORTANT]
> O valor da propriedade [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) só é respeitado quando a propriedade [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) é definida como `Mandatory`ou `MandatorySingle`.

#### <a name="start"></a>Iniciar

O membro `SnapPointsAlignment.Start` indica que os pontos de ajuste estão alinhados com a borda à esquerda dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem em uma [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalmente, o item à esquerda será alinhado à esquerda da exibição:

[![Captura de tela de um CarouselView com pontos de ajuste iniciais, no iOS e no Android](scrolling-images/snappoints-start.png "CarouselView com pontos de ajuste de início")](scrolling-images/snappoints-start-large.png#lightbox "CarouselView com pontos de ajuste de início")

#### <a name="center"></a>Center

O membro `SnapPointsAlignment.Center` indica que os pontos de ajuste estão alinhados ao centro dos itens.

Por padrão, em um [`CarouselView`](xref:Xamarin.Forms.CarouselView), a propriedade [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) é definida como `Center`. No entanto, para fins de integridade, o exemplo XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem em uma [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalmente, o item central será alinhado ao centro da exibição:

[![Captura de tela de um CarouselView com pontos de alinhamento centralizados, no iOS e no Android](scrolling-images/snappoints-center.png "CarouselView com pontos de alinhamento do centro")](scrolling-images/snappoints-center-large.png#lightbox "CarouselView com pontos de alinhamento do centro")

#### <a name="end"></a>Encerramento

O membro `SnapPointsAlignment.End` indica que os pontos de ajuste estão alinhados com a borda à direita dos itens. O exemplo de XAML a seguir mostra como definir esse membro de enumeração:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Quando um usuário passa o dedo para iniciar uma rolagem em uma [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalmente, o item à direita será alinhado à direita da exibição.

[![Captura de tela de um CarouselView com pontos de ajuste finais, no iOS e no Android](scrolling-images/snappoints-end.png "CarouselView com pontos de ajuste finais")](scrolling-images/snappoints-end-large.png#lightbox "CarouselView com pontos de ajuste finais")

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
