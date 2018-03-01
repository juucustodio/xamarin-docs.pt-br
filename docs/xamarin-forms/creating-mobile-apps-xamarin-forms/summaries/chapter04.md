---
title: "Resumo do capítulo 4. A pilha de rolagem"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 59d51c48cee30a242f5e1adcb1c886169334e9b2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Resumo do capítulo 4. A pilha de rolagem

Este capítulo principalmente dedicado para introduzir o conceito de *layout*, que é o termo geral para as classes e as técnicas que xamarin. Forms usa para organizar a exibição visual de vários modos de exibição na página.

Layout envolve várias classes que derivam de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/). Este capítulo enfoca [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

Também é apresentado neste capítulo são o [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/), [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/), e [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) classes.

## <a name="stacks-of-views"></a>Pilhas de modos de exibição

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) deriva `Layout<View>` e herda um [ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) propriedade do tipo `IList<View>`. Adicionar vários itens de exibição para essa coleção e `StackLayout` os exibe em uma pilha horizontal ou vertical.

Definir o [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) propriedade `StackLayout` a um membro do [ `StackOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackOrientation/) enumeração, ou [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Vertical/) ou [ `Horizontal`](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Horizontal/). O padrão é `Vertical`.

Definir o [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) propriedade `StackLayout` para um `double` valor para especificar um espaçamento entre os filhos. O valor padrão é 6.

No código, você pode adicionar itens a serem a `Children` coleção de `StackLayout` em uma `for` ou `foreach` loop conforme demonstrado no [ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) exemplo, ou você pode inicializar o `Children` coleção com uma lista dos modos de exibição individuais, como demonstrado no [ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Os filhos devem derivar de `View` , mas pode incluir outros `StackLayout` objetos.

## <a name="scrolling-content"></a>Rolar o conteúdo

Se um `StackLayout` contém muitos filhos para exibir em uma página, você pode colocar o `StackLayout` em uma [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) para permitir a rolagem.

Definir o [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) propriedade `ScrollView` para o modo de exibição para rolar. Isso geralmente é um `StackLayout`, mas pode ser qualquer modo de exibição.

Definir o [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) propriedade de `ScrollView` a um membro do [ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/) propriedade [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Vertical/), [ `Horizontal` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Horizontal/), ou [ `Both` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Both/). O padrão é `Vertical`. Se o conteúdo de um `ScrollView` é um `StackLayout`, as duas orientações devem ser consistentes.

O [ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) exemplo demonstra o uso de `ScrollView` e `StackLayout` para exibir as cores disponíveis. O exemplo também demonstra como usar reflexão do .NET para obter todas as propriedades de estáticas públicas e os campos do `Color` estrutura sem a necessidade de listá-los explicitamente.

## <a name="the-expands-option"></a>A opção expande

Quando um `StackLayout` pilhas de seus filhos, cada filho ocupa um slot específico dentro a altura total do `StackLayout` que depende de tamanho do filho e as configurações de seu `HorizontalOptions` e `VerticalOptions` propriedades. Essas propriedades são atribuídas valores do tipo [ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/).

O `LayoutOptions` estrutura define duas propriedades:

- [`Alignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) o tipo de enumeração [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/) com quatro membros, [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), e [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)
- [`Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) do tipo `bool`

Para sua conveniência, o `LayoutOptions` estrutura também define oito campos estáticos somente leitura do tipo `LayoutOptions` que abrange todas as combinações das propriedades de dois instância:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

A discussão a seguir envolve um `StackLayout` com orientação vertical padrão. Horizontal `StackLayout` é semelhante.

Para um vertical `StackLayout`, o `HorizontalOptions` determina como o filho é posicionado horizontalmente dentro da largura do `StackLayout`. Um `Alignment` de `Start`, `Center`, ou `End` faz com que o filho seja horizontalmente irrestrita. O filho determina sua própria largura e é posicionado na esquerda, centro ou à direita do `StackLayout`. O `Fill` opção faz com que o filho a ser restringido horizontalmente e preenche a largura do `StackLayout`.

Para um vertical `StackLayout`, cada filho é irrestrito verticalmente e obtém uma vertical slot dependendo da altura do filho, caso em que o `VerticalOptions` configuração é irrelevante.

Se vertical `StackLayout` em si é irrestrita & #x 2014; que está se seu `VerticalOptions` configuração é `Start`, `Center`, ou `End`, em seguida, a altura do `StackLayout` é a altura total de seus filhos.

No entanto, se vertical `StackLayout` verticalmente é restrito & #x 2014; se seu `VerticalOptions` configuração é `Fill`& #x 2014; em seguida, a altura do `StackLayout` será a altura de seu contêiner, que pode ser maior que a altura total do seus filhos. Se esse for o caso, e se tem pelo menos um filho um `VerticalOptions` configuração com um `Expands` sinalizador de `true`, em seguida, o espaço extra no `StackLayout` é alocado igualmente entre todos os filhos com um `Expands` sinalizador de `true`. A altura total de filhos, em seguida, será igual a altura do `StackLayout`e o `Alignment` parte do `VerticalOptions` determina como o filho é posicionado verticalmente no slot.

Isso é demonstrado no [ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) exemplo.

## <a name="frame-and-boxview"></a>Quadro e BoxView

Esses dois modos de exibição retangulares geralmente são usados para fins de apresentação.

O [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) exibe um quadro retangular em torno de outro modo de exibição, que pode ser um layout, como `StackLayout`. `Frame` herda um [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) que você definir para o modo de exibição a ser exibido dentro de `Frame`. O `Frame` é transparente por padrão. Defina as seguintes três propriedades para personalizar a aparência do quadro:

- O [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/) propriedade para torná-lo visível. É comum para definir `OutlineColor` para `Color.Accent` quando você não souber o esquema de cores subjacente.
- O [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/) propriedade pode ser definida como `true` para exibir uma sombra preta em dispositivos iOS.
- Definir o [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedade para um `Thickness` valor deixar um espaço entre o e o quadro do conteúdo. O valor padrão é de 20 unidades em todos os lados.

O `Frame` padrão `HorizontalOptions` e `VerticalOptions` valores de `LayoutOptions.Fill`, o que significa que o `Frame` preencher seu contêiner. Com outras configurações, o tamanho do `Frame` é baseado no tamanho do seu conteúdo.

O `Frame` é demonstrada no [ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) exemplo.

O [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) exibe uma área retangular de cor especificado pelo seu [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propriedade.

Se o `BoxView` é restrito (seu `HorizontalOptions` e `VerticalOptions` propriedades têm suas configurações padrão de `LayoutOptions.Fill`), o `BoxView` preenche o espaço disponível para ele. Se o `BoxView` irrestrita (com `HorizontalOptions` e `LayoutOptions` configurações de `Start`, `Center`, ou `End`), ele tem uma dimensão padrão de quadrado de 40 unidades. Um `BoxView` pode ser restrita em uma dimensão e irrestrita no outro.

Geralmente, você definirá o o [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propriedades de `BoxView` dar-lhe um tamanho específico. Isso é ilustrado pelo [ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) exemplo.

Você pode usar várias instâncias do `StackLayout` para combinar uma `BoxView` e várias `Label` instâncias em um `Frame` para exibir uma cor específica e, em seguida, coloque cada um desses modos de exibição em um `StackLayout` em um `ScrollView` para criar o atraente lista de cores mostrada a [ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) exemplo:

[![Captura de tela de blocos de cor tripla](images/ch04fg11-small.png "lista de cores")](images/ch04fg11-large.png "lista de cores")

## <a name="a-scrollview-in-a-stacklayout"></a>Um ScrollView em um StackLayout?

Colocar um `StackLayout` em uma `ScrollView` é comum, mas colocar um `ScrollView` em uma `StackLayout` às vezes também é conveniente. Em teoria, isso não deve ser possível porque os filhos de um vertical `StackLayout` são verticalmente irrestrita. Mas um `ScrollView` deve ser restrita verticalmente. Ele deve ser dada uma altura específica para que, em seguida, ele pode determinar o tamanho de seu filho de rolagem.

O truque é dar a `ScrollView` filho a `StackLayout` um `VerticalOptions` configuração de `FillAndExpand`. Isso é demonstrado no [ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) exemplo.

O **BlackCat** exemplo também demonstra como definir e acessar os recursos do programa que são inseridos na biblioteca de classe portátil (PCL). Isso pode ser feito com projetos de ativo compartilhado (SAPs), mas o processo é um pouco mais complicado, como o [ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) demonstra.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 4 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemplos do capítulo 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Exemplos de capítulo 4 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
