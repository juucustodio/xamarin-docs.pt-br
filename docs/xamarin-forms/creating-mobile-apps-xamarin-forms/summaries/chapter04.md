---
title: Resumo do capítulo 4. Rolagem da pilha
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 4. Rolagem da pilha'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 26b9aa33267c56a82edfd38a1d7eefa6a5cd82a2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370358"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumo do capítulo 4. Rolagem da pilha

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Este capítulo é dedicado principalmente à introdução do conceito de *layout* , que é o termo geral para as classes e técnicas que o Xamarin.Forms usa para organizar a exibição visual de várias exibições na página.

O layout envolve várias classes que derivam de [`Layout`](xref:Xamarin.Forms.Layout) e  [`Layout<T>`](xref:Xamarin.Forms.Layout`1) . Este capítulo se concentra em [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> O [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introduzido no Xamarin.Forms 3,0 pode ser usado de maneiras semelhantes a `StackLayout` , mas com mais flexibilidade.

Também foram apresentadas neste capítulo as [`ScrollView`](xref:Xamarin.Forms.ScrollView) classes, [`Frame`](xref:Xamarin.Forms.Frame) e [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="stacks-of-views"></a>Pilhas de modos de exibição

[`StackLayout`](xref:Xamarin.Forms.StackLayout) deriva de `Layout<View>` e herda uma [`Children`](xref:Xamarin.Forms.Layout`1) Propriedade do tipo `IList<View>` . Você adiciona vários itens de exibição a essa coleção e `StackLayout` os exibe em uma pilha horizontal ou vertical.

