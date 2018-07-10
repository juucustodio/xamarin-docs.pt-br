---
title: Resumo do capítulo 4. Rolagem da pilha
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 4. Rolagem da pilha'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7860df998fbfe580362aff0f4f01374a4ae1f923
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935547"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumo do capítulo 4. Rolagem da pilha

Este capítulo dedica-se principalmente para introduzir o conceito de *layout*, que é o termo geral para as classes e as técnicas que usa o xamarin. Forms para organizar a exibição visual de vários modos de exibição na página.

Layout envolve várias classes que derivam de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/). Este capítulo enfoca [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

Também é apresentado neste capítulo são as [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/), [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/), e [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) classes.

## <a name="stacks-of-views"></a>Pilhas de modos de exibição

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) deriva `Layout<View>` e herda um [ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) propriedade do tipo `IList<View>`. Adicionar vários itens de exibição para essa coleção, e `StackLayout` exibe-os em uma pilha horizontal ou vertical.

Defina a [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriedade do `StackLayout` a um membro da [ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation) enumeração, qualquer um dos [ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical) ou [ `Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). O padrão é `Vertical`.

Defina as [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) propriedade do `StackLayout` para um `double` valor para especificar um espaçamento entre os filhos. O valor padrão é 6.

No código, você pode adicionar itens a serem a `Children` coleção de `StackLayout` em um `for` ou `foreach` loop conforme demonstrado no [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) exemplo, ou você pode inicializar o `Children` coleção com uma lista das exibições individuais conforme demonstrado no [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Os filhos devem derivar `View` , mas pode incluir outros `StackLayout` objetos.

## <a name="scrolling-content"></a>Rolando conteúdo

Se um `StackLayout` contém muitos filhos serem exibidos em uma página, você pode colocar o `StackLayout` em um [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) para permitir a rolagem.

Defina as [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) propriedade do `ScrollView` para o modo de exibição para rolar. Isso geralmente é um `StackLayout`, mas pode ser qualquer modo de exibição.

Defina a [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) propriedade do `ScrollView` a um membro da [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) propriedade, [ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical), [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal), ou [ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both). O padrão é `Vertical`. Se o conteúdo de um `ScrollView` é um `StackLayout`, as duas orientações devem ser consistentes.

O [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) exemplo demonstra o uso de `ScrollView` e `StackLayout` para exibir as cores disponíveis. O exemplo também demonstra como usar a reflexão do .NET para obter todos os campos de e as propriedades públicas estáticas a `Color` estrutura sem a necessidade de listá-los explicitamente.

## <a name="the-expands-option"></a>A opção expande

Quando um `StackLayout` pilhas de seus filhos, cada filho ocupa um slot específico dentro a altura total do `StackLayout` que depende do tamanho de seu filho e as configurações de seu `HorizontalOptions` e `VerticalOptions` propriedades. Essas propriedades são atribuídas valores do tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

O `LayoutOptions` estrutura define duas propriedades:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) o tipo de enumeração [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment) com quatro membros, [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) do tipo `bool`

Para sua conveniência, o `LayoutOptions` estrutura também define oito campos estáticos somente leitura do tipo `LayoutOptions` que abrangem todas as combinações das propriedades de dois instância:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

A discussão a seguir envolve uma `StackLayout` com uma orientação vertical do padrão. Horizontal `StackLayout` é análogo.

Para vertical `StackLayout`, o `HorizontalOptions` configuração determina como o filho é posicionado horizontalmente dentro da largura do `StackLayout`. Uma `Alignment` configuração do `Start`, `Center`, ou `End` faz com que o filho seja horizontalmente irrestrita. O filho determina sua própria largura e é posicionado na esquerda, centro ou direita do `StackLayout`. O `Fill` opção faz com que o filho a ser restringido horizontalmente e preenche a largura do `StackLayout`.

Para vertical `StackLayout`, cada filho é irrestrito verticalmente e obtém uma vertical slot dependendo da altura de seu filho, caso em que o `VerticalOptions` configuração é irrelevante.

Se vertical `StackLayout` em si é irrestrita&mdash;que é se seu `VerticalOptions` configuração é `Start`, `Center`, ou `End`, em seguida, a altura do `StackLayout` é a altura total de seus filhos.

No entanto, se a vertical `StackLayout` verticalmente é restrito&mdash;se seu `VerticalOptions` configuração é `Fill` &mdash;, em seguida, a altura do `StackLayout` será a altura de seu contêiner, que pode ser maior que o total altura de seus filhos. Se esse for o caso e se tem pelo menos um filho uma `VerticalOptions` com um `Expands` do sinalizador de `true`, em seguida, o espaço extra no `StackLayout` é alocado igualmente entre todos os filhos com um `Expands` do sinalizador de `true`. A altura total dos filhos, em seguida, será igual a altura do `StackLayout`e o `Alignment` fazem parte do `VerticalOptions` configuração determina como o filho é posicionado verticalmente em seu slot.

Isso é demonstrado na [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) exemplo.

## <a name="frame-and-boxview"></a>Quadro e BoxView

Esses dois modos de exibição retangulares geralmente são usados para fins de apresentação.

O [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) modo de exibição exibe um quadro retangular em torno de outro modo de exibição, que pode ser um layout, como `StackLayout`. `Frame` herda um [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade do [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) que você definir para o modo de exibição a ser exibido dentro de `Frame`. O `Frame` é transparente por padrão. Defina as seguintes três propriedades para personalizar a aparência do quadro:

- O [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/) propriedade para torná-la visível. É comum para definir `OutlineColor` para `Color.Accent` quando você não souber o esquema de cores subjacente.
- O [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/) propriedade pode ser definida como `true` para exibir uma sombra preta em dispositivos iOS.
- Defina a [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedade para um `Thickness` conteúdo do valor deixar um espaço entre o quadro e quadro. O valor padrão é de 20 unidades de todos os lados.

O `Frame` possui padrão `HorizontalOptions` e `VerticalOptions` valores de `LayoutOptions.Fill`, o que significa que o `Frame` será preencher seu contêiner. Com outras configurações, o tamanho do `Frame` baseia-se no tamanho do seu conteúdo.

O `Frame` é demonstrado na [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) exemplo.

O [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) exibe uma área retangular da cor especificada pelo seu [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propriedade.

Se o `BoxView` é restrito (sua `HorizontalOptions` e `VerticalOptions` propriedades têm suas configurações padrão de `LayoutOptions.Fill`), o `BoxView` preenche o espaço disponível para ele. Se o `BoxView` irrestrita (com `HorizontalOptions` e `LayoutOptions` configurações de `Start`, `Center`, ou `End`), ele tem uma dimensão padrão do quadrado de 40 unidades. Um `BoxView` pode ser restrito em uma dimensão e sem restrições no outro.

Muitas vezes, você definirá à [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propriedades de `BoxView` para dar a ele um tamanho específico. Isso é ilustrado pelo [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) exemplo.

Você pode usar várias instâncias do `StackLayout` para combinar uma `BoxView` e várias `Label` instâncias em um `Frame` para exibir uma cor específica e, em seguida, colocar cada um desses modos de exibição em um `StackLayout` em um `ScrollView` para criar o atraente lista de cores mostrada a [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) exemplo:

[![Captura de tela tripla de blocos de cor](images/ch04fg11-small.png "lista de cores")](images/ch04fg11-large.png#lightbox "lista de cores")

## <a name="a-scrollview-in-a-stacklayout"></a>Um ScrollView em um StackLayout?

Colocando um `StackLayout` em um `ScrollView` é comum, mas colocando um `ScrollView` em um `StackLayout` , às vezes, também é conveniente. Em teoria, isso não deve ser possível porque os filhos de um vertical `StackLayout` são verticalmente irrestrita. Mas um `ScrollView` deve ser restrito verticalmente. Ele deve receber uma altura específica para que, em seguida, ele pode determinar o tamanho de seu filho para rolagem.

O truque é dar a `ScrollView` filho do `StackLayout` um `VerticalOptions` configuração de `FillAndExpand`. Isso é demonstrado na [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) exemplo.

O **BlackCat** exemplo também demonstra como definir e acessar os recursos do programa que são inseridos na biblioteca de classe portátil (PCL). Isso também pode ser obtido com projetos de ativos compartilhados (SAPs), mas o processo é um pouco mais complicado, como o [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) demonstra.



## <a name="related-links"></a>Links relacionados

- [Capítulo 4 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemplos do capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Exemplos do capítulo 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
