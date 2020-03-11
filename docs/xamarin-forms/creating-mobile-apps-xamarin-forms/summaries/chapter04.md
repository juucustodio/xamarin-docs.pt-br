---
title: Resumo do capítulo 4. Rolagem da pilha
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 4. Rolagem da pilha'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032871"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumo do capítulo 4. Rolagem da pilha

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Este capítulo é dedicado principalmente à introdução do conceito de *layout*, que é o termo geral para as classes e técnicas que o Xamarin. Forms usa para organizar a exibição visual de várias exibições na página.

O layout envolve várias classes que derivam de [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1). Este capítulo se concentra em [`StackLayout`](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> As [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introduzidas no Xamarin. Forms 3,0 podem ser usadas de maneiras semelhantes às `StackLayout`, mas com mais flexibilidade.

Também foram apresentadas neste capítulo as classes [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`Frame`](xref:Xamarin.Forms.Frame)e [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="stacks-of-views"></a>Pilhas de modos de exibição

[`StackLayout`](xref:Xamarin.Forms.StackLayout) deriva de `Layout<View>` e herda uma propriedade [`Children`](xref:Xamarin.Forms.Layout`1) do tipo `IList<View>`. Você adiciona vários itens de exibição a essa coleção e `StackLayout` os exibe em uma pilha horizontal ou vertical.

Defina a propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) de `StackLayout` como um membro da enumeração [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) ou [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). O padrão é `Vertical`.

Defina a propriedade [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) de `StackLayout` como um valor `double` para especificar um espaçamento entre os filhos. O valor padrão é 6.

No código, você pode adicionar itens à coleção de `Children` de `StackLayout` em um loop `for` ou `foreach`, conforme demonstrado no exemplo [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , ou pode inicializar a coleção de `Children` com uma lista de exibições individuais, conforme demonstrado em [**colorlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Os filhos devem derivar de `View`, mas podem incluir outros objetos `StackLayout`.

## <a name="scrolling-content"></a>Rolando conteúdo

Se um `StackLayout` contiver um número excessivo de filhos para ser exibido em uma página, você poderá colocar o `StackLayout` em uma [`ScrollView`](xref:Xamarin.Forms.ScrollView) para permitir a rolagem.

Defina a propriedade [`Content`](xref:Xamarin.Forms.ScrollView.Content) de `ScrollView` para a exibição que você deseja rolar. Isso geralmente é um `StackLayout`, mas pode ser qualquer modo de exibição.

Defina a propriedade [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) de `ScrollView` como um membro da propriedade [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical), [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)ou [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both). O padrão é `Vertical`. Se o conteúdo de um `ScrollView` for um `StackLayout`, as duas orientações devem ser consistentes.

O exemplo [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) demonstra o uso de `ScrollView` e `StackLayout` para exibir as cores disponíveis. O exemplo também demonstra como usar a reflexão do .NET para obter todas as propriedades e os campos públicos estáticos da estrutura de `Color` sem a necessidade de listá-los explicitamente.

## <a name="the-expands-option"></a>A opção expande

Quando um `StackLayout` empilha seus filhos, cada filho ocupa um slot específico dentro da altura total do `StackLayout` que depende do tamanho do filho e das configurações de suas propriedades `HorizontalOptions` e `VerticalOptions`. Essas propriedades são atribuídas a valores do tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions).

A estrutura de `LayoutOptions` define duas propriedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) do tipo de enumeração [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) com quatro membros, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End)e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) do tipo `bool`

Para sua conveniência, a estrutura de `LayoutOptions` também define oito campos somente leitura estáticos do tipo `LayoutOptions` que abrangem todas as combinações das duas propriedades de instância:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

A discussão a seguir envolve uma `StackLayout` com uma orientação vertical padrão. O `StackLayout` horizontal é análogo.

Para um `StackLayout`vertical, a configuração `HorizontalOptions` determina como um filho é posicionado horizontalmente dentro da largura da `StackLayout`. Uma configuração `Alignment` de `Start`, `Center`ou `End` faz com que o filho seja horizontalmente irrestrito. O filho determina sua própria largura e é posicionado à esquerda, no centro ou à direita da `StackLayout`. A opção `Fill` faz com que o filho seja restringido horizontalmente e preencha a largura do `StackLayout`.

Para um `StackLayout`vertical, cada filho é verticalmente irrestrito e Obtém um slot vertical dependendo da altura do filho; nesse caso, a configuração de `VerticalOptions` é irrelevante.

Se a `StackLayout` vertical em si for irrestrita&mdash;se sua configuração de `VerticalOptions` for `Start`, `Center`ou `End`, a altura da `StackLayout` será a altura total de seus filhos.

No entanto, se a `StackLayout` vertical for verticalmente restrita&mdash;se sua configuração de `VerticalOptions` for `Fill`&mdash;a altura do `StackLayout` será a altura de seu contêiner, que pode ser maior que a altura total de seus filhos. Se esse for o caso e se pelo menos um filho tiver uma configuração de `VerticalOptions` com um sinalizador de `Expands` de `true`, o espaço extra no `StackLayout` será alocado igualmente entre todos os filhos com um sinalizador de `Expands` de `true`. A altura total dos filhos será igual à altura da `StackLayout`e a `Alignment` parte da configuração `VerticalOptions` determina como o filho é posicionado verticalmente em seu slot.

Isso é demonstrado no exemplo de [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Quadro e BoxView

Esses dois modos de exibição retangulares geralmente são usados para fins de apresentação.

A exibição [`Frame`](xref:Xamarin.Forms.Frame) exibe um quadro retangular ao contrário de outra exibição, que pode ser um layout como `StackLayout`. `Frame` herda uma propriedade [`Content`](xref:Xamarin.Forms.ContentView.Content) de [`ContentView`](xref:Xamarin.Forms.ContentView) que você definiu para a exibição a ser exibida no `Frame`. O `Frame` é transparente por padrão. Defina as seguintes três propriedades para personalizar a aparência do quadro:

- A propriedade [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) para torná-la visível. É comum definir `OutlineColor` como `Color.Accent` quando você não conhece o esquema de cores subjacente.
- A propriedade [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) pode ser definida como `true` para exibir uma sombra preta em dispositivos IOS.
- Defina a propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) como um valor de `Thickness` para deixar um espaço entre o quadro e o conteúdo do quadro. O valor padrão é de 20 unidades de todos os lados.

O `Frame` tem `HorizontalOptions` padrão e `VerticalOptions` valores de `LayoutOptions.Fill`, o que significa que o `Frame` preencherá seu contêiner. Com outras configurações, o tamanho do `Frame` é baseado no tamanho de seu conteúdo.

O `Frame` é demonstrado no exemplo de [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

O [`BoxView`](xref:Xamarin.Forms.BoxView) exibe uma área retangular de cor especificada por sua propriedade [`Color`](xref:Xamarin.Forms.BoxView.Color) .

Se a `BoxView` for restrita (suas propriedades `HorizontalOptions` e `VerticalOptions` tiverem suas configurações padrão de `LayoutOptions.Fill`), o `BoxView` preencherá o espaço disponível para ele. Se a `BoxView` for irrestrita (com `HorizontalOptions` e `LayoutOptions` configurações de `Start`, `Center`ou `End`), ela terá uma dimensão padrão de 40 unidades quadrado. Uma `BoxView` pode ser restrita em uma dimensão e não restrita no outro.

Muitas vezes, você definirá as propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de `BoxView` para dar a ela um tamanho específico. Isso é ilustrado pelo exemplo de [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

Você pode usar várias instâncias de `StackLayout` para combinar uma `BoxView` e várias instâncias de `Label` em uma `Frame` para exibir uma cor específica e, em seguida, colocar cada uma dessas exibições em uma `StackLayout` em uma `ScrollView` para criar a lista atraente de cores mostradas no exemplo [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Captura de tela tripla de blocos de cor](images/ch04fg11-small.png "Lista de cores")](images/ch04fg11-large.png#lightbox "Lista de cores")

## <a name="a-scrollview-in-a-stacklayout"></a>Um ScrollView em um StackLayout?

Colocar um `StackLayout` em uma `ScrollView` é comum, mas colocar um `ScrollView` em um `StackLayout` também é conveniente. Teoricamente, isso não deve ser possível porque os filhos de um `StackLayout` vertical não são restringidos verticalmente. Mas um `ScrollView` deve ser verticalmente restrito. Ele deve receber uma altura específica para que, em seguida, ele pode determinar o tamanho de seu filho para rolagem.

O truque é dar ao `ScrollView` filho do `StackLayout` uma `VerticalOptions` configuração de `FillAndExpand`. Isso é demonstrado no exemplo de [**blackcat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

O exemplo de **blackcat** também demonstra como definir e acessar recursos do programa que são inseridos na biblioteca compartilhada. Isso também pode ser obtido com os projetos de ativos compartilhados (SAPs), mas o processo é um pouco mais complicado, como demonstra o exemplo de [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 4 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemplos do capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Exemplos do F# capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
