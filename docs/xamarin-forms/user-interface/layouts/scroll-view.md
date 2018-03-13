---
title: ScrollView
description: "Use ScrollView para apresentar os layouts que não é possível ajustar em uma única tela e conteúdo liberar espaço para o teclado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2016
ms.openlocfilehash: 648125ca8bd2c7c8a015b4c29195dc75c0bbf0a0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="scrollview"></a>ScrollView

[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) contém layouts e permite que eles rolagem fora da tela. `ScrollView` também é usado para permitir que os modos de exibição mover automaticamente para a parte visível da tela quando o teclado está mostrando.

[![](scroll-view-images/layouts-sml.png "Layouts de xamarin. Forms")](scroll-view-images/layouts.png#lightbox "xamarin. Forms Layouts")

Este artigo aborda:

- **[Finalidade](#Purpose)**  &ndash; a finalidade de `ScrollView` e quando ele é usado.
- **[Uso](#Usage)**  &ndash; como usar `ScrollView` na prática.
- **[Propriedades](#Properties)**  &ndash; propriedades públicas que podem ser lidos e modificadas.
- **[Métodos](#Methods)**  &ndash; métodos públicos que podem ser chamados para rolar a exibição.
- **[Eventos](#Events)**  &ndash; eventos que podem ser usados para ouvir alterações nos Estados da exibição.

## <a name="purpose"></a>Finalidade

`ScrollView` pode ser usado para garantir que os modos de exibição maior exibem bem em telefones menores. Por exemplo, um layout que funciona em um iPhone 6s pode ser cortado em um iPhone 4s. Usando um `ScrollView` permitiria que as partes cortadas do layout da ser exibida na tela menor.

## <a name="usage"></a>Uso

> [!NOTE]
> `ScrollView`s não devem ser aninhados. Além disso, `ScrollView`s não devem ser aninhados com outros controles que fornecem a rolagem como `ListView` e `WebView`.

`ScrollView` expõe um `Content` propriedade que pode ser definida para um único modo de exibição ou layout. Considere este exemplo de um layout com um boxView muito grande, seguido por um `Entry`:

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
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,   HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Antes do usuário rola para baixo, apenas o `BoxView` está visível:

![](scroll-view-images/scroll-start.png "BoxView em ScrollView")

Observe que, quando o usuário começa a digitar um texto no `Entry`, o modo de exibição rola para mantê-lo visível na tela:

![](scroll-view-images/scroll-end.png "Entrada em ScrollView")

## <a name="properties"></a>Propriedades

ScrollView tem as seguintes propriedades:

- **Conteúdo** &ndash; obtém ou define o modo de exibição para exibir o `ScrollView`.
- **[ContentSize](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)**  &ndash; somente leitura, obtém o tamanho do conteúdo, que tem um componente de largura e altura. Esta é uma propriedade ligável
- **[Orientação](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)**  &ndash; este é um [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/), que é uma enumeração que pode ser definida como `Horizontal`, `Vertical`, ou `Both`.
- **ScrollX** &ndash; somente leitura, obtém a atual posição de rolagem na dimensão de X.
- **ScrollY** &ndash; somente leitura, obtém a atual posição de rolagem na dimensão de Y.

## <a name="methods"></a>Métodos

`ScrollView` Fornece um `ScrollToAsync` método, que pode ser usado para rolar a exibição usando coordenadas ou ao especificar uma determinada exibição deve ficar visível.

Ao usar coordenadas, especifique o `x` e `y` coordenadas, juntamente com um valor booleano que indica se a rolagem deve ser animada:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Ao rolar para um elemento específico, o `ScrollToPosition` enumeração Especifica onde no modo de exibição do elemento será exibido:

- **Centro de** &ndash; rola o elemento para o centro da parte visível do modo de exibição.
- **Final** &ndash; rola o elemento ao final da parte visível do modo de exibição.
- **MakeVisible** &ndash; rola o elemento para que seja visível no modo de exibição.
- **Iniciar** &ndash; rola o elemento para o início da parte visível do modo de exibição.

O `IsAnimated` propriedade especifica como o modo de exibição será rolado. Quando definido como true, uma animação suave será usado, em vez de mover instantaneamente seu conteúdo em exibição.

## <a name="events"></a>Eventos

`ScrollView` expõe um evento, `Scrolled`. `Scrolled` é gerado quando o modo de exibição tiver terminado de rolagem. O manipulador de eventos `Scrolled` usa `ScrolledEventArgs`, que tem o `ScrollX` e `ScrollY` propriedades. A seguir demonstra como atualizar um rótulo com a atual posição de rolagem de uma `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Observe que as posições de rolagem podem ser negativas, devido ao efeito devolução durante a rolagem no final de uma lista.


## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
