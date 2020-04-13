---
title: Resumo do Capítulo 4. Rolagem da pilha
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 4. Rolagem da pilha'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032871"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumo do Capítulo 4. Rolagem da pilha

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Este capítulo é principalmente dedicado à introdução do conceito de *layout*, que é o termo global para as classes e técnicas que xamarin.forms usa para organizar a exibição visual de múltiplas visualizações na página.

O layout envolve várias classes que derivam [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1). Este capítulo se [`StackLayout`](xref:Xamarin.Forms.StackLayout)concentra em .

> [!NOTE]
> O [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introduzido no Xamarin.Forms 3.0 pode ser `StackLayout` usado de maneiras semelhantes, mas com mais flexibilidade.

Também são introduzidas [`ScrollView`](xref:Xamarin.Forms.ScrollView)neste [`Frame`](xref:Xamarin.Forms.Frame)capítulo [`BoxView`](xref:Xamarin.Forms.BoxView) as aulas e as classes.

## <a name="stacks-of-views"></a>Pilhas de visualizações

[`StackLayout`](xref:Xamarin.Forms.StackLayout)deriva `Layout<View>` e herda [`Children`](xref:Xamarin.Forms.Layout`1) uma propriedade `IList<View>`do tipo. Você adiciona vários itens de exibição `StackLayout` a esta coleção e os exibe em uma pilha horizontal ou vertical.

Defina [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) a `StackLayout` propriedade de [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) um membro da [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)enumeração, ou . O padrão é `Vertical`.

Defina [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) a `StackLayout` propriedade `double` de um valor para especificar um espaçamento entre as crianças. O valor padrão é 6.

Em código, você pode adicionar `Children` itens `StackLayout` à `for` coleção `foreach` de em um ou loop como demonstrado `Children` na amostra [**ColorLoop,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) ou você pode inicializar a coleção com uma lista das visualizações individuais como demonstrado em [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). As crianças `View` devem derivar, mas podem incluir outros `StackLayout` objetos.

## <a name="scrolling-content"></a>Rolagem de conteúdo

Se `StackLayout` um contém muitas crianças para exibir em `StackLayout` uma [`ScrollView`](xref:Xamarin.Forms.ScrollView) página, você pode colocar o em um para permitir rolagem.

Defina [`Content`](xref:Xamarin.Forms.ScrollView.Content) a `ScrollView` propriedade da vista que deseja rolar. Isso é `StackLayout`muitas vezes um , mas pode ser qualquer vista.

Defina [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) a `ScrollView` propriedade de [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) um [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)membro [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)da [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)propriedade, ou . O padrão é `Vertical`. Se o conteúdo `ScrollView` de `StackLayout`a é a, as duas orientações devem ser consistentes.

A amostra [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) demonstra `ScrollView` `StackLayout` o uso e para exibir as cores disponíveis. A amostra também demonstra como usar a reflexão .NET para `Color` obter todas as propriedades e campos estáticos públicos da estrutura sem a necessidade de listá-las explicitamente.

## <a name="the-expands-option"></a>A opção Expande

Quando `StackLayout` um empilha seus filhos, cada criança ocupa uma determinada `StackLayout` ranhura dentro da altura total do `HorizontalOptions` que `VerticalOptions` depende do tamanho da criança e das configurações de suas propriedades. Essas propriedades são valores [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)atribuídos do tipo .

A `LayoutOptions` estrutura define duas propriedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) do tipo de enumeração [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)com [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End)quatro membros, e[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)de tipo`bool`

Para sua conveniência, a `LayoutOptions` estrutura também define oito `LayoutOptions` campos de tipo somente leitura estática que abrangem todas as combinações das duas propriedades de instância:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

A discussão a `StackLayout` seguir envolve uma orientação vertical padrão. A `StackLayout` horizontal é análoga.

Para uma `StackLayout`vertical, a `HorizontalOptions` configuração determina como uma criança está `StackLayout`posicionada horizontalmente dentro da largura do . Um `Alignment` ajuste `Start` `Center`de `End` , ou faz com que a criança seja horizontalmente desconstrangida. A criança determina sua própria largura e está posicionada à `StackLayout`esquerda, centro ou direita do . A `Fill` opção faz com que a criança seja limitada `StackLayout`horizontalmente e preencha a largura do .

Para uma `StackLayout`vertical, cada criança é verticalmente sem restrições e obtém uma ranhura vertical dependendo da altura da criança, nesse caso a `VerticalOptions` configuração é irrelevante.

Se a `StackLayout` vertical em&mdash;si não `VerticalOptions` for `Start` `Center`constrangida, isto é, se o seu ajuste é , ou `End`, então a altura do `StackLayout` é a altura total de seus filhos.

No entanto, `StackLayout` se a&mdash;vertical `VerticalOptions` for `Fill` &mdash;verticalmente restrita `StackLayout` se sua configuração for, então a altura do seu recipiente será maior do que a altura total de seus filhos. Se esse for o caso, e se `VerticalOptions` pelo `Expands` menos `true`uma criança tiver um `StackLayout` cenário com uma bandeira de `Expands` , `true`então o espaço extra no é alocado igualmente entre todas as crianças com uma bandeira de . A altura total das crianças será `StackLayout`então igual `Alignment` à `VerticalOptions` altura do , e a parte do ajuste determina como a criança está posicionada verticalmente em seu slot.

Isso é demonstrado na amostra [**VerticalOptionsDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)

## <a name="frame-and-boxview"></a>Frame e BoxView

Essas duas visualizações retangulares são frequentemente usadas para fins de apresentação.

A [`Frame`](xref:Xamarin.Forms.Frame) exibição exibe um quadro retangular em torno `StackLayout`de outra exibição, que pode ser um layout como . `Frame`herda [`Content`](xref:Xamarin.Forms.ContentView.Content) uma [`ContentView`](xref:Xamarin.Forms.ContentView) propriedade a partir daquilo que você `Frame`definiu para a exibição a ser exibida dentro do . O `Frame` é transparente por padrão. Defina as três propriedades a seguir para personalizar a aparência do quadro:

- A [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) propriedade para torná-la visível. É comum `OutlineColor` definir `Color.Accent` quando você não conhece o esquema de cores subjacente.
- A [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) propriedade pode `true` ser definida para exibir uma sombra negra em dispositivos iOS.
- Defina [`Padding`](xref:Xamarin.Forms.Layout.Padding) a `Thickness` propriedade como um valor para deixar um espaço entre o quadro e o conteúdo do quadro. O valor padrão é de 20 unidades em todos os lados.

O `Frame` tem `HorizontalOptions` `VerticalOptions` padrão `LayoutOptions.Fill`e valores `Frame` de , o que significa que o vai encher seu recipiente. Com outras configurações, `Frame` o tamanho do é baseado no tamanho de seu conteúdo.

O `Frame` é demonstrado na amostra [**FramedText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)

O [`BoxView`](xref:Xamarin.Forms.BoxView) exibe uma área retangular de [`Color`](xref:Xamarin.Forms.BoxView.Color) cor especificada por sua propriedade.

Se `BoxView` o for constrangido `HorizontalOptions` (suas propriedades e `VerticalOptions` suas propriedades têm suas configurações padrão de), `LayoutOptions.Fill`o `BoxView` preenchimento do espaço disponível para ele. Se `BoxView` o não for `HorizontalOptions` constrangido (com `LayoutOptions` e configurações de `Start`, `Center`ou `End`), ele tem uma dimensão padrão de 40 unidades quadradas. A `BoxView` pode ser constrangido em uma dimensão e sem restrições na outra.

Muitas vezes, você [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) definirá `BoxView` as propriedades de [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) dar-lhe um tamanho específico. Isso é ilustrado pela amostra [**SizedBoxView.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)

Você pode usar `StackLayout` várias `BoxView` instâncias `Label` de `Frame` para combinar uma e várias instâncias em `StackLayout` um `ScrollView` para exibir uma cor específica e, em seguida, colocar cada uma dessas visualizações em um para criar a lista atraente de cores mostradas na amostra [**ColorBlocks:**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)

[![Captura de tela tripla de blocos de cores](images/ch04fg11-small.png "Lista de Cores")](images/ch04fg11-large.png#lightbox "Lista de Cores")

## <a name="a-scrollview-in-a-stacklayout"></a>Um ScrollView em um Layout stack?

Colocar `StackLayout` um `ScrollView` em um é `ScrollView` comum, mas colocar um em um `StackLayout` também às vezes é conveniente. Em teoria, isso não deve ser possível `StackLayout` porque os filhos de uma vertical são verticalmente sem restrições. Mas `ScrollView` deve ser verticalmente constrangido. Deve ser dada uma altura específica para que possa então determinar o tamanho de seu filho para rolagem.

O truque é `ScrollView` dar ao `StackLayout` `VerticalOptions` filho `FillAndExpand`um cenário de . Isso é demonstrado na amostra [**BlackCat.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)

A amostra **BlackCat** também demonstra como definir e acessar os recursos do programa que estão incorporados na biblioteca compartilhada. Isso também pode ser alcançado com SAPs (SAPs) de Projetos de Ativos Compartilhados ( SAPs), mas o processo é um pouco mais complicado, como demonstra a amostra [**BlackCatSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)

## <a name="related-links"></a>Links relacionados

- [Capítulo 4 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Capítulo 4 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Capítulo 4 Amostras F#](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
