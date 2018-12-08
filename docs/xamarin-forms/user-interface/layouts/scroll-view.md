---
title: Xamarin. Forms ScrollView
description: Este artigo explica como usar a classe do xamarin. Forms ScrollView para apresentar os layouts que não cabem em apenas uma única tela, e que têm conteúdo liberar espaço para o teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 34339b9ca3a15c7f7f24edee5401c542fd09ba74
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53048981"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contém layouts e permite que eles para fora da tela de rolagem. `ScrollView` também é usado para permitir que os modos de exibição mover automaticamente para a parte visível da tela quando o teclado está mostrando.

[![](scroll-view-images/layouts-sml.png "Xamarin. Forms Layouts")](scroll-view-images/layouts.png#lightbox "Layouts do xamarin. Forms")

Este artigo aborda:

- **[Finalidade](#purpose)**  &ndash; a finalidade de `ScrollView` e quando ele é usado.
- **[Uso](#usage)**  &ndash; como usar `ScrollView` na prática.
- **[As propriedades](#properties)**  &ndash; propriedades públicas que podem ser lidos e modificadas.
- **[Métodos](#methods)**  &ndash; métodos públicos que podem ser chamados para rolar a exibição.
- **[Eventos](#events)**  &ndash; eventos que podem ser usados para ouvir as alterações nos Estados da exibição.

## <a name="purpose"></a>Finalidade

`ScrollView` pode ser usado para garantir que os modos de exibição maiores exibem bem em telefones menores. Por exemplo, um layout que funciona em um iPhone 6s poderão ser cortado em um iPhone 4s. Usando um `ScrollView` permitiria que as partes recortadas do layout a ser exibido na tela menor.

## <a name="usage"></a>Uso

> [!NOTE]
> `ScrollView`s não devem ser aninhados. Além disso, `ScrollView`s não devem ser aninhados com outros controles que fornecem a rolagem, como `ListView` e `WebView`.

`ScrollView` expõe um `Content` propriedade que pode ser definida como um único modo de exibição ou layout. Considere este exemplo de um layout com um boxView muito grande, seguido por um `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

No C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Antes do usuário rola para baixo, apenas o `BoxView` está visível:

![](scroll-view-images/scroll-start.png "BoxView em ScrollView")

Observe que, quando o usuário começa a inserir texto no `Entry`, o modo de exibição rola para mantê-lo visível na tela:

![](scroll-view-images/scroll-end.png "Entrada em ScrollView")

## <a name="properties"></a>Propriedades

`ScrollView` Define as propriedades a seguir:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtém uma [ `Size` ](xref:Xamarin.Forms.Size) valor que representa o tamanho do conteúdo.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtém ou define um [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valor de enumeração que representa a direção de rolagem do `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Obtém um `double` que representa a atual posição de rolagem X.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Obtém um `double` que representa a atual posição de rolagem de Y.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtém ou define um [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem horizontal está visível.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtém ou define um [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem vertical está visível.

## <a name="methods"></a>Métodos

`ScrollView` Fornece um `ScrollToAsync` método, que pode ser usado para rolar a exibição usando coordenadas ou ao especificar um modo de exibição específico que deve ficar visível.

Ao usar coordenadas, especifique o `x` e `y` coordenadas, juntamente com um valor booliano que indica se a rolagem deve ser animada:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Durante a rolagem para um elemento específico, o `ScrollToPosition` Especifica enumeração onde no modo de exibição do elemento será exibido:

- **Centro** &ndash; rola o elemento para o centro da parte visível da exibição.
- **Término** &ndash; rola o elemento ao final da parte visível da exibição.
- **MakeVisible** &ndash; rola o elemento para que ele fique visível dentro da exibição.
- **Inicie** &ndash; rola o elemento ao início da parte visível da exibição.

O `IsAnimated` propriedade especifica como o modo de exibição será rolado. Quando definido como true, uma animação suave será usado, em vez de instantaneamente movendo o conteúdo no modo de exibição.

## <a name="events"></a>Eventos

`ScrollView` define apenas um evento, `Scrolled`. `Scrolled` é gerado quando o modo de exibição terminou de rolagem. Manipulador de eventos do `Scrolled` leva `ScrolledEventArgs`, que tem o `ScrollX` e `ScrollY` propriedades. A seguir demonstra como atualizar um rótulo com a atual posição de rolagem de um `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Observe que as posições de rolagem podem ser negativas, devido ao efeito de devolução durante a rolagem no final de uma lista.


## <a name="related-links"></a>Links relacionados

- [Layout (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