Defina a [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade de `StackLayout` como um membro da [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) enumeração, [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) ou [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) . O padrão é `Vertical`.

Defina a [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propriedade de `StackLayout` como um `double` valor para especificar um espaçamento entre os filhos. O valor padrão é 6.

No código, você pode adicionar itens à `Children` coleção de `StackLayout` em um `for` loop ou, `foreach` conforme demonstrado no exemplo de [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , ou pode inicializar a `Children` coleção com uma lista de exibições individuais, conforme demonstrado em [**colorlist**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Os filhos devem derivar de `View` , mas podem incluir outros `StackLayout` objetos.

## <a name="scrolling-content"></a>Rolando conteúdo

Se um `StackLayout` contiver muitos filhos para exibir em uma página, você poderá colocar o `StackLayout` em um [`ScrollView`](xref:Xamarin.Forms.ScrollView) para permitir a rolagem.

Defina a [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriedade de `ScrollView` como a exibição que você deseja rolar. Isso geralmente é um `StackLayout` , mas pode ser qualquer modo de exibição.

Defina a [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriedade de `ScrollView` como um membro da [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) propriedade, [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical) , [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) ou [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both) . O padrão é `Vertical`. Se o conteúdo de um `ScrollView` for a `StackLayout` , as duas orientações devem ser consistentes.

O exemplo [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) demonstra o uso de `ScrollView` e `StackLayout` para exibir as cores disponíveis. O exemplo também demonstra como usar a reflexão do .NET para obter todas as propriedades e os campos públicos estáticos da `Color` estrutura sem a necessidade de listá-los explicitamente.

## <a name="the-expands-option"></a>A opção expande

Quando uma `StackLayout` pilha de seus filhos, cada filho ocupa um slot específico dentro da altura total do `StackLayout` que depende do tamanho do filho e das configurações de suas `HorizontalOptions` `VerticalOptions` Propriedades e. Essas propriedades são atribuídas a valores do tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) .

A `LayoutOptions` estrutura define duas propriedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) do tipo de enumeração [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) com quatro membros, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) , [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) , [`End`](xref:Xamarin.Forms.LayoutAlignment.End) e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) do tipo `bool`

Para sua conveniência, a `LayoutOptions` estrutura também define oito campos estáticos somente leitura do tipo `LayoutOptions` que abrangem todas as combinações das duas propriedades de instância:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

A discussão a seguir envolve um `StackLayout` com uma orientação vertical padrão. A horizontal `StackLayout` é análoga.

Para um vertical `StackLayout` , a `HorizontalOptions` configuração determina como um filho é posicionado horizontalmente dentro da largura do `StackLayout` . Uma `Alignment` configuração de `Start` , `Center` ou `End` faz com que o filho seja horizontalmente irrestrito. O filho determina sua própria largura e é posicionado à esquerda, no centro ou à direita do `StackLayout` . A `Fill` opção faz com que o filho seja restringido horizontalmente e preencha a largura do `StackLayout` .

Para um vertical `StackLayout` , cada filho é verticalmente irrestrito e Obtém um slot vertical dependendo da altura do filho; nesse caso, a `VerticalOptions` configuração é irrelevante.

Se a `StackLayout` sua configuração for a própria vertical, &mdash; isto é, se for `VerticalOptions` `Start` , `Center` ou `End` , a altura de `StackLayout` é a altura total de seus filhos.

No entanto, se a vertical `StackLayout` for restrita verticalmente &mdash; se sua `VerticalOptions` configuração for `Fill` &mdash; , a altura de `StackLayout` será a altura de seu contêiner, que pode ser maior que a altura total de seus filhos. Se esse for o caso e se pelo menos um filho tiver uma `VerticalOptions` configuração com um `Expands` sinalizador de `true` , o espaço extra no `StackLayout` será alocado igualmente entre todos os filhos com um `Expands` sinalizador de `true` . A altura total dos filhos será igual à altura do `StackLayout` e a `Alignment` parte da `VerticalOptions` configuração determinará como o filho será posicionado verticalmente em seu slot.

Isso é demonstrado no exemplo de [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Quadro e BoxView

Essas duas exibições retangulares são geralmente usadas para fins de apresentação.

A [`Frame`](xref:Xamarin.Forms.Frame) exibição mostra um quadro retangular ao contrário de outra exibição, que pode ser um layout como `StackLayout` . `Frame` herda uma [`Content`](xref:Xamarin.Forms.ContentView.Content) Propriedade do [`ContentView`](xref:Xamarin.Forms.ContentView) que você define para a exibição a ser exibida dentro do `Frame` . O `Frame` é transparente por padrão. Defina as três propriedades a seguir para personalizar a aparência do quadro:

- A [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) propriedade para torná-la visível. É comum definir `OutlineColor` como `Color.Accent` quando você não souber o esquema de cores subjacente.
- A [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) propriedade pode ser definida como `true` para exibir uma sombra preta em dispositivos IOS.
- Defina a [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriedade como um `Thickness` valor para deixar um espaço entre o quadro e o conteúdo do quadro. O valor padrão é 20 unidades em todos os lados.

O `Frame` tem o padrão `HorizontalOptions` e os `VerticalOptions` valores de `LayoutOptions.Fill` , o que significa que o `Frame` preencherá seu contêiner. Com outras configurações, o tamanho do `Frame` é baseado no tamanho do seu conteúdo.

O `Frame` é demonstrado no exemplo de [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

O [`BoxView`](xref:Xamarin.Forms.BoxView) exibe uma área retangular de cor especificada por sua [`Color`](xref:Xamarin.Forms.BoxView.Color) propriedade.

Se o `BoxView` for restrito (suas `HorizontalOptions` `VerticalOptions` Propriedades e têm suas configurações padrão de `LayoutOptions.Fill` ), o `BoxView` preencherá o espaço disponível para ele. Se o `BoxView` for irrestrito (com `HorizontalOptions` `LayoutOptions` as configurações `Start` , `Center` ou `End` ), ele terá uma dimensão padrão de 40 unidades quadrado. Um `BoxView` pode ser restrito em uma dimensão e irrestrito no outro.

Muitas vezes, você definirá [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) as [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriedades e de `BoxView` para dar a ela um tamanho específico. Isso é ilustrado pelo exemplo de [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

Você pode usar várias instâncias do `StackLayout` para combinar uma `BoxView` e várias `Label` instâncias em um `Frame` para exibir uma cor específica e, em seguida, colocar cada uma dessas exibições em uma `StackLayout` `ScrollView` para criar a lista atraente de cores mostradas no exemplo [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Captura de tela tripla de blocos de cor](images/ch04fg11-small.png "Lista de cores")](images/ch04fg11-large.png#lightbox "Lista de cores")

## <a name="a-scrollview-in-a-stacklayout"></a>Um ScrollView em um StackLayout?

Colocar um `StackLayout` em um `ScrollView` é comum, mas colocar um `ScrollView` em um `StackLayout` também é conveniente. Teoricamente, isso não deve ser possível porque os filhos de um vertical não `StackLayout` são restringidos verticalmente. Mas um `ScrollView` deve ser verticalmente restrito. Ele deve receber uma altura específica para que possa determinar o tamanho de seu filho para rolagem.

O truque é dar ao `ScrollView` filho a `StackLayout` `VerticalOptions` configuração de `FillAndExpand` . Isso é demonstrado no exemplo de [**blackcat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

O exemplo de **blackcat** também demonstra como definir e acessar recursos do programa que são inseridos na biblioteca compartilhada. Isso também pode ser obtido com os projetos de ativos compartilhados (SAPs), mas o processo é um pouco mais complicado, como demonstra o exemplo de [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Links relacionados

- [Capítulo 4 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemplos do capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Capítulo 4 amostras F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scrollview.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
