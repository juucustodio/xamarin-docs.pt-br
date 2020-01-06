---
title: Xamarin. Forms ScrollView
description: Este artigo explica como usar a classe do xamarin. Forms ScrollView para apresentar os layouts que não cabem em apenas uma única tela, e que têm conteúdo liberar espaço para o teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: bb10cda7c9899f176861ceee712cc876984c56ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488264"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contém layouts e permite que eles para fora da tela de rolagem. `ScrollView` também é usado para permitir que os modos de exibição mover automaticamente para a parte visível da tela quando o teclado está mostrando.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidade

[`ScrollView`](xref:Xamarin.Forms.ScrollView) pode ser usado para garantir que exibições maiores sejam bem exibidas em telefones menores. Por exemplo, um layout que funciona em um iPhone 6s poderão ser cortado em um iPhone 4s. Usando um `ScrollView` permitiria que as partes recortadas do layout a ser exibido na tela menor.

## <a name="usage"></a>Medição de

> [!NOTE]
> os objetos de [`ScrollView`](xref:Xamarin.Forms.ScrollView) não devem ser aninhados. Além disso, `ScrollView`s não devem ser aninhados com outros controles que fornecem a rolagem, como `ListView` e `WebView`.

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
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Antes do usuário rola para baixo, apenas o `BoxView` está visível:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Observe que, quando o usuário começa a inserir texto no `Entry`, o modo de exibição rola para mantê-lo visível na tela:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>{1&gt;Propriedades&lt;1}

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define as propriedades a seguir:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtém uma [ `Size` ](xref:Xamarin.Forms.Size) valor que representa o tamanho do conteúdo.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtém ou define um [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valor de enumeração que representa a direção de rolagem do `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Obtém um `double` que representa a atual posição de rolagem X.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Obtém um `double` que representa a atual posição de rolagem de Y.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtém ou define um [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem horizontal está visível.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtém ou define um [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa quando a barra de rolagem vertical está visível.

> [!NOTE]
> A rolagem pode ser desabilitada definindo a propriedade [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) como `Neither`.

## <a name="methods"></a>{1&gt;Métodos&lt;1}

[`ScrollView`](xref:Xamarin.Forms.ScrollView) fornece um método `ScrollToAsync`, que pode ser usado para rolar a exibição usando coordenadas ou especificando uma determinada exibição que deve se tornar visível.

Ao usar coordenadas, especifique o `x` e `y` coordenadas, juntamente com um valor booliano que indica se a rolagem deve ser animada:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> O método `ScrollToAsync` não fará a rolagem quando a propriedade [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) for definida como `Neither`.

Ao rolar para um elemento específico, a enumeração `ScrollToPosition` especifica onde o elemento aparecerá:

- **Centro** &ndash; rola o elemento para o centro da parte visível da exibição.
- **Término** &ndash; rola o elemento ao final da parte visível da exibição.
- **MakeVisible** &ndash; rola o elemento para que ele fique visível dentro da exibição.
- **Inicie** &ndash; rola o elemento ao início da parte visível da exibição.

O `IsAnimated` propriedade especifica como o modo de exibição será rolado. Quando definido como `true`, uma animação suave será usada, em vez de mover instantaneamente o conteúdo para a exibição.

## <a name="events"></a>Events

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define apenas um evento, `Scrolled`. `Scrolled` é gerado quando o modo de exibição terminou de rolagem. Manipulador de eventos do `Scrolled` leva `ScrolledEventArgs`, que tem o `ScrollX` e `ScrollY` propriedades. A seguir demonstra como atualizar um rótulo com a atual posição de rolagem de um `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Observe que as posições de rolagem podem ser negativas, devido ao efeito de devolução durante a rolagem no final de uma lista.

## <a name="related-links"></a>Links relacionados

- [Layout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
