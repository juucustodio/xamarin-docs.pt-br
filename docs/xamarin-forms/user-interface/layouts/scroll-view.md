---
title: ScrollView Xamarin. Forms
description: Este artigo explica como usar a classe ScrollView do Xamarin. Forms para apresentar layouts que não cabem em apenas uma tela e que têm espaço para o teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8d523c6da6ca7feaf6894123822f789f37455865
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696860"
---
# <a name="xamarinforms-scrollview"></a>ScrollView Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contém layouts e permite que eles rolem fora da tela. `ScrollView` também é usado para permitir que as exibições se movam automaticamente para a parte visível da tela quando o teclado estiver sendo exibido.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidade

[`ScrollView`](xref:Xamarin.Forms.ScrollView) pode ser usado para garantir que exibições maiores sejam bem exibidas em telefones menores. Por exemplo, um layout que funciona em um iPhone 6s pode ser recortado em um 4s de iPhone. O uso de um `ScrollView` permitiria que as partes recortadas do layout fossem exibidas na tela menor.

## <a name="usage"></a>Uso

> [!NOTE]
> os objetos de [`ScrollView`](xref:Xamarin.Forms.ScrollView) não devem ser aninhados. Além disso, `ScrollView`s não deve ser aninhada com outros controles que fornecem rolagem, como `ListView` e `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) expõe uma propriedade `Content`, que pode ser definida como um único modo de exibição ou layout. Considere este exemplo de um layout com um boxView muito grande, seguido por um `Entry`:

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

Antes que o usuário Role para baixo, somente o `BoxView` é visível:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Observe que, quando o usuário começa a inserir texto na `Entry`, a exibição rola para mantê-lo visível na tela:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propriedades

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define as propriedades a seguir:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtém um valor de [`Size`](xref:Xamarin.Forms.Size) que representa o tamanho do conteúdo.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtém ou define um valor de enumeração de [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) que representa a direção da rolagem do `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) obtém um `double` que representa a posição de rolagem X atual.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) obtém um `double` que representa a posição de rolagem Y atual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtém ou define um valor de [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa quando a barra de rolagem horizontal é visível.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtém ou define um valor de [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa quando a barra de rolagem vertical é visível.

> [!NOTE]
> A rolagem pode ser desabilitada definindo a propriedade [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) como `Neither`.

## <a name="methods"></a>Métodos

[`ScrollView`](xref:Xamarin.Forms.ScrollView) fornece um método `ScrollToAsync`, que pode ser usado para rolar a exibição usando coordenadas ou especificando uma determinada exibição que deve se tornar visível.

Ao usar coordenadas, especifique as coordenadas de `x` e `y`, juntamente com um booliano que indica se a rolagem deve ser animada:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> O método `ScrollToAsync` não fará a rolagem quando a propriedade [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) for definida como `Neither`.

Ao rolar para um elemento específico, a enumeração `ScrollToPosition` especifica onde o elemento aparecerá:

- **Centralizar** &ndash; rola o elemento para o centro da parte visível da exibição.
- **End** &ndash; rola o elemento para o final da parte visível da exibição.
- **MakeVisible** &ndash; rola o elemento para que ele fique visível na exibição.
- **Iniciar** &ndash; rola o elemento para o início da parte visível da exibição.

A propriedade `IsAnimated` especifica como a exibição será rolada. Quando definido como `true`, uma animação suave será usada, em vez de mover instantaneamente o conteúdo para a exibição.

## <a name="events"></a>Eventos

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define apenas um evento, `Scrolled`. `Scrolled` é gerado quando a exibição termina de rolagem. O manipulador de eventos para `Scrolled` usa `ScrolledEventArgs`, que tem as propriedades `ScrollX` e `ScrollY`. O seguinte demonstra como atualizar um rótulo com a posição de rolagem atual de uma `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Observe que as posições de rolagem podem ser negativas, devido ao efeito de repercussão durante a rolagem no final de uma lista.

## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
