---
title: Resumo do capítulo 14. Layout absoluto
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 14. Layout absoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771127"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumo do capítulo 14. Layout absoluto

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Como `StackLayout`, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deriva de `Layout<View>` e herda uma propriedade `Children`. `AbsoluteLayout` implementa um sistema de layout que exige que o programador especifique as posições de seus filhos e, opcionalmente, seu tamanho. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` em unidades independentes do dispositivo. `AbsoluteLayout` também implementa um recurso de dimensionamento e posicionamento proporcional.

`AbsoluteLayout` deve ser considerada como um sistema de layout de finalidade especial para ser usado somente quando o programador puder impor um tamanho nos filhos (por exemplo, elementos `BoxView`) ou quando o tamanho do elemento não afetar o posicionamento de outros filhos. As propriedades `HorizontalOptions` e `VerticalOptions` não têm efeito sobre os filhos de um `AbsoluteLayout`.

Este capítulo também apresenta o recurso importante das *propriedades vinculáveis anexadas* que permitem que as propriedades definidas em uma classe (nesse caso `AbsoluteLayout`) sejam anexadas a outra classe (um filho da `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout no código

Você pode adicionar um filho à coleção de `Children` de um `AbsoluteLayout` usando o método de [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) padrão, mas `AbsoluteLayout` também fornece um método [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) estendido que permite especificar um [`Rectangle`](xref:Xamarin.Forms.Rectangle). Outro método de [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) requer apenas um [`Point`](xref:Xamarin.Forms.Point), caso em que o filho é irrestrito e se dimensiona.

Você pode criar um valor de `Rectangle` com um [Construtor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que exige quatro valores &mdash; os dois primeiros indicando a posição do canto superior esquerdo do filho em relação ao seu pai e os dois que indicam o tamanho do filho. Ou você pode usar um [Construtor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) que requer um `Point` e um valor de [`Size`](xref:Xamarin.Forms.Size) .

Esses métodos `Add` são demonstrados em [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), que posiciona `BoxView` elementos usando valores `Rectangle` e um elemento `Label` usando apenas um valor de `Point`.

O exemplo [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) usa 32 elementos de `BoxView` para criar o padrão xadrez. O programa dá aos elementos de `BoxView` um tamanho embutido em código de 35 unidades quadrado. O `AbsoluteLayout` tem seu `HorizontalOptions` e `VerticalOptions` definido como `LayoutOptions.Center`, o que faz com que o `AbsoluteLayout` tenha um tamanho total de 280 unidades quadrado.

## <a name="attached-bindable-properties"></a>Propriedades vinculáveis anexadas

Também é possível definir a posição e, opcionalmente, o tamanho de um filho de um `AbsoluteLayout` depois que ele foi adicionado à coleção de `Children` usando o método estático [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). O primeiro argumento é o filho; o segundo é um objeto `Rectangle`. Você pode especificar que os tamanhos filho são horizontal e/ou verticalmente, definindo valores de largura e altura para a constante de [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) .

O exemplo [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) coloca a `AbsoluteLayout` em um `ContentView` com um manipulador `SizeChanged` para chamar `AbsoluteLayout.SetLayoutBounds` em todos os filhos para torná-los o mais amplo possível.  

A propriedade vinculável anexada que `AbsoluteLayout` define é o campo somente leitura estático do tipo `BindableProperty` chamado [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). O método de `AbsoluteLayout.SetLayoutBounds` estático é implementado chamando `SetValue` no filho com o `AbsoluteLayout.LayoutBoundsProperty`. O filho contém um dicionário no qual a propriedade associável anexada e seu valor são armazenados. Durante o layout, o `AbsoluteLayout` pode obter esse valor chamando [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), que é implementado com uma chamada de `GetValue`.

## <a name="proportional-sizing-and-positioning"></a>Dimensionamento proporcional e o posicionamento

`AbsoluteLayout` implementa um recurso de dimensionamento e posicionamento proporcional. A classe define uma segunda propriedade vinculável anexada, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), com os métodos estáticos relacionados [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) e [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

O argumento para `AbsoluteLayout.SetLayoutFlags` e o valor de retorno de `AbsoluteLayout.GetLayoutFlags` é um valor do tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags), uma enumeração com os seguintes membros:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Você pode combinar tudo isso com o C# operador OR de bit a bit.

Com esses sinalizadores definidos, determinadas propriedades da estrutura de limites de layout de `Rectangle` usada para posicionar e dimensionar o filho são interpretadas proporcionalmente.

Quando o sinalizador de `WidthProportional` é definido, um valor de `Width` de 1 significa que o filho tem a mesma largura que o `AbsoluteLayout`. Uma abordagem semelhante é usada para a altura.

O posicionamento proporcional considera o tamanho. Quando o sinalizador de `XProportional` é definido, a propriedade `X` dos limites de layout `Rectangle` é proporcional. Um valor de 0 significa que a borda esquerda do filho está posicionada na borda esquerda da `AbsoluteLayout`, mas uma posição de 1 significa que a borda direita do filho está posicionada na borda direita da `AbsoluteLayout`, não além da borda direita da `AbsoluteLayout` como você pode esperar. Uma propriedade `X` de 0,5 centraliza o filho horizontalmente no `AbsoluteLayout`.

O exemplo [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) demonstra o uso de dimensionamento e posicionamento proporcionais.

## <a name="working-with-proportional-coordinates"></a>Trabalhar com coordenadas proporcionais

Às vezes, é mais fácil imaginar o posicionamento proporcional de maneira diferente de como ele é implementado na `AbsoluteLayout`. Você pode preferir trabalhar com coordenadas proporcionais em que uma propriedade `X` de 1 posiciona a borda esquerda do filho (em vez da borda direita) na borda direita da `AbsoluteLayout`.

Esse esquema alternativo de posicionamento pode ser chamada "coordinates filho fracionários." Você pode converter de coordenadas de filho fracionários para os limites de layout necessários para `AbsoluteLayout` usando as seguintes fórmulas:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

O exemplo [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) demonstra isso.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

Você pode usar um `AbsoluteLayout` em XAML e definir as propriedades vinculáveis anexadas nos filhos de um `AbsoluteLayout` usando valores de atributo de `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Isso é demonstrado nos exemplos de [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . O último programa contém 32 `BoxView` elementos, mas usa um `Style` implícito que inclui a propriedade `AbsoluteLayout.LayoutFlags` para reduzir o mínimo da marcação.

Um atributo em XAML que consiste em um nome de classe, um ponto e um nome de propriedade é *sempre* uma propriedade vinculável anexada.

## <a name="overlays"></a>Sobreposições

Você pode usar `AbsoluteLayout` para construir uma *sobreposição*, que aborda a página com outros controles, talvez para proteger o usuário de interagir com os controles normais na página.

O exemplo [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) demonstra essa técnica e também demonstra a [`ProgressBar`](xref:Xamarin.Forms.ProgressBar), que exibe a extensão para a qual um programa concluiu uma tarefa.

## <a name="some-fun"></a>Alguma diversão

O exemplo [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exibe a hora atual com uma exibição de matriz de pontos de 5x7 simulada. Cada ponto é um `BoxView` (há 228 deles) dimensionado e posicionado na `AbsoluteLayout`.

[![Captura de tela tripla de relógio de matriz de ponto](images/ch14fg08-small.png "Relógio de matriz de ponto")](images/ch14fg08-large.png#lightbox "Relógio de matriz de ponto")

O programa [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima dois objetos `Label` para saltar horizontalmente e verticalmente na tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 14 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemplos do capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
